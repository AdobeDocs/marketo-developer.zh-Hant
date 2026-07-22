---
title: 資料串流
description: Marketo Engage資料串流概觀，實現近乎即時的銷售機會活動和使用者稽核事件，放寬效能層級客戶的API限制
exl-id: 5617b6a5-ebc8-4d97-a290-e3b87f83e360
TQID: https://experienceleague.adobe.com/JnhN70HexjmNueZa9MAVrxjEhZ5yJatWqZiowl22quA
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1342
ht-degree: 3%

---

# 資料串流

>[!NOTE]
>
>在[使用資料串流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams#)找到目前關於資料串流的資訊。
>

資料串流可以近乎即時地將大量Marketo Engage資料傳送到外部系統。 使用串流資料來支援及時決策、目標定位行銷活動、外部行銷流程和稽核。

「資料串流」具備下列優點：

- 減少對速率限制API要求的依賴。
- 減少API限制警報。
- 傳送資料而不執行大量匯出。

已購買[Marketo Engage效能層套件](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)的使用者可使用資料串流。

## 潛在客戶活動資料串流概觀

潛在客戶活動資料串流會近乎即時地將大量潛在客戶活動資料傳送至外部系統。 使用資料流稽核潛在客戶事件和使用模式、檢視潛在客戶變更，以及從潛在客戶事件觸發工作流程。

您可以訂閱超過144種活動型別。

串流可以包括：

1. 變更所有銷售機會欄位和新建立的銷售機會。
1. 所有記錄的潛在客戶活動型別。
1. 已刪除銷售機會。
1. 所有潛在客戶自訂物件（若有要求）。 您無法選取個別自訂物件。

常見的使用案例包括：

- 自訂警報：將條件不一致的潛在客戶新增至清單。 串流會將「新增至清單」活動傳送至後續流程。
- 機器學習模型：在外部評分模型中使用活動深入分析，然後將分數傳送至Marketo，以影響Nurture行銷活動或其他程式。

串流活動清單：

| AchieveGoalInReferral | ClickPredictiveContent | ReceivedForwardToFriendEmail |
| --- | --- | --- |
| AddToList | ClickRTPCallToAction | ReceiveSalesEmail |
| AddToNurture | ClickSalesEmail | ReferToSocialApp |
| AddToOpportunity | ClickShareLink | RemoveFromList |
| AddToSalesCampaign | 轉換潛在客戶 | RemoveFromOpportunity |
| CallWebhook | 刪除銷售機會 | RequestCampaign |
| 變更資料值 | 取消抽獎資格 | 銷售電子郵件已退回 |
| ChangeLeadPartition | EarnEntryInSocialApp | 傳送警報 |
| ChangeNurtureCadence | 電子郵件已退回 | SendEmail |
| ChangeNurtureTrack | EmailBouncedSoft | SendSalesEmail |
| ChangeOwner | EmailDelivered | SentForwardToFriendEmail |
| ChangeProgramData | ExtensiveWithDataDotCom | SFDCA活動 |
| ChangeProgramMemberData | EnterSweepstackes | Sharecontent |
| ChangeRevenueStage | FillOutFacebookLeadAdsForm | SignupForReferralOffer |
| ChangeRevenueStageManually | 填寫表單 | SyncLeadToMicrosoft |
| ChangeScore | InterestedMoment | SyncLeadToSFDC |
| ChangeSegment | MergeLead | UnsubscribeEmail |
| ChangeStatusInProgress | NewLead | 更新機會 |
| ChangeStatusInSalesCampaign | OpenEmail | VisitWebpage |
| Clickemail | OpenSalesEmail | VoteInPoll |
| ClickLink | PushLeadToMarketo | WinSweepstackes |

串流自訂物件時，請包含所有與潛在客戶相關的自訂物件。 您無法選取個別自訂物件。

## 使用者稽核資料流概觀

使用者稽核資料流會近乎即時追蹤使用者對資產的變更。 使用它可稽核資產事件、檢視使用者變更，以及觸發稽核事件的程式。

Adobe I/O Events會將變更傳送至可設定的端點。 訂閱每種資產型別所需的事件型別。

一個使用案例是：

- 跨行銷系統追蹤變更：當CRM或其他系統與Marketo交換銷售機會時，請使用稽核事件來識別進行最新變更的系統。

串流使用者稽核事件清單：

| 元件 | 事件型別清單 |
| --- | --- |
| 預設方案 | 原地複製、建立、刪除、編輯頻道、匯出、修改程式設定、修改程式權杖、重新命名 |
| 電子郵件 | 核准、原地複製、建立、刪除、編輯、移動、重新命名、取消核准 |
| 電子郵件批次程式 | 核准，子更新，複製，建立，刪除，編輯，編輯頻道，修改方案排程，修改方案設定，修改方案權杖，重新命名，取消核准 |
| 電子郵件範本 | 核准、複製、建立、刪除、草稿建立、草稿捨棄、編輯、重新命名、取消核准 |
| 參與方案 | 複製、建立、刪除、編輯頻道、修改程式設定、修改程式流、修改程式權杖、重新命名 |
| 事件方案 | 複製、建立、刪除、編輯頻道、修改方案排程、修改方案設定、修改方案權杖、重新命名 |
| 資料夾 | 建立、刪除、編輯、重新命名 |
| 表單 | 核准、複製、建立、刪除、草稿建立、編輯、移動、重新命名 |
| 表單 — >登陸頁面表單 | 建立、複製、編輯、刪除、核准、重新命名 |
| 登陸頁面 | 核准，複製，建立，刪除，草稿捨棄，編輯，重新命名，取消核准 |
| 登陸頁面範本 | 核准、複製、建立、刪除、草稿建立、草稿捨棄、編輯、重新命名、取消核准 |
| 智慧清單 | 原地複製、建立、刪除、編輯、匯出、修改智慧清單設定、重新命名 |
| 行銷資料夾 | 建立、編輯、刪除 |
| 培養方案 | 複製、建立、刪除、編輯頻道、修改方案設定、修改方案流、修改方案權杖、重新命名 |
| 區段 | 建立、刪除、編輯、重新命名 |
| 細分 | 核准、建立、刪除、draftCreated、draftDiscarded、重新命名、取消核准 |
| 智慧行銷活動 | 中止、啟動、複製、建立、停用、刪除、編輯、修改行銷活動排程、修改流程步驟動作、修改智慧列示設定、移動、重新命名 |
| 程式碼片段 | 核准、核准且無草稿、複製、建立、刪除、編輯、重新命名、取消核准 |
| 管理UI -> Launchpoint ->整合 | 建立、刪除、編輯 |
| 管理員UI ->使用者 | 建立、編輯、刪除（僅適用於API使用者） |
| 管理員登入 — >使用者 | 登入成功，登入失敗 |
| 程式 — >電子郵件批次程式 | 編輯（用於變更選取的電子郵件地址）資產API |
| 方案 — >行銷方案 | 建立，原地複製 |

使用者稽核事件範例：

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "id": "b77c743a-8e28-40f2-8aab-9541bbc85e68",
        "type": "com.adobe.platform.marketo.audit.user.email",
        "source": "https://www.marketo.com",
        "time": "2020-05-28T19:20:47.28Z",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentId": 232459,
            "componentType": "Email",
            "eventAction": "approve",
            "munchkinId": "123-ABC-456",
            "imsOrgId": "ADOBEORGID@AdobeOrg",
            "user": 253,
            "userId": "example@marketo.com"
        }
    }
}
```

## 通知資料流總覽

通知資料流是Marketo Engage效能等級產品的一部分。

Marketo通知中心可將通知傳送至電子郵件地址。 Notification Data Stream也會透過Adobe I/O Events將這些通知傳送至可設定的端點。 這些通知與Marketo UI鈴鐺圖示中的通知相同。

通知事件清單：

| 元件 | 事件型別清單 |
| --- | --- |
| 通知 | campaign中止、campaign失敗、nurture （程式已耗盡）、salesforce同步失敗、測試群組（A/B測試結果）、網站服務（每日配額） |

通知事件範例：

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "type": "com.adobe.platform.marketo.notification.campaign_abort",
        "source": "https://www.marketo.com",
        "time": "2021-05-27T10:22:37.489-5:00",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentType": "campaign_abort",
            "subType": "user_campaign_abort",
            "eventAction": {
                "campaignId":1234,
                "userId":"example@marketo.com",
            }
            "imsOrgId":"ADOBEORGID@AdobeOrg",
            "munchkinId":"123-ABC-456"
        }
    }
}
```

