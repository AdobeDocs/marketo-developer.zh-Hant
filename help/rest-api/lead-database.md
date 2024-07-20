---
title: 潛在客戶資料庫
feature: REST API, Database
description: 操控主要銷售機會資料庫。
exl-id: e62e381f-916b-4d56-bc3d-0046219b68d3
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1345'
ht-degree: 0%

---

# 潛在客戶資料庫

Marketo Lead Database API是Marketo最常使用的API，可用於從Marketo交換人員和人員相關資料，例如活動、商機和公司。

## 物件

Lead Database物件包括：

- 銷售機會
- 公司/帳戶
- 具名帳戶
- 機會
- 機會角色
- SalesPerson
- 自訂物件
- 活動
- 清單和計畫成員資格

這些物件大多至少包含「建立」、「讀取」、「更新」和「刪除」方法。 此外也包含一個「說明」方法，提供每種型別的可用欄位清單，以及重複資料刪除（針對非Lead物件）所使用的欄位清單，以及可搜尋哪些欄位以擷取記錄。 提供最豐富的一組功能給潛在客戶，因為在Marketo應用程式中，他們的功能種類最多。

## API

如需潛在客戶資料庫API端點的完整清單，包括引數和模型資訊，請參閱[潛在客戶資料庫API端點參考](https://developer.adobe.com/marketo-apis/api/mapi/)。

若執行個體已啟用原生CRM整合(Microsoft Dynamics或Salesforce.com)，則公司、機會、機會角色和銷售人員API都會停用。 記錄會在啟用時透過CRM管理，且無法透過Marketo的API存取或更新。

- 最大批次大小（標準）：300筆記錄
- 最大批次大小（大量）：10MB檔案
- 預設批次大小： 300筆記錄
- Content-type標頭（標準）： application/json
- Content-type標頭（大量）：multipart/form-data

## 說明

針對銷售機會、公司、商機、角色、銷售人員和自訂物件，提供說明API。 呼叫此項會擷取物件的中繼資料，以及可用於更新和查詢的欄位清單。 說明是設計與Marketo適當整合的重要一環。 它提供豐富的中繼資料，說明物件可以和無法互動的方式，以及如何建立、更新和查詢物件。 除了Describe Lead之外，每個都傳回`dedupeFields`回應引數中`deduplication`可用的金鑰清單。 欄位清單可做為索引鍵以查詢`searchableFields`回應引數。

```
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

在此範例中，`dedupeFields`實際上是複合金鑰。 這表示在未來的更新及建立中，當您使用`dedupeFields`模式時，必須包含每個角色的`externalOpportunityId`、`leadId`及`role`的所有三項。 `searchableFields`陣列也提供可用於查詢角色記錄的欄位清單。 這也包括`externalOpportunityId`、`leadId`和`role`的複合索引鍵。

此外還有欄位回應引數，可提供每個欄位的名稱、`displayName`在Marketo UI中的顯示方式、欄位的資料型別、建立後是否可更新，以及欄位的長度（如適用）。

## 查詢

Lead Database物件都共用基本模式，以查詢簡單索引鍵，其中僅引用一個欄位。

```
GET /rest/v1/{type}.json?filterType={field to query}&filterValues={comma-separated list of possible values}
```

對於潛在客戶以外的所有物件，您可以從對應的說明呼叫的searchableFields中選取要查詢的{fields}，並撰寫最多300個值的逗號分隔清單。 也有這些選用的查詢引數：

- `batchSize` — 要傳回的結果數的整數計數。 預設和最大值為300。
- `nextPageToken` — 從先前呼叫傳回的分頁語彙基元。 如需詳細資訊，請參閱[分頁權杖](paging-tokens.md)。
- `fields` — 以逗號分隔的欄位名稱清單，可針對每個記錄傳回。 如需有效欄位清單，請參閱對應的說明。 如果要求特定欄位但未傳回，則值會隱含為空值。
- `_method` — 用於使用POSTHTTP方法提交查詢。 請參閱下方的_method=GET一節以取得使用方式。

舉個簡單的例子，讓我們來看看查詢商機：

```
GET /rest/v1/opportunities.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
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

此呼叫中指定的`filterType`是&quot;idField&quot;，而非&quot;marketoGUID&quot;。 這和「dedupeFields」都是特殊情況，其中對應至idField或dedupeFields的欄位可以這種方式成為別名。 「marketoGUID」仍是呼叫中的結果查詢欄位，但並未在呼叫中明確設定。 物件說明的`idField`和`dedupeFields`所指示的欄位和/或欄位集對於查詢一律有效`filterTypes`。 此呼叫會搜尋符合filterValues中所包含GUID的記錄，並傳回任何符合的記錄。 如果沒有使用此方法找到記錄，回應仍會指出成功，但結果陣列將是空的，因為搜尋已成功執行，但沒有任何記錄可傳回。

如果查詢中的記錄集超過300或指定的`batchSize` （以較小者為準），則回應會具有值為true的成員`moreResult`，以及可包含在後續呼叫以擷取更多集合的`nextPageToken`。 如需詳細資訊，請參閱[分頁權杖](paging-tokens.md)。

### 長URI

有時，例如透過GUID進行查詢時，您的URI可能會很長，並且超過REST服務所允許的8KB。 在此情況下，您必須使用HTTPPOST方法而非GET，並新增查詢引數`_method=GET`。 此外，其餘的查詢引數必須以「application/x-www-form-urlencoded」字串形式傳遞至POST內文中，並傳遞相關聯的Content-type標頭。

```
POST /rest/v1/opportunities.json?_method=GET
```

```
Content-Type: application/x-www-form-urlencoded
```

```
filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb,544fb7f5-2ddf-4fca-ae32-7e6ef1415e9f,f1ba41a2-69d1-4a35-9807-0e159d66f2c9,f7521272-3331-4a89-a768-222baff2f894
```

除了長URI之外，查詢複合鍵時也需要此引數。

### 複合鍵

查詢複合索引鍵的模式與簡單索引鍵不同，因為它需要提交具有JSON主體的POST。 這並非所有情況都需要，只有在具有多個欄位的`dedupeFields`選項已用作`filterType`的情況下。 目前複合金鑰僅供機會角色及某些自訂物件使用。 讓我們看一下具有來自`dedupeFields`的複合索引鍵的商機角色查詢範例：

```
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{  
   "filterType":"dedupeFields",
   "fields":[  
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input":[  
      {  
        "externalOpportunityId":"Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {  
        "externalOpportunityId":"Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {  
        "externalOpportunityId":"Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

JSON物件的結構大部分是平面的，而且具有簡單索引鍵的查詢的所有查詢引數都是有效的成員，但`filterValues`除外。 JSON物件的「輸入」陣列不是篩選值，而是複合索引鍵中的每個欄位都必須有一個成員；在此例中，它們是`externalOpportunityId`、`leadId`和`role`。 這會針對提供的輸入執行`roles`的查詢，並傳回相符的結果。 如果回應傳回帶有`moreResult=true`和`nextPageToken`的引數，您必須包含所有原始輸入和`nextPageToken`，查詢才能正確執行。

## 建立和更新

潛在客戶資料庫記錄的建立和更新都是透過具有JSON本文的POST執行的。 商機、角色、自訂物件、公司和SalesPerson的介面都相同。 Lead的介面稍有不同，您可以在此處閱讀更多相關資訊。

唯一需要的引數是名為`input`的陣列，最多包含300個物件，每個物件都有您要插入/更新為成員的欄位。 您也可以選擇加入`action`引數，可以是： `createOnly`、`updateOnly`或`createOrUpdate`其中之一。 如果省略動作，則模式預設為`createOrUpdate`。 `dedupeBy`是另一個可選引數，當動作設為createOnly或`createOrUpdate`時可使用。 ` dedupeBy`可以是`idField`或`dedupeFields`。 如果選取`idField`，則說明中列出的`idField`會用於重複資料刪除，且必須包含在每個記錄中。 `idField`模式與`createOnly`模式不相容。 如果選取`dedupeFields`，則在使用的物件描述中列出`dedupeFields`，每個記錄中都必須包含每個物件。 如果省略`dedupeBy`引數，則模式預設為`dedupeFields`。

傳遞欄位值清單時，`null`的值或空字串會寫入資料庫為`null`。

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

除了潛在客戶API之外，建立或更新潛在客戶資料庫物件的呼叫會在`result`陣列中的每個物件中傳回`seq`欄位。 列出的數字與請求中更新記錄的順序相對應。 每個專案都會傳回物件型別`idField`的值，以及`status`。 狀態列位會指出「已建立」、「已更新」或「已略過」其中之一。  如果略過狀態，則也會有對應的「原因」陣列，其中包含一或多個原因物件，其中包含程式碼和訊息，指出略過記錄的原因。 如需其他詳細資訊，請參閱[錯誤碼](error-codes.md)。

### 刪除

刪除的介面是銷售機會資料庫物件的標準介面，除了銷售機會。 除了輸入之外，只有一個必要引數`deleteBy,`可以有idField或dedupeFields的值。 讓我們來看看刪除某些自訂物件。

```
POST /rest/v1/customobjects/{name}/delete.json
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
```

```json
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

您現在應該已經熟悉`seq`、`status`、`marketoGUID`和`reasons`。

如需有關使用每種個別物件型別的CRUD作業的詳細資訊，請檢視其個別頁面。
