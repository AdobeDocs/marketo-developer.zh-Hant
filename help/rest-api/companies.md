---
title: 公司
feature: REST API
description: 使用Marketo API設定公司資料。
exl-id: 80e514a2-1c86-46a7-82bc-e4db702189b0
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '564'
ht-degree: 1%

---

# 公司

[公司端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

公司代表潛在客戶記錄所屬的組織。 使用`externalCompanyId`同步銷售機會[或](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST)大量銷售機會匯入[端點填入其對應的](bulk-lead-import.md)欄位，以將銷售機會新增到公司。 將潛在客戶新增至公司後，您即無法從該公司中刪除該潛在客戶（除非您將潛在客戶新增至其他公司）。 連結到公司記錄的銷售機會將直接繼承來自公司記錄的值，就像值存在於銷售機會自己的記錄上一樣。

對於已啟用[SFDC Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en)的訂閱，公司API是唯讀存取權。

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

[查詢公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompaniesUsingGET)的模式非常遵循潛在客戶API的模式，新增限製為`filterType`引數接受Describe Companies呼叫或dedupeFields的searchableFields陣列中所列的欄位。

`filterType`和`filterValues`是必要的查詢引數。  `fields`、`nextPageToken`和`batchSize`是選用引數。  這些引數的功能就像潛在客戶與商機API中的對應引數一樣。 請求`fields`的清單時，如果請求特定欄位但未傳回，則值會隱含為空值。

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

[同步公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST)端點接受包含公司物件陣列的必要`input`引數。 就像商機一樣，建立和更新公司有三種模式：createOnly、updateOnly和createOrUpdate。  在要求的`action`引數中指定模式。 `dedupeBy`和`action`引數都是選用引數，分別預設為dedupeFields和createOrUpdate模式。

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

下列端點可讓您查詢公司物件上的欄位。 這些API要求擁有的API使用者必須擁有具有`Read-Write Schema Standard Field`或`Read-Write Schema Custom Field`許可權之一或兩者的角色。

### 查詢欄位

查詢公司欄位簡單明瞭。 您可以依API名稱查詢單一公司欄位，或查詢所有公司欄位集。

#### 依名稱

[依名稱取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldByNameUsingGET)端點會擷取公司物件上單一欄位的中繼資料。 必要的`fieldApiName`路徑引數指定欄位的API名稱。 回應類似於Describe Company端點，但包含其他中繼資料，例如`isCustom`屬性，其代表欄位是否為自訂欄位。

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

[取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldsUsingGET)端點會擷取公司物件上所有欄位的中繼資料。 依預設，最多會傳回300筆記錄。 您可以使用`batchSize`查詢引數來減少此數目。 如果`moreResult`屬性為true，則表示有更多結果可用。 繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。 從此API傳回的`nextPageToken`應一律重複用於此呼叫的下一個反複專案。

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

刪除條件是在`input`陣列中指定，該陣列包含搜尋值清單。  刪除方法已在`deleteBy`引數中指定。  允許值為：dedupeFields、idField。  預設為dedupeFields。

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
