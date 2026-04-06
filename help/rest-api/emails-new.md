---
title: 電子郵件
feature: REST API
description: 使用Marketo Asset REST API可查詢、建立、更新、複製、刪除、核准和檢查電子郵件資產的相依性。
exl-id: b41a3ae5-2b25-4103-84b4-320fc2c44bd6
source-git-commit: 0e0a3e5a08e81f349044cbc327d1aba963ab30e4
workflow-type: tm+mt
source-wordcount: '497'
ht-degree: 5%

---

# 電子郵件

[電子郵件端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails_New)

電子郵件是定義訊息中繼資料、內容設定、設定和核准狀態的資產記錄。

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

您可以依資產`id`或使用篩選器端點擷取電子郵件中繼資料。

### 依ID

#### 請求

```text
GET /rest/asset/v2/email/{id}
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7b3c#1900ab12cd3",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "description": "Main announcement email",
      "status": "draft"
    }
  ]
}
```

### 篩選器

篩選器端點支援在工作區中搜尋，以及使用其他查詢引數縮小結果。

`workspaceId`為必要項。

支援的查詢引數：

| 參數 | 說明 |
| - | - |
| `workspaceId` | 用來限定篩選要求範圍所需的工作區識別碼。 |
| `folderId` | 將結果篩選至單一資料夾。 |
| `folderIds` | 將結果篩選到多個資料夾的重複引數。 |
| `status` | 依一或多個電子郵件狀態篩選的重複引數。 |
| `pageIndex` | 分頁結果的頁面索引從零開始。 |
| `pageSize` | 每頁要傳回的結果數上限。 |
| `createdBy` | 依建立的使用者篩選結果。 |
| `createdAtStart` | 傳回在此時間戳記之上或之後建立的資產。 |
| `createdAtEnd` | 傳回在此時間戳記之上或之前建立的資產。 |
| `modifiedBy` | 依上次修改的使用者篩選結果。 |
| `modifiedAtStart` | 傳回在此時間戳記當天或之後修改的資產。 |
| `modifiedAtEnd` | 傳回在此時間戳記當天或之前修改的資產。 |
| `name` | 依電子郵件名稱篩選結果。 |
| `sortKey` | 選取用來排序結果的欄位。 |
| `sortOrder` | 設定排序方向。 |
| `isCreatedByMe` | 將結果限製為目前使用者建立的資產。 |
| `isModifiedByMe` | 將結果限製為目前使用者上次修改的資產。 |
| `templateId` | 依範本識別碼篩選結果。 |
| `scriptEngine` | 依指令碼引擎設定篩選結果。 |
| `isValueNonNullable` | 根據值是否為不可為空的進行篩選。 |
| `includeArchived` | 在結果中包含已封存的資產。 |

#### 請求

```text
GET /rest/asset/v2/email/filter?workspaceId=1001&name=Spring%20Launch&status=draft&status=approved&pageIndex=0&pageSize=20
```

#### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "5dd1#1900ab13011",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

當您需要依名稱尋找電子郵件時使用`name`引數。

## 建立

傳送JSON裝載以建立電子郵件。 需要`name`、`appData`和`headers`。 `headers.subject`為必要項，且`appData`必須至少包含`folderId`、`workspaceId`或`programId`其中之一。

### 請求

```text
POST /rest/asset/v2/email
Content-Type: application/json
```

```json
{
  "name": "Spring Launch Email",
  "description": "Main announcement email",
  "appData": {
    "workspaceId": "1001",
    "folderId": "2002",
    "editorType": "email"
  },
  "headers": {
    "subject": "Introducing the Spring Launch",
    "fromName": "Marketing Team",
    "fromEmail": "marketing@example.com",
    "replyEmail": "reply@example.com",
    "preheader": "See what changed this quarter",
    "ccEmails": [
      "owner@example.com"
    ]
  },
  "settings": {
    "isOperational": false,
    "isTextOnly": false,
    "isWebPageView": true,
    "enableUrlTracking": true
  },
  "templateId": "3003"
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "238c#1900ab1313f",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

要求內文可能也包含`data`、`editorContext`、`themeId`、`appType`和`status`。

### 建立欄位

* `appData`會識別電子郵件的建立位置及編輯方式。
* `headers`包含郵件標頭值，包括主旨、寄件者、回覆地址、預先標頭，以及選用的副本收件者。
* `settings`控制操作和呈現行為，例如純文字模式、網頁檢視和URL追蹤。
* `templateId`會識別建立電子郵件時使用的電子郵件範本。

## 更新

依資產ID更新電子郵件。 要求內文使用`UpdateEmailRequest`結構描述，而且所有屬性都是選擇性的，因此您只能傳送您要變更的欄位。

### 請求

```text
POST /rest/asset/v2/email/{id}/update
Content-Type: application/json
```

```json
{
  "description": "Updated announcement email",
  "headers": {
    "subject": "Spring Launch Is Live",
    "preheader": "Read the latest release notes"
  },
  "settings": {
    "enableUrlTracking": true,
    "isWebPageView": true
  }
}
```

### 回應

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "9fd3#1900ab13210",
  "result": [
    {
      "id": "1017"
    }
  ]
}
```

## 管理狀態

電子郵件使用草稿和核准的生命週期。 使用狀態轉換端點來核准電子郵件、取消核准、捨棄草稿或建立新草稿。

有效的`action`值為：

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### 請求

```text
POST /rest/asset/v2/email/state/transition
Content-Type: application/json
```

### 回應

```json
{
  "contentId": "1017",
  "action": "approve"
}
```

## 原地複製

使用複製端點可建立現有電子郵件的復本。

### 請求

```text
POST /rest/asset/v2/email/clone
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "1017",
  "newAsset": {
    "name": "Spring Launch Email Copy",
    "description": "Cloned from Spring Launch Email"
  }
}
```

## 刪除

依資產ID刪除電子郵件。

### 請求

```text
POST /rest/asset/v2/email/{id}/delete
Content-Type: application/json
```

此端點取得路徑中的資產`id`，且未定義要求內文。

## 使用者

使用`usedby`端點來擷取參考指定電子郵件的資產。

### 請求

```text
POST /rest/asset/v2/email/usedby
Content-Type: application/json
```

### 回應

```json
{
  "assetId": "1017",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## 附註

查詢端點會傳回資產的中繼資料。 將端點參考用於可用欄位的完整結構描述和任何特定於環境的屬性。
