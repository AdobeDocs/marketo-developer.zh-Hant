---
title: 資料攝取
feature: REST API, Dynamic Content
description: 使用Marketo Data Ingestion API大量擷取人員、自訂物件、公司和方案成員資料，並降低延遲擷取次數。
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: 6dc068f92d5b0c94035ca484fd1508dfe87bbd76
workflow-type: tm+mt
source-wordcount: '1789'
ht-degree: 13%

---

# 資料擷取API

資料擷取API是一項高容量、低延遲、高可用性的服務，專門用於以最低延遲有效處理大量與人員相關的資料擷取。

透過提交非同步執行的請求來內嵌資料。 訂閱來自[Marketo可觀察性資料流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup)的事件，即可擷取要求狀態。

介面提供四種物件型別：人員、自訂物件、公司和程式成員。 記錄作業僅限「插入或更新」，但同時支援刪除的「程式成員」除外。

>[!NOTE]
>
>存取資料擷取API需要取得[Marketo Engage效能層](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)套件的許可權。

## Authentication

資料擷取API使用與Marketo REST API相同的OAuth 2.0驗證方法來產生存取權杖，但必須透過HTTP標頭`X-Mkto-User-Token`傳遞存取權杖。 您無法透過查詢引數傳遞存取權杖。

透過標題的範例存取Token：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 權限

資料擷取使用與Marketo REST API相同的許可權模型，不需要使用任何其他特殊許可權，不過每個端點需要特定許可權。

| 端點 | 權限 |
| --- | --- |
| 人員 | 讀寫潛在客戶 |
| 自訂物件 | 讀寫自訂物件 |
| 公司 | 讀寫公司 |
| 計畫成員 | 讀寫潛在客戶 |

## 支援的物件型別

| 物件類型 | 支援的作業 |
| --- | --- |
| 人員 | 更新插入（插入或更新） |
| 自訂物件 | 更新插入（插入或更新） |
| 公司 | 同步(`createOnly`， `updateOnly`， `createOrUpdate`) |
| 計畫成員 | 同步（更新插入狀態）、刪除（從程式中移除） |

## 標頭

資料擷取會使用以下自訂HTTP標頭。

### 請求

| 索引鍵 | 價值 | 必要 | 說明 |
| --- | --- | --- | --- |
| `X-Correlation-Id` | 任意字串（長度上限為255個字元）。 | 無 | 可用於透過系統追蹤請求。 請參閱Marketo可觀察性資料流 |
| `X-Request-Source` | 任意字串（長度上限為50個字元）。 | 無 | 可用來透過系統追蹤要求的來源。 請參閱Marketo可觀察性資料流 |

### 回應

| 索引鍵 | 價值 | 必要 |
| --- | --- | --- |
| `X-Request-Id` | 唯一請求識別碼。 | 是 |

## 請求

使用HTTP POST方法將資料傳送至伺服器。

資料表示以application/json的形式包含在請求本文中。

網域名稱是： `mkto-ingestion-api.adobe.io`

路徑以`/subscriptions/MunchkinId`開頭，其中MunchkinId是您的Marketo執行個體專屬的。 您可以在Marketo Engage UI的&#x200B;**管理員** > **我的帳戶** > **支援資訊**&#x200B;下找到您的Munchkin ID。  路徑的其餘部分用於指定感興趣的資源。

人員的URL範例：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自訂物件的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

公司的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/companies`

方案成員的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/programmembers`

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

當呼叫產生錯誤時，會傳回非202狀態以及包含其他錯誤詳細資料的回應內文。 回應本文是`application/json`，且包含具有成員`error_code`與`message`的單一物件。

以下是來自Adobe Developer閘道的可重複使用的錯誤代碼。

| HTTP狀態代碼 | error_code | 訊息 |
| --- | --- | --- |
| 401 | 401013 | Oauth代號無效 |
| 403 | 403010 | 缺少Oauth代號 |
| 404 | 404040 | 找不到資源 |
| 429 | 429001 | 達到服務使用量限制 |

