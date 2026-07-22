---
title: 公司
feature: REST API
description: 使用Marketo Companies REST API來說明、查詢及同步公司記錄、管理欄位並依externalCompanyId重複資料刪除，並注意CRM同步為唯讀。
exl-id: 80e514a2-1c86-46a7-82bc-e4db702189b0
TQID: https://experienceleague.adobe.com/LdJYN4lx9JfcE-02zTz8ktfYXm4EdPtxMYOx9gGR0sg
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
source-wordcount: 582
ht-degree: 1%

---

# 公司

[公司端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies)

公司代表潛在客戶記錄所屬的組織。 若要新增銷售機會至公司，請使用[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/syncLeadUsingPOST)或[大量銷售機會匯入](bulk-lead-import.md)端點填入其`externalCompanyId`欄位。

除非您將銷售機會新增至其他公司，否則無法從公司中移除銷售機會。 連結至公司記錄的潛在客戶會繼承該記錄的值，就好像這些值存在於潛在客戶記錄上一樣。

公司API為已啟用[SFDC Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=zh-Hant)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=zh-Hant)的訂閱提供唯讀存取權。

## 說明

描述公司物件，以擷取與公司記錄互動所需的資訊。

```http
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

[查詢公司](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies/operation/getCompaniesUsingGET)的模式非常遵循Leads API。 不過，`filterType`引數只接受Describe Companies回應或dedupeFields之searchableFields陣列中所列的欄位。

查詢引數包括：

- `filterType`和`filterValues`：必要的引數。
- `fields`、`nextPageToken`和`batchSize`：選擇性引數，其作用與Leads和Opportunities API中的對應引數類似。

當您請求`fields`的清單時，未傳回的請求欄位具有隱含值null。

如果您省略fields引數，回應預設會傳回這些欄位：

- ID
- dedupeField
- 更新時間
- createdAt

```http
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

[同步公司](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies/operation/syncCompaniesUsingPOST)端點接受包含公司物件陣列的必要`input`引數。

與商機一樣，端點支援三種建立和更新模式：createOnly、updateOnly和createOrUpdate。 在請求的`action`引數中指定模式。

`dedupeBy`和`action`引數是選用的。 它們分別預設為dedupeFields和createOrUpdate。

```http
POST /rest/v1/companies.json
```

```text
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

公司物件包含由顯示名稱、API名稱和dataType等屬性定義的欄位。 這些屬性統稱為中繼資料。

公司物件上的下列端點查詢欄位。 API使用者必須具有具有`Read-Write Schema Standard Field`許可權、`Read-Write Schema Custom Field`許可權或兩者的角色。

### 查詢欄位

依API名稱查詢一個公司欄位或擷取所有公司欄位。

#### 依名稱

[依名稱取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies/operation/getCompanyFieldByNameUsingGET)端點會擷取公司物件上某個欄位的中繼資料。 必要的`fieldApiName`路徑引數指定欄位的API名稱。

回應類似於「描述公司」回應，但包含其他中繼資料。 例如，`isCustom`屬性指出欄位是否為自訂欄位。

```http
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

[取得公司欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies/operation/getCompanyFieldsUsingGET)端點會擷取公司物件上所有欄位的中繼資料。 依預設，它最多會傳回300筆記錄。 使用`batchSize`查詢引數來減少此數目。

如果`moreResult`屬性為true，則有更多結果可用。 繼續使用傳回的`nextPageToken`呼叫端點，直到`moreResult`為false。

```http
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

指定刪除准則作為`input`陣列中的搜尋值清單。 在`deleteBy`引數中指定刪除方法。

允許的值為dedupeFields和idField。 預設值為dedupeFields。

```text
Content-Type: application/json
```

```http
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

- 除非另有說明，否則公司端點的逾時值為30秒。
- 同步處理公司的逾時時間為60秒。
- 刪除公司的逾時時間為60秒。
