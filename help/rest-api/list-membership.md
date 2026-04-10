---
title: 清單成員資格（靜態清單）
feature: REST API, Static Lists
description: 使用Marketo銷售機會資料庫REST API將銷售機會新增至靜態清單、移除銷售機會、擷取清單成員，以及檢查清單成員資格。
exl-id: b8f74bcf-834a-44db-81fd-621048afeba4
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '482'
ht-degree: 4%

---

# 清單成員資格（靜態清單）

[列出成員資格端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

List Membership API為使用靜態清單成員提供潛在客戶資料庫端點。 這些端點可用於將潛在客戶新增到清單、從清單中移除潛在客戶、擷取清單的成員，以及判斷一個或多個潛在客戶是否為清單的成員。

## 端點

| 端點 | 方法 | 路徑 |
| --- | --- | --- |
| 新增至清單 | POST | `/rest/v1/lists/{listId}/leads.json` |
| 從清單中移除 | DELETE | `/rest/v1/lists/{listId}/leads.json` |
| 依清單ID取得銷售機會 | GET | `/rest/v1/lists/{listId}/leads.json` |
| 清單的成員 | GET | `/rest/v1/lists/{listId}/leads/ismember.json` |

## 新增至清單

[新增至清單](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST)端點用於將一或多個成員新增至清單。 端點採用必要的`listId`路徑引數，以及一或多個包含潛在客戶ID的`id`查詢引數（允許的最大值為300）。

回應包含`result`陣列，由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```http
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

## 從清單中移除

[從清單移除](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE)端點用於從清單中移除一或多個成員。 端點採用必要的`listId`路徑引數，以及一或多個包含潛在客戶ID的`id`查詢引數（允許的最大值為300）。

回應包含`result`陣列，由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```http
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```json
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

## 依清單ID取得銷售機會

[依清單ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET)取得銷售機會端點用於擷取清單的成員。 端點採用必要的`listId`路徑引數，並允許多個可選的查詢引數來指定篩選條件。

`batchSize`引數是用來指定單一呼叫中要傳回的潛在客戶記錄數目。 預設和最大值為300。

`nextPageToken`引數是用來分頁大型結果集。 此引數不會在第一次呼叫中傳遞，但只會在後續分頁呼叫中傳遞。

`fields`引數包含回應中要傳回的欄位名稱清單（以逗號分隔）。 若此要求中未包含`fields`引數，則會傳回下列預設欄位： `email`、`updatedAt`、`createdAt`、`lastName`、`firstName`以及`id`。

回應包含`result`陣列，由JSON物件組成，其中包含要求中指定的潛在客戶欄位。

```http
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

## 清單的成員

清單](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)端點的[成員用於檢視一個或多個潛在客戶是否為清單的成員。 端點採用必要的`listId`路徑引數，以及一或多個包含潛在客戶ID的`id`查詢引數（允許的最大值為300）。

回應包含`result`陣列，由JSON物件組成，要求中指定的每個潛在客戶ID都具有狀態。

```http
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
