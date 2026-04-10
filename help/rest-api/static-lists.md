---
title: 靜態清單
feature: REST API, Static Lists
description: 使用Marketo REST API來查詢、建立、更新和刪除靜態清單，其端點包括ID、名稱和瀏覽、資料夾範圍設定、分頁和日期篩選器。
exl-id: 20679fd2-fae2-473e-84bc-cb4fdf2f5151
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '496'
ht-degree: 1%

---

# 靜態清單

[靜態清單端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

Marketo提供了一組REST API，可用於對靜態清單執行CRUD操作。 這些API遵循資產API的標準介面模式，提供查詢、建立、更新和刪除選項。

有關清單成員的Lead資料庫作業，請參閱[清單成員資格](list-membership.md)。

## 查詢

查詢靜態清單會依循[的標準查詢型別（識別碼](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)、[名稱](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET)和[瀏覽](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET)）。

### 依Id

[依ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)的查詢，以單一靜態清單`id`作為路徑引數，並傳回單一靜態清單記錄。

```http
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

[依名稱查詢](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET)以靜態清單`name`作為引數，並傳回單一靜態清單記錄。 會針對執行個體中的所有靜態清單名稱執行完全相符的字串，並傳回符合該名稱的靜態清單結果。

```http
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

靜態清單也可以以批次](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET)擷取[。 `folder`引數可用來指定要在其中執行查詢的父資料夾，並格式化為包含`id`和`type`的JSON物件。 如同其他大量資產擷取端點，`offset`和`maxReturn`是可用於分頁的選用引數。 `earliestUpdatedAt`和`latestUpdatedAt`引數可讓您設定低日期時間與高日期時間浮水印，以傳回在指定範圍內建立或更新之靜態清單。 日期時間值必須是有效的ISO-8601字串，而且不應該包含毫秒。

```http
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

[建立靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/createStaticListUsingPOST)是使用`application/x-www-form-urlencoded` POST及兩個必要引數執行。 `folder`引數是用來指定要在其中建立靜態清單的父資料夾，且會格式化為包含`id`和`type`的JSON物件。 `name`引數是用來命名靜態清單，而且必須是唯一的。 可選擇使用`description`引數描述靜態清單。

```http
POST /rest/asset/v1/staticLists.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[更新靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/updateStaticListUsingPOST)是透過具有兩個選擇性引數的個別端點完成的。 `description`引數可用來更新靜態清單描述。 `name`引數可用來更新靜態清單名稱，而且必須是唯一的。

```http
POST /rest/asset/v1/staticList/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

## 刪除

[刪除靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST)會以單一靜態清單`id`作為路徑引數。 無法刪除匯入或匯出操作正在使用的靜態清單，或其他資產正在使用的靜態清單。

```http
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
