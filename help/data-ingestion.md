---
title: 「資料擷取」
description: 「資料擷取API概觀」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '952'
ht-degree: 8%

---


# 資料擷取

資料擷取API是一項高容量、低延遲、高可用性的服務，專門用於以最低延遲有效處理大量人員和人員相關資料的擷取。 

透過提交非同步執行的請求來內嵌資料。 請求狀態可透過訂閱事件從 [Marketo可觀察性資料串流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup/). &#x200B;

介面提供兩種物件型別：人員、自訂物件。 記錄作業僅限「插入或更新」。

資料擷取API目前處於私人測試階段。 受邀者必須擁有 [Marketo Engage效能層套件](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835).

## 驗證

資料擷取API使用與Marketo REST API相同的OAuth 2.0驗證方法來產生存取權杖，但存取權杖必須透過HTTP標頭傳遞 `X-Mkto-User-Token`. 您無法透過查詢引數傳遞存取權杖。

透過標題的範例存取Token：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 權限

資料擷取使用與Marketo REST API相同的許可權模型，不需要使用任何其他特殊許可權，不過每個端點需要特定許可權。

| 端點 | 權限 |
|---|---|
| 人員 | 讀寫潛在客戶 |
| 自訂物件 | 讀寫自訂物件 |

## 標頭

資料擷取會使用以下自訂HTTP標頭。

### 請求

| 索引鍵 | 值 | 必填 | 說明 |
|---|---|---|---|
| X-Correlation-Id | 任意字串（長度上限為255個字元）。 | 否 | 可用於透過系統追蹤請求。 請參閱Marketo可觀察性資料流 |
| X-Request-Source | 任意字串（長度上限為50個字元）。 | 否 | 可用於透過系統追蹤要求的來源。 請參閱Marketo可觀察性資料流 |

### 回應

| 索引鍵 | 值 | 必填 | 說明 |
|---|---|---|---|
| X-Request-Id | 唯一請求識別碼。 | 是 | |

## 請求

使用HTTPPOST方法將資料傳送至伺服器。

資料表示以application/json的形式包含在請求本文中。

網域名稱是： `mkto-ingestion-api.adobe.io`

路徑開頭為 `/subscriptions/_MunchkinId_` 位置 `_MunchkinId_` 專屬於您的Marketo執行個體。 您可以在Marketo EngageUI中找到您的Munchkin ID，位於 **管理員** >**我的帳戶** > **支援資訊**. 路徑的其餘部分用於指定感興趣的資源。

人員的URL範例：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自訂物件的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

## 回應

所有回應會透過 `X-Request-Id` 標頭。

透過標題的請求ID範例：

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 成功

當呼叫成功時，會傳回202狀態。 未傳回任何回應內文。

成功回應的範例：

`HTTP/1.1 202 Accepted` `X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598` `Content-Length: 0` `Date: Wed, 18 Oct 2023 18:56:49 GMT`

### 錯誤

當呼叫產生錯誤時，會傳回非202狀態以及包含其他錯誤詳細資料的回應內文。 回應內文為application/json，並包含具有成員的單一物件 `error_code` 和 `message`.

以下是來自Adobe Developer閘道的可重複使用的錯誤代碼。

| HTTP狀態代碼 | error_code | 訊息 |
|--- |--- |--- |
| 401 | 401013 | Oauth代號無效 |
| 403 | 403010 | 缺少Oauth代號 |
| 404 | 404040 | 找不到資源 |
| 429 | 429001 | 達到服務使用量限制 |

以下是資料擷取API特有的錯誤代碼，由三個區段組成。 前三位是狀態(由AdobeIO閘道傳回)，後面是零「0」，後面是三位數。

| HTTP狀態代碼 | error_code | 訊息 |
|--- |--- |--- |
| 400 | 4000801 | 錯誤的請求 |
| 400 | 4000802 | 無效的資料 |
| 403 | 4030801 | 未獲授權 |
| 429 | 4290801 | 已達到每日配額 |
| 500 | 5000801 | 內部伺服器錯誤 |

錯誤回應範例：

`HTTP/1.1 403 Forbidden` `Content-Type: application/json` `Content-Length: 54` `Date: Wed, 18 Oct 2023 19:10:07 GMT` `X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw` `{"error_code":"403010","message":"Oauth token is missing"}`

