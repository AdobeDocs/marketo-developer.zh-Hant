---
title: 資料攝取
feature: REST API, Dynamic Content
description: 使用Marketo API使用資料。
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: 8a785b0719e08544ed1a87772faf90bd9dda3077
workflow-type: tm+mt
source-wordcount: '965'
ht-degree: 10%

---

# 資料擷取API

資料擷取API是一項高容量、低延遲、高可用性的服務，專門用於以最低延遲有效處理大量與人員相關的資料擷取。

透過提交非同步執行的請求來內嵌資料。 訂閱來自[Marketo可觀察性資料流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup)的事件，即可擷取要求狀態&#x200B;。

介面提供兩種物件型別：人員、自訂物件。 記錄作業僅限「插入或更新」。

資料擷取API目前處於私人測試階段。  受邀者必須擁有[Marketo Engage效能階層](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)套件的權益。

## Authentication

資料擷取API使用與Marketo REST API相同的OAuth 2.0驗證方法來產生存取權杖，但必須透過HTTP標頭`X-Mkto-User-Token`傳遞存取權杖。 您無法透過查詢引數傳遞存取權杖。

透過標題的範例存取Token：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 權限

資料擷取使用與Marketo REST API相同的許可權模型，不需要使用任何其他特殊許可權，不過每個端點需要特定許可權。

| 端點 | 權限 |
|-|-|
| 人員 | 讀寫潛在客戶 |
| 自訂物件 | 讀寫自訂物件 |

## 標頭

資料擷取會使用以下自訂HTTP標頭。

### 請求

| 索引鍵 | 值 | 必要 | 說明 |
| - | - | - | - |
| X-Correlation-Id | 任意字串（長度上限為255個字元）。 | 無 | 可用於透過系統追蹤請求。  請參閱Marketo可觀察性資料流 |
| X-Request-Source | 任意字串（長度上限為50個字元）。 | 無 | 可用來透過系統追蹤要求的來源。  請參閱Marketo可觀察性資料流 |

### 回應

| 索引鍵 | 值 | 必要 |
| - | - | - |
| X-Request-Id | 唯一請求識別碼。 | 是 |

## 請求

使用HTTP POST方法將資料傳送至伺服器。

資料表示以application/json的形式包含在請求本文中。

網域名稱是： `mkto-ingestion-api.adobe.io`

路徑以`/subscriptions/MunchkinId`開頭，其中MunchkinId是您的Marketo執行個體專屬的。 您可以在Marketo Engage UI的&#x200B;**管理員** > **我的帳戶** > **支援資訊**&#x200B;下找到您的Munchkin ID。  路徑的其餘部分用於指定感興趣的資源。

人員的URL範例：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自訂物件的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

### 回應

所有回應都會透過`X-Request-Id`標頭傳回唯一的請求識別碼。

透過標題的請求ID範例：

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 成功

當呼叫成功時，會傳回202狀態。  未傳回任何回應內文。

成功回應的範例：

```
HTTP/1.1 202 Accepted
X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598
Content-Length: 0
Date: Wed, 18 Oct 2023 18:56:49 GMT
```

### 錯誤

當呼叫產生錯誤時，會傳回非202狀態以及包含其他錯誤詳細資料的回應內文。  回應內文為application/json，且包含單一物件，內含成員error_code和message。

以下是來自Adobe Developer閘道的可重複使用的錯誤代碼。

| HTTP狀態代碼 | error_code | 訊息 |
| - | - | - |
| 401 | 401013 | Oauth代號無效 |
| 403 | 403010 | 缺少Oauth代號 |
| 404 | 404040 | 找不到資源 |
| 429 | 429001 | 達到服務使用量限制 |

以下是資料擷取API特有的錯誤代碼，由3個區段組成。  前三位是狀態(由Adobe Developer閘道傳回)，後面接著零「0」，再後面三位。

| HTTP狀態代碼 | error_code | 訊息 |
| - | - | - |
| 400 | 4000801 | 錯誤的請求 |
| 400 | 4000802 | 無效的資料 |
| 403 | 4030801 | 未經授權 |
| 429 | 4290801 | 已達到每日配額 |
| 500 | 5000801 | 內部伺服器錯誤 |