## 技術細節

以下小節說明從每個資料流接收資料所需的設定。 每個資料流都需要端點設定和整合程式碼。

### 潛在客戶活動資料流

潛在客戶活動資料流會傳送具有下列服務特性的已訂閱潛在客戶活動事件：

- 預設會每兩秒推送一次資料。
- 每個訂閱都使用100-500筆記錄的批次。
- 客戶REST服務有20秒的逾時和三次重試，間隔為三分鐘。 成功的重試會自動啟用服務。 在第三次失敗後，服務會暫停並每三分鐘重試一次，除非手動取消布建。
- 佇列的資料最多可保留七天。

若要實施銷售機會活動資料流：

1. 公開可從公用網際網路接收含JSON內文之POST請求的HTTP端點。 活動推送資料流傳送請求至：
1. 為Adobe提供下列功能：
   1. 適用於其訂閱的Marketo Munchkin ID
   1. 步驟1中的端點URL
   1. 他們想要收到的活動型別（以上完整的清單）
   1. 一種驗證方法，可讓客戶驗證請求是否合法。 可以：
      1. OAuth [使用者端認證驗證](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)的身分提供者URL、使用者端識別碼和使用者端密碼
      1. API權杖，可包含在授權http標頭中，由潛在客戶活動資料流傳送的請求中

