---
title: 已命名帳戶
feature: REST API
description: Marketo REST指南，針對ABM的具名帳戶執行CRUD，其中包含說明、查詢、建立更新範例、可搜尋欄位、重複資料刪除規則，以及無銷售機會連結。
exl-id: 2aa1d2a0-9e54-4a9a-abb1-0d0479ed3558
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '697'
ht-degree: 1%

---

# 已命名帳戶

[具名帳戶端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts)

Marketo提供了一組API，可在具名帳戶上執行CRUD作業，以與Marketo ABM搭配使用。 這些API遵循潛在客戶資料庫API的標準介面模式，提供「說明」、「建立/更新」、「刪除」和「查詢」選項。

目前，透過Marketo的API提供的唯一與ABM相關的功能是針對具名帳戶的CRUD操作；銷售機會無法透過任何API連結至具名帳戶。

## 說明

說明具名帳戶會透過Marketo的API傳回與具名帳戶的使用相關的中繼資料，包括在查詢時可搜尋的有效欄位清單，以及可供API使用的所有欄位清單。 具名帳戶的`idField`一律為`marketoGUID`，唯一可用的`dedupeField`且建立金鑰為物件的`name`欄位。

```
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

查詢具名帳戶是根據filterType和一組最多300個逗號分隔的filterValues的使用情況。 `filterType`可能是任何單一欄位，在具名帳戶的describe結果的`searchableFields`成員中傳回，而filterValues可能是該欄位資料型別的任何有效輸入。 若要從傳回特定欄位集，必須傳遞欄位引數，其中的值是要於回應中傳回的欄位清單（以逗號分隔）。 如同其他查詢選項，單一查詢頁面的記錄數上限為300，而且必須使用呼叫傳回的nextPageToken來請求集合中的其他記錄。

```
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

建立和更新具名帳戶會遵循標準潛在客戶資料庫模式。 必須在POST請求的JSON內文輸入成員中傳遞記錄。 `input`是唯一必要的成員，`action`與`dedupeBy`為選用成員。 輸入中最多可包含300筆記錄。 動作可以是createOnly、updateOnly或createOrUpdate其中之一。 如果未指定，動作會預設為createOrUpdate。 dedupeBy只能在action為updateOnly時指定，且僅接受其中一個dedupeFields或idField （分別對應至name和marketoGUID欄位）。

```
POST /rest/v1/namedaccounts.json
```

```
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

具名帳戶物件包含一組欄位。 每個欄位定義都由一組描述該欄位的屬性組成。 屬性的範例為顯示名稱、API名稱和dataType。 這些屬性統稱為中繼資料。

下列端點可讓您查詢公司物件上的欄位。 這些API要求擁有的API使用者必須具備讀寫結構描述標準欄位或讀寫結構描述自訂欄位許可權之一或兩者的角色。

### 查詢欄位

查詢具名帳戶欄位簡單明瞭。 您可以依API名稱查詢單一具名帳戶欄位，或查詢所有公司欄位集。

#### 依名稱

[依名稱取得具名帳戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)端點會擷取具名帳戶物件上單一欄位的中繼資料。 必要的fieldApiName路徑引數會指定欄位的API名稱。 回應類似於「描述具名帳戶」端點，但包含其他中繼資料，例如isCustom屬性，其代表欄位是否為自訂欄位。

```
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

[取得具名帳戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)端點會擷取具名帳戶物件上所有欄位的中繼資料。 依預設，最多會傳回300筆記錄。 您可以使用batchSize查詢引數來減少此數目。 如果moreResult屬性為true，則表示有更多結果可用。 繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。 從此API傳回的nextPageToken一律應用於此呼叫的下一個反複專案。

```
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

刪除是透過JSON POST要求完成，並擁有必要的輸入成員以及選用的deleteBy成員。 deleteBy可能是「dedupeFields」或「idField」之一，分別對應至name或marketoGUID，如果未設定，將預設為dedupeFields。 輸入成員接受最多300筆記錄的陣列，每個包含一名成員，根據deleteBy的設定，可以是name或marketoGUID。

```
POST /rest/v1/namedaccounts/delete.json
```

```
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

- 已命名的帳戶端點的逾時為30秒，除非以下說明
   - 同步已命名帳戶： 120秒
   - 刪除具名帳戶：60秒