## 重試次數

偵測到暫時性錯誤時，服務會重試作業三次。  第一次重試在5分鐘的等待期間後發生，第二次在30分鐘後發生，最後第三次在30分鐘後發生。  重試的原因有很多，主要是相依服務逾時或暫時無法使用時。

## 端點

擷取端點適用於人員和自訂物件。

### 人員

用於更新插入人員記錄的端點。

| 方法 | 路徑 |
| - | - |
| POST | /subscriptions/{munchkinId}/person |

#### 標頭

| 索引鍵 | 值 |
| - | - |
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 值 | 預設值 |
| - | - | - | - | - |
| 優先順序 | 字串 | 無 | 請求的優先順序：正常或高 | 一般 |
| partitionName | 字串 | 無 | 個人資料分割的名稱 | 預設 |
| dedupeField | 物件 | 無 | 要取消重複資料刪除的屬性。 允許一或兩個屬性名稱。 <br/>在AND作業中使用兩個屬性。 例如，如果同時指定`email`和`firstName`，它們都會用來使用AND作業來查詢人員。 <br/>支援的屬性為： `id`、`email`、`sfdcAccountId`、`sfdcContactId`、`sfdcLeadId` `sfdcLeadOwnerId`、自訂屬性（僅限「字串」和「整數」型別）、`email` |
| 人員 | 物件陣列 | 是 | 個人的屬性名稱 — 值組清單 | - |

所需的許可權為`Read-Write Lead`。

### 人員範例

#### 請求

`POST /subscriptions/{munchkinId}/persons`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "priority": "high",
   "partitionName": "EMEA",
   "dedupeFields": {
      "field1": "email",
      "field2": "firstName"
   },
   "persons":[
      {
         "email": "brooklyn.parker@karnv.com",
         "firstName": "Brooklyn",
         "lastName": "Parker"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal"
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 自訂物件

用來更新插入自訂物件記錄的端點

| 方法 | 路徑 |
| - | - |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### 標頭

| 索引鍵 | 值 |
| - | - |
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 值 | 預設值 |
| - |- | - | - | - |
| 優先順序 | 字串 | 無 | 請求的優先順序：正常、高 | 一般 |
| dedupeBy | 字串 | 無 | 要重複資料刪除的屬性： dedupeFields、marketoGUID | dedupeField |
| 自訂物件 | 物件陣列 | 是 | 物件的屬性名稱 — 值組清單。 | - |

必要的許可權為`Read-Write Custom Object`。

如果在請求中指定了某個「人員」的連結欄位，但該「人員」不存在，則會發生多次重試。 若在重試期間（65分鐘）新增該人員，則更新成功。 例如，如果連結欄位是「人員」的電子郵件，而「人員」不存在，則會發生重試。

### 自訂物件範例

#### 請求

`POST /subscriptions/{munchkinId}/customobjects/{customObjectAPIName}`


#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "dedupeBy": "dedupeFields",
   "priority": "high",
   "customObjects": [
      {
         "email": "brooklyn.parker@karnv.com",
         "vin": "20UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 330i",
         "year": 2003
      },
      {
         "email": "johnny.neal@yvu30.com",
         "vin": "19UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 325i",
         "year": 1989
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

## 限制

以下為護欄的使用清單：

* 請求大小上限： 1 MB
* 每個物件型別的每個請求的最大物件數： 1,000
* 每個使用者端ID每秒的最大要求數： 5,000
* 每日最大物件數： 10,000,000

## 資料擷取API與REST API

以下是資料擷取API與其他Marketo REST API之間的差異清單：

* 這不是完整的CRUD介面，僅支援更新插入
* 若要驗證，您必須使用`X-Mkto-User-Token`標頭傳遞存取權杖
* URL網域名稱是`mkto-ingestion-api.adobe.io`
* URL路徑的開頭為`/subscriptions/MunchkinId`
* 沒有查詢引數
* 如果呼叫成功，則會傳回202狀態且回應本文是空的
* 如果呼叫失敗，會傳回非202狀態，而且回應本文包含`{ "error_code" : "Error Code", "message" : "Message" }`
* 要求識別碼是透過`X-Request-Id`標頭傳回
