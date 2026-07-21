---
title: 商機資料庫
feature: REST API, Database
description: Marketo銷售機會資料庫API的指南，涵蓋物件、CRUD和描述方法、查詢模式、批次限制和CRM整合限制。
exl-id: e62e381f-916b-4d56-bc3d-0046219b68d3
TQID: https://experienceleague.adobe.com/7lGbhE92lvIE-XkMyUIaK9GrreZVRdM-WVZTpHARhxE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1058
ht-degree: 1%

---

# 商機資料庫

Marketo銷售機會資料庫API會與Marketo交換人員和人員相關資料。 此資料包括活動、機會和公司。

## 物件

Lead資料庫包含下列物件：

- 銷售機會
- 公司/帳戶
- 已命名帳戶
- 機會
- 機會角色
- SalesPerson
- 自訂物件
- 活動
- 清單和計畫成員資格

大部分的Lead資料庫物件都支援Create、Read、Update和Delete方法。 Describe方法提供每個物件型別的可用欄位。 對於非Lead物件，它也會識別用於重複資料刪除的欄位，以及擷取記錄時可搜尋的欄位。

潛在客戶物件支援最廣泛的功能集，因為潛在客戶在Marketo應用程式中的使用種類最多。

## API

如需潛在客戶資料庫API端點、引數和模型資訊的完整清單，請參閱[潛在客戶資料庫API端點參考](https://developer.adobe.com/marketo-apis/api/mapi)。

當執行個體具有原生Microsoft Dynamics或Salesforce.com CRM整合時，公司、機會、機會角色和銷售人員API都會停用。 CRM會管理這些記錄，因此您無法透過Marketo API存取或更新記錄。

- 最大批次大小（標準）：300筆記錄
- 最大批次大小（大量）：10MB檔案
- 預設批次大小： 300筆記錄
- Content-type標頭（標準）： application/json
- Content-type標頭（大量）：multipart/form-data

## 說明

Describe API適用於銷售機會、公司、商機、角色、銷售人員和自訂物件。 使用它來擷取物件中繼資料，以及可用於更新和查詢的欄位。

除了Describe Leads，每個Describe端點都會傳回：

- `dedupeFields`：可用於重複資料刪除的金鑰。
- `searchableFields`：可供查詢的金鑰。

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

在此範例中，`dedupeFields`是複合金鑰。 當您使用`dedupeFields`模式進行未來的建立和更新時，請包含每個角色的`externalOpportunityId`、`leadId`和`role`。

`searchableFields`陣列列出可供查詢角色記錄的欄位。 此清單包含`externalOpportunityId`、`leadId`和`role`的複合索引鍵。

`fields`回應引數提供每個欄位的下列資訊：

- 名稱。
- `displayName`如Marketo UI中所示。
- 資料型別。
- 欄位是否可在建立後更新。
- 欄位長度（如適用）。

## 查詢

Lead Database物件會針對參照一個欄位的簡單索引鍵共用基本查詢模式。

```http
GET /rest/v1/{type}.json?filterType={field to query}&filterValues={comma-separated list of possible values}
```

針對潛在客戶以外的所有物件，從對應的Describe回應中選取`searchableFields`中的`{field to query}`。 提供最多300個值的逗號分隔清單。

您也可以包含下列選用查詢引數：

- `batchSize`：指定傳回結果數目的整數。 預設值和最大值為300。
- `nextPageToken`：從先前呼叫傳回的分頁語彙基元。 如需詳細資訊，請參閱[分頁權杖](paging-tokens.md)。
- `fields`：要傳回每個記錄的欄位名稱清單（以逗號分隔）。 檢視有效欄位的對應說明。 如果您要求未傳回的欄位，其值會隱含為空值。
- `_method`：使用POST HTTP方法提交查詢。 請參閱_method=GET區段以瞭解使用方式。

下列範例會查詢商機：

```http
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

此呼叫中的`filterType`是&quot;idField&quot;，而非&quot;marketoGUID&quot;。 「idField」和「dedupeFields」都是特殊情況，可讓您針對對應的欄位使用別名。 雖然呼叫未明確設定「marketoGUID」，但會保留為查詢欄位。

物件描述中由`idField`和`dedupeFields`識別的欄位或欄位集一律對查詢有效`filterTypes`。 此呼叫傳回符合filterValues中GUID的記錄。 如果沒有相符的記錄，回應會指出成功，並傳回空的結果陣列。

如果相符的記錄集超過300或指定的`batchSize` （取較小者），回應會包含值為true的`moreResult`和`nextPageToken`。 在後續呼叫中包含代號，以擷取更多記錄。 如需詳細資訊，請參閱[分頁權杖](paging-tokens.md)。

### 長URI

URI可以超過REST服務的8KB限制，例如當您依GUID進行查詢時。 在此情況下，請使用HTTP POST方法而不是GET，並新增`_method=GET`查詢引數。

以「application/x-www-form-urlencoded」字串形式傳遞POST內文中的其餘查詢引數。 同時傳遞相關聯的Content-type標題。

```http
POST /rest/v1/opportunities.json?_method=GET
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb,544fb7f5-2ddf-4fca-ae32-7e6ef1415e9f,f1ba41a2-69d1-4a35-9807-0e159d66f2c9,f7521272-3331-4a89-a768-222baff2f894
```

查詢複合索引鍵時也需要`_method=GET`引數。

### 複合鍵

若要查詢複合索引鍵，請提交具有JSON本文的POST請求。 只有當`filterType`是具有多個欄位的`dedupeFields`選項時，才使用此模式。

複合金鑰目前僅供機會角色和某些自訂物件使用。 下列範例使用複合索引鍵從`dedupeFields`查詢機會角色：

```http
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

JSON物件接受所有用於簡單索引鍵查詢的查詢引數，但`filterValues`除外。 提供JSON物件的「輸入」陣列，而非`filterValues`。 每個物件都必須包含複合索引鍵中的每個欄位。 在此範例中，欄位是`externalOpportunityId`、`leadId`和`role`。

要求會根據提供的輸入查詢`roles`並傳回相符的結果。 如果回應包含`moreResult=true`和`nextPageToken`，請在下一個要求中包含所有原始輸入和`nextPageToken`。

## 建立和更新

透過傳送包含JSON內文的POST請求來建立和更新Lead Database記錄。 商機、角色、自訂物件、公司和SalesPerson使用相同的介面。 潛在客戶使用不同的介面，有關說明請參閱潛在客戶檔案。

唯一需要的引數是`input`，一個最多包含300個物件的陣列。 每個物件都包含要插入或更新的欄位。

您也可以包含下列選用引數：

- `action`：接受`createOnly`、`updateOnly`或`createOrUpdate`。 如果省略，模式會預設為`createOrUpdate`。
- `dedupeBy`：當動作設為createOnly或`createOrUpdate`時，接受`idField`或`dedupeFields`。 如果省略，模式會預設為`dedupeFields`。

當`dedupeBy`為`idField`時，說明中列出的`idField`會用於重複資料刪除，且必須包含在每個記錄中。 `idField`模式與`createOnly`模式不相容。

當`dedupeBy`為`dedupeFields`時，請在每個記錄中包含物件描述中列出的每個`dedupeFields`欄位。

當您傳遞欄位值時，資料庫會將`null`的值或空字串寫入為`null`。

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

除了Leads API，建立和更新呼叫在`result`陣列中的每個物件中傳回`seq`欄位。 編號對應於請求中已更新記錄的位置。

每個結果也會傳回物件型別的`idField`值以及「已建立」、「已更新」或「已略過」的`status`。 如果跳過狀態，結果會包含「原因」陣列。 每個原因物件都包含程式碼和訊息，說明略過記錄的原因。 如需詳細資訊，請參閱[錯誤碼](error-codes.md)。

### 刪除

除了潛在客戶之外，Lead Database物件使用標準的刪除介面。 除了輸入之外，唯一需要的引數是接受idField或dedupeFields的`deleteBy,`。

以下範例會刪除自訂物件：

```http
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

回應包括`seq`、`status`和`marketoGUID`。 對於略過的記錄，它也包含`reasons`。

如需特定物件型別的CRUD作業詳細資訊，請參閱該物件的檔案。
