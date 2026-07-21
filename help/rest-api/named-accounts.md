---
title: 已命名帳戶
feature: REST API
description: Marketo REST指南，針對ABM的具名帳戶執行CRUD，其中包含說明、查詢、建立更新範例、可搜尋欄位、重複資料刪除規則，以及無銷售機會連結。
exl-id: 2aa1d2a0-9e54-4a9a-abb1-0d0479ed3558
TQID: https://experienceleague.adobe.com/iY3UYVelm3aKuuDBCTxaVCbkXfwnJzDjV3Kvn9rcNbA
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 590
ht-degree: 1%

---

# 已命名帳戶

[具名帳戶端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts)

Marketo提供API，可在具名帳戶上執行CRUD作業，以與Marketo ABM搭配使用。 這些API遵循標準Lead Database介面模式，並提供Describe、Create/Update、Delete和Query選項。

目前，Marketo API僅支援針對具名帳戶的CRUD操作。 您無法透過API將銷售機會連結至具名帳戶。

## 說明

說明具名帳戶會透過Marketo API傳回使用具名帳戶的中繼資料。 回應包含有效的可搜尋欄位和API可用的所有欄位。

具名帳戶的`idField`一律為`marketoGUID`。 物件的`name`欄位是唯一可用的`dedupeField`和建立金鑰。

```http
GET /rest/v1/namedaccounts/describe.json
```

```json
{
   "requestId":"d65e#156c27ac57d",
   "result":[
      {
         "name":"Named Account",
         "description":"Marketo standard account attribute map",
         "createdAt":"2016-08-18T20:16:41Z",
         "updatedAt":"2016-08-18T20:16:41Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "name"
         ],
         "searchableFields":[
            [
               "marketoGUID",
            ],
            [
               "annualRevenue"
            ],
            [
               "city"
            ],
            [
               "country"
            ],
            [
               "domainName"
            ],
            [
               "industry"
            ],
            [
               "logoUrl"
            ],
            [
               "membershipCount"
            ],
            [
               "name"
            ],
            [
               "numberOfEmployees"
            ],
            [
               "opptyAmount"
            ],
            [
               "opptyCount"
            ],
            [
               "score1"
            ],
            [
               "score2"
            ],
            [
               "score3"
            ],
            [
               "score4"
            ],
            [
               "score5"
            ],
            [
               "sicCode"
            ],
            [
               "state"
            ]
         ],
         "fields":[
            {
               "name":"marketoGUID",
               "displayName":"Marketo GUID",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"annualRevenue",
               "displayName":"annualRevenue",
               "dataType":"currency",
               "updateable":true
            },
            {
               "name":"city",
               "displayName":"city",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {
               "name":"country",
               "displayName":"country",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ],
   "success":true
}
```

### 查詢

使用filterType和最多300個逗號分隔的filterValues來查詢具名帳戶。 filterType可以是Describe回應的`searchableFields`成員中傳回的任何單一欄位。 每個filterValues專案都必須是欄位資料型別的有效值。

若要傳回特定欄位，請使用逗號分隔的欄位清單來傳遞欄位引數。 查詢頁面最多包含300筆記錄。 若要擷取其他記錄，請使用呼叫傳回的nextPageToken。

```http
GET /rest/v1/namedaccounts.json?filterType=name&filterValues=Google,Yahoo
```

```json
{
    "requestId": "6dac#157d4ddc9d7",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "16efafdd-0148-4ea7-8782-f451d7c6345d",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Google",
            "updatedAt": "2016-10-17T22:49:04Z"
        },
        {
            "seq": 1,
            "marketoGUID": "44d62353-7f9d-4d43-b9cc-7ef0f7a09137",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Yahoo",
            "updatedAt": "2016-10-17T22:49:04Z"
        }
    ],
    "success": true
}
```

### 建立和更新

使用標準Lead Database模式來建立和更新具名帳戶。 在POST請求的JSON內文的輸入成員中傳遞記錄。 您最多可以包含300筆記錄。

請求成員包括：

- `input`：唯一需要的成員。
- `action`：可接受createOnly、updateOnly或createOrUpdate的選用成員。 預設值為createOrUpdate。
- `dedupeBy`：選擇性成員僅在動作為updateOnly時可用。 它接受dedupeFields或idField，兩者分別對應至name和marketoGUID欄位。

```http
POST /rest/v1/namedaccounts.json
```

```text
Content-Type: application/json
```

```json
{
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "name":"Google",
         "domainName":"www.google.com"
      },
      {
         "name":"Yahoo",
         "domainName":"www.yahoo.com"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "status":"updated",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":1,
         "status":"created",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

### 欄位

具名帳戶物件包含由顯示名稱、API名稱和dataType等屬性定義的欄位。 這些屬性統稱為中繼資料。

公司物件上的下列端點查詢欄位。 API使用者必須具有具有讀寫結構描述標準欄位許可權和/或讀寫結構描述自訂欄位許可權的角色。

### 查詢欄位

依API名稱查詢一個具名帳戶欄位或擷取所有公司欄位。

#### 依名稱

[依名稱取得具名帳戶欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)端點會擷取具名帳戶物件上某個欄位的中繼資料。 必填的fieldApiName路徑引數指定欄位的API名稱。

回應類似於「描述具名帳戶」回應，但包含其他中繼資料。 例如，isCustom屬性會指出欄位是否為自訂欄位。

```http
GET /rest/v1/namedaccounts/schema/fields/annualRevenue.json
```

```json
{
    "requestId": "371c#17e979c5d1f",
    "result": [
        {
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true
}
```

#### 瀏覽

[取得具名帳戶欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)端點會擷取具名帳戶物件上所有欄位的中繼資料。 依預設，它最多會傳回300筆記錄。 使用batchSize查詢引數來減少此數目。

如果moreResult屬性為true，則有更多結果可用。 繼續使用傳回的nextPageToken呼叫端點，直到moreResult為false。

```http
GET /rest/v1/namedaccounts/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "f287#17e995bd0c5",
    "result": [
        {
            "displayName": "Name",
            "name": "name",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Domain Name",
            "name": "domainName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Industry",
            "name": "industry",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "SIC Code",
            "name": "sicCode",
            "description": null,
            "dataType": "string",
            "length": 40,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "City",
            "name": "city",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "N42LHXWEULHZ3N2I77DKOJUVOY======",
    "moreResult": true
}
```

### 刪除

傳送具有JSON內文的POST要求，以刪除具名帳戶。 請求包含必要的輸入成員和選用的deleteBy成員。

deleteBy成員接受「dedupeFields」或「idField」，分別對應至name和marketoGUID。 如果未設定，預設為dedupeFields。 輸入成員最多可接受300筆記錄。 根據deleteBy設定，每個記錄都包含name或marketoGUID。

```http
POST /rest/v1/namedaccounts/delete.json
```

```text
Content-Type: application/json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "name":"Google"
      },
      {
         "name":"Yahoo"
      },
      {
         "name":"Marketo"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status":"deleted"
      },
      {
         "seq":1,
         "id":"dff23271-f996-47d7-984f-f2676861b5fc",
         "status":"deleted"
      },
      {
         "seq":2,
         "status":"skipped",
         "reasons":[
            {
               "code":"1013",
               "message":"Record not found"
            }
         ]
      }
   ]
}
```

## 逾時

- 除非另有註明，具名帳戶端點的逾時值為30秒。
- 同步具名帳戶的逾時時間為120秒。
- 刪除具名帳戶的逾時時間為60秒。
