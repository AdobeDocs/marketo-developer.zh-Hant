---
title: 自訂物件
feature: REST API, Custom Objects
description: 瞭解如何透過REST API建立和管理Marketo自訂物件，包括清單和說明端點、中繼資料、關係、欄位和查詢。
exl-id: 88e8829b-f8f1-46d7-a753-5aa6e20e2c40
TQID: https://experienceleague.adobe.com/NWm9CjFVqQdVDJRrnE4nA299-Lg53-JR7xvY-82dUqY
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: c5f60233-d5ea-4453-a799-0ad258b4d399id: d1d0a9cd-295d-4976-8c39-ddae266f240e
subfeature_v2: id: ea4e3ff5-e7b9-4b4c-a5a0-dc27cc3f4275
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dcid: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 2844
ht-degree: 0%

---

# 自訂物件

[**自訂物件端點參考**](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects)

Marketo自訂物件可與Marketo標準物件（例如銷售機會和公司）相關，或是與其他Marketo自訂物件相關。 在[Marketo UI](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)中或使用此檔案中說明的自訂物件中繼資料API，建立Marketo自訂物件。

存取自訂物件中繼資料API需要適當的Marketo訂閱型別。 請聯絡您的CSM以取得詳細資料。

## 清單

除了潛在客戶資料庫物件的標準Describe、Query、Update和Delete呼叫之外，自訂物件還提供[清單呼叫](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectsUsingGET)。 端點會傳回目的地例項中可用的自訂物件，以及有關每個物件的中繼資料。

```http
GET /rest/v1/customobjects.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "relatedTo":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
         ]
      }
   ]
}
```

回應會列出每個物件的關係。 每個關係都包含：

- `field`：物件上儲存連結值的欄位。
- `type`：相關物件是父物件還是子物件。
- `relatedTo`：相關物件的名稱及其連結欄位。

## 說明

自訂物件的[Describe呼叫](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_1)遵循與「商機」和「公司」相同的模式，包含兩個新增專案：

- `apiName`路徑引數指定要描述之自訂物件型別的API名稱。
- 回應包含`relationships`陣列，列出自訂物件型別可用的關聯。

```http
GET /rest/v1/customobjects/{apiName}/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "object":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
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
               "name":"vin",
               "displayName":"VIN",
               "description":"Vehicle Identification Number",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"siebelId",
               "displayName":"External Id",
               "description":"External Id",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"make",
               "displayName":"Make",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"model",
               "displayName":"Model",
               "description":"Vehicle Model",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {
               "name":"year",
               "displayName":"Year",
               "dataType":"integer",
               "updateable":true
            },
            {
               "name":"color",
               "displayName":"Color",
               "description":"Vehicle color",
               "dataType":"String",
               "length": 255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查詢

[查詢自訂物件](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectsUsingGET)與查詢其他Lead資料庫物件稍有不同。 和Describe一樣，要求採用`apiName`路徑引數。

針對一般filterType，傳送包含必要`filterType`和`filterValues`引數的GET要求。 您也可以包含選用的`**fields**`、`batchSize`和`nextPageToken`引數。

當您請求欄位清單時，未傳回的請求欄位具有隱含值null。

```http
GET /rest/v1/customobjects/{apiName}.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "vin":"19UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "vin":"29UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
   ]
}
```

使用複合金鑰進行查詢時，API的行為類似機會角色API，並接受具有JSON內文的POST請求。 主體可以包含與GET查詢相同的成員，但`filterValues`除外。

請提供`input`物件陣列，而非篩選值。 每個物件都包含物件型別`dedupeFields`中每個欄位的成員。

```http
POST /rest/v1/customobjects/{apiName}.json?_method=GET
```

```json
{
   "filterType":"dedupeFields",
   "fields":[
      "marketoGuid",
      "Bedrooms",
      "yearBuilt"
   ],
   "input":[
      {
         "mlsNum":"1962352",
         "houseOwnerId":"42645756"
      },
      {
         "mlsNum":"2962352",
         "houseOwnerId":"52645756"
      },
      {
         "mlsNum":"3962352",
         "houseOwnerId":"62645756"
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "Bedrooms":3,
         "yearBuilt":1948,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "Bedrooms":4,
         "yearBuilt":1956,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":2,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc",
         "Bedrooms":3,
         "yearBuilt":2001,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      }
   ]
}
```

## 建立和更新

使用[同步自訂物件](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncCustomObjectsUsingPOST)端點來建立或更新自訂物件。 使用`action`引數指定作業。 每個呼叫最多可建立或更新300筆記錄。

根據[Describe Custom Objects](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_1)端點傳回的資訊，`input`陣列中的值。 在範例car物件中，唯一的重複資料刪除欄位是`vin`。 當您使用dedupeFields模式來建立或更新記錄時，請在輸入陣列中的每個物件中至少加入`vin`欄位。

```http
POST /rest/v1/customobjects/{apiName}.json
```

```json
{
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000",
         "siebelId":"f2676861b5fb",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"29UYA31581L000000",
         "siebelId":"f2676861b5fc",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"39UYA31581L000000",
         "siebelId":"f2676861b5fd",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":1,
         "status": "created",
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1004",
               "message":"Lead not found"
            }
         ]
      }
   ]
}
```

當您以`idField`模式更新記錄時，`idField`一律為`marketoGUID`。 在每個記錄中包含`marketoGUID`欄位。

因為此欄位是系統管理的，`idField`僅對updateOnly動作型別有效。 結果陣列包含每個記錄的&#x200B;**狀態**。 它也包含成功作業的`marketoGUID`或失敗作業的`reasons`陣列。

## 刪除

若要[刪除記錄](https://developer.adobe.com/marketo-apis/api/mapi#operation/deleteCustomObjectsUsingPOST)，請選取`idField`或`dedupeFields`的`deleteBy`模式。 在`input`陣列的每個記錄中包含對應的欄位。 每個呼叫最多允許300筆記錄。

```http
POST /rest/v1/customobjects/{apiName}/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000"
      },
      {
         "vin":"29UYA31581L000000"
      },
      {
         "vin":"39UYA31581L000000"
      }
   ]
}

