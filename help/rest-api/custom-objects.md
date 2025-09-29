---
title: 自訂物件
feature: REST API, Custom Objects
description: 瞭解如何透過REST API建立和管理Marketo自訂物件，包括清單和說明端點、中繼資料、關係、欄位和查詢。
exl-id: 88e8829b-f8f1-46d7-a753-5aa6e20e2c40
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '2925'
ht-degree: 0%

---

# 自訂物件

[**自訂物件端點參考**](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects) Marketo可讓使用者定義與Marketo標準物件（銷售機會、公司）或其他Marketo自訂物件相關的Marketo自訂物件。  Marketo自訂物件可依照[此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)所述，使用Marketo UI建立，或依照以下所述使用自訂物件中繼資料API建立。

必須有適當的Marketo訂閱型別才能存取自訂物件中繼資料API。  如需詳細資訊，請洽詢您的CSM。

## 清單

除了可用於潛在客戶資料庫物件的標準說明、查詢、更新及刪除呼叫之外，自訂物件還有[清單呼叫](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)可用。  呼叫此端點將會傳回回應，其中包含目的地執行個體中可用的自訂物件清單，以及有關物件的其他中繼資料。

```
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

回應會提供存在於每個物件上的關係清單。  關聯性將具有`field`成員（表示保留連結值的物件上的欄位）、`type`成員（表示關聯性是屬於父系或子系型別物件）和`relatedTo`物件（表示相關物件的名稱），以及該物件上的連結欄位。

## 說明

自訂物件的[describe呼叫](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)遵循與Opportunities和Companies相同的模式，在回應中加上`relationships`陣列，並在URI中加入`apiName`路徑引數，該引數採用要描述之自訂物件型別的API名稱。  就像清單呼叫一樣，這會列出此自訂物件型別可用的任何關係。

```
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

[查詢自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)與其他Lead Database API稍有不同，且採用`apiName`路徑引數，如describe。  對於一般的filterType引數，查詢是類似其他記錄型別的查詢的簡單GET，需要`filterType`和`filterValues`。  它會選擇性地接受`**fields**`、`batchSize`和`nextPageToken`引數。  請求欄位清單時，如果請求了特定欄位但未傳回，則值會隱含為空值。

```
GET /rest/v1/customobjects/{apiName}.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa,dff23271-f996-47d7-984f-f2676861b5fb
```

```
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

或者，使用複合金鑰進行查詢時，API的行為類似機會角色API，接受具有JSON內文的POST。  JSON內文可能有與GET查詢相同的成員，但`filterValues`除外。  有一個`input`陣列取代篩選值，它採用物件，其中包含每個物件型別`dedupeFields`的命名成員。

```
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