以下是資料擷取API特有的錯誤代碼，由3個區段組成。  前三位是狀態（由Adobe Developer閘道傳回），後面接著零「0」，再後面三位。

| HTTP狀態代碼 | error_code | 訊息 |
| --- | --- | --- |
| 400 | 4000801 | 錯誤的請求 |
| 400 | 4000802 | 無效的資料 |
| 403 | 4030801 | 未經授權 |
| 429 | 4290801 | 已達到每日配額 |
| 500 | 5000801 | 內部伺服器錯誤 |

## 重試次數

偵測到暫時性錯誤時，服務會重試操作。 重試的原因有很多，主要是相依服務逾時或暫時無法使用時。

重試間隔：

* 初始操作和第一次重試：5分鐘
* 第1和第2:15分鐘
* 第2和第3:20分鐘
* 第3和第4:20分鐘
* 第4和第5 ：2小時
* 第5次重試後 — > 3小時

## 端點

擷取端點適用於人員、自訂物件、公司和方案成員。

### 人員

用於更新插入人員記錄的端點。

| 方法 | 路徑 |
| --- | --- |
| POST | /subscriptions/{munchkinId}/person |

#### 標頭

| 索引鍵 | 價值 |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 價值 | 預設值 |
| --- | --- | --- | --- | --- |
| `priority` | 字串 | 無 | 請求的優先順序：正常或高 | 一般 |
| `partitionName` | 字串 | 無 | 個人資料分割的名稱 | 預設 |
| `dedupeFields` | 物件 | 無 | 要取消重複資料刪除的屬性。 允許一或兩個屬性名稱。<br/> 在AND作業中使用兩個屬性。 例如，如果同時指定`email`和`firstName`，它們都會用來使用AND作業來查詢人員。 <br/>支援的屬性為： `id`、`email`、`sfdcAccountId`、`sfdcContactId`、`sfdcLeadId` `sfdcLeadOwnerId`、自訂屬性（僅限「字串」和「整數」型別）、`email` |  |
| `persons` | 物件陣列 | 是 | 個人的屬性名稱 — 值組清單 | - |

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
         "lastName": "Parker",
         "company": "Karnv"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal",
         "company": "Acme Inc"
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 自訂物件

用來更新插入自訂物件記錄的端點。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### 標頭

| 索引鍵 | 價值 |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 價值 | 預設值 |
| --- | --- | --- | --- | --- |
| `priority` | 字串 | 無 | 請求的優先順序：正常、高 | 一般 |
| `dedupeBy` | 字串 | 無 | 要重複資料刪除的屬性： dedupeFields、marketoGUID | dedupeField |
| `customObjects` | 物件陣列 | 是 | 物件的屬性名稱 — 值組清單。 | - |

必要的許可權為`Read-Write Custom Object`。

如果在請求中指定了某個「人員」的連結欄位，但該「人員」不存在，則會發生多次重試。 若在重試期間（65分鐘）新增該人員，則更新成功。 例如，如果Person的連結欄位為`email`，且Person不存在，則會發生重試。

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

### 公司