## 重試次數

偵測到暫時性錯誤時，服務會重試作業三次。 第一次重試在5分鐘的等待期間後發生，第二次在30分鐘後發生，最後第三次在30分鐘後發生。 重試的原因有很多，主要是相依服務逾時或暫時無法使用時。

## 端點

擷取端點適用於人員和自訂物件。

### 人員

用於更新插入人員記錄的端點。

| 方法 |
|---|
| POST |

| 路徑 |
|---|
| /subscriptions/{munchkinId}/persones |

| HeadersKey | 值 |
|---|---|
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

要求內文

| 索引鍵 | 資料類型 | 必填 | 值 | 預設值 |
|---|---|---|---|---|
| 優先順序 | 字串 | 否 | 請求的優先順序：normalhigh | 一般 |
| partitionName | 字串 | 否 | 個人資料分割的名稱 | 預設 |
| dedupeField | 物件 | 否 | 要取消重複資料刪除的屬性。 允許一或兩個屬性名稱。 在AND作業中使用兩個屬性。 例如，如果兩者 `email` 和 `firstName` 都指定了，兩者都可用來使用AND作業來查詢人員。 支援的屬性包括：`idemail`， `sfdcAccountId`， `sfdcContactId`， `sfdcLeadId`， `sfdcLeadOwnerIdCustom` 屬性（僅限「string」和「integer」型別） | 電子郵件 |
| 人員 | 物件陣列 | 是 | 個人的屬性名稱 — 值組清單 | - |

| 權限 |
|---|
| 讀寫潛在客戶 |

#### 人員範例

```
POST /subscriptions/{munchkinId}/persons
```

```
Content-Type: application/json
X-Mkto-User-Token: {accessToken}
```

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

```
HTTP/1.1 202
X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw
```

### 自訂物件

用來更新插入自訂物件記錄的端點。

| 方法 |
|---|
| POST |

| 路徑 |
|---|
| /subscriptions/{munchkinId}/customobjects/{customObjectAPIName} |

標頭

| 索引鍵 | 值 |
|---|---|
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

要求內文 | 索引鍵 | 資料型別 | 必填 | 值 | 預設值 | |—|—|—|—|—| | 優先順序 | 字串 | 否 | 請求的優先順序：normalhigh | 一般 | | dedupeBy | 字串 | 否 | 要重複資料刪除的屬性： dedupeFieldsmarketoGUID | dedupeField | | 自訂物件 | 物件陣列 | 是 | 物件的屬性名稱 — 值組清單。 | - |

| 權限 |
|---|
| 讀寫自訂物件 |

#### 個人不存在

如果在請求中指定了某個「人員」的連結欄位，但該「人員」不存在，則會發生多次重試。 若在重試期間（65分鐘）新增該人員，則更新成功。 例如，如果連結欄位為 `email` 在「人員」上，但「人員」不存在，則會發生重試。

#### 自訂物件範例

```
POST /subscriptions/{munchkinId}/customobjects/{customObjectAPIName}
```

```
Content-Type: application/json
X-Mkto-User-Token: {accessToken}
```

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

```
HTTP/1.1 202
X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw
```

## 限制

以下列出護欄的使用方式：

- 請求大小上限： 1 MB
- 每個物件型別的每個請求的最大物件數： 1,000
- 每個使用者端ID每秒的最大要求數： 5,000
- 每日最大物件數： 10,000,000

## 資料擷取API與REST API

以下是資料擷取API與其他Marketo REST API之間的差異清單：

- 這不是完整的CRUD介面，僅支援「更新插入」
- 若要驗證，您必須使用傳遞存取權杖 `X-Mkto-User-Token` 頁首
- URL網域名稱為 `mkto-ingestion-api.adobe.io`
- URL路徑的開頭為 `/subscriptions/_MunchkinId_`
- 沒有查詢引數
- 如果呼叫成功，則會傳回202狀態且回應本文是空的
- 如果呼叫失敗，會傳回非202狀態，且回應本文包含 `{ "error_code" : "_Error Code_", "message" : "_Message_" }`
- 透過傳回請求ID `X-Request-Id` 頁首
