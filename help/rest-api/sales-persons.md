---
title: 銷售人員
feature: REST API
description: 使用SFDC或Dynamics sync使用externalSalesPersonId來與銷售機會建立關聯及執行查詢、更新插入、刪除的銷售人員記錄Marketo REST API指南。
exl-id: f8ed5aa5-63c1-4c5b-8683-bf47eed1ea18
TQID: https://experienceleague.adobe.com/JwLNgM0zgztyoYJotCiSdGxMixnzA0kvkFbvq8kEkzE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 363
ht-degree: 0%

---

# 銷售人員

[銷售人員端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Sales-Persons)

銷售人員API為已啟用[SFDC Sync](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync)的訂閱提供唯讀存取權。

「銷售人員」是代表潛在客戶記錄之銷售擁有者的個人記錄。 每個Lead記錄上的externalSalesPersonId欄位會將Lead與Sales Person相關聯。 填入此欄位時，Marketo會填入潛在客戶記錄上對應的潛在客戶擁有者查詢欄位。 接著，您可以使用相關聯的篩選器和權杖。

將externalSalesPersonId屬性傳遞至對應的端點，使銷售人員與其他記錄產生關聯：

- 潛在客戶記錄：[同步處理潛在客戶](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncLeadUsingPOST)。
- 機會記錄： [同步處理機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncOpportunitiesUsingPOST)。
- 公司記錄： [同步公司](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncCompaniesUsingPOST)。

銷售人員記錄只能透過API編輯。

## 說明

使用Lead Database物件的標準模式來描述「銷售人員」記錄。

```http
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

根據預設，銷售人員`idField`為&quot;id&quot;，`dedupeFields`為&quot;externalSalesPersonId&quot;。

## 查詢

使用簡易鍵的標準查詢模式來查詢「銷售人員」。 下列範例使用使用者的電子郵件做為externalSalesPersonId。

依預設，查詢會傳回符合記錄的所有填入欄位。

```http
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

使用標準更新模式來建立或更新「業務代表」。

```http
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

使用標準刪除模式刪除銷售人員。

您無法刪除使用中的銷售人員。 在以下情況下，請求會略過「銷售人員」：

- 銷售人員與有效銷售機會相關聯。
- 此銷售人員與已刪除的公司相關聯。

```http
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

- 除非另有說明，否則銷售人員端點的逾時值為30秒。
- 同步處理銷售人員的逾時時間為60秒。
- 刪除銷售人員的逾時時間為60秒。