用來同步公司記錄的端點。 支援透過外部公司ID或Marketo內部ID執行重複資料刪除的建立、更新和更新插入操作。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/companies` |

#### 標頭

| 索引鍵 | 價值 | 必要 |
| --- | --- | --- |
| `Content-Type` | application/json | 是 |
| `X-Mkto-User-Token` | {accessToken} | 是 |
| `X-Correlation-Id` | 任意字串（長度上限為255個字元） | 無 |
| `X-Request-Source` | 任意字串（長度上限為50個字元） | 無 |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 價值 | 預設值 |
| --- | --- | --- | --- | --- |
| `action` | 字串 | 無 | 同步動作： `createOnly`、`updateOnly`或`createOrUpdate` | `createOrUpdate` |
| `dedupeBy` | 字串 | 無 | 要取消重複資料刪除的欄位： `dedupeFields`或`idField` （不區分大小寫）。 對於`createOnly`和`createOrUpdate`，只允許使用`dedupeFields`。 對於`updateOnly`，兩者皆可。 | `dedupeFields` |
| `input` | 物件陣列 | 是 | 公司屬性名稱 — 值配對清單。 接受JSON索引鍵`input`或`companies`。 | - |

`input`陣列中的每個公司物件支援下列欄位：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| `externalCompanyId` | 字串 | 條件式 | 外部公司識別碼。 當`dedupeBy`為`dedupeFields`時為必要。 `dedupeBy`為`idField`時不允許使用。 |
| `id` | 長整數 | 條件式 | Marketo內部公司ID。 當`dedupeBy`為`idField`且`action`為`updateOnly`時為必要。 `dedupeBy`為`dedupeFields`時不允許使用。 |
| `company` | 字串 | 無 | 公司名稱。 |
| （任何欄位） | 任何 | 無 | 由[描述公司](companies.md)定義的其他標準或自訂公司欄位。 欄位名稱不區分大小寫。 |

必要的許可權為`Read-Write Company`。

### 公司範例

#### 請求

`POST /subscriptions/{munchkinId}/companies`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "externalCompanyId": "ext-company-001",
         "company": "Acme Corporation",
         "industry": "Technology",
         "numberOfEmployees": 5000,
         "annualRevenue": 100000000
      },
      {
         "externalCompanyId": "ext-company-002",
         "company": "Globex Industries",
         "industry": "Manufacturing",
         "numberOfEmployees": 1200
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 公司依ID更新範例

```json
{
   "action": "updateOnly",
   "dedupeBy": "idField",
   "input": [
      {
         "id": 12345,
         "company": "Acme Corporation (Renamed)",
         "numberOfEmployees": 5500
      }
   ]
}
```

### 公司驗證規則

| 規則 | 詳細資料 |
| --- | --- |
| 動作 | 必須是： `createOnly`、`updateOnly`、`createOrUpdate`其中之一。 區分大小寫。 |
| dedupeBy | 必須是`dedupeFields`或`idField` （不區分大小寫）。 預設為`dedupeFields`。 |
| dedupeBy +動作 | `createOnly`和`createOrUpdate`僅允許`dedupeFields`。 `updateOnly`同時允許`dedupeFields`和`idField`。 |
| 當`dedupeBy=dedupeFields` | 每個公司都必須有`externalCompanyId`。 欄位`id`不得存在。 |
| 當`dedupeBy=idField` | 每個公司都必須有`id`。 欄位`externalCompanyId`不得存在。 |
| `input` / `companies` | 不得為Null或空白。 |
| 每個請求的最大物件數 | 1,000 |

### 程式成員（同步）

端點用於同步程式成員狀態、新增潛在客戶到程式或更新其程式狀態。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers` |

#### 標頭

| 索引鍵 | 價值 | 必要 |
| --- | --- | --- |
| Content-Type | application/json | 是 |
| X-Mkto-User-Token | {accessToken} | 是 |
| X-Correlation-Id | 任意字串（長度上限為255個字元） | 無 |
| X-Request-Source | 任意字串（長度上限為50個字元） | 無 |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 價值 | 預設值 |
| --- | --- | --- | --- | --- |
| 計畫 | 物件陣列 | 是 | 程式作業清單。 每個都會指定程式、目標狀態以及要同步處理的銷售機會。 | - |

`programs`陣列中的每個物件都包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| programId | 長整數 | 是 | Marketo方案ID。 必須是正整數。 |
| 狀態 | 字串 | 是 | 要設定的程式成員狀態，例如`"Member"`或`"Influenced"`。 接受JSON索引鍵`statusName`或`status`。 值不得為`"Not in Program"`；請改用刪除端點。 |
| 成員 | 物件陣列 | 是 | 要在方案中新增或更新之潛在客戶參考的清單。 接受JSON索引鍵`input`或`members`。 |

`members`陣列中的每個物件都包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| leadId | 長整數 | 是 | Marketo銷售機會ID。 |
| （任何欄位） | 任何 | 無 | 其他自訂方案成員欄位。 欄位名稱不區分大小寫。 |

