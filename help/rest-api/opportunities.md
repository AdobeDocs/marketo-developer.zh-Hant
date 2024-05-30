---
title: "商機"
feature: REST API
description: 「使用Marketo API設定商機。」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '786'
ht-degree: 0%

---


# 機會

[機會端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)

Marketo公開API以供讀取、寫入、建立和更新商機記錄。 在Marketo中，機會記錄會透過中繼機會角色物件連結至銷售線索和聯絡人記錄，因此機會可能會連結至許多個別銷售線索。  這兩種物件型別都會透過API公開，而且就像大多數Lead Database物件型別一樣，它們都有相對應的Describe呼叫，這會傳回有關物件型別的中繼資料。

Opportunity API是訂閱的唯讀存取權，具有 [SFDC同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en) 或 [Microsoft Dynamics同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en) 已啟用。

## 說明

說明Opportunity記錄會遵循潛在客戶資料庫物件的標準模式。

```
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

此回應型別最重要的欄位包括 `idField`， `dedupeFields`、和 `searchableFields`.  idField會指出機會的主要索引鍵marketoGUID。  這是系統產生的唯一索引鍵，可用於讀取和更新操作，但不能用於插入，因為它由系統管理。  dedupeFields陣列指出哪些欄位是插入作業的有效金鑰；在機會的情況下，這只有externalOpportunityId。  searchableFields陣列提供您一組有效的欄位，可用於查詢、externalOpportunityId和marketoGUID。

## 查詢

的模式 [查詢商機](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunitiesUsingGET) 密切遵循銷售機會API的規則，新增限制： `filterType` 引數接受列在 `searchableFields` 陣列或對應的描述呼叫或dedupeFields。  請注意，如果您使用自訂機會欄位，只有String或Integer型別的自訂機會欄位會列在searchableFields陣列中。

```
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

您也可以包含選用的查詢引數 `fields`，以傳回其他機會欄位， `nextPageToken`，用於分頁比批次大小大的集合， `batchSize`，預設為，最多為300個。  請求清單時 `fields`，如果要求特定欄位但未傳回，則值會隱含為空值。

## 建立和更新

機會會密切遵循潛在客戶API模式，但有一些限制。  可用的值 `action` 為：createOnly、createOrUpdate和updateOnly。  使用createOnly或createOrUpdate模式時，externalOpportunityId欄位必須包含在每個記錄中。  對於updateOnly模式，可能會使用marketoGUID或externalOpportunityId。  如果未指定，模式會預設為createOrUpdate。

此 `lookupField` 銷售機會API中的引數無法使用，且被dedupeBy引數取代，這只有在動作為updateOnly時有效。  dedupeBy可用的值是&quot;dedupeFields&quot;或&quot;idField&quot;，這兩個值分別由描述呼叫指定為externalOpportunityId和marketoGUID。  如果未指定dedupeBy，預設為dedupeFields模式。  &#39;name&#39;欄位不可為Null。

您一次最多可以提交300筆記錄。

```
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

API會以回應 `marketoGUID` 每個記錄，以及 `status` 欄位，指示每個記錄的個別成功或失敗，以及 `seq` 用於將提交的記錄與回應順序關聯的欄位。  欄位中的數字是請求中提交的記錄索引。

### 欄位

公司物件包含一組欄位。  每個欄位定義都由一組描述該欄位的屬性組成。  屬性的範例為顯示名稱、API名稱和dataType。  這些屬性統稱為中繼資料。

下列端點可讓您查詢公司物件上的欄位。 這些API要求擁有的API使用者必須具備具備以下一項或兩項的角色： `Read-Write Schema Standard Field` 或 `Read-Write Schema Custom Field` 許可權。

### 查詢欄位

查詢機會欄位非常簡單。  您可以依API名稱查詢單一公司欄位，或查詢所有公司欄位集。

#### 依名稱

此 [依名稱取得機會欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldByNameUsingGET) 端點會擷取公司物件上單一欄位的中繼資料。  必要的 `fieldApiName` path引數指定欄位的API名稱。  回應類似於Describe Opportunity端點，但包含其他中繼資料，例如 `isCustom` 表示欄位是否為自訂欄位的屬性。

```
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

此 [取得機會欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldsUsingGET) 端點會擷取公司物件上所有欄位的中繼資料。  依預設，最多會傳回300筆記錄。  您可以使用 `batchSize` 查詢引數以縮小此數目。  如果 `moreResult` attribute為true，表示有更多結果可用。  繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。  此 `nextPageToken` 從此API傳回的應一律重複用於此呼叫的下一個反複專案。

```
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

您可以依重複資料刪除欄位或ID欄位來刪除商機。 使用 `deleteBy` 具有dedupeFields或idField值的引數。 如果未指定，預設值為dedupeFields。 請求內文包含 `input` 要刪除的機會陣列。 每個呼叫最多允許300個機會。

```
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

- 機會端點的逾時為30秒，除非以下說明
   - 同步處理機會：60秒 
   - 刪除機會：60秒
