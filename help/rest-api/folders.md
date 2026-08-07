---
title: 資料夾
feature: REST API
description: Marketo REST API指南涵蓋建立、更新、刪除、依ID和名稱查詢、使用根、工作區、maxDepth和分頁大量瀏覽的資料夾。
exl-id: 4b55c256-ef0a-42b4-9548-ff8a4106f064
TQID: https://experienceleague.adobe.com/OxCNdy8qW6jwq8u57RF9mqVKPVvH99UmuiOBjFprHCM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: d65b4a73-87a3-4d56-b638-74e74d9939ce
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 792
ht-degree: 1%

---

# 資料夾

[資料夾端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders)

資料夾是Marketo的核心組織資產。 每個其他資產型別至少有一個父級是資料夾或計畫。 資料夾是純組織性的，而方案與其他資產型別具有功能關係，也可以包含資產。

使用資料夾API來建立、查詢、更新和刪除資料夾或擷取其內容。 資料夾查詢可以傳回程式，但您必須使用程式API來建立、更新或刪除程式。

## 查詢

資料夾支援標準資產查詢模式： [依id](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderByIdUsingGET)、[依名稱](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderByNameUsingGET)，以及[瀏覽](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderUsingGET)。

### 依Id

```http
GET /rest/asset/v1/folder/{id}.json?type=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1241b#14e21ca814a",
    "result": [
        {
            "name": "Social Media",
            "description": null,
            "createdAt": "2011-03-04T17:01:32Z+0000",
            "updatedAt": "2011-03-04T17:01:32Z+0000",
            "url": null,
            "folderId": {
                "id": 341,
                "type": "Folder"
            },
            "folderType": "Email",
            "parent": {
                "id": 11,
                "type": "Folder"
            },
            "path": "/Design Studio/Default/Emails/Social Media",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 341
        }
    ]
}
```

`type`引數為必要項，必須是`Folder`或`Program`。 這會決定端點要查詢資料夾ID還是程式ID。 端點會傳回結果陣列中的一個記錄。

回應`folderType`會識別資料夾可以包含的內容。 行銷活動資料夾具有行銷資料夾或方案的型別，並且可以包含多個資產型別。 Design Studio資料夾的型別與其可包含的資產相對應。 例如，電子郵件資料夾可以包含資料夾型別為「電子郵件」或「電子郵件範本」的電子郵件和子資料夾。

資料夾型別包括：

- 電子郵件
- 電子郵件範本
- 登陸頁面
- 登陸頁面範本
- 程式碼片段
- 檔案

### 依名稱