{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq":1,
         "marketoGUID":"da42707c-4dc4-4fc1-9fef-f30a3017240a",
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1013",
               "message":"Object not found"
            }
         ]
      }
   ]
}
```

和更新一樣，結果包含每個記錄的狀態。 其中也包含成功刪除的`marketoGUID`或失敗刪除的`reasons`陣列。

## 自訂物件型別

自訂物件中繼資料API可讓您遠端管理自訂物件結構。 用它來建立自訂物件型別或修改現有的物件型別。 建立或修改型別後，請先核准再使用。

如需詳細資訊，請參閱[自訂物件產品檔案](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。

- 您無法在Marketo UI中修改API建立的自訂物件型別。
- 自訂物件型別的最大數量為10種。
- 每個型別的自訂物件欄位數上限為50。
- 自訂物件型別API名稱和顯示名稱可包含英數字元和底線字元「_」。

### 查詢型別

以下列其中一種方式擷取自訂物件型別中繼資料：

- 說明自訂物件型別傳回一個自訂物件型別記錄，並支援依核准狀態篩選。
- 清單自訂物件型別會傳回訂閱中的所有自訂物件型別，並支援依名稱和核准狀態篩選。

### 說明型別

[Describe自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_1)端點傳回一個自訂物件型別的中繼資料。 必要的`apiName`路徑引數指定要描述的型別的API名稱。

如果核准版本存在，端點會傳回該版本。 否則，它會傳回草稿版本。 使用選用的`state`引數來要求`draft`、`approved`或`approvedWithDraft`。

```http
GET /rest/v1/customobjects/schema/{apiName}/describe.json?state=approved
```

```json
{
    "requestId": "d9bf#16876fa84b9",
    "result": [
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "Automobile owned",
            "apiName": "car",
            "idField": "marketoGUID",
            "createdAt": "2019-01-22T19:12:18Z",
            "updatedAt": "2019-01-22T19:12:18Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "leadID"
                ]
            ],
            "relationships": [
                {
                    "field": "leadID",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadID",
                    "displayName": "Lead ID",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

回應包含：

- 中繼資料： state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships。
- 標準欄位： marketoGUID、createdAt、updatedAt。
- 自訂欄位：leadId、vin、make、model、year。

### 清單型別

[清單自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/listCustomObjectTypesUsingGET)端點會傳回目的地執行個體中所有可用自訂物件型別的中繼資料。

如果核准版本存在，端點會傳回該版本。 否則，它會傳回草稿版本。

選用的引數包括：

- **state**：指定要傳回的版本。 有效值為&#x200B;**草稿**、**已核准**&#x200B;和&#x200B;**approvedWithDraft**。
- **names**：指定要以逗號分隔的API名稱清單傳回的自訂物件型別。

```http
GET /rest/v1/customobjects/schema.json?names=purchaseHistory
```

```json
{
    "requestId": "a181#167ebe94703",
    "result": [
        {
            "state": "approved",
            "displayName": "Purchases",
            "description": "Purchase data",
            "apiName": "purchaseHistory",
            "idField": "marketoGUID",
            "createdAt": "2014-09-12T16:13:37Z",
            "updatedAt": "2014-09-12T16:13:42Z",
            "dedupeFields": [
                "lead_id",
                "product_name"
            ],
            "searchableFields": [
                [
                    "lead_id",
                    "product_name"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "lead_id"
                ]
            ],
            "relationships": [
                {
                    "field": "lead_id",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "lead_id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "marketoGUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "amount",
                    "displayName": "Amount",
                    "dataType": "float",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "lead_id",
                    "displayName": "lead_id",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "product_name",
                    "displayName": "Product Name",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "purchase_date",
                    "displayName": "Transaction Date",
                    "dataType": "datetime",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        },
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "No really, it is a car!",
            "apiName": "car_c",
            "idField": "marketoGUID",
            "createdAt": "2017-02-22T19:55:51Z",
            "updatedAt": "2018-12-11T23:52:56Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ]
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

### 建立和更新型別

#### 建立型別

使用[同步自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncCustomObjectsUsingPOST)端點來建立或更新自訂物件型別。

屬性包括：

- **action**：控制記錄作業的選擇性屬性。 有效值為&#x200B;**createOnly**、**createOrUpdate**&#x200B;和&#x200B;**updateOnly**。 預設值為createOrUpdate。
- **displayName**&#x200B;和&#x200B;**apiName**：除非動作是updateOnly，否則為必要。 兩者都必須是唯一的，以避免與客戶布建的型別發生衝突。 LaunchPoint合作夥伴應在代表名稱空間前加上。 對於apiName，請使用小寫或駝峰式大小寫來與其他文字字串區分開來。
- **pluralName**：指定displayName複數形式的選擇性屬性。
- **description**：描述自訂物件型別的選擇性屬性。
- **showInLeadDetail**：選用的Boolean屬性，可在Marketo UI的Lead資料庫頁面中啟用自訂物件資料。 預設值為false。

請謹慎選擇自訂物件名稱。 在每個新自訂物件名稱前面加上字串，用以識別您的公司。 前置詞可包含英數字元或底線。 此慣例有助於在MLM UI中更容易找到物件，並有助於確保物件名稱是唯一的。

下列範例會建立具有API名稱「transaction」的自訂物件型別。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"createOnly",
  "displayName": "Transaction",
  "apiName": "transaction",
  "description": "Commerce happens"
}
```

```json
{
    "requestId": "fb9d#167f2879557",
    "result": [],
    "success": true
}
```

以下請求說明新建立的型別。

```http
GET /rest/v1/customobjects/schema/transaction/describe.json
```

```json
{
    "requestId": "cf9b#167f28db0a9",
    "result": [
        {
            "state": "draft",
            "displayName": "Transaction",
            "description": "Commerce happens",
            "apiName": "transaction",
            "idField": null,
            "createdAt": null,
            "updatedAt": null,
            "dedupeFields": [],
            "searchableFields": [
                []
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

回應包含：

- 中繼資料： state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships。
- 標準欄位： marketoGUID、createdAt、updatedAt。

#### 更新型別

下列範例會更新API名稱為「transaction」之現有型別的說明。 **apiName**&#x200B;屬性為必要項。 因為型別已經存在，所以要求會針對選用的&#x200B;**action**&#x200B;屬性使用updateOnly。

除了&#x200B;**apiName**&#x200B;之外，您也可以更新建立期間可用的屬性。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"updateOnly",
  "apiName": "transaction",
  "description":"No really, commerce happens!"
}
```

```json
{
    "requestId": "103c3#167f2223fd7",
    "result": [],
    "success": true
}
```

## 核准型別

核准自訂物件型別後再使用。 當您使用[同步自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncCustomObjectTypeUsingPOST)端點建立型別時，Marketo會建立草稿版本。 新增自訂欄位後，核准草稿。 核准會建立已核准的版本並刪除草稿。

當您使用「同步自訂物件型別」或「新增/更新/刪除自訂物件型別欄位」端點修改現有型別時，Marketo會建立草稿。 對型別或其欄位的變更只會影響草稿版本。 進行變更後，核准草稿。 核准會以草稿取代已核准的版本，並刪除草稿。

如需詳細資訊，請參閱[自訂物件核准檔案](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)。

在核准自訂物件型別後，您無法：

- 更新`displayName`或`apiName`。
- 新增或移除連結欄位。
- 新增或移除重複資料刪除欄位。

在核准型別之前，請仔細規劃結構描述和命名慣例。

### 核准型別

使用[核准自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/approveCustomObjectTypeUsingPOST)端點將草稿發佈為新核准版本。 唯一需要的引數是&#x200B;**apiName**&#x200B;路徑引數。

只有當型別處於草稿狀態並符合記錄的[驗證規則](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)時，才能核准型別。

```http
POST /rest/v1/customobjects/schema/{apiName}/approve.json
```

```json
{
    "requestId": "11d86#1685304a983",
    "result": [],
    "success": true
}
```

### 捨棄型別

使用[捨棄自訂物件型別草稿](https://developer.adobe.com/marketo-apis/api/mapi#operation/discardCustomObjectTypeUsingPOST)端點來刪除草稿版本。 唯一需要的引數是`apiName`路徑引數。

您只能捨棄處於草稿狀態的型別。 您無法捨棄已核准的型別。

```http
POST /rest/v1/customobjects/schema/{apiName}/discardDraft.json
```

```json
{
    "requestId": "5228#1684edde793",
    "result": [],
    "success": true
}
```

### 刪除型別

使用[刪除自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/deleteCustomObjectsUsingPOST)端點刪除核准的版本。 唯一需要的引數是`apiName`路徑引數。

此作業具有破壞性，無法復原。 在刪除型別之前，請從觸發器和篩選器等資產中移除其使用。 使用取得自訂物件相依Assets端點來擷取型別的相依資產。

POST /rest/v1/customobjects/schema/{apiName}/delete.json

```json
{
    "requestId": "14e36#1684efc4227",
    "result": [],
    "success": true
}
```

## 自訂物件欄位

依預設，所有自訂物件型別都包含下列標準欄位：

- Marketo GUID：自訂物件型別的唯一識別碼。
- 建立時間：建立自訂物件型別的日期時間。
- 更新時間：上次更新自訂物件型別的日期時間。

使用下列端點來新增、變更或刪除自訂欄位。

- 欄位數量上限為50。
- 在核准自訂物件後，您最多可以新增20個額外的欄位到其中。
- 至少需要一個重複資料刪除欄位。 最多允許三個重複資料刪除欄位。
- 欄位API名稱和顯示名稱可包含英數字元和底線字元「_」。

如需詳細資訊，請參閱[自訂物件欄位檔案](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 新增欄位

使用[新增自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/addCustomObjectTypeFieldsUsingPOST)端點新增一或多個欄位至自訂物件。 要求內文包含具有一或多個元素的`input`陣列。 每個元素都是一個JSON物件，其屬性用於描述欄位。

欄位屬性為：

- `name`：必要。 欄位的API名稱，該名稱必須是自訂物件的唯一名稱。 使用小寫或駝峰式大小寫來區分名稱與其他文字字串。
- `displayName`：必要。 可讀取的欄位名稱，該名稱必須是自訂物件的唯一名稱。
- `dataType`：必要。 欄位的資料型別。 使用[取得自訂物件型別欄位資料型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectTypeFieldDataTypesUsingGET)端點來擷取允許的資料型別。
- `description`：選擇性。 欄位說明。
- `isDedupeField`：選擇性布林值，指定在自訂物件更新作業期間是否使用欄位進行重複資料刪除。 預設值為false。 一對多關係需要重複資料刪除欄位。
- `relatedTo`：指定連結欄位的選用物件。 對於一對多關係，`name`會識別「連結物件」或父物件，而`field`會識別父物件中的「連結欄位」或索引鍵欄位。

自訂物件可包含資料型別為「連結」的欄位。 連結欄位會建立自訂物件與其他物件型別（例如「銷售機會」與「公司」）之間的關係。 如需連結欄位的詳細資訊，請參閱[自訂物件欄位檔案](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。 使用[取得自訂物件可連結物件](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectTypeLinkableObjectsUsingGET)端點來擷取允許的連結物件。

自訂物件無法連結至具有現有連結欄位的另一個自訂物件。 如需詳細資訊，請參閱[連結欄位檔案](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 一對多關係

對於一對多自訂物件結構，請使用連結欄位將自訂物件連線至標準Lead或Company物件。 以下工作流程使用[車主範例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)建立自訂物件，以儲存車資訊並連線至銷售機會。

1. 建立&#x200B;**Car**&#x200B;物件。
1. 將欄位新增至&#x200B;**Car**&#x200B;物件： **VIN**&#x200B;上的重複資料刪除，以及連結至&#x200B;**銷售機會****/銷售機會ID**。
1. 核准&#x200B;**Car**&#x200B;物件。

首先，建立包含特定車輛資訊的自訂物件型別。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Car",
    "pluralName": "Cars"
    "apiName": "car",
    "description": "Automobile owned",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "cbaa#16876dd3da6",
    "result": [],
    "success": true
}
```

接下來，將欄位新增至Car自訂物件型別。 使用連結欄位來指定要連線的物件和欄位。 在此範例中，連結物件為銷售機會，連結欄位為ID。

使用字串欄位進行重複資料刪除(VIN)。 新增三個欄位以儲存「製作」、「模型」和「年」屬性。

```http
POST /rest/v1/customobjects/schema/car/addField.json
```

```json
{
  "input": [
    {
      "displayName": "Lead ID",
      "description": "Link field to Lead object",
      "name": "leadID",
      "dataType": "link",
      "relatedTo": {
        "field": "id",
        "name": "lead"
      }
    },
    {
      "displayName": "VIN",
      "description": "Vehicle ID number",
      "name": "vin",
      "dataType": "string",
      "isDedupeField": true
    },
    {
      "displayName": "Make",
      "description": "Vehicle make",
      "name": "make",
      "dataType": "string"
    },
    {
      "displayName": "Model",
      "description": "Vehicle model",
      "name": "model",
      "dataType": "string"
    },
    {
      "displayName": "Year",
      "description": "Vehicle year",
      "name": "year",
      "dataType": "integer"
    }
  ]
}

{
    "requestId": "b359#16876f17996",
    "result": [],
    "success": true
}
```

最後，核准自訂物件型別。

```http
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

### 多對多關係

多對多關係會使用標準物件（例如Lead或Company）與「邊緣」自訂物件之間的「橋接」自訂物件。 邊緣物件是主要實體，並包含描述性欄位。

橋接物件會解析與兩個連結欄位的關係。 一個欄位指向父標準物件，如一對多關係。 其他則指向邊緣物件，該物件為無連結的自訂物件。 橋接器物件也可以包含描述性欄位。

下列工作流程使用[大學課程註冊範例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)。 它會建立Course邊緣物件和Enrollment bridge物件，將Courses與Leads連線起來。

1. 建立&#x200B;**課程**&#x200B;邊緣物件。
1. 在&#x200B;**課程ID**&#x200B;上新增欄位至&#x200B;**課程：**&#x200B;重複資料刪除。
1. 核准&#x200B;**課程**。
1. 建立&#x200B;**註冊**&#x200B;橋接器物件。
1. 新增欄位至&#x200B;**註冊：**&#x200B;註冊ID **上的**&#x200B;重複資料刪除、連結至&#x200B;**課程****/課程ID**&#x200B;欄位，以及連結至**銷售機會****/銷售機會ID**。
1. 核准&#x200B;**註冊**。

首先，建立包含課程特定資訊的邊緣物件型別：

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Course",
    "pluralName": "Courses",
    "apiName": "course",
    "description": "Modeling a college course, an edge object in Marketo",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "4aec#168879ede00",
    "result": [],
    "success": true
}
```

接著，新增四個自訂欄位來模型化大學課程：課程ID、課程講師、課程地點和課程名稱。 將課程ID指定為重複資料刪除欄位，因為至少需要一個重複資料刪除欄位。

```http
POST /rest/v1/customobjects/schema/course/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Course ID",
            "name": "courseID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Course Instructor",
            "name": "courseInstructor",
            "dataType": "string"
        },
        {
            "displayName": "Course Location",
            "name": "courseLocation",
            "dataType": "string"
        },
        {
            "displayName": "Course Name",
            "name": "courseName",
            "dataType": "string"
        }
    ]
}
```

```json
{
    "requestId": "cc36#16895b82a41",
    "result": [],
    "success": true
}
```

核准邊緣物件型別，以便在連結至橋接物件型別時可以參考它。 自訂物件型別必須先獲得核准，才能選取為連結物件。

```http
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

完成Edge物件後，請建立包含註冊特定資訊的Bridge物件型別。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action": "createOnly",
    "displayName": "Enrollment",
    "pluralName": "Enrollments",
    "apiName": "enrollment",
    "description": "Bridge object for Course custom object",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "8fbb#168960f671b",
    "result": [],
    "success": true
}
```

將兩個連結欄位新增至Bridge物件型別：一個連結至Lead物件，另一個連結至Course物件。 使用「銷售機會Id」欄位來連結至「銷售機會」，並使用「課程Id」欄位來連結至「課程」。

將註冊ID新增為重複資料刪除欄位，因為至少需要一個重複資料刪除欄位。 然後新增「成績」欄位以追蹤學生的表現。

```http
POST /rest/v1/customobjects/schema/enrollment/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Lead ID",
            "description": "Link field to Lead object",
            "name": "leadID",
            "dataType": "link",
            "relatedTo": {
                "field": "id",
                "name": "lead"
            }
        },
        {
            "displayName": "Course ID",
            "description": "Link field to Course object",
            "name": "courseID",
            "dataType": "link",
            "relatedTo": {
                "field": "courseID",
                "name": "course"
            }
        },
        {
            "displayName": "Enrollment ID",
            "description": "Unique ID for deduplication",
            "name": "enrollmentID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Grade",
            "description": "Grade for the course",
            "name": "grade",
            "dataType": "string"
        }
    ]
}
```

```json
{
    "requestId": "7be5#168973f5052",
    "result": [],
    "success": true
}
```

最後，核准橋接器物件。

```http
POST /rest/v1/customobjects/schema/enrollment/approve.json
```

```json
{
    "requestId": "9a76#16897b0e84b",
    "result": [],
    "success": true
}
```

使用[同步處理自訂物件](#create_and_update)或[大量自訂物件匯入](https://experienceleague.adobe.com/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import.html?lang=en)，以程式設計方式填入自訂物件記錄。 或者，在Marketo UI中使用[匯入自訂物件資料](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/import-custom-object-data)。

## 更新欄位

使用[更新自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/updateCustomObjectTypeFieldUsingPOST)端點來更新草稿自訂物件中的欄位。

必要的路徑引數包括：

- `apiName`：自訂物件型別的API名稱。
- `fieldAPIName`：自訂物件型別欄位的API名稱。

請求內文包含JSON物件，其索引鍵/值配對會指定要更新的欄位屬性。

```http
POST /rest/v1/customobjects/schema/{apiName}/{fieldApiName}/updateField.json
```

```json
{
  "displayName": "Very Long Title",
  "dataType": "text"
}
```

```json
{
    "requestId": "d523#1684f355db9",
    "result": [],
    "success": true
}
```

## 刪除欄位

使用[刪除自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/deleteCustomObjectTypeFieldsUsingPOST)端點從自訂物件中刪除一或多個欄位。 必要的`apiName`路徑引數指定了自訂物件型別的API名稱。

要求內文包含的JSON物件具有一或多個元素的`input`陣列。 每個元素都是JSON物件，其`name`屬性會指定要刪除之欄位的API名稱。

```http
POST /rest/v1/customobjects/schema/{apiName}/deleteField.json
```

```json
{
    "input":
    [
        {
            "name": "title"
        },
        {
            "name": "author"
        }
    ]
}
```

```json
{
"requestId": "b359#19934f17996",
"result": [],
"success": true
}
```

## 清單欄位資料型別

[取得自訂物件型別欄位資料型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectTypeFieldDataTypesUsingGET)端點傳回所有允許的欄位資料型別。 使用此端點可識別建模自訂物件型別時可用的自訂欄位資料型別。

```http
GET /rest/v1/customobjects/schema/fieldDataTypes.json
```

```json
{
    "requestId": "c405#167ed49e866",
    "result": [
        "string",
        "boolean",
        "integer",
        "float",
        "link",
        "email",
        "currency",
        "date",
        "datetime",
        "phone",
        "text"
    ],
    "success": true
}
```

## 列出可連結自訂物件

[取得自訂物件可連結物件](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectTypeLinkableObjectsUsingGET)端點傳回所有允許的連結物件及其連結欄位。 回應包含Lead和Company等Standard Object，以及在執行個體中建立的任何Custom Object。

```http
GET /rest/v1/customobjects/schema/linkableObjects.json
```

```json
{
    "requestId": "11e62#167f1160e4e",
    "result": [
        {
            "name": "lead",
            "displayName": "Lead",
            "fields": [
                {
                    "name": "Account Balance",
                    "displayName": "Account Balance",
                    "dataType": "integer"
                },
                {
                    "name": "Email Address",
                    "displayName": "Email Address",
                    "dataType": "email"
                },
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Display Name",
                    "displayName": "Marketo Social Facebook Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Id",
                    "displayName": "Marketo Social Facebook Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Photo URL",
                    "displayName": "Marketo Social Facebook Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Profile URL",
                    "displayName": "Marketo Social Facebook Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Reach",
                    "displayName": "Marketo Social Facebook Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Enrollments",
                    "displayName": "Marketo Social Facebook Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Visits",
                    "displayName": "Marketo Social Facebook Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Gender",
                    "displayName": "Marketo Social Gender",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Display Name",
                    "displayName": "Marketo Social LinkedIn Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Id",
                    "displayName": "Marketo Social LinkedIn Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Photo URL",
                    "displayName": "Marketo Social LinkedIn Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Profile URL",
                    "displayName": "Marketo Social LinkedIn Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Reach",
                    "displayName": "Marketo Social LinkedIn Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Enrollments",
                    "displayName": "Marketo Social LinkedIn Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Visits",
                    "displayName": "Marketo Social LinkedIn Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Syndication Id",
                    "displayName": "Marketo Social Syndication Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Total Referred Enrollments",
                    "displayName": "Marketo Social Total Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Total Referred Visits",
                    "displayName": "Marketo Social Total Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Display Name",
                    "displayName": "Marketo Social Twitter Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Id",
                    "displayName": "Marketo Social Twitter Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Photo URL",
                    "displayName": "Marketo Social Twitter Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Profile URL",
                    "displayName": "Marketo Social Twitter Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Reach",
                    "displayName": "Marketo Social Twitter Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Enrollments",
                    "displayName": "Marketo Social Twitter Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Visits",
                    "displayName": "Marketo Social Twitter Referred Visits",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "company",
            "displayName": "Company",
            "fields": [
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "car_c",
            "displayName": "Car",
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string"
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string"
                }
            ]
        }
    ],
    "success": true
}
```

## 取得自訂物件相依的Assets

[取得自訂物件相依的Assets](https://developer.adobe.com/marketo-apis/api/mapi#operation/getCustomObjectTypeDependentAssetsUsingGET)端點傳回自訂物件型別的相依資產及其在執行個體中的位置。 移除整合時請使用它，以識別使用自訂物件型別的每個位置。

```http
GET /rest/v1/customobjects/schema/{apiName}/dependentAssets.json
```

```json
{
    "requestId": "71cf#16a21f30ed6",
    "result": [
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)"
        },
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)",
            "usedFields": [
                "leadID",
                "make",
                "model",
                "vin",
                "year"
            ]
        }
    ],
    "success": true
}
```

## 逾時

- 除非另有說明，否則自訂物件端點的逾時值為30秒。
- 同步自訂物件的逾時時間為120秒。
- 刪除自訂物件的逾時時間為60秒。