必要的許可權為`Read-Write Lead`。

### 程式成員同步範例

#### 請求

`POST /subscriptions/{munchkinId}/programmembers`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "programs": [
      {
         "programId": 1001,
         "status": "Member",
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "status": "Influenced",
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: e3d92152-0fb1-444a-8f8f-29d5a2338598`

### 程式成員同步驗證規則

| 規則 | 詳細資料 |
| --- | --- |
| 計畫 | 不得為Null或空白。 |
| programId | 必填。 必須是正整數。 |
| 狀態 | 必填。 不得為空白。 不得為`"Not in Program"` （區分大小寫）。 請改用刪除端點。 |
| 成員 | 不得為Null或空白。 |
| leadId | 輸入陣列中的每個成員都需要。 |
| 每個請求的最大潛在客戶 | 所有方案中共有1,000名成員。 |

### 計畫成員（刪除）

用於從程式中移除潛在客戶的端點。 這會將潛在客戶的成員資格狀態設為`"Not in Program"`，並從該計畫中移除該成員。

>[!NOTE]
>
>此端點使用POST而非DELETE，因為要求需要包含結構化資料的JSON內文。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers/delete` |

#### 標頭

| 索引鍵 | 價值 | 必要 |
| --- | --- | --- |
| Content-Type | application/json | 是 |
| X-Mkto-User-Token | {accessToken} | 是 |
| X-Correlation-Id | 任意字串（長度上限為255個字元） | 無 |
| X-Request-Source | 任意字串（長度上限為50個字元） | 無 |

#### 要求內文

| 索引鍵 | 資料類型 | 必要 | 價值 | 預設值 |
| --- | --- | --- | --- | --- |
| 計畫 | 物件陣列 | 是 | 程式刪除作業清單。 每個都指定計畫和要移除的潛在客戶。 | - |

`programs`陣列中的每個物件都包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| programId | 長整數 | 是 | Marketo方案ID。 必須是正整數。 |
| 成員 | 物件陣列 | 是 | 要從方案中移除的潛在客戶參考清單。 接受JSON索引鍵`input`或`members`。 |

`members`陣列中的每個物件都包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| leadId | 長整數 | 是 | Marketo銷售機會ID。 |

必要的許可權為`Read-Write Lead`。

### 程式成員刪除範例

#### 請求

`POST /subscriptions/{munchkinId}/programmembers/delete`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "programs": [
      {
         "programId": 1001,
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890`

### 程式成員刪除驗證規則

| 規則 | 詳細資料 |
| --- | --- |
| 計畫 | 不得為Null或空白。 |
| programId | 必填。 必須是正整數。 |
| 成員 | 不得為Null或空白。 |
| leadId | 輸入陣列中的每個成員都需要。 |
| 每個請求的最大潛在客戶 | 所有方案中共有1,000名成員。 |

## 限制

以下是護欄的更新清單：

* 請求大小上限： 1 MB
* 每個物件型別的每個請求的最大物件數： 1,000
* 每個使用者端ID每秒的最大要求數： 5,000
* 每日最大物件數： 10,000,000

這些限制會統一套用至人員、自訂物件、公司和方案成員。 對於方案成員，「每個請求的物件」是單一請求中所有方案的潛在客戶參考總數。

## 資料擷取API與REST API

以下是資料擷取API與其他Marketo REST API之間的差異清單：

* 若要驗證，您必須使用`X-Mkto-User-Token`標頭傳遞存取權杖
* URL網域名稱是`mkto-ingestion-api.adobe.io`
* URL路徑的開頭為`/subscriptions/MunchkinId`
* 沒有查詢引數
* 如果呼叫成功，則會傳回202狀態且回應本文是空的
* 如果呼叫失敗，會傳回非202狀態，而且回應本文包含`{ "error_code" : "Error Code", "message" : "Message" }`
* 要求識別碼是透過`X-Request-Id`標頭傳回
