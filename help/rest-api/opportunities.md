---
title: 機會
feature: REST API
description: Marketo REST API可說明、查詢、建立及更新商機、重複資料刪除及可搜尋的欄位、限制，以及使用SFDC或Dynamics同步的唯讀行為。
exl-id: 46451285-4125-4857-890a-575069a68288
TQID: https://experienceleague.adobe.com/rBDJcXWQrN5qyKRWHyzVC-sc9BH2mQFLm7fKUk-NUn8
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 708
ht-degree: 0%

---

# 機會

[機會端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities)

Marketo提供用於讀取、寫入、建立和更新商機記錄的API。 在Marketo中，中繼機會角色物件會將機會記錄連結至銷售線索和聯絡人記錄。 因此，機會可以連結到許多個別銷售機會。

API會公開兩種物件型別。 和大多數Lead Database物件型別一樣，每個物件型別都有對應的Describe呼叫，可傳回物件中繼資料。

商機API為已啟用[SFDC Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en)的訂閱提供唯讀存取權。

## 說明

使用Lead Database物件的標準模式描述Opportunity記錄。

```http
GET /rest/v1/opportunities/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"opportunity",
         "displayName":"Opportunity",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "externalOpportunityId"
         ],
         "searchableFields":[
            [
               "externalOpportunityId"
            ],
            [
               "marketoGUID"
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
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            }
         ]
      }
   ]
}
```

主要回應欄位包括：

- `idField`：識別機會主索引鍵marketoGUID。 這個系統產生的金鑰支援讀取和更新操作，但不支援插入。
- `dedupeFields`：識別插入作業的有效金鑰。 對於機會，唯一的索引鍵是externalOpportunityId。
- `searchableFields`：識別對查詢有效的欄位。 這些欄位是externalOpportunityId和marketoGUID。

## 查詢

[查詢商機](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunitiesUsingGET)的模式非常遵循Leads API。 但是，`filterType`引數只接受在對應的Describe回應或dedupeFields的`searchableFields`陣列中列出的欄位。

對於自訂機會欄位，只有String或Integer型別的欄位會出現在可搜尋的fields陣列中。

```http
GET /rest/v1/opportunities.json?filterType=marketoGUID&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa ",
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc ",
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

您可以包含下列選用查詢引數：

- `fields`：傳回其他機會欄位。
- `nextPageToken`：結果集的頁數大於批次大小。
- `batchSize`：指定批次大小。 預設值和最大值為300。

當您請求`fields`的清單時，未傳回的請求欄位具有隱含值null。

## 建立和更新

機會會遵循銷售機會API模式，但有一些限制。 `action`值為createOnly、createOrUpdate和updateOnly。

- 對於createOnly或createOrUpdate模式，請在每個記錄中包含externalOpportunityId欄位。
- 對於updateOnly模式，請使用marketoGUID或externalOpportunityId。
- 如果未指定，則模式預設為createOrUpdate。

Leads API中的`lookupField`引數無法使用。 dedupeBy引數會取代該引數，而且只有在action為updateOnly時才有效。

dedupeBy值為「dedupeFields」和「idField」，Description回應會分別將其識別為externalOpportunityId和marketoGUID。 如果未指定dedupeBy，預設為dedupeFields模式。 &#39;name&#39;欄位不可為Null。

您一次最多可以提交300筆記錄。

```http
POST /rest/v1/opportunities.json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
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
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

回應包括每個記錄的下列值：

- `marketoGUID`：記錄識別碼。
- `status`：個別記錄的成功或失敗。
- `seq`：提交記錄的索引，此索引會將要求記錄與回應順序建立關聯。

### 欄位

公司物件包含由顯示名稱、API名稱和dataType等屬性定義的欄位。 這些屬性統稱為中繼資料。

公司物件上的下列端點查詢欄位。 API使用者必須具有具有`Read-Write Schema Standard Field`許可權、`Read-Write Schema Custom Field`許可權或兩者的角色。

### 查詢欄位

依API名稱查詢一個公司欄位或擷取所有公司欄位。

#### 依名稱

[依名稱取得機會欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunityFieldByNameUsingGET)端點會擷取公司物件上某個欄位的中繼資料。 必要的`fieldApiName`路徑引數指定欄位的API名稱。

回應類似於Describe Opportunity回應，但包含其他中繼資料。 例如，`isCustom`屬性指出欄位是否為自訂欄位。

```http
GET /rest/v1/opportunities/schema/fields/externalOpportunityId.json
```

```json
{
    "requestId": "12331#17e9779cb4b",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
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

[取得機會欄位](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunityFieldsUsingGET)端點會擷取公司物件上所有欄位的中繼資料。 依預設，它最多會傳回300筆記錄。 使用`batchSize`查詢引數來減少此數目。

如果`moreResult`屬性為true，則有更多結果可用。 繼續使用傳回的`nextPageToken`呼叫端點，直到moreResult為false。

```http
GET /rest/v1/opportunities/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b4a#17e995b31da",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
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
            "displayName": "Description",
            "name": "description",
            "description": null,
            "dataType": "string",
            "length": 2000,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Type",
            "name": "type",
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
            "displayName": "Stage",
            "name": "stage",
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
    "nextPageToken": "E5ZONGE4SAHALYYW6FS25KB5BM======",
    "moreResult": true
}
```

#### 刪除

依重複資料刪除欄位或ID欄位刪除商機。 將`deleteBy`引數設定為dedupeFields或idField。 預設值為dedupeFields。

要求內文包含要刪除的`input`機會陣列。 每個通話最多允許300個機會。

```http
POST /rest/v1/opportunities/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "externalOpportunityId":"19UYA31581L000000"
      },
      {
         "externalOpportunityId":"29UYA31581L000000"
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
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb",
         "status":"deleted"
      }
   ]
}
```

## 逾時

- 除非另有說明，否則機會端點的逾時值為30秒。
- 同步處理機會的逾時時間為60秒。
- 刪除商機的逾時時間為60秒。