依名稱[&#128279;](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderByNameUsingGET)的查詢端點需要`name`，它會針對資料夾名稱執行完全相符的專案，並傳回每個相符的資料夾。

端點也接受以下選用引數：

- `type`：資料夾型別，`Folder`或`Program`。
- `root`：要搜尋的資料夾識別碼。 如果您設定`root`，您也必須設定`type`。
- `workspace`：要搜尋的工作區名稱。

```http
GET /rest/asset/v1/folder/byName.json?name=Test%2010%20-%20deverly
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "19#14e1f2f3688",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Marketing Programs - deverly/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

行銷活動和Design Studio是根資料夾。 依名稱擷取根，然後使用它周遊目標執行個體中的資料夾階層。

### 瀏覽

您也可以[大量擷取資料夾](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderUsingGET)。 使用`root`引數指定要查詢的父資料夾。 將`root`傳遞為具有兩個成員的內嵌JSON物件：

1. `id`：資料夾或程式的識別碼。
1. `type`： `Folder`或`Program`，視根資料夾型別而定。

如果您不知道根資料夾或想擷取某個區域中的所有資料夾，請使用Marketing Activities、Design Studio或Lead Database根。 將區域名稱傳遞至[依名稱取得資料夾](https://developer.adobe.com/marketo-apis/api/asset#operation/getFolderByNameUsingGET) API，以擷取根ID。

如同其他大量資產擷取端點，請使用選用的`offset`和`maxReturn`引數進行分頁。 其他選用引數包括：

- `workSpace`：篩選依據的工作區名稱。
- `maxDepth`：資料夾階層中要周遊的層級數目上限。 值為0隻會傳回`root`所指定的資料夾。 預設值為2。

```http
GET /rest/asset/v1/folders.json?root={"id":14,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "9bd8#14e1f49047c",
    "result": [
        {
            "name": "Marketing Activities",
            "description": "Root node for the Marketing Activities app area",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 14,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": null,
            "path": "/Marketing Activities",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 14
        },
        {
            "name": "Default",
            "description": "Root node of the Marketing activities Default",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 15,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": {
                "id": 14,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 15
        },
        {
            "name": "Archive",
            "description": "",
            "createdAt": "2010-03-27T18:28:17Z+0000",
            "updatedAt": "2010-03-27T18:28:17Z+0000",
            "url": "https://app-abm.marketo.com/#MF157A1",
            "folderId": {
                "id": 310,
                "type": "Folder"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default/Archive",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 310
        }
    ]
}
```

## 回應結構

`folderId`和`parent`欄位是包含資料夾識別碼和型別的JSON物件。 API在查詢、`root`和`parent`引數中使用此型別來區分資料夾和程式資料夾型別。

`folderType`欄位說明資料夾的使用方式。 其值可為行銷資料夾、方案、電子郵件、電子郵件範本、登陸頁面、登陸頁面範本、代碼片段、影像、區域或檔案。 行銷活動中有行銷資料夾和方案，並且可以包含多種資產型別。 其他檔案夾型別僅包含對應的資產型別、子檔案夾以及該資產型別的範本版本（如適用）。 Zone代表行銷活動中的根層級資料夾。

資料夾`path`將其階層顯示為Unix樣式路徑。 第一個專案一律為Marketing Activities或Design Studio。 如果例證具有工作區，則第二個專案是擁有的工作區名稱。

`url`欄位包含指定執行個體的資產URL。 這不是通用連結，需要使用者驗證。 `isSystem`欄位指出資料夾是否為唯讀系統資料夾。 您可以在系統資料夾下建立子資料夾。

## 建立和更新

若要[建立資料夾](https://developer.adobe.com/marketo-apis/api/asset#operation/createFolderUsingPOST)，請使用下列引數傳送`application/x-www-form-urlencoded` POST要求：

- `name`：包含資料夾名稱的必要字串。
- `parent`：必要的內嵌JSON物件包含`id`和`type`。 型別是`Folder`或`Program`，視父項而定。
- `description`：最多2000個字元的可選字串。

```http
POST /rest/asset/v1/folders.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
parent={"id":416,"type":"Folder"}&name=Test 10 - deverly&description=This is a test
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "111be#14e1f193e31",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

使用更新端點來變更選用的`description`、`name`或`isArchive`引數。 將`isArchive`設定為`true`會在Marketo UI中封存資料夾。 將其設定為`false`會從封存中移除資料夾。

無法使用此API更新程式。

```http
POST /rest/asset/v1/folder/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```sql
type=Folder&description=This is a test (update 01)
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "c5b2#14e1f3954bf",
    "result": [
        {
            "name": "Learning - deverly",
            "description": "This is a test (update 01)",
            "createdAt": "2015-03-17T00:17:02Z+0000",
            "updatedAt": "2015-06-23T07:02:07Z+0000",
            "url": "https://app-abm.marketo.com/#MF1044A1",
            "folderId": {
                "id": 407,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Learning - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 407
        }
    ]
}
```

### 刪除

您只能刪除不含任何資產或子資料夾的單一資料夾。 無法使用此API來刪除`isSystem`欄位為`true`的方案或資料夾。

```http
POST /rest/asset/v1/folder/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4180#14e1f3fc017",
    "result": [
        {
            "id": 453
        }
    ]
}
```
