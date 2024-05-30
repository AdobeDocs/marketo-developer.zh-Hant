---
title: "Token"
feature: REST API, Tokens
description: 「在Marketo中管理Token」。
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
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

[依資料夾ID取得權杖](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/getTokensByFolderIdUsingGET) 需要 `id` 作為「程式」或「資料夾」型別的路徑引數。 此型別由 `folderType` 引數。

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

此 [建立Token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/addTokenTOFolderUsingPOST) 端點會建立權杖，或如果已存在，則使用已提交的值來更新。 代號會在資料夾或方案的內容中建立。 必要的 `id` path引數是與token建立關聯的資料夾識別碼。 此 `name`， `type`， `value`、和 `folderType` 是Token的所有必要引數。 資料以x-www-form-urlencodedPOST傳遞，而非以JSON格式傳遞。 此 `name` 語彙基元的欄位不可超過50個字元。

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

[依名稱刪除Token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/deleteTokenByNameUsingPOST) 將id作為Program或Folder型別的路徑引數。 此型別由 `folderType` 引數。 系統會根據其上層資料夾( `name`，以及 `type` 權杖的URL，每個權杖都是必要的。 資料以x-www-form-urlencodedPOST傳遞，而非以JSON格式傳遞。

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
