---
title: 清單成員資格（靜態清單）
feature: REST API, Static Lists
description: 使用Marketo銷售機會資料庫REST API將銷售機會新增至靜態清單、移除銷售機會、擷取清單成員，以及檢查清單成員資格。
exl-id: b8f74bcf-834a-44db-81fd-621048afeba4
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: '427'
ht-degree: 5%

---

# 清單成員資格（靜態清單）

[列出成員資格端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists)

List Membership API提供管理靜態清單成員的Lead Database端點。 使用這些端點可以：

- 將潛在客戶新增至清單。
- 從清單中移除銷售機會。
- 擷取清單的成員。
- 判斷潛在客戶是否為清單的成員。

## 端點

| 端點 | 方法 | 路徑 |
| --- | --- | --- |
| 新增至清單 | POST | `/rest/v1/lists/{listId}/leads.json` |
| 從清單中移除 | DELETE | `/rest/v1/lists/{listId}/leads.json` |
| 依清單ID取得銷售機會 | GET | `/rest/v1/lists/{listId}/leads.json` |
| 清單的成員 | GET | `/rest/v1/lists/{listId}/leads/ismember.json` |

## 新增至清單

使用[新增至清單](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/addLeadsToListUsingPOST)端點新增一或多個成員至清單。 傳遞必要的`listId`路徑引數以及一或多個包含潛在客戶ID的`id`查詢引數。 潛在客戶ID的最大數量為300。

回應包含`result`陣列，其狀態為要求中的每個潛在客戶ID。

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

使用[從清單移除](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE)端點從清單中移除一或多個成員。 傳遞必要的`listId`路徑引數以及一或多個包含潛在客戶ID的`id`查詢引數。 潛在客戶ID的最大數量為300。

回應包含`result`陣列，其狀態為要求中的每個潛在客戶ID。

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

使用[依清單識別碼](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/getLeadsByListIdUsingGET)取得銷售機會，以擷取清單的成員。 傳遞必要的`listId`路徑引數。 您也可以傳遞選用的查詢引數以指定篩選條件。

選用的查詢引數包括：

- `batchSize`：指定在一次呼叫中要傳回的潛在客戶記錄數目。 預設值和最大值為300。
- `nextPageToken`：分頁瀏覽大型結果集。 在第一次呼叫時省略此引數，並將其納入後續呼叫。
- `fields`：指定要傳回的欄位名稱清單（以逗號分隔）。 若您省略此引數，回應會包含`email`、`updatedAt`、`createdAt`、`lastName`、`firstName`和`id`。

回應包含`result`陣列，且要求中指定了潛在客戶欄位。

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

使用[Member of List](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)端點來判斷一或多個潛在客戶是否為清單的成員。 傳遞必要的`listId`路徑引數以及一或多個包含潛在客戶ID的`id`查詢引數。 潛在客戶ID的最大數量為300。

回應包含`result`陣列，其狀態為要求中的每個潛在客戶ID。

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
