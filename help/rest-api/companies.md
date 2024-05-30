---
title: 「公司」
feature: REST API
description: 「使用Marketo API設定公司資料。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '564'
ht-degree: 0%

---


# 公司

[公司端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

公司代表潛在客戶記錄所屬的組織。 潛在客戶可透過填入其對應的 `externalCompanyId` 欄位使用 [同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST) 或 [大量潛在客戶匯入](bulk-lead-import.md) 端點。 將潛在客戶新增至公司後，您即無法從該公司中刪除該潛在客戶（除非您將潛在客戶新增至其他公司）。 連結到公司記錄的銷售機會將直接繼承來自公司記錄的值，就像值存在於銷售機會自己的記錄上一樣。

公司API是訂閱的唯讀存取權，訂閱具有 [SFDC同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en) 或 [Microsoft Dynamics同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en) 已啟用。

## 說明

描述公司物件可為您提供與其互動所需的所有資訊。

```
GET /rest/v1/companies/describe.json
```

```json
{  
   "success":true,
   "requestId":"5847#14d44113ad7",
   "result":[  
      {  
         "name":"Company",
         "description":"Company object",
         "createdAt":"2015-05-11T17:11:32Z",
         "updatedAt":"2015-05-11T17:11:32Z",
         "idField":"id",
         "dedupeFields":[  
            "externalCompanyId"
         ],
         "searchableFields":[  
            [  
               "externalCompanyId"
            ],
            [  
               "id"
            ],
            [  
               "company"
            ]
         ],
         "fields":[  
            {  
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"externalCompanyId",
               "displayName":"External Company Id",
               "dataType":"string",
               "length":100,
               "updateable":false
            },
            {  
               "name":"id",
               "displayName":"Id",
               "dataType":"integer",
               "updateable":false
            },
            {  
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"annualRevenue",
               "displayName":"Annual Revenue",
               "dataType":"currency",
               "updateable":true
            }
            {  
               "name":"company",
               "displayName":"Company Name",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查詢

的模式 [查詢公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompaniesUsingGET) 密切遵循銷售機會API的規則，新增限制： `filterType` 引數接受Describe Companies呼叫或dedupeFields之searchableFields陣列中列出的欄位。

`filterType` 和 `filterValues` 是必要的查詢引數。  `fields`， `nextPageToken`、和 `batchSize` 是選用引數。  這些引數的功能就像潛在客戶與商機API中的對應引數一樣。 請求清單時 `fields`，如果要求特定欄位但未傳回，則值會隱含為空值。

如果省略fields引數，則傳回的預設欄位集為：

- ID
- dedupeField
- 更新時間
- createdAt

```
GET /rest/v1/companies.json?filterType=id&filterValues=3433,5345
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "id":3433,
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {  
         "seq":1,
         "id":5345,
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
      }
   ]
}
```

## 建立和更新

此 [同步公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST) 端點接受必要的 `input` 包含公司物件陣列的引數。 就像商機一樣，建立和更新公司有三種模式：createOnly、updateOnly和createOrUpdate。  模式指定於 `action` 請求的引數。 兩者 `dedupeBy` 和 `action` 引數是選用的，並分別預設為dedupeFields和createOrUpdate模式。

```
POST /rest/v1/companies.json
```

```
Content-Type: application/json
```

```json
{  
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[  
      {  
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {  
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
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
         "id":1232
      },
      {  
         "seq":1,
         "status":"created",
         "id":1323
      }
   ]
}
```

### 欄位

公司物件包含一組欄位。 每個欄位定義都由一組描述該欄位的屬性組成。 屬性的範例為顯示名稱、API名稱和dataType。 這些屬性統稱為中繼資料。

下列端點可讓您查詢公司物件上的欄位。 這些API要求擁有的API使用者必須具備具備以下一項或兩項的角色： `Read-Write Schema Standard Field` 或 `Read-Write Schema Custom Field` 許可權。

### 查詢欄位

查詢公司欄位簡單明瞭。 您可以依API名稱查詢單一公司欄位，或查詢所有公司欄位集。

#### 依名稱

此 [依名稱取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldByNameUsingGET) 端點會擷取公司物件上單一欄位的中繼資料。 必要的 `fieldApiName` path引數指定欄位的API名稱。 回應類似於Describe Company端點，但包含其他中繼資料，例如 `isCustom` 表示欄位是否為自訂欄位的屬性。

```
GET /rest/v1/companies/schema/fields/industry.json
```

```json
{
    "requestId": "88f6#17e976d6ab4",
    "result": [
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
        }
    ],
    "success": true
}
```

#### 瀏覽

此 [取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldsUsingGET) 端點會擷取公司物件上所有欄位的中繼資料。 依預設，最多會傳回300筆記錄。 您可以使用 `batchSize` 查詢引數以縮小此數目。 如果 `moreResult` attribute為true，表示有更多結果可用。 繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。 此 `nextPageToken` 從此API傳回的應一律重複用於此呼叫的下一個反複專案。

```
GET /rest/v1/companies/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b50e#17e995c2d35",
    "result": [
        {
            "displayName": "Company Name",
            "name": "company",
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
            "displayName": "Site",
            "name": "site",
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
            "displayName": "Website",
            "name": "website",
            "description": null,
            "dataType": "url",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Main Phone",
            "name": "mainPhone",
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
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "L7XD3EFJ3OLFZKXKJBWYULOTRA======",
    "moreResult": true
}
```

### 刪除

刪除准則指定於 `input` 陣列，其中包含搜尋值清單。  刪除方法指定於 `deleteBy` 引數。  允許值為：dedupeFields、idField。  預設為dedupeFields。

```
Content-Type: application/json
```

```
POST /rest/v1/companies/delete.json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "externalCompanyId":"19UYA31581L000000"
      },
      {  
         "externalCompanyId":"29UYA31581L000000"
      },
      {  
         "externalCompanyId":"39UYA31581L000000"
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
         "id":1234,
         "status":"deleted"
      },
      {  
         "seq":1,
         "id":56456,
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

- 公司端點的逾時為30秒，除非以下說明
   - 同步處理公司：60秒 
   - 刪除公司：60秒
