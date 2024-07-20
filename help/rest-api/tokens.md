---
title: Token
feature: REST API, Tokens
description: 在Marketo中管理Token。
exl-id: 4f8d87d7-ba2a-4c90-8b39-4d20679d404a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '297'
ht-degree: 2%

---

# Token

[權杖端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens)

Marketo中的Token是類似短程式碼的特殊字串，在執行階段會由單獨的資料片段取代。 Marketo中有多種型別的代號可用，但只有「我的代號」可以透過API編輯。 我的Token是位於特定資料夾或程式本機的子Token。 Token可透過API讀取、建立和刪除。

## 資料類型

可使用下列資料型別建立代號：

| 類型 | 說明 |
|---------------|----------------------------------------------------|
| 日期 | 日期值，格式為&quot;yyyy-MM-dd&quot; |
| 數字 | 整數或浮點數 |
| RTF文字 | HTML字串 |
| 分數 | 帶正負號的32位元整數 |
| sfdc行銷活動 | 用於Salesforce行銷活動管理整合 |
| 文字 | 文字字串 |


透過API建立Token時，只有這些資料型別可以使用。

## 查詢

[依資料夾識別碼取得權杖](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/getTokensByFolderIdUsingGET)會將`id`當成程式或資料夾型別的路徑引數。 此型別由`folderType`引數指定。

```curl
GET /rest/asset/v1/folder/{id}/tokens.json?folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4fbe#14e27fc9bbf",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "AprilFool - deverly",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## 建立和更新

[建立Token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/addTokenTOFolderUsingPOST)端點會建立Token，或如果存在，會以提交的值更新它們。 代號會在資料夾或方案的內容中建立。 必要的`id`路徑引數為與權杖關聯的資料夾識別碼。 `name`、`type`、`value`和`folderType`都是權杖的必要引數。 資料以x-www-form-urlencodedPOST傳遞，而非以JSON格式傳遞。 權杖的`name`欄位不可超過50個字元。

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&type=date&value=2015-04-01&folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "e3c2#14e280db5dc",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "April Fools",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## 刪除

[依名稱刪除Token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/deleteTokenByNameUsingPOST)會將ID當做Program或Folder型別的路徑引數。 此型別由`folderType`引數指定。 系統會根據其父資料夾、`name`和權杖的`type`刪除權杖，每個都是必要專案。 資料以x-www-form-urlencodedPOST傳遞，而非以JSON格式傳遞。

```
POST /rest/asset/v1/folder/{id}/tokens/delete.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=AprilFool - deverly&type=date&folderType=Program
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "12ed2#14e2800f89c",
    "result": [
        {
            "id": 416
        }
    ]
}
```
