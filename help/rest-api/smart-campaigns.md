---
title: 智慧行銷活動
feature: REST API, Smart Campaigns
description: 瞭解如何將Marketo REST API用於Smart Campaigns，包括依ID或名稱查詢、瀏覽篩選器、建立複製刪除，以及排程或請求觸發程式
exl-id: 540bdf59-b102-4081-a3d7-225494a19fdd
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1012'
ht-degree: 1%

---

# 智慧行銷活動

[智慧行銷活動端點參考（資產）](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns)

[行銷活動端點參考（潛在客戶）](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)

Marketo提供了一組REST API，可用於對智慧行銷活動執行操作。 這些API遵循資產API的標準介面模式，提供查詢、建立、複製和刪除選項。 此外，您也可以排程批次行銷活動或請求觸發行銷活動，以管理智慧行銷活動的執行。

## 查詢

查詢智慧型行銷活動會遵循[的標準查詢型別（依識別碼](#by_id)、[依名稱](#by_name)和[瀏覽](#browse)）。

### 依Id

[依ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET)取得Smart Campaign端點將單一Smart Campaign `id`當做路徑引數，並傳回單一Smart Campaign記錄。

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

使用此端點，`result`陣列的第一個位置將一律有單一記錄。

### 依名稱

[依名稱取得Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET)端點將單一Smart Campaign `name`作為引數，並傳回單一Smart Campaign記錄。

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

使用此端點，`result`陣列的第一個位置將一律有單一記錄。

### 瀏覽

[Get Smart Campaigns](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET)端點的運作方式與其他資產API瀏覽端點類似，並可允許數個選用查詢引數來指定篩選條件。

`earliestUpdatedAt`和`latestUpdatedAt`引數接受ISO-8601格式的`datetimes` （不含毫秒）。 如果兩者皆已設定，則earliestUpdatedAt必須在latestUpdatedAt之前。

`folder`引數指定要瀏覽的父資料夾。 格式為包含`id`和`type`屬性的JSON區塊。

`maxReturn`引數是整數，指定要傳回的專案數目上限。 預設值為20。 最大值為200。

`offset`引數是整數，指定從何處開始擷取專案。 可與`maxReturn`搭配使用。 預設值為0。

`isActive`引數是布林值，指定只傳回作用中的觸發促銷活動。

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

使用此端點，`result`陣列中將有一或多個記錄。

## 建立

[建立Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST)端點是使用具有兩個必要引數的application/x-www-form-urlencoded POST執行。 `name`引數指定要建立的智慧行銷活動的名稱。 `folder`引數會指定智慧行銷活動建立所在的父資料夾。 格式為包含`id`和`type`屬性的JSON區塊。

您可以選擇使用`description`引數（最多2,000個字元）描述智慧行銷活動。

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

[Update Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/)端點使用application/x-www-form-urlencoded POST執行。 它以單一智慧行銷活動`id`作為路徑引數。 您可以使用`name`引數來更新智慧行銷活動的名稱，或使用`description`引數來更新智慧行銷活動的說明。

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

[仿製智慧型行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端點是使用具有三個必要引數的application/x-www-form-urlencoded POST執行。 它需要指定要複製的智慧行銷活動的`id`引數、指定新智慧行銷活動名稱的`name`引數，以及指定建立新智慧行銷活動的父資料夾的`folder`引數。 格式為包含`id`和`type`屬性的JSON區塊。

您可以選擇使用`description`引數（最多2,000個字元）描述智慧行銷活動。

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

[刪除Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST)端點會將單一Smart Campaign `id`當做路徑引數。

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

使用[排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/scheduleCampaignUsingPOST)端點來排程要立即執行或在未來日期執行的批次行銷活動。 行銷活動`id`是必要的路徑引數。 選用引數是`tokens`、`runAt`和`cloneToProgram`，它們會在要求內文中以application/json形式傳遞。

Token陣列引數是My Token的陣列，可覆寫現有的程式Token。 行銷活動執行後，會捨棄代號。  每個權杖陣列專案都包含名稱/值配對。 權杖的名稱必須格式化為&quot;{{my.name}}&quot;。

runAt日期時間引數會指定執行促銷活動的時間。 如果未指定，行銷活動將在呼叫端點後5分鐘執行。 日期時間值在未來的兩年內不得超過。

透過此API排程的行銷活動在執行之前一律至少要等待五分鐘。

`cloneToProgram`字串引數包含產生程式的名稱。  設定後，促銷活動、上層方案及其所有資產都會以產生的新名稱建立。 父方案已複製，並將排程新建立的行銷活動。 產生的程式會在父項下建立。 包含程式碼片段、推播通知、應用程式內訊息、靜態清單、報表和社交資產的程式可能無法以這種方式複製。 使用時，此端點限制每天最多20次呼叫。 [復製程式](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端點是建議的替代方案。

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

## 觸發程序

觸發智慧型行銷活動會根據觸發的事件而一次影響一個人。

### 請求

使用[要求行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/triggerCampaignUsingPOST)端點將一組銷售機會傳遞至觸發行銷活動，以透過行銷活動流程執行。 行銷活動必須有一個以「網站服務API」作為來源的「已要求行銷活動」觸發器。

此端點需要行銷活動`id`做為路徑引數，以及包含潛在客戶ID的`leads`整數陣列引數。 每次呼叫最多允許100個銷售機會。

`tokens`陣列引數可選擇性用來覆寫促銷活動上層方案的本機My Token。 `tokens`接受最多100個權杖。 每個`tokens`陣列專案都包含一個名稱/值組。 權杖的名稱必須格式化為&quot;{{my.name}}&quot;。 如果您使用[新增系統權杖作為電子郵件](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email)方法中的連結來新增「viewAsWebPageLink」系統權杖，則無法使用`tokens`覆寫它。 改為使用[將檢視新增為網頁連結至電子郵件](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email)方法，可讓您使用`tokens`覆寫「viewAsWebPageLink」。

`leads`和`tokens`引數是以application/json的形式傳入要求內文中。

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

### 啟動

[啟動Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST)端點很簡單。 需要`id`路徑引數。 若要成功啟用，行銷活動必須符合下列條件：

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

[停用智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST)簡單明瞭。 需要`id`路徑引數。 若要成功停用，必須啟動行銷活動。

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
