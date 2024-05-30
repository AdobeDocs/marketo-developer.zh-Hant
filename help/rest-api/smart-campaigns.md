---
title: 「智慧型行銷活動」
feature: REST API, Smart Campaigns
description: 「智慧行銷活動概覽」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '989'
ht-degree: 1%

---


# Smart Campaign

[智慧行銷活動端點參考（資產）](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns)

[行銷活動端點參考（銷售機會）](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)

Marketo提供了一組REST API，可用於對智慧行銷活動執行操作。 這些API遵循資產API的標準介面模式，提供查詢、建立、複製和刪除選項。 此外，您也可以排程批次行銷活動或請求觸發行銷活動，以管理智慧行銷活動的執行。

## 查詢

查詢智慧行銷活動會遵循以下資產的標準查詢型別 [依id](#by_id)， [依名稱](#by_name)、和 [瀏覽](#browse).

### 依Id

此 [依ID取得Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET) 端點需要單一智慧行銷活動 `id` 作為路徑引數，並傳回單一智慧行銷活動記錄。

```
GET /rest/asset/v1/smartCampaign/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "7883#169838a32f0",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        }
    ]
}
```

使用此端點， `result` 陣列。

### 依名稱

此 [依名稱取得智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET) 端點需要單一智慧行銷活動 `name` 作為引數，並傳回單一智慧行銷活動記錄。

```
GET /rest/asset/v1/smartCampaign/byName.json?name=Test Trigger Campaign
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14494#16c886ffa44",
    "warnings": [],
    "result": [
        {
            "id": 1069,
            "name": "Test Trigger Campaign",
            "description": "",
            "createdAt": "2018-02-16T01:34:39Z+0000",
            "updatedAt": "2019-08-13T00:45:21Z+0000",
            "folder": {
                "id": 327,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 2747,
            "flowId": 1088,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1069A1"
        }
    ]
}
```

使用此端點， `result` 陣列。

### 瀏覽

此 [取得智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET) 端點的運作方式與其他資產API瀏覽端點類似，可允許使用數個選用的查詢引數來指定篩選條件。

此 `earliestUpdatedAt` 和 `latestUpdatedAt` 引數accept `datetimes` ISO-8601格式（不含毫秒）。 如果兩者皆已設定，則earliestUpdatedAt必須在latestUpdatedAt之前。

此 `folder` parameter指定要瀏覽的父資料夾。 格式為包含的JSON區塊 `id` 和 `type` 屬性。

此 `maxReturn` parameter是整數，指定要傳回的最大專案數。 預設值為20。 最大值為200。

此 `offset` parameter是一個整數，它指定從何處開始擷取專案。 可搭配使用 `maxReturn`. 預設值為0。

此 `isActive` parameter是布林值，指定僅傳回作用中的觸發促銷活動。

```
GET /rest/asset/v1/smartCampaigns.json?earliestUpdatedAt=2016-09-10T23:15:00-00:00&latestUpdatedAt=2016-09-10T23:17:00-00:00
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "626#16983a92965",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        },
        {
            "id": 1002,
            "name": "Process Unsubscribes",
            "description": "System smart campaign for processing unsubscribe events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1002,
            "flowId": 1002,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1002A1"
        }
    ]
}
```

使用此端點， `result` 陣列。

## 建立

此 [建立智慧型行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST) 使用application/x-www-form-urlencodedPOST執行端點，其中包含兩個必要的引數。 此 `name` 引數會指定要建立的Smart Campaign名稱。 此 `folder` 引數會指定智慧行銷活動建立所在的父資料夾。 格式為包含的JSON區塊 `id` 和 `type` 屬性。

您可以選擇使用 `description` 引數（最多2,000個字元）。

```
POST /rest/asset/v1/smartCampaigns.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Smart Campaign 02&folder={"type": "folder","id": 640}&description=This is a smart campaign creation test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "25bc#16c9138f148",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02",
            "description": "This is a smart campaign creation test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T17:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## 更新

此 [更新Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/) 端點會以application/x-www-form-urlencodedPOST執行。 只需要單一智慧行銷活動 `id` 作為路徑引數。 您可以使用 `name` 引數來更新智慧行銷活動的名稱，或 `description` 引數來更新智慧行銷活動的說明。

```
POST /rest/asset/v1/smartCampaign/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Smart Campaign 02 Update&description=This is a smart campaign update test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14b6a#16c924b992f",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02 Update",
            "description": "This is a smart campaign update test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T22:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## 原地複製

此 [仿製智慧型行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) 端點會以application/x-www-form-urlencodedPOST執行，並包含三個必要的引數。 這需要 `id` 指定要複製之智慧型促銷活動的引數，a `name` 指定新智慧行銷活動名稱的引數，以及 `folder` 引數，指定建立新智慧行銷活動的父資料夾。 格式為包含的JSON區塊 `id` 和 `type` 屬性。

您可以選擇使用 `description` 引數（最多2,000個字元）。

```
POST /rest/asset/v1/smartCampaign/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Test Trigger Campaign Clone&folder={"type": "folder","id": 640}&description=This is a smart campaign clone test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "681d#16c9339499b",
    "warnings": [],
    "result": [
        {
            "id": 1077,
            "name": "Test Trigger Campaign Clone",
            "description": "This is a smart campaign clone test.",
            "createdAt": "2019-08-15T03:01:41Z+0000",
            "updatedAt": "2019-08-15T03:01:41Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5135,
            "flowId": 1096,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1077A1"
        }
    ]
}
```

## 刪除

此 [刪除Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST) 端點需要單一智慧行銷活動 `id` 作為路徑引數。

```
POST /rest/asset/v1/smartCampaign/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d757#16c934216ac",
    "warnings": [],
    "result": [
        {
            "id": 1077
        }
    ]
}
```

## 批次

批次智慧型行銷活動會在特定時間啟動，並一次影響一組特定的銷售機會。

## 排程

使用 [排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/scheduleCampaignUsingPOST) 端點排程批次行銷活動，以立即或在未來日期執行。 行銷活動 `id` 是必要的路徑引數。 可選引數包括 `tokens`， `runAt`、和 `cloneToProgram` 在要求內文中以application/json傳遞。

Token陣列引數是My Token的陣列，可覆寫現有的程式Token。 行銷活動執行後，會捨棄代號。  每個權杖陣列專案都包含名稱/值配對。 代號的名稱必須格式化為&quot;{{my.name}}「。

runAt日期時間引數會指定執行促銷活動的時間。 如果未指定，行銷活動將在呼叫端點後5分鐘執行。 日期時間值在未來的兩年內不得超過。

透過此API排程的行銷活動在執行之前一律至少要等待五分鐘。

此 `cloneToProgram` 字串引數包含產生程式的名稱。  設定後，促銷活動、上層方案及其所有資產都會以產生的新名稱建立。 父方案已複製，並將排程新建立的行銷活動。 產生的程式會在父項下建立。 包含程式碼片段、推播通知、應用程式內訊息、靜態清單、報表和社交資產的程式可能無法以這種方式複製。 使用時，此端點限制每天最多20次呼叫。 此 [復製程式](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) 端點為建議的替代方法。

```
POST /rest/v1/campaigns/{id}/schedule.json
```

```json
{
   "input":
      {
         "runAt": "2018-03-28T18:05:00+0000",
         "tokens": [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
          ]
      }
}
```

```json
{
    "requestId": "52b#161d90e1743",
    "result": [
        {
            "id": 3713
        }
    ],
    "success": true
}
```

## 觸發

觸發智慧型行銷活動會根據觸發的事件而一次影響一個人。

### 請求

使用 [請求行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/triggerCampaignUsingPOST) 端點來傳遞一組銷售機會，然後觸發行銷活動以透過行銷活動流程執行。 行銷活動必須有一個以「網站服務API」作為來源的「已要求行銷活動」觸發器。

此端點需要行銷活動 `id` 作為路徑引數，以及 `leads` 包含銷售機會id的整數陣列引數。 每次呼叫最多允許100個銷售機會。

或者 `tokens` 陣列引數可用來覆寫促銷活動父方案本機的「我的Token」 。 `tokens` 最多接受100個權杖。 每個 `tokens` 陣列專案包含名稱/值組。 代號的名稱必須格式化為&quot;{{my.name}}「。 如果您使用 [新增系統權杖作為電子郵件中的連結](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email) 新增「viewAsWebPageLink」系統權杖的方法，您無法使用覆寫它 `tokens`. 請改用 [將檢視新增為網頁連結至電子郵件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email) 可讓您覆寫「viewAsWebPageLink」的方法，使用 `tokens`.

此 `leads` 和 `tokens` 引數會以application/json的形式傳遞至要求內文。

```
POST /rest/v1/campaigns/{id}/trigger.json
```

```json
{
   "input":
      {
         "leads" : [
            {
               "id" : 318592
            },
            {
               "id" : 318593
            }
         ],
         "tokens" : [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
         ]
      }
}
```

```json
{
    "requestId": "9e01#161d922f1aa",
    "result": [
        {
            "id": 3712
        }
    ],
    "success": true
}
```

### 啟用

此 [啟動Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST) 端點直接對應。 一個 `id` path引數為必要項。 若要成功啟用，行銷活動必須符合下列條件：

- 必須停用
- 必須至少有一個觸發器和一個流程步驟
- 必須有沒有錯誤的觸發器、篩選器和流程步驟

```
POST /rest/asset/v1/smartCampaign/{id}/activate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "a33a#161d9c0dcf3",
    "result": [
        {
            "id": 1069
        }
    ]
}
```

### 停用

此 [停用Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST) 簡單明瞭。 一個 `id` path引數為必要項。 若要成功停用，必須啟動行銷活動。

```
POST /rest/asset/v1/smartCampaign/{id}/deactivate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6228#161d9c29fbf",
    "result": [
        {
            "id": 1069
        }
    ]
}
```
