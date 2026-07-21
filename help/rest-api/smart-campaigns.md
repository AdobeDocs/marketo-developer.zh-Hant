---
title: 智慧行銷活動
feature: REST API, Smart Campaigns
description: 瞭解如何將Marketo REST API用於Smart Campaigns，包括依ID或名稱查詢、瀏覽篩選器、建立複製刪除，以及排程或請求觸發程式
exl-id: 540bdf59-b102-4081-a3d7-225494a19fdd
TQID: https://experienceleague.adobe.com/iysRjtqd9plkreyIMuNjAF3YVFHtDUIrc-GInB4V8mg
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
subfeature_v2:
  - id: ad89fb33-8541-4339-afe7-bb13d1633714
  - id: d0251300-e25f-466f-9856-7e11ce8fa7aa
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1009
ht-degree: 1%

---

# 智慧行銷活動

[智慧行銷活動端點參考（資產）](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns)

[行銷活動端點參考（銷售機會）](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns)

使用Smart Campaign REST API來查詢、建立、複製和刪除智慧行銷活動。 您也可以排程批次行銷活動、請求觸發行銷活動，以及管理行銷活動啟動。

## 查詢

依識別碼[&#128279;](#by_id)、[依名稱](#by_name)或[瀏覽](#browse)查詢智慧行銷活動。

### 依Id

[依ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET)取得Smart Campaign端點將單一Smart Campaign `id`當做路徑引數，並傳回單一Smart Campaign記錄。

```http
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

端點在`result`陣列的第一個位置傳回一個記錄。

### 依名稱

[依名稱取得Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET)端點將單一Smart Campaign `name`作為引數，並傳回單一Smart Campaign記錄。

```http
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

端點在`result`陣列的第一個位置傳回一個記錄。

### 瀏覽

[Get Smart Campaigns](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET)端點支援用於篩選和分頁的選用查詢引數。

`earliestUpdatedAt`和`latestUpdatedAt`引數接受ISO-8601格式的`datetimes` （不含毫秒）。 如果兩者皆已設定，則earliestUpdatedAt必須在latestUpdatedAt之前。

`folder`引數指定要瀏覽的父資料夾。 將其傳遞為包含`id`和`type`的JSON物件。

`maxReturn`整數指定專案數目上限。 預設值為20，最大值為200。

`offset`整數指定開始擷取專案的位置。 與`maxReturn`搭配使用。 預設值為0。

設定`isActive`布林值引數以僅傳回作用中的觸發程式行銷活動。

```http
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

端點傳回`result`陣列中的一或多個記錄。

## 建立

傳送`application/x-www-form-urlencoded` POST要求至[建立Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST)端點。 需要`name`和`folder`引數。 將`folder`傳遞為包含`id`和`type`的JSON物件。

您可以選擇使用`description`引數（最多2,000個字元）描述智慧行銷活動。

```http
POST /rest/asset/v1/smartCampaigns.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

傳送`application/x-www-form-urlencoded` POST要求至[更新Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset)端點。 必須有smart-campaign `id`路徑引數。 使用`name`變更名稱，或使用`description`變更描述。

```http
POST /rest/asset/v1/smartCampaign/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```sql
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

傳送`application/x-www-form-urlencoded` POST要求至[仿製智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset#tag/Sales-Persons/operation/describeUsingGET_5)端點。 需要`id`、`name`和`folder`引數。 它們會指定來源促銷活動、新促銷活動名稱和上層資料夾。 將`folder`傳遞為包含`id`和`type`的JSON物件。

您可以選擇使用`description`引數（最多2,000個字元）描述智慧行銷活動。

```http
POST /rest/asset/v1/smartCampaign/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[刪除Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST)端點會將單一Smart Campaign `id`當做路徑引數。

```http
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

批次智慧型行銷活動會在指定的時間執行，並一起處理定義的銷售機會集。

## 排程

使用[排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/scheduleCampaignUsingPOST)來排程批次行銷活動。 行銷活動`id`路徑引數為必要項。 在JSON要求內文中傳遞選用的`tokens`、`runAt`和`cloneToProgram`引數。

`tokens`陣列會覆寫此回合的現有程式My Token。 Marketo會在行銷活動執行後捨棄覆寫。 每個專案都包含名稱/值組，而權杖名稱必須使用`{{my.name}}`格式。

`runAt`日期時間引數會指定何時執行行銷活動。 如果省略，行銷活動會在要求後五分鐘執行。 此值以後不能超過兩年。

透過此API排程的行銷活動在執行之前一律至少要等待五分鐘。

`cloneToProgram`字串引數包含產生程式的名稱。  設定後，促銷活動、上層方案及其所有資產都會以產生的新名稱建立。 父方案已複製，並將排程新建立的行銷活動。 產生的程式會在父項下建立。 包含程式碼片段、推播通知、應用程式內訊息、靜態清單、報表和社交資產的程式可能無法以這種方式複製。 使用時，此端點限制每天最多20次呼叫。 [復製程式](https://developer.adobe.com/marketo-apis/api/asset#tag/Sales-Persons/operation/describeUsingGET_5)端點是建議的替代方案。

```http
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

觸發智慧行銷活動一次處理一個人以回應事件。

### 請求

使用[要求行銷活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/triggerCampaignUsingPOST)透過觸發行銷活動的流程傳遞銷售機會。 行銷活動必須使用具有Web服務API作為來源的「已要求行銷活動」觸發器。

行銷活動`id`路徑引數及`leads`潛在客戶ID的整數陣列是必要的。 每個呼叫最多接受100個銷售機會。

`tokens`陣列引數可選擇性用來覆寫促銷活動上層方案的本機My Token。 `tokens`接受最多100個權杖。 每個`tokens`陣列專案都包含一個名稱/值組。 權杖的名稱必須格式化為&quot;`{{my.name}}`&quot;。 如果您使用[新增系統權杖作為電子郵件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email)方法中的連結來新增「viewAsWebPageLink」系統權杖，則無法使用`tokens`覆寫它。 改為使用[將檢視新增為網頁連結至電子郵件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email)方法，可讓您使用`tokens`覆寫「viewAsWebPageLink」。

在JSON要求內文中傳遞`leads`和`tokens`引數。

```http
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

[啟動Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST)端點很簡單。 需要`id`路徑引數。 若要成功啟用，行銷活動必須符合下列條件：

- 行銷活動已停用。
- 行銷活動至少有一個觸發器和一個流程步驟。
- 行銷活動具有無錯誤的觸發器、篩選器和流程步驟。

```http
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

[停用智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST)簡單明瞭。 需要`id`路徑引數。 若要成功停用，必須啟動行銷活動。

```http
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