使用[同步自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端點來建立或更新自訂物件，您可以使用`action`引數指定作業。  在一次呼叫中可建立或更新最多300筆記錄。  `input`陣列中使用的值主要是以[描述自訂物件](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/endpoint-reference#!/Custom_Objects/describeUsingGET_1)端點傳回的資訊為基礎。 在範例car物件中，只有一個重複資料刪除欄位，`vin`。  若要在使用dedupeFields模式時更新或建立記錄，輸入陣列中的每個記錄都必須至少包含一個`vin`欄位。

```
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

透過`idField`模式執行更新時，`idField`將一律為`marketoGUID`，因此每個記錄將一律需要`marketoGUID`欄位。  請記住，`idField`僅對updateOnly動作型別有效，因為此欄位一律由系統管理。  您的回應將包含結果陣列中每個個別記錄的&#x200B;**狀態**，以及`marketoGUID`或`reasons`陣列（視個別記錄的作業是否成功而定）。

## 刪除

[刪除記錄](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)非常簡單。  只要選取您的`deleteBy`模式（`idField`或`dedupeFields`），並在`input`陣列中的每個記錄中包含對應的欄位。 每個呼叫最多允許300筆記錄。

```
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

如同更新，您的結果將包含每個個別記錄的狀態，以及`marketoGUID`或`reasons`陣列（視刪除是否成功而定）。

## 自訂物件型別

自訂物件中繼資料API可讓您從遠端管理自訂物件結構。  此API可讓您建立新的自訂物件型別或修改現有型別。  在建立或修改自訂物件型別後，必須核准它才能使用。  如需自訂物件的詳細資訊，請參閱產品檔案[這裡](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。

* 由API建立的自訂物件型別不可使用Marketo UI進行修改
* 允許的自訂物件型別數目上限為10
* 每個型別的自訂物件欄位數量上限為50
* 自訂物件型別API名稱和顯示名稱可能包含英數字元和底線「_」

### 查詢型別

有兩種方式可擷取自訂物件型別中繼資料：說明自訂物件型別，這會傳回  單一自訂物件型別的記錄，可依核准狀態篩選，以及清單自訂物件型別，其會傳回訂閱中所有自訂物件型別的清單，並可依名稱和核准狀態篩選。

### 說明型別

[Describe自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)端點傳回單一自訂物件型別的中繼資料。 所需的`apiName`路徑引數是說明的自訂物件型別的API名稱。  如果核准版本存在，則會傳回該版本。  否則會傳回草稿版本。  選用的`state`引數用於指定要傳回的版本： `draft`、`approved`或`approvedWithDraft`。

```
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

我們可以在這裡看到以下屬性：

* 中繼資料： state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships
* 標準欄位： marketoGUID、createdAt、updatedAt
* 自訂欄位leadId、vin、make、  模型，年

### 清單型別

[清單自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/listCustomObjectTypesUsingGET)端點會傳回目的地執行個體中所有可用自訂物件型別的中繼資料。  請注意，此端點類似於[清單自訂物件](https://experienceleague.adobe.com/docs/marketo-developer/marketo/soap/custom-objects/custom-objects.html?lang=en)，但更全面，並包含其他中繼資料，例如狀態、關聯性和欄位。 如果核准版本存在，則會傳回該版本。  否則會傳回草稿版本。  選用的&#x200B;**state**&#x200B;引數用於指定要傳回的自訂物件型別的版本： **草稿**、**已核准**&#x200B;或&#x200B;**已核准並具草稿**。  選用的&#x200B;**names**&#x200B;引數用於指定要傳回的特定自訂物件型別名稱；其結構為API名稱清單（以逗號分隔）。

```
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
            "description": "No really, it's a car!",
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

[同步自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端點用來建立或更新自訂物件型別。  要執行的記錄作業是由選用的&#x200B;**action**&#x200B;屬性所控制，該屬性可以是&#x200B;**createOnly**、**createOrUpdate**&#x200B;或&#x200B;**updateOnly**。  預設設定為createOrUpdate。 **displayName**&#x200B;和&#x200B;**apiName**&#x200B;屬性是必要的，除非使用updateOnly作為您的動作。   兩者都必須是唯一的，以避免與客戶布建的型別發生衝突。  如果您是LaunchPoint合作夥伴，您應在這些名稱前面加上代表名稱空間。  對於apiName，慣例是使用小寫或駝峰式大小寫來協助區分其他文字字串。 選用的&#x200B;**pluralName**&#x200B;屬性指定displayName的複數形式。  選用的&#x200B;**description**&#x200B;屬性是用來描述自訂物件型別。  選用的&#x200B;**showInLeadDetail**&#x200B;布林值屬性可用來在Marketo UI的Lead資料庫頁面中檢視自訂物件資料。  預設設定為false。

命名自訂物件時請小心。 建立新的自訂物件時，建議您在名稱前面加上字串，用以表示您的公司名稱（允許使用英數字元或底線）。 這可讓自訂物件在MLM UI中輕鬆搜尋，也有助於確定名稱是唯一的。

以下是使用API建立新自訂物件型別的範例  名稱「transaction」。

```
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

以下為後續呼叫，說明新建立的型別。

```
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

我們可以在這裡看到以下與自訂物件相關的資料：

* 中繼資料： state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships
* 標準欄位： marketoGUID、createdAt、updatedAt

#### 更新型別

以下範例說明如何更新API名稱為「transaction」之現有型別的說明。  **apiName**&#x200B;屬性為必要項。  在此處，我們將假設該型別已經存在，並對選用的&#x200B;**action**&#x200B;屬性使用updateOnly。  除了&#x200B;**apiName**&#x200B;之外，可用於建立的屬性也可能會更新。

```
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

自訂物件型別必須先經過核准，才能使用。 使用[同步自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectTypeUsingPOST)端點建立新的自訂物件型別時，會建立為草稿版本。 新增完自訂欄位後，您必須核准草稿版本。 這樣會建立核准版本並刪除草稿版本。 當使用「同步自訂物件型別」端點或使用「新增/更新/刪除自訂物件型別」欄位端點修改現有自訂物件型別時，會建立草稿版本。 對型別或其欄位的所有修改只會影響草稿版本。 完成修改後，您必須核准草稿版本。 這會將核准的版本替換為草稿版本，並刪除草稿版本。 如需自訂物件核准的詳細資訊，請參閱產品檔案[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)。

在核准自訂物件型別後，您無法：

* 更新`displayName`或`apiName`
* 新增或移除連結欄位
* 新增或移除重複資料刪除欄位

基於這些原因，請務必仔細考慮您計畫使用的結構描述和命名慣例。

### 核准型別

使用[核准自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/approveCustomObjectTypeUsingPOST)端點將草稿版本發佈為新核准版本。  **apiName**&#x200B;是唯一可作為路徑引數的必要引數。  除非型別處於草稿狀態，且符合描述為[此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)的一組驗證規則，否則無法核准型別。

```
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

使用[捨棄自訂物件型別草稿](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/discardCustomObjectTypeUsingPOST)端點來刪除草稿版本。 `apiName`是唯一需要的引數做為路徑引數。 型別必須處於草稿狀態才能捨棄，亦即無法捨棄已核准的型別。

```
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

使用[刪除自訂物件型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)端點刪除核准的版本。  `apiName`是唯一需要的引數做為路徑引數。  請注意，這是破壞性作業；無法復原。  除非已將型別從任何資產（例如觸發程式或篩選器）的使用中移除，否則無法刪除型別。  「取得自訂物件相依Assets」端點可用來擷取指定型別的相依資產清單。

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

* Marketo GUID — 自訂物件型別的唯一識別碼
* 建立時間 — 建立自訂物件型別的日期時間
* 更新時間 — 上次更新自訂物件型別的日期時間

您可以自由使用下述端點新增/變更/刪除自訂欄位。

* 允許的最大欄位數為50
* 在核准自訂物件後，您最多可以向自訂物件新增20個額外的欄位
* 至少需要1個重複資料刪除欄位，最多允許3個
* 欄位API名稱和顯示名稱可能包含英數字元和底線「_」

如需自訂物件欄位的詳細資訊，請參閱產品檔案[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 新增欄位

[新增自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/addCustomObjectTypeFieldsUsingPOST)端點可讓您新增一或多個欄位至自訂物件。  要求內文包含具有一或多個元素的`input`陣列。  每個元素都是一個JSON物件，其屬性用於描述欄位。 必要的`name`屬性是欄位的API名稱，且必須是自訂物件唯一的。   慣例是使用小寫或駝峰式大小寫來協助區分其他文字字串。 必要的`displayName`屬性是使用者可讀取的欄位名稱，且必須是自訂物件的唯一名稱。 必要的`dataType`屬性是欄位的資料型別。  A  呼叫[取得自訂物件型別欄位資料型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)端點，即可取得允許的資料型別清單。  自訂物件可包含資料型別為「連結」的欄位。  連結欄位可用來建立自訂物件與系統中其他物件型別（例如銷售機會、公司）之間的關係。  連結欄位的詳細資訊可在[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)找到。 選用的`description`屬性是欄位的描述。 選用的`isDedupeField`布林值屬性會指定在自訂物件更新作業期間是否使用欄位進行重複資料刪除。  預設設定為false。  對於一對多關係，需要重複資料刪除欄位。 選用的`relatedTo`物件屬性指定了連結欄位。  對於一對多關係，此物件包含`name`屬性（要連結的「連結物件」或父物件）和`field`屬性（即「連結欄位」）。  或父物件內的欄位以用作索引鍵屬性。  呼叫[取得自訂物件可連結物件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端點以擷取允許的連結物件清單。  如需連結欄位的詳細資訊，請參閱產品檔案[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。 自訂物件無法連結至具有現有連結欄位的另一個自訂物件。

### 一對多關係

對於一對多自訂物件結構，請使用自訂物件中的連結欄位來將其連線到標準物件：潛在客戶或公司。 我們以Marketo產品檔案[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)的汽車擁有者範例為例，建立包含汽車相關資訊的自訂物件來連線銷售機會。

1. 建立&#x200B;**Car**&#x200B;物件
1. 新增欄位至&#x200B;**Car**&#x200B;物件： **VIN**&#x200B;上的重複資料刪除，連結至&#x200B;**銷售機會****/銷售機會ID**
1. 核准&#x200B;**車**&#x200B;物件

首先，建立自訂物件型別以包含車輛特定資訊。

```
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

現在，新增欄位至Car自訂物件型別。 我們使用連結欄位來指定要連線的物件和欄位。 在此案例中，連結物件為銷售機會，連結欄位為ID。 使用字串欄位進行重複資料刪除(VIN)。  我們將新增三個欄位以儲存其他汽車屬性（製造商、型號、年份）。

```
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

```
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

多對多關係在標準自訂物件（如Lead或Company）與「邊緣」自訂物件之間使用「橋接器」或中介、自訂物件來表示。 Edge物件是包含描述性屬性（欄位）的主要實體。 橋接物件包含資料，可使用2個連結欄位來解析物件關係。  有一個連結欄位指向父標準物件，就像在  一對多關係設定。  另一個連結欄位指向邊緣物件，這是沒有連結的自訂物件。  橋接器物件也可能包含描述性屬性（欄位）。 我們使用Marketo產品檔案[此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)中的大學課程註冊範例，建立邊緣自訂物件以包含課程相關資訊，並建立註冊橋接物件，用於將課程與潛在客戶連線。 步驟如下：

1. 建立&#x200B;**課程**&#x200B;邊緣物件
1. 在&#x200B;**課程ID**&#x200B;上新增欄位至&#x200B;**課程：**&#x200B;重複資料刪除
1. 核准&#x200B;**課程**
1. 建立&#x200B;**註冊**&#x200B;橋接器物件
1. 新增欄位至&#x200B;**註冊：**&#x200B;註冊ID **上的**&#x200B;重複資料刪除、連結至&#x200B;**課程****/課程ID**欄位，以及連結至&#x200B;**銷售機會****/銷售機會ID**
1. 核准&#x200B;**註冊**

首先，建立Edge物件型別以包含課程特定資訊：

```
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

接下來，我們將自訂欄位新增至邊緣物件型別。  在此範例中，我們將新增下列四個自訂欄位來模型化大學課程：課程ID、課程講師、課程位置、課程名稱。  請注意，由於至少需要一個重複資料刪除欄位，因此我們指定課程ID作為重複資料刪除欄位。

```
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

```
{
    "requestId": "cc36#16895b82a41",
    "result": [],
    "success": true
}
```

現在，我們需要核准Edge物件型別，以便稍後在連結至Bridge物件型別時可以參考它。  請注意，自訂物件型別必須經過核准，才能選取為連結物件。

```
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

邊緣物件已完成。  現在，讓我們繼續建立橋接器物件型別，以包含註冊特定資訊。

```
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

若要將自訂欄位新增至橋接器物件型別，請新增兩個連結欄位：一個連結至Lead物件，另一個連結至我們剛建立的Course物件。 若要連結至銷售機會物件，請使用「銷售機會ID」欄位。 若要連結至課程物件，請使用「課程ID」欄位。  接著，新增註冊ID唯一識別碼作為重複資料刪除欄位，因為至少需要一個重複資料刪除欄位。 最後，新增「成績」欄位以追蹤學生的成績。

```
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

```
POST /rest/v1/customobjects/schema/enrollment/approve.json
```

```json
{
    "requestId": "9a76#16897b0e84b",
    "result": [],
    "success": true
}
```

您可以使用[同步自訂物件](#create_and_update)或[大量自訂物件匯入](https://experienceleague.adobe.com/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import.html?lang=en)，以程式設計方式填入自訂物件記錄。 或者，您可以使用Marketo UI功能[匯入自訂物件資料](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/import-custom-object-data)。

## 更新欄位

[更新自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/updateCustomObjectTypeFieldUsingPOST)端點可讓您更新草稿自訂物件中的欄位。  必要的路徑引數`apiName`是自訂物件型別的API名稱。  必要的路徑引數`fieldAPIName`是自訂物件型別欄位的API名稱。  請求內文包含JSON物件，其中包含指定要更新的欄位屬性的索引鍵/值組。

```
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

[刪除自訂物件型別欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectTypeFieldsUsingPOST)端點可讓您從自訂物件中刪除一或多個欄位。  必要的路徑引數`apiName`是自訂物件型別的API名稱。  要求內文包含的JSON物件具有`input`陣列以及一個或多個元素。  每個元素都是具有`name`屬性的JSON物件，其指定要刪除之欄位的API名稱。

```
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

[取得自訂物件型別欄位資料型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)端點傳回所有允許欄位資料型別的清單。 在為自訂物件型別建模以識別受支援的自訂欄位資料型別時，這非常有用。

```
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

[取得自訂物件可連結物件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端點傳回所有允許連結物件及其連結欄位的清單。  此清單將包含「標準物件」（銷售機會、公司），以及在執行個體中建立的任何「自訂物件」。

```
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

[取得自訂物件相依Assets](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeDependentAssetsUsingGET)端點傳回自訂物件型別的相依資產清單，包括其執行個體內位置。  這在移除整合時相當實用，而且您需要識別正在使用自訂物件型別的每個位置。

```
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

* 自訂物件端點的逾時為30秒，除非以下說明
   * 同步自訂物件：120秒
   * 刪除自訂物件：60秒
