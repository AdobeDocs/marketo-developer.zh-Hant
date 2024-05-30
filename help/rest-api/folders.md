---
title: "資料夾"
feature: REST API
description: 「使用Marketo API操控資料夾。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '1008'
ht-degree: 0%

---


# 資料夾

[資料夾端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders)

資料夾是Marketo中的核心組織資產，而所有其他型別的資產至少有一個資料夾作為父級。 此父資料夾可能是純粹為組織的資料夾，也可能是與其他資產型別具有功能關係的方案，也可以是其他資產的父級。 您可以透過API建立、查詢、更新及刪除資料夾，也可以擷取其內容清單。 雖然程式可以透過查詢資料夾API返回，但建立、更新和刪除程式必須透過程式API執行。

## 查詢

查詢資料夾遵循資產的標準查詢型別 [依id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByIdUsingGET)， [依名稱](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET)、和 [瀏覽](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET).

### 依Id

```
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

type引數為必要項，且必須是「資料夾」或「程式」其中之一。  型別會指定對資料夾的查閱是根據資料夾ID或方案ID完成。 對於此端點，結果陣列中只傳回單一記錄。 請注意回應中的folderType引數。 這可能表示許多不同型別的資料夾。 「Marketo活動」資料夾有行銷資料夾或方案的型別，其中可包含許多不同型別的資產，而Design Studio資料夾則有與其可保留的資產型別相對應的型別。 例如，folderType為「電子郵件」的資料夾可能僅包含電子郵件或其他子資料夾，這些子資料夾可能具有folderType為「電子郵件」或「電子郵件範本」。 型別可能包括：

- 電子郵件
- 電子郵件範本
- 登陸頁面
- 登陸頁面範本
- 程式碼片段
- 檔案

### 依名稱

[依名稱查詢](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET) 也允許使用。 依名稱端點查詢的名稱是唯一必要的引數。 Name會針對執行個體中資料夾的名稱欄位執行完全相符的字串，並傳回符合該名稱的每個資料夾的結果。 其中也有選填的「型別」查詢引數，可能是「資料夾」或「方案」、「根」要搜尋的資料夾識別碼，或「工作區」要搜尋的工作區名稱。 如果設定了根引數，則也必須設定型別引數。

```
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

依名稱搜尋時，請務必注意，「行銷活動」和Design Studio都是各自的根資料夾，因此可依名稱擷取，並用於在目的地執行個體中周游資料夾階層的其餘部分。

### 瀏覽

資料夾也可以 [大量擷取](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET). 「root」引數可用來指定要在其中執行查詢的父資料夾，並格式化為內嵌作為查詢引數值的JSON物件。 根有兩個成員：

1. id — 資料夾或程式的ID。
1. 型別 — 資料夾或程式，視要瀏覽的根資料夾型別而定。

如果根資料夾不明，或目的是要擷取指定區域中的所有資料夾，可將根指定為「行銷活動」、「Design Studio」或「潛在客戶資料庫」區域。 您可透過擷取每個專案的ID [依名稱取得資料夾](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET) API，並指定所需區域的名稱。

如同其他大量資產擷取端點，offset和maxReturn是分頁的可選引數。   其他選用引數包括：

- workspace — 要篩選的工作區名稱。
- maxDepth — 資料夾階層中可周遊的層級數目上限。 如果設為0，則只會傳回在root中指定的資料夾。 如果未指定，預設值為2。

```
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

許多資料夾回應結構都可自行解釋，但有幾個欄位值得個別注意。 此 `folderId` 和父欄位是JSON物件，包含資料夾本身的明確ID和型別。 API會將此型別用於查詢、根和父引數，以確保資料夾和程式型別資料夾之間有正確的界線。 `folderType` 反映資料夾的使用情況，資料夾可能是「行銷資料夾」、「方案」、「電子郵件」、「電子郵件範本」、「登陸頁面」、「登陸頁面範本」、「程式碼片段」、「影像」、「區域」或「檔案」的其中之一。  行銷資料夾和方案型別指出它們存在於行銷活動中，並可包含多種型別的資產。 其他型別表示它們可能僅包含該型別的資產、子檔案夾和該型別的範本版本（如果適用）。 型別Zone代表在行銷活動中找到的根層級資料夾。

資料夾的路徑在資料夾樹狀結構中會顯示其階層，類似於Unix樣式的路徑。 路徑中的第一個專案永遠是Marketing Activities或Design Studio。 如果目標例項具有工作區，則路徑中的第二個專案將是擁有工作區的名稱。 此 `url` 欄位會顯示指定執行個體中資產的明確URL。 這不是通用連結，必須透過使用者身分驗證才能正常運作。 `isSystem` 指示資料夾是否為系統資料夾。 如果此項設定為true，則資料夾本身是唯讀的，不過資料夾可以建立為它的子系。

## 建立和更新

[建立資料夾](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/createFolderUsingPOST) 會非常簡單，而且會以application/x-www-form-urlencodedPOST執行，其中包含兩個必要的引數：「name」（名稱）、「string」（字串）和「parent」（父項），以建立資料夾。此資料夾為內嵌JSON物件，包含兩個成員、id和型別：「Folder」（資料夾）或「Program」（視目標資料夾的型別而定）。 也可選擇加入字串「description」，最多2000個字元。

```
POST /rest/asset/v1/folders.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

資料夾的更新會透過個別端點、說明、名稱和 `isArchive` 是供更新的選用引數。 如果 `isArchive` 會因更新而變更，因此在Marketo UI中封存資料夾（如果變更為true）或取消封存資料夾（如果變更為false）。 無法使用此API更新程式。

```
POST /rest/asset/v1/folder/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

如果單一資料夾為空（即不含任何資產或子資料夾），則可針對單一資料夾進行刪除。 如果資料夾屬於Program型別，或將isSystem欄位設定為true，則無法使用此API將其刪除。

```
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
