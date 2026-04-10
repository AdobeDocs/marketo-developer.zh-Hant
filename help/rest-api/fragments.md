---
title: 片段
feature: REST API
description: 使用Marketo Asset REST API來查詢、建立、更新、複製、刪除、核准和檢查片段的相依性。
exl-id: 9dd532d1-1dd7-4581-86dd-1943fab66cbb
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '272'
ht-degree: 9%

---

# 片段

片段是可重複使用的內容資產，可供其他資產（例如電子郵件）參考。

## 存取權

本文所述的端點需要存取權杖：

```text
?access_token=<access_token>
```

要求也需要`x-app-type`標頭：

```text
x-app-type: <app-type>
```

## 查詢

您可以依資產ID或使用篩選器端點來擷取片段中繼資料。

### 依ID

#### 請求

```http
GET /rest/asset/v2/fragment/{id}
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "fa0f#1900ab15001",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "description": "Reusable hero block",
      "status": "approved"
    }
  ]
}
```

### 篩選器

篩選器端點支援在工作區中搜尋，以及使用其他查詢引數縮小結果。 `workspaceId`為必要項。

todo：讓此為表格
支援的篩選器包括`folderId`、重複`folderIds`、重複`status`、`pageIndex`、`pageSize`、`createdBy`、`createdAtStart`、`createdAtEnd`、`modifiedBy`、`modifiedAtStart`、`modifiedAtEnd`、`name`、`fragmentType`、`sortKey`、`sortOrder`、`isCreatedByMe`、`isModifiedByMe`、`scriptEngine`、`isValueNonNullable`和`includeArchived`。

#### 請求

```http
GET /rest/asset/v2/fragment/filter?workspaceId=1001&fragmentType=email&pageIndex=0&pageSize=20
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "f9cc#1900ab1504a",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "status": "approved"
    }
  ]
}
```

當您需要依名稱尋找片段時使用`name`引數。

## 建立

傳送JSON裝載來建立片段。 需要`name`、`appData`和`settings`。 `settings`必須包含`fragmentType`和`supportedChannels`。

### 請求

```http
POST /rest/asset/v2/fragment
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment",
  "description": "Reusable hero block",
  "appData": {
    "workspaceId": "1001",
    "folderId": "395",
    "editorType": "fragment"
  },
  "settings": {
    "fragmentType": "email",
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "bd57#1900ab1509d",
  "result": [
    {
      "id": "13",
      "name": "Hero Banner Fragment",
      "status": "draft"
    }
  ]
}
```

要求內文可能也包含`data`、`editorContext`、`themeId`、`appType`和`status`。

### 建立欄位

* `appData`會識別片段的儲存位置及其編輯方式。
* `settings.fragmentType`會識別片段類別，例如電子郵件片段。
* `settings.fragmentSubType`可用來進一步定義片段格式。
* `settings.supportedChannels`列出可使用片段的管道。

## 更新

依資產ID更新片段。

### 請求

```http
POST /rest/asset/v2/fragment/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment v2",
  "description": "Updated reusable hero block",
  "settings": {
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "73d9#1900ab150f0",
  "result": [
    {
      "id": "13"
    }
  ]
}
```

## 管理狀態

片段使用草稿和已核准的生命週期。 使用狀態轉變端點來核准片段、取消核准、捨棄草稿或建立新草稿。

有效的`action`值為：

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### 請求

```http
POST /rest/asset/v2/fragment/state/transition
Content-Type: application/json
```

### 回應

```json
{
  "contentId": "13",
  "action": "approve"
}
```

## 原地複製

使用原地複製端點可建立現有片段的復本。

### 請求

```http
POST /rest/asset/v2/fragment/clone
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "13",
  "newAsset": {
    "name": "Hero Banner Fragment Copy",
    "description": "Cloned fragment"
  }
}
```

## 刪除

依資產ID刪除片段。

### 請求

```http
POST /rest/asset/v2/fragment/{id}/delete
Content-Type: application/json
```

此端點會採用路徑中的片段ID，且不會定義請求內文。

## 使用者

使用`usedby`端點來擷取參考指定片段的資產。

### 請求

```http
POST /rest/asset/v2/fragment/usedby
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "13",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```
