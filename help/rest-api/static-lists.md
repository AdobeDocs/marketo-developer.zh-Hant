---
title: 靜態清單
feature: REST API, Static Lists
description: 「對靜態清單執行CRUD作業。」
source-git-commit: e8bb45a7b3bee71c3d0ab6117296a75c8959d72e
workflow-type: tm+mt
source-wordcount: '741'
ht-degree: 0%

---


# 靜態清單

[靜態清單端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

[列出成員資格端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

Marketo提供了一組REST API，可用於對靜態清單執行CRUD操作。 這些API遵循資產API的標準介面模式，提供查詢、建立、更新和刪除選項。

## 查詢

查詢靜態清單會遵循以下資產的標準查詢型別： [依id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)， [依名稱](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET)、和 [瀏覽](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET).

### 依Id

[依識別碼查詢](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) 採用單一靜態清單 `id` 作為路徑引數，並傳回單一靜態清單記錄。

```
GET /rest/asset/v1/staticList/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "843c#1641f969e96",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### 依名稱

[依名稱查詢](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Lists/operation/getSmartListByNameUsingGET) 採用靜態清單 `name` 作為引數，並傳回單一靜態清單記錄。 會針對執行個體中的所有靜態清單名稱執行完全相符的字串，並傳回符合該名稱的靜態清單結果。

```
GET /rest/asset/v1/staticList/byName.json?name=Foundation Seed List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "28ab#1641fa246b9",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### 瀏覽

靜態清單也可 [批次擷取](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET). 此 `folder` 引數可用來指定執行查詢的父資料夾，並將其格式化為包含ID和型別的JSON物件。 如同其他大量資產擷取端點， `offset` 和 `maxReturn` 是可用於分頁的可選引數。 此 `earliestUpdatedAt` 和 `latestUpdatedAt` 引數可讓您設定高日期時間下限與高日期時間浮水印，以傳回在指定範圍內建立或更新之靜態清單。 日期時間值必須是有效的ISO-8601字串，而且不應該包含毫秒

```
GET /rest/asset/v1/staticLists.json?folder={"id":13,"type":"Folder"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2dc0#1641f846633",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        },
        {
            "id": 1022,
            "name": "Blacklist Seed List",
            "createdAt": "2017-07-27T23:19:33Z+0000",
            "updatedAt": "2017-07-27T23:21:29Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1022A1"
        },
        {
            "id": 1023,
            "name": "Possible Duplicates Seed List",
            "createdAt": "2017-07-28T00:10:02Z+0000",
            "updatedAt": "2017-07-28T00:11:22Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1023A1"
        }
    ]
}
```

## 建立和更新

[建立靜態清單](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/createStaticListUsingPOST) 會以application/x-www-form-urlencodedPOST執行，其中包含兩個必要的引數。 此 `folder` 引數可用來指定要在其中建立靜態清單的父資料夾，格式化為包含ID和型別的JSON物件。 此 `name` 引數是用來命名靜態清單，且必須是唯一的。 選擇性 `description` 引數可用來說明靜態清單。

```
POST /rest/asset/v1/staticLists.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
folder={"id":1034,"type":"Program"}&name=My Static List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1269d#164209d6e1e",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "createdAt": "2018-06-21T04:32:25Z+0000",
            "updatedAt": "2018-06-21T04:32:25Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

[靜態清單的更新](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/updateStaticListUsingPOST) 是透過個別端點及兩個選用引數所產生。 此 `description` 引數可用來更新靜態清單說明。 此 `name` 引數可用來更新靜態清單名稱，且必須是唯一的。

```
POST /rest/asset/v1/staticList/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is a static list used for testing
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "f84f#16420b4c746",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "description": "This is a static list used for testing",
            "createdAt": "2018-06-21T04:32:26Z+0000",
            "updatedAt": "2018-06-21T04:57:55Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

### 刪除

[刪除靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST) 採用單一靜態清單 `id` 作為路徑引數。 無法刪除匯入或匯出操作正在使用的靜態清單，或其他資產正在使用的靜態清單。

```
POST /rest/asset/v1/staticList/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2c79#16420ded0e9",
    "result": [
        {
            "id": 1027
        }
    ]
}
```

## 清單成員資格

清單成員資格端點提供新增、移除和查詢靜態清單成員的功能。 此外，您可以查詢靜態清單成員資格。

### 新增至清單

此 [新增至清單](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST) 端點用於將一或多個成員新增至清單。 端點需要一個 `listId` path引數，以及一或多個包含銷售機會id的id查詢引數（允許的最大值為300）。

回應包含 `result` 陣列由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```
POST /rest/v1/lists/{listId}/leads.json?id=318594&id=318595
```

```json
{
    "requestId": "6860#1706170ba29",
    "result": [
        {
            "id": 318594,
            "status": "added"
        },
        {
            "id": 318595,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### 從清單移除

此 [從清單移除](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE) 端點用於從清單中移除一或多個成員。 端點需要一個 `listId` path引數，以及一或多個 `id` 包含銷售機會id的查詢引數（允許的最大值為300）。

回應包含 `result` 陣列由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```
{
    "requestId": "9e79#17061689ac3",
    "result": [
        {
            "id": 318603,
            "status": "removed"
        },
        {
            "id": 318595,
            "status": "removed"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### 查詢清單

此 [依清單Id取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET) 端點用於擷取清單的成員。 端點需要一個 `listId` 路徑引數，並可讓數個選用的查詢引數指定篩選條件。

此 `batchSize` 引數可用來指定單一呼叫中傳回的潛在客戶記錄數量（預設值及上限皆為300）。

此 `nextPageToken` 引數用於分頁大型結果集。 此引數不會在第一次呼叫中傳遞，但只會在分頁的後續呼叫中傳遞。

此 `fields` 引數包含回應中傳回之欄位名稱的逗號分隔清單。 若此請求中未包含欄位引數，則會傳回下列預設欄位： email、updatedAt、createdAt、lastName、firstName和id。

回應包含 `result` 陣列包含JSON物件，其中包含請求中指定的潛在客戶欄位。

```
GET /rest/v1/lists/{listId}/leads.json?batchSize=3
```

```json
{
    "requestId": "ddae#170615ba0cc",
    "result": [
        {
            "id": 318594,
            "firstName": "Hanna",
            "lastName": "Crawford",
            "email": "208161Robert.L.Deacon@pookmail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318595,
            "firstName": "Bertha",
            "lastName": "Fulton",
            "email": "208160Tyrone.V.Dyer@trashymail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318596,
            "firstName": "Faith",
            "lastName": "England",
            "email": "208159Rex.M.Bailey@dodgit.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        }
    ],
    "success": true,
    "nextPageToken": "PS5VL5WD4UOWGOUCJR6VY7JQO24LC2U5DRBU4WO4RQMPHDHTK2T3BEZOR75VLQXYB3245WW2GMDSK==="
}
```

#### 依銷售機會Id查詢清單成員資格

此 [清單成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET) 端點可用來檢視一或多個潛在客戶是否為清單的成員。 端點需要一個 `listId` path引數，以及一或多個 `id` 包含銷售機會id的查詢引數（允許的最大值為300）。

回應包含 `result` 陣列由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```
GET /rest/v1/lists/{listId}/leads/ismember.json?id=309901&id=318603&id=999999
```

```json
{
    "requestId": "693a#17061475cf9",
    "result": [
        {
            "id": 309901,
            "status": "memberof"
        },
        {
            "id": 318603,
            "status": "notmemberof"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```
