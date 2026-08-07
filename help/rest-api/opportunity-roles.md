---
title: 機會角色
feature: REST API
description: 透過REST API管理Marketo商機角色，包括說明、使用複合重複資料刪除欄位進行查詢、建立更新刪除、逾時和無CRM同步。
exl-id: 2ba84f4d-82d0-4368-94e8-1fc6d17b69ed
TQID: https://experienceleague.adobe.com/aE27mBhsrn-0SO41M-pV5NFjoMq--1Lp-L2TQGL7-8Y
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 252
ht-degree: 0%

---

# 機會角色

[機會角色端點參考](https://developer.adobe.com/marketo-apis/api/mapi#operation/getOpportunityRolesUsingGET)

中繼`opportunityRole`物件連結指向機會。

機會角色API僅適用於未啟用原生CRM同步處理的訂閱。

## 說明

和機會一樣，API也提供機會角色的Describe呼叫和CRUD作業。

```http
GET /rest/v1/opportunities/roles/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"opportunityRole",
         "displayName":"Opportunity Role",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "externalOpportunityId",
            "leadId",
            "role"
         ],
         "searchableFields":[
            [
               "externalOpportunityId",
               "leadId",
               "role"
            ],
            [
               "marketoGUID"
            ],
            [
               "leadId"
            ],
            [
               "externalOpportunityId"
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
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"leadId",
               "displayName":"Lead Id",
               "dataType":"integer",
               "updateable":false
            },
            {
               "name":"role",
               "displayName":"Role",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"isPrimary",
               "displayName":"Is Primary",
               "dataType":"boolean",
               "updateable":true
            },
            {
               "name":"externalCreatedDate",
               "displayName":"External Created Date",
               "dataType":"datetime",
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查詢

`dedupeFields`和`searchableFields`值與機會不同。 `dedupeFields`提供需要`externalOpportunityId`、`leadId`和`role`的複合金鑰。 若要成功建立記錄，目的地執行個體中必須存在識別碼欄位參考的機會和銷售機會。

`searchableFields`值`marketoGUID`、`leadId`和`externalOpportunityId`對使用與機會相同模式的個別查詢有效。 您也可以使用複合鍵進行查詢。 此查詢需要透過POST以`_method=GET`查詢引數提交的JSON物件。

```http
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{
   "filterType": "dedupeFields",
   "fields": [
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input": [
      {
        "externalOpportunityId": "Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {
        "externalOpportunityId": "Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {
        "externalOpportunityId": "Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

此請求會產生與標準GET查詢相同的回應型別，但使用不同的請求介面。

## 建立和更新

使用與商機相同的介面，建立並更新商機角色。

```http
POST /rest/v1/opportunities/roles.json
```

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456783,
         "role": "Technical Buyer",
         "isPrimary": false
      },
      {
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456784,
         "role": "Technical Buyer",
         "isPrimary": false
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result":[
      {
         "seq": 0,
         "status": "updated",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

## 刪除

依重複資料刪除欄位或ID欄位刪除商機角色。 將deleteBy引數設定為dedupeFields或idField。 預設值為dedupeFields。

請求內文包含要刪除的機會角色的輸入陣列。 每個呼叫最多允許300個機會角色。

```http
POST /rest/v1/opportunities/roles/delete.json
```

```json
{
   "deleteBy": "dedupeFields",
   "input": [
      {
        "externalOpportunityId": "19UYA31581L000000",
        "leadId": 456783,
        "role": "Technical Buyer"
      }
   ]
}
```

```json
{
    "requestId": "10f7c#173264db42d",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
            "status": "deleted"
        }
    ]
    "success": true
}
```

## 逾時

- 除非另有說明，否則機會角色端點的逾時值為30秒。
- 同步機會角色的逾時時間為60秒。
- 刪除機會角色的逾時時間為60秒。
