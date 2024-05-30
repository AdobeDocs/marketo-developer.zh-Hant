---
title: "User Management"
feature: REST API
description: 「對使用者記錄執行CRUD作業。」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '1155'
ht-degree: 0%

---


# User Management

[使用者管理端點參考](https://developer.adobe.com/marketo-apis/api/user/)

Marketo提供了一組「使用者管理」端點，可讓您對Marketo中的使用者記錄執行CRUD操作。 使用者是透過傳送邀請給使用者來建立，接著使用者會設定密碼，並首次獲得Marketo存取權。

不同於其他Marketo REST API，使用「使用者管理API」時：

- 您必須使用HTTP標頭方法來傳送存取權杖以進行驗證。 您無法傳遞存取權杖作為查詢字串引數。 有關驗證的更多資訊為 [此處](authentication.md).
- 在建立使用者角色時，您必須從兩個不同的群組選取一個角色許可權 [自訂服務](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api) 對於REST API：
   1. 來自的「存取使用者」許可權 [存取管理員](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions) 群組
   1. 從存取使用者管理Api [Access API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions) 群組
- 回應主體不包含「success」布林值屬性，指出呼叫的成功或失敗。 您必須改為評估HTTP回應狀態代碼。 如果呼叫成功，則會傳回200狀態代碼。 如果呼叫失敗，會傳回非200層級的狀態代碼，且回應內文包含標準「錯誤」陣列，其中包含錯誤代碼和描述性錯誤訊息。
- 日期時間字串的格式為&quot;yyyyMdd&#39;T&#39;HH:mm:ss.SSS&#39;t&#39;+|-hhmm&quot;。 這適用於下列屬性： createdAt、updatedAt、expiresAt。
- 使用者管理API端點未像其他端點一樣以「/rest」為前置詞。

## 查詢

使用者管理的查詢支援包括擷取所有使用者、角色和工作區的功能。 此外，您也可以依使用者ID擷取單一使用者記錄，或依使用者ID擷取角色/文字空間記錄。

### 使用者（依ID）

此 [依ID取得使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserUsingGET) 端點需要單一 `userid` path引數並傳回已接受其邀請之使用者的單一使用者記錄。

```
GET /userservice/management/v1/users/{userid}/user.json
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "Jamie",
  "lastName": "Lannister",
  "emailAddress": "jamie@lannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2020-12-31T08:00:00.000t+0000",
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

### 依識別碼邀請的使用者

此 [依Id取得受邀使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getInvitedUserUsingGET) 端點需要單一 `userid` path引數並傳回「擱置中」使用者的單一使用者記錄（尚未接受其邀請）。

```
GET /userservice/management/v1/users/{userid}/invite.json
```

```json
{
    "id": 25112,
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@lannister.com",
    "userId": "jamie@lannister.com",
    "subscriptionId": 3381,
    "status": "pending",
    "expiresAt": "20200807T20:49:54.0t+0000",
    "createdAt": "20200731T20:49:54.0t+0000",
    "updatedAt": "20200731T20:49:54.0t+0000"
}
```

### 依Id區分的角色和工作區

此 [依Id取得角色和工作區](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserRolesAndWorkspacesUsingGET) 端點需要單一 `userid` path引數並傳回使用者角色和工作區記錄清單。 回應包含一個物件的陣列，其中含有指定使用者的角色和工作區ID與名稱。

```
GET /userservice/management/v1/users/{userid}/roles.json
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

### 瀏覽使用者

此 [取得使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUsersUsingGET) 端點會傳回所有使用者記錄的清單。 選填 `pageSize` parameter是整數，指定要傳回的最大專案數。 預設值為20。 最大值為200。 選填 `pageOffset` parameter是一個整數，它指定從何處開始擷取專案。 可搭配使用 `pageSize`. 預設值為0。

```
GET /userservice/management/v1/users/allusers.json
```

```json
[
  {
    "userid": "jamie@lannister.com",
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@houselannister.com",
    "id": 6785,
    "apiOnly": false
  },
  {
    "userid": "jeoffery@housebaratheon.com",
    "firstName": "Jeoffery",
    "lastName": "Baratheon",
    "emailAddress": "jeoffery@housebaratheon.com",
    "id": 7718,
    "apiOnly": false
  },
  {
    "userid": "rickon@housestark.com",
    "firstName": "Rickon",
    "lastName": "Stark",
    "emailAddress": "rickon@housestark.com",
    "id": 8612,
    "apiOnly": false
  }
]
```

### 瀏覽角色

此 [取得角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getRolesUsingGET) 端點會傳回所有角色記錄的清單。

```
GET /userservice/management/v1/users/roles.json
```

```json
[
    {
        "id": 1,
        "name": "Admin",
        "description": "All permissions",
        "type": "system",
        "hidden": false,
        "onlyAllZones": true,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 2,
        "name": "Standard User",
        "description": "All permissions except Admin",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 24,
        "name": "RTP Launcher",
        "description": "Role required for launcher in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 25,
        "name": "RTP Editor",
        "description": "Role required for editor in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 101,
        "name": "Analytics User",
        "description": "Has access to Analytics",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 102,
        "name": "Marketing User",
        "description": "All permissions except Admin",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 103,
        "name": "Web Designer",
        "description": "Has access to Design Studio except approval permission",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    }
]
```

### 瀏覽工作區

此 [取得工作區](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getWorkspacesUsingGET) 端點會傳回所有工作區記錄的清單。

```
GET /userservice/management/v1/users/workspaces.json
```

```json
[
  {
    "id": 1,
    "name": "Default",
    "description": "Initial workspace for Marketing Activities, Design Studio, and so on.",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20160910T23:08:05.0t+0000",
    "updatedAt": "20160910T23:08:05.0t+0000"
  },
  {
    "id": 1008,
    "name": "World",
    "description": "",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20181119T21:59:36.0t+0000",
    "updatedAt": "20181119T21:59:36.0t+0000"
  },
  {
    "id": 1009,
    "name": "Reproduction - US English - All Leads",
    "description": "A Workspace for recreating customer-reported problems.",
    "globalViz": 1,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190129T23:36:37.0t+0000",
    "updatedAt": "20190129T23:36:37.0t+0000"
  },
  {
    "id": 1010,
    "name": "US",
    "description": "United States - Qualified Leads",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190322T15:55:40.0t+0000",
    "updatedAt": "20190322T15:55:40.0t+0000"
  }
]
```

## 邀請使用者

開啟 [Adobe IMS整合式訂閱](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)，此端點支援邀請 [僅限API的使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) 僅限。 邀請 [標準使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)，使用 [Adobe使用者管理API](https://developer.adobe.com/umapi/) 而非。

此 [邀請使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/inviteUserUsingPOST) 端點會傳送「歡迎使用Marketo」電子郵件邀請給新使用者。 電子郵件內文包含「登入Marketo」連結，可讓使用者首次存取Marketo。 若要接受邀請，電子郵件收件者請按一下「登入Marketo」連結、建立密碼，然後取得Marketo的存取權。 在接受程式完成之前，邀請處於「擱置中」狀態，使用者記錄可能無法編輯。 未決的邀請會在傳送七天後過期。 有關管理使用者的更多資訊可以找到 [此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users).

引數會以application/json格式傳遞至要求內文。

需要下列引數：  `emailAddress`， `firstName`， `lastName, userRoleWorkspaces`. 此 `userRoleWorkspaces` parameter是一個物件陣列，包含 `accessRoleId` 和 `workspaceId` 屬性。

此 `userid` parameter是用於使用者登入目的的唯一使用者識別碼字串值，且必須格式化為電子郵件地址。 如果未在請求中提供，則的值 `userid` 預設為中提供的值 `emailAddress` 引數。

布林值 `apiOnly` 引數會指定使用者是否為 [僅限API的使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user). 此 `expiresAt` 引數指定使用者登入到期的時間，並使用W3C ISO-8601格式（不含毫秒）格式化。 若未於要求中提供，表示使用者永不過期。 此 `reason` parameter是說明使用者邀請原因的字串。

如果成功，端點會傳回「true」值，否則會傳回錯誤訊息。

```
POST /userservice/management/v1/users/invite.json
```

```
Content-Type: application/json
```

```json
{
  "emailAddress": "daenerys@housetargaryen.com",
  "firstName": "Daenerys",
  "lastName": "Targaryen",
  "expiresAt": "2020-12-31T23:59:59-05:00",
  "reason": "Keeper of dragons",
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "workspaceId": 0
    }
  ]
}
```

```
true
```

以下是傳送給新使用者的「歡迎使用Marketo」電子郵件邀請範例。 電子郵件主旨列為「Marketo登入資訊」，寄件者是與 [REST API自訂服務](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api)，而收件者會透過firstName、lastName和emailAddress引數指定。

![邀請使用者電子郵件](assets/invite-user-email.png)

使用者透過輸入兩次密碼並按一下「建立密碼」按鈕來接受電子郵件邀請。 之後，她便第一次獲得Marketo的存取權。

## 更新使用者

更新對使用者的支援包括更新使用者屬性或刪除使用者的功能。 只有已接受其邀請的使用者才能更新。 屬性會以application/json格式傳遞為請求內文的引數。

### 更新使用者屬性

開啟 [Adobe IMS整合式訂閱](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)，此端點支援更新屬性 [僅限API的使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) 僅限。 更新屬性 [標準使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)，使用 [Adobe使用者管理API](https://developer.adobe.com/umapi/) 而非。

此 [更新使用者屬性](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/updateUserAttributeUsingPOST) 端點需要單一 `userid` path引數並傳回單一使用者記錄。 請求內文包含一或多個要更新的使用者屬性： `emailAddress`， `firstName`， `lastName`， `expiresAt`.

```
POST /userservice/management/v1/users/{userid}/update.json
```

```
Content-Type: application/json
```

```json
{
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "expiresAt": "20211231T08:00:00.000t+0000"
}
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "emailAddress": "jamie@houselannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2021-12-31T08:00:00.000t+0000"
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

#### 刪除使用者

開啟 [Adobe IMS整合式訂閱](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)，此端點支援刪除 [僅限API的使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) 僅限。 刪除 [標準使用者](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)，使用 [Adobe使用者管理API](https://developer.adobe.com/umapi/) 而非。

此 [刪除使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteUserUsingPOST) 端點需要單一 `userid` path引數並從執行個體中刪除對應的使用者。 這是破壞性刪除，無法回覆。 如果成功，會傳回200狀態碼，否則會傳回錯誤訊息。

```
POST /userservice/management/v1/users/{userid}/delete.json
```

#### 刪除受邀使用者

此 [刪除受邀使用者](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteInvitedUserUsingPOST) 端點需要單一 `userid` 路徑引數並從執行個體中刪除對應的「擱置」使用者（使用者尚未接受其邀請）。 這是破壞性刪除，無法回覆。 如果成功，會傳回200狀態碼，否則會傳回錯誤訊息。

```
POST /userservice/management/v1/users/{userid}/invite/delete.json
```

## 更新角色

對角色的更新支援包括新增和刪除角色的功能。 屬性會以application/json格式傳遞為請求內文的引數。

## 新增角色

此 [新增角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/addRolesUsingPOST) 端點需要單一 `userid` path引數並將一個或多個使用者角色新增到對應的使用者。 請求內文包含一或多個物件的清單，每個物件都包含  `accessRoleId` 和 `workspaceId` 屬性。 如果成功，則完整清單 `accessRoleId/workspaceId` 會傳回指定使用者的配對。

```
POST /userservice/management/v1/users/{userid}/roles/create.json
```

```
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

## 刪除角色

此 [刪除角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteRolesUsingPOST) 端點需要單一 `userid` path引數並從對應的使用者中刪除一或多個使用者角色。 請求內文包含一或多個物件的清單，每個物件都包含  `accessRoleId` 和 `workspaceId` 屬性。 如果成功，則會傳回指定使用者的accessRoleId/workspaceId配對的其餘清單。

```
POST /userservice/management/v1/users/{userid}/roles/delete.json
```

```
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  }
]
```
