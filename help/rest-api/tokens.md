---
title: 權杖
feature: REST API, Tokens
description: 使用Asset REST API管理Marketo的「我的Token」 。 檢視支援的資料型別、依資料夾或程式取得、透過表單編碼POST建立或更新，以及依名稱刪除。
exl-id: 4f8d87d7-ba2a-4c90-8b39-4d20679d404a
TQID: https://experienceleague.adobe.com/uqOpu2vDuiQiZhILKuxZJQGadd0K14zwIaAdmNfK1-I
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 284
ht-degree: 3%

---

# 權杖

[權杖端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens)

代號是Marketo在執行階段以其他資料取代的字串。 API只能編輯「我的權杖」，這些權杖是資料夾或程式本機的子權杖。

使用Token API來讀取、建立、更新和刪除My Token。

## 資料類型

可使用下列資料型別建立代號：

| 類型 | 說明 |
| --- | --- |
| 日期 | 日期值，格式為&quot;yyyy-MM-dd&quot; |
| 數字 | 整數或浮點數 |
| RTF文字 | HTML字串 |
| 分數 | 帶正負號的32位元整數 |
| sfdc行銷活動 | 用於Salesforce行銷活動管理整合 |
| 文字 | 文字字串 |

建立Token時，API僅支援這些資料型別。

## 查詢

[依資料夾識別碼取得權杖](https://developer.adobe.com/marketo-apis/api/asset#operation/getTokensByFolderIdUsingGET)會以程式或資料夾識別碼作為路徑引數。 使用`folderType`引數指定型別。

```http
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

[建立Token](https://developer.adobe.com/marketo-apis/api/asset#operation/addTokenTOFolderUsingPOST)端點會建立Token或使用提交的值更新現有的Token。 Token屬於資料夾或程式。

`id`路徑引數可識別父資料夾。 需要`name`、`type`、`value`和`folderType`引數。 以POST `x-www-form-urlencoded`傳遞資料，而非以JSON傳遞。 語彙基元`name`不能超過50個字元。

```http
POST /rest/asset/v1/folder/{id}/tokens.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[依名稱刪除Token](https://developer.adobe.com/marketo-apis/api/asset#operation/deleteTokenByNameUsingPOST)會將程式或資料夾的ID當成路徑引數。 使用`folderType`指定型別。

需要父資料夾、權杖`name`和權杖`type`。 以POST `x-www-form-urlencoded`傳遞資料，而非以JSON傳遞。

```http
POST /rest/asset/v1/folder/{id}/tokens/delete.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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
