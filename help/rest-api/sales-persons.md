---
title: 銷售人員
feature: REST API
description: 讀取有關銷售人員的資料。
exl-id: f8ed5aa5-63c1-4c5b-8683-bf47eed1ea18
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '303'
ht-degree: 0%

---

# 銷售人員

[銷售人員端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Sales-Persons)

針對已啟用[SFDC Sync](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync)的訂閱，銷售人員API是唯讀存取。 「銷售人員」是銷售機會記錄的銷售擁有者之人員記錄的型態。 它們與每個Lead記錄上的externalSalesPersonId欄位上的Lead記錄有關。 當銷售機會透過填入的externalSalesPersonId欄位與銷售人員相關時，對應的銷售機會擁有者查閱欄位會針對Marketo中的該銷售機會記錄填入，以允許使用對應的篩選器和代號。

銷售人員使用[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST)端點並傳遞externalSalesPersonId屬性，與銷售機會記錄相關聯。

銷售人員使用[Sync Opportunities](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/syncOpportunitiesUsingPOST)端點並傳遞externalSalesPersonId屬性，與Opportunity記錄相關聯。

使用[Sync Companies](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST)端點並傳遞externalSalesPersonId屬性，銷售人員與公司記錄相關。

銷售人員記錄只能透過API編輯。

## 說明

描述銷售人員記錄會遵循潛在客戶資料庫物件的標準模式。

```
GET /rest/v1/salespersons/describe.json
```

```json
{  
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[  
      {  
         "name":"SalesPerson",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"id",
         "dedupeFields":[  
            "externalSalesPersonId"
         ],
         "searchableFields":[  
            [  
               "email"
            ],
            [  
               "id"
            ],
            [
               "externalSalesPersonId"
            ]
         ],
         "fields":[  
            {  
               "name":"id",
               "displayName":"Marketo Id",
               "dataType":"integer",
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
               "name":"email",
               "displayName":"Email",
               "dataType":"string",
               "length":255,
               "updateable":false
            },
            {  
               "name":"externalSalesPersonId",
               "displayName":"External Sales Person Id",
               "dataType":"string",
               "length":255,
               "updateable":false
            }
         ]
      }
   ]
}
```

依預設，銷售人員的`idField`是&quot;id&quot;，`dedupeFields`只是&quot;externalSalesPersonId&quot;。

## 查詢

銷售人員使用簡易鍵的標準查詢模式。 此範例顯示使用者電子郵件當作externalSalesPersonId使用。 依預設，查詢會傳回針對傳回記錄填入的所有欄位。

```
GET /rest/v1/salespersons.json?filterType=dedupeFields&filterValues=david@test.com,sam@test.com
```

```json
 {  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "id":53453,
         "externalSalesPersonId":"sam@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      },
      {  
         "seq":1,
         "id":53454,
         "externalSalesPersonId":"david@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      }
   ]
}
```

## 建立和更新

更新模式為標準模式。

```
POST /rest/v1/salespersons.json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalSalesPersonId":"sam@test.com",
         "email":"sam@test.com",
         "firstName":"Sam",
         "lastName":"Sanosin"
      },
      {
         "externalSalesPersonId":"david@test.com",
         "email":"david@test.com",
         "firstName":"David",
         "lastName":"Aulassak"
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
         "status": "updated",
         "id":45232
      },
      {
         "seq":1,
         "status": "created",
         "id":45236
      }
   ]
}
```

## 刪除

刪除的模式是標準模式。

「使用中」時不允許刪除銷售人員。 在此情況下，會略過「銷售人員」。 範例：

- 當銷售人員與有效潛在客戶相關聯時
- 當銷售人員與已刪除的公司相關聯時

```
POST /rest/v1/salespersons/delete.json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "externalSalesPersonId":"sam@test.com"
      },
      {  
         "externalSalesPersonId":"david@test.com"
      },
      {  
         "externalSalesPersonId":"raj@test.com"
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
         "id":56343,
         "status": "deleted"
      },
      {
         "seq":1,
         "id":53453,
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
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

- 銷售人員端點的逾時為30秒，除非以下說明
   - 同步銷售人員：60多歲
   - 刪除銷售人員：60歲
