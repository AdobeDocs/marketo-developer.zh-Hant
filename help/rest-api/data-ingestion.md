---
title: 資料攝取
feature: REST API, Dynamic Content, Static Lists
description: 使用Marketo資料擷取API來擷取大量人員、自訂物件、公司、方案成員和清單，並降低延遲的資料擷取。
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
TQID: https://experienceleague.adobe.com/xby7hs-CSLrVzy-FXEBi1FeU1-ca7vI4kB85BYJ9snk
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 2151
ht-degree: 14%

---

# 資料擷取API

資料擷取API是一項高容量、低延遲、高可用的服務。 使用它可以以最低延遲擷取大量人員和個人相關資料。

資料擷取要求會非同步執行。 若要擷取要求狀態，請從[Marketo可觀察性資料流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup)訂閱事件。

API提供五種物件型別的介面：

- 「人員」、「自訂物件」和「公司」支援「插入或更新」作業。
- 程式成員支援「插入或更新」和刪除作業。
- 清單（靜態清單）支援新增和移除操作。

閱讀[資料擷取API檔案](https://developer.adobe.com/marketo-apis/api/data-ingestion)。

>[!NOTE]
>
>存取資料擷取API需要取得[Marketo Engage效能層](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)套件的許可權。

## Authentication

資料擷取API使用與Marketo REST API相同的OAuth 2.0驗證方法來產生存取權杖。 在`X-Mkto-User-Token` HTTP標頭中傳遞存取權杖。 您無法將其作為查詢引數傳遞。

以下範例會在標頭中傳遞存取權杖：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 權限

資料擷取使用Marketo REST API許可權模型，不需要額外許可權。 每個端點都需要特定的現有許可權，如下表所示。

| 端點 | 權限 |
| --- | --- |
| 人員 | 讀寫潛在客戶 |
| 自訂物件 | 讀寫自訂物件 |
| 公司 | 讀寫公司 |
| 計畫成員 | 讀寫潛在客戶 |
| 清單 | 讀寫潛在客戶 |

## 支援的物件型別

| 物件類型 | 支援的作業 |
| --- | --- |
| 人員 | 更新插入（插入或更新） |
| 自訂物件 | 更新插入（插入或更新） |
| 公司 | 同步(`createOnly`， `updateOnly`， `createOrUpdate`) |
| 計畫成員 | 同步（更新插入狀態）、刪除（從程式中移除） |
| 清單 | 新增至清單、從清單移除 |

## 標頭

資料擷取支援下列自訂HTTP標頭。

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

將資料以application/json的形式納入請求內文中。

使用網域`mkto-ingestion-api.adobe.io`。

路徑以`/subscriptions/MunchkinId`開頭，其中MunchkinId是您的Marketo執行個體專屬的。 在Marketo Engage UI的&#x200B;**管理員** > **我的帳戶** > **支援資訊**&#x200B;下尋找您的Munchkin ID。 路徑的其餘部分會指定資源。

人員的URL範例：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自訂物件的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

公司的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/companies`

方案成員的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/programmembers`

清單的範例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/lists`

### 回應

每個回應都會在`X-Request-Id`標頭中傳回唯一請求識別碼。

透過標題的請求ID範例：

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 成功

成功的呼叫傳回狀態202且沒有回應內文。

成功回應的範例：

```http
HTTP/1.1 202 Accepted
X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598
Content-Length: 0
Date: Wed, 18 Oct 2023 18:56:49 GMT
```

### 錯誤

呼叫失敗時，會傳回非202狀態和包含錯誤詳細資料的回應內文。 `application/json`回應本文包含一個物件，其中具有`error_code`和`message`個成員。

Adobe Developer閘道中會重複使用下列錯誤碼。

| HTTP狀態代碼 | error_code | 訊息 |
| --- | --- | --- |
| 401 | 401013 | Oauth代號無效 |
| 403 | 403010 | 缺少Oauth代號 |
| 404 | 404040 | 找不到資源 |
| 429 | 429001 | 達到服務使用量限制 |

資料擷取API特有的錯誤碼包含三個區段：Adobe Developer閘道傳回的三位數狀態、零「0」和三個額外的位數。

| HTTP狀態代碼 | error_code | 訊息 |
| --- | --- | --- |
| 400 | 4000801 | 錯誤的請求 |
| 400 | 4000802 | 無效的資料 |
| 403 | 4030801 | 未經授權 |
| 429 | 4290801 | 已達到每日配額 |
| 500 | 5000801 | 內部伺服器錯誤 |

## 重試次數

當服務偵測到暫時性錯誤時，會重試操作。 重試主要發生於相依服務逾時或暫時無法使用時。

此服務會使用以下重試間隔：

- 首次重試的初始操作：5分鐘
- 第一次重試到第二次重試： 15分鐘
- 第二次重試至第三次重試：20分鐘
- 第三次重試到第四次重試：20分鐘
- 第四次重試到第五次重試：2小時
- 第五次重試後：3小時

## 端點

擷取端點適用於人員、自訂物件、公司、方案成員和清單。 每個端點區段都會定義要求並提供範例。

### 人員

使用此端點來更新個人記錄。

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
| `persons` | 物件陣列 | 是 | 個人的屬性名稱 — 值組清單 | – |

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

使用此端點來更新插入自訂物件記錄。

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
| `customObjects` | 物件陣列 | 是 | 物件的屬性名稱 — 值組清單。 | – |

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

使用此端點來同步公司記錄。 它支援透過外部公司ID或Marketo內部ID執行重複資料刪除的建立、更新和更新插入操作。

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
| `input` | 物件陣列 | 是 | 公司屬性名稱 — 值配對清單。 接受JSON索引鍵`input`或`companies`。 | – |

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
| 計畫 | 物件陣列 | 是 | 程式作業清單。 每個都會指定程式、目標狀態以及要同步處理的銷售機會。 | – |

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
| 計畫 | 物件陣列 | 是 | 程式刪除作業清單。 每個都指定計畫和要移除的潛在客戶。 | – |

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

### 清單（新增至清單）

用於將潛在客戶新增到靜態清單的端點。 銷售機會由其Marketo銷售機會ID識別。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/lists` |

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
| `listId` | 長整數 | 是 | Marketo靜態清單識別碼。 必須是正整數。 | – |
| `leads` | 物件陣列 | 是 | 要新增至清單的銷售機會參考清單。 接受JSON索引鍵`input`或`leads`。 | – |

輸入陣列中的每個物件包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| `leadId` | 長整數 | 是 | Marketo銷售機會ID。 接受JSON索引鍵`leadId`或`id`。 |

必要的許可權為`Read-Write Lead`。

### 清單新增到清單範例

#### 請求

`POST /subscriptions/{munchkinId}/lists`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "listId": 1001,
   "leads": [
      {
         "leadId": 10001
      },
      {
         "leadId": 10002
      },
      {
         "leadId": 10003
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 清單新增至清單驗證規則

| 規則 | 詳細資料 |
| --- | --- |
| listId | 必填。 必須為正整數(> 0)。 |
| 銷售機會 | 必填。 不得為Null或空白。 |
| leadId | 輸入陣列中的每個潛在客戶都需要。 |
| 每個請求的最大潛在客戶 | 在輸入陣列中總共1,000個銷售機會。 |

### 清單（從清單中移除）

用於從靜態清單中移除潛在客戶的端點。 銷售機會由其Marketo銷售機會ID識別。

>[!NOTE]
>
>此端點使用POST而非DELETE，因為要求需要包含結構化資料的JSON內文。

| 方法 | 路徑 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/lists/remove` |

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
| `listId` | 長整數 | 是 | Marketo靜態清單識別碼。 必須是正整數。 | – |
| `leads` | 物件陣列 | 是 | 要從清單中移除的潛在客戶參照清單。 接受JSON索引鍵`input`或`leads`。 | – |

輸入陣列中的每個物件包含：

| 索引鍵 | 資料類型 | 必要 | 說明 |
| --- | --- | --- | --- |
| `leadId` | 長整數 | 是 | Marketo銷售機會ID。 接受JSON索引鍵`leadId`或`id`。 |

必要的許可權為`Read-Write Lead`。

### 從清單移除的清單範例

#### 請求

`POST /subscriptions/{munchkinId}/lists/remove`

#### 標頭

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 內文

```json
{
   "listId": 1001,
   "leads": [
      {
         "leadId": 10001
      },
      {
         "leadId": 10002
      }
   ]
}
```

#### 回應

`HTTP/1.1 202`
`X-Request-ID: e3d92152-0fb1-444a-8f8f-29d5a2338598`

### 從清單驗證規則中移除的清單

| 規則 | 詳細資料 |
| --- | --- |
| listId | 必填。 必須為正整數(> 0)。 |
| 銷售機會 | 必填。 不得為Null或空白。 |
| leadId | 輸入陣列中的每個潛在客戶都需要。 |
| 每個請求的最大潛在客戶 | 在輸入陣列中總共1,000個銷售機會。 |

## 限制

資料擷取API具備下列護欄：

- 最大請求大小： 1 MB
- 每種物件型別的每個要求最大物件數： 1,000
- 每個使用者端ID的每秒最大要求數：5,000
- 每日最大物件數： 10,000,000

這些限制會統一套用至人員、自訂物件、公司、方案成員和清單。 對於方案成員，「每個請求的物件」是單一請求中所有方案的潛在客戶參考總數。 若為List，「每個請求的物件」為輸入陣列中的潛在客戶參考數。

## 資料擷取API與REST API

資料擷取API與其他Marketo REST API有下列差異：

- 在`X-Mkto-User-Token`標頭中傳遞存取權杖。
- 使用`mkto-ingestion-api.adobe.io`網域。
- URL路徑的開頭為`/subscriptions/MunchkinId`。
- 請勿使用查詢引數。
- 成功的呼叫會傳回狀態202和空白的回應內文。
- 失敗的呼叫傳回非202狀態和包含`{ "error_code" : "Error Code", "message" : "Message" }`的回應內文。
- `X-Request-Id`標頭會傳回要求識別碼。