Adobe會在收到所需資訊後啟用資料流。 然後，端點開始接收資料。

典型潛在客戶活動資料流呼叫的UML圖表：

![潛在客戶活動資料流圖表](assets/lead-activity-data-stream.png)

URL端點建立範例：

```javascript
/*
Copyright 2022 Adobe
All Rights Reserved.

NOTICE: Adobe permits you to use, modify, and distribute this file in
accordance with the terms of the Adobe license agreement accompanying
it.
*/
constexpress=require('express')
constwinston=require('winston');
constport=3000

constapp=express().use(express.json())

constlogger=winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: {service: 'activity-stream-consumer-example'},
  transports: [
    // - Write all logs with level `error` and below to `error.log`
    newwinston.transports.File({filename: 'error.log',level: 'error'}),
    // - Write all logs with level `info` and below to `combined.log`
    newwinston.transports.File({filename: 'combined.log'}),
    newwinston.transports.Console({format: winston.format.simple()})
  ],
});

app.get('/',(req,res)=>{
  logger.info(JSON.stringify(req.query))
  res.sendStatus(200)
})

app.post('/',(req,res)=>{
  logger.info(JSON.stringify(req.body))
  res.sendStatus(200)
})

app.listen(port,()=>{
  logger.info(`app listening on port ${port}`)
})
```

如需範例應用程式程式碼，請參閱[潛在客戶活動資料流消費者範例](https://github.com/ihgrant/activity-stream-consumer-example)。

### 使用者稽核資料流和通知資料流

使用者稽核事件會透過Adobe I/O傳送。若要透過Adobe ID使用：

1. 為Adobe提供下列資訊：
   1. Adobe ID
   1. 適用於其訂閱的Marketo Munchkin ID
1. 公開REST端點（通常是webhook）以使用事件。
1. 在收到端點資訊後，Adobe會啟用訂閱的資料流。
1. 在Adobe I/O中設定資料流。
   1. 此步驟需要Adobe組織
   1. 需要Adobe組織使用者擁有開發人員或系統管理員角色

若要設定Adobe I/O，請參閱[使用Adobe I/O設定Marketo使用者稽核資料流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-user-audit-data-stream-setup#)。

### 在Marketo中設定使用者稽核資料流

「使用者稽核資料流」目前可與其他3個「資料流」一起作為「效能」套裝程式的一部分使用。 如需封裝的詳細資訊，請參閱[產品說明頁面](https://helpx.adobe.com/tw/legal/product-descriptions/adobe-marketo-engage---product-description.html)以瞭解產品限制和功能。

### 設定Adobe I/O

[請參閱Adobe I/O Events快速入門](https://developer.adobe.com/runtime/docs/guides/getting-started/)

如需此使用案例的基本指示，請從[console.adobe.io](https://developer.adobe.com/console)開始：

出現提示時，請選取&#x200B;**[!UICONTROL Create New Project]**&#x200B;或&#x200B;**[!UICONTROL Add Event]**。

### 開始使用您的新專案

若要開始使用Adobe服務、新增API、事件或執行階段，請檢視我們的[檔案](https://developer.adobe.com/runtime/docs/)。

## 公開檔案

- [Marketo資料串流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams/)
- [Adobe IO活動與Webhook簡介](https://developer.adobe.com/events/docs/guides/)
- [資料串流部落格](https://blog.developer.adobe.com/introducing-the-adobe-marketo-engage-data-streams-61198b567fbb)
