---
title: 電子郵件範本
feature: REST API
description: 使用Marketo Asset REST API可查詢、建立、更新、複製、刪除、核准和檢查電子郵件範本的相依性。
exl-id: 50bb0047-d6ea-4c94-a900-18c37b17a147
source-git-commit: 0e0a3e5a08e81f349044cbc327d1aba963ab30e4
workflow-type: tm+mt
source-wordcount: '292'
ht-degree: 9%

---

# 電子郵件範本

[電子郵件範本端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates)

電子郵件範本定義建立電子郵件時使用的結構和可重複使用的版面。

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

您可以依資產ID或篩選端點來擷取電子郵件範本中繼資料。

### 依ID

#### 請求

```text
GET /rest/asset/v2/emailtemplate/{id}
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "14f9e#1900ab14001",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "description": "Core responsive template",
      "status": "draft"
    }
  ]
}
```

### 篩選器

篩選器端點支援在工作區中搜尋，以及使用其他查詢引數縮小結果。 `workspaceId`為必要項。

支援的篩選器包括`folderId`、重複`folderIds`、重複`status`、`pageIndex`、`pageSize`、`createdBy`、`createdAtStart`、`createdAtEnd`、`modifiedBy`、`modifiedAtStart`、`modifiedAtEnd`、`name`、`sortKey`、`sortOrder`、`isCreatedByMe`、`isModifiedByMe`、`scriptEngine`、`isValueNonNullable`和`includeArchived`。

#### 請求

```text
GET /rest/asset/v2/emailtemplate/filter?workspaceId=1001&name=Newsletter&pageIndex=0&pageSize=20
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "33c4#1900ab1402f",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

當您需要依名稱尋找範本時使用`name`引數。

## 建立

傳送JSON裝載來建立電子郵件範本。 需要`name`和`appData`。 `appData`必須包含至少`folderId`或`workspaceId`。

### 請求

```text
POST /rest/asset/v2/emailtemplate
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template",
  "description": "Core responsive template",
  "appData": {
    "workspaceId": "1001",
    "folderId": "15",
    "editorType": "emailTemplate"
  },
  "themeId": "42"
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "a99f#1900ab1407e",
  "result": [
    {
      "id": "1022",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

要求內文可能也包含`data`、`editorContext`、`appType`和`status`。

### 建立欄位

`appData`會識別範本的儲存位置及編輯方式。

`themeId`可用於將主題與範本建立關聯（如果適用）。

## 更新

依資產ID更新範本。

### 請求

```text
POST /rest/asset/v2/emailtemplate/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template v2",
  "description": "Updated responsive template",
  "appData": {
    "folderId": "15"
  }
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "cf10#1900ab140b3",
  "result": [
    {
      "id": "1022"
    }
  ]
}
```

## 管理狀態

電子郵件範本使用草稿和已核准的生命週期。 使用狀態轉變端點可核准範本、取消核准、捨棄草稿或建立新草稿。

有效的`action`值為：

- `approve`
- `unapprove`
- `discard`
- `create_draft`

### 請求

```text
POST /rest/asset/v2/emailtemplate/state/transition
Content-Type: application/json
```

### 回應

```json
{
  "contentId": "1022",
  "action": "approve"
}
```

## 原地複製

使用原地複製端點可建立現有範本的復本。

### 請求

```text
POST /rest/asset/v2/emailtemplate/clone
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "1022",
  "newAsset": {
    "name": "Base Newsletter Template Copy",
    "description": "Cloned template"
  }
}
```

## 刪除

依資產ID刪除範本。

### 請求

```text
POST /rest/asset/v2/emailtemplate/{id}/delete
Content-Type: application/json
```

此端點會採用路徑中的範本ID，且不會定義要求內文。

## 使用者

使用`usedby`端點來擷取參考指定範本的資產。

### 請求

```text
POST /rest/asset/v2/emailtemplate/usedby
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "1022",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## 附註

查詢端點會傳回資產的中繼資料。 使用完整回應結構描述的端點參照以及任何特定於環境的屬性。
