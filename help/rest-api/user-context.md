---
title: 使用者內容
feature: REST API
description: 瞭解如何啟用並使用Marketo RTP User Context API來設定自訂變數、跨造訪讀取使用者資料，以及追蹤已檢視和已點按的行銷活動。
exl-id: b8daace2-07a5-4621-aa3a-03fa9f66ea73
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '286'
ht-degree: 5%

---

# 使用者內容

使用者內容JavaScript API會公開多個工作階段中的使用者和訪客層級資料，以使用歷史使用者行為和資料啟用進階個人化功能。 此API超越了資料讀取與公開自訂變數的範圍，可讓您將有意義的資料和事件推送到RTP後端，以用於進階分段和個人化目的。 其他功能： [觸發器](../javascript-api/triggers.md)，[模式符合](../javascript-api/pattern-match.md)。

- 您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。
- 使用者內容API是一項功能，必須由Marketo支援根據請求啟用。 啟用API時，RTP全域物件下的userContext物件將會公開。

## 使用者內容屬性

| 名稱 | 類型 | 說明 |
|------------------|-------------|------|
| customVar[1-5] | 字串 | 儲存在使用者內容上的自訂資料。 |
| viewedCampaigns | 促銷活動ID的逗號分隔字串 | 已在目前或先前的造訪中檢視促銷活動。 |
| clickedCampaign | 促銷活動ID的逗號分隔字串 | 在目前或先前的造訪中點進促銷活動。 |

## 設定自訂變數

新增自訂資料至使用者內容。

### 使用情況

`rtp('set', 'customVar'[1-5], my_custom_value);`

| 參數 | 選用/必要 | 類型 | 說明 |
|-----------------|-------------------|--------|-----------------|
| &#39;設定&#39; | 必要 | 字串 | 方法動作。 |
| customVar | 必要 | 字串 | 自訂變數名稱。 |
| my_custom_value | 必要 | 字串 | 要儲存在索引1-5中的自訂變數上的自訂值。 |

注意：自訂變數只有在檢視呼叫中才會傳送至RTP，因此建議在呼叫檢視之前設定自訂變數。 否則，此摘要只會在下次檢視呼叫時傳送。

自訂Var限制

- 自訂變數長度不能超過100個字元。
- 行銷活動資料僅限於最近10次瀏覽，每次瀏覽包含10個行銷活動。

### 使用情況

`rtp('set', 'customVar', 'A');`

```javascript
// Set and get customVars
rtp('set', 'customVar1', 'foo');

// Read location
if (rtp.userContext.location.state == 'CA')  {
    // Do something
}

// Check if user viewed campaign id 45:
// The campaign id is exposed in the RTP UI when hovering over a campaign name.
if (rtp.userContext.viewedCampaign('45')) {
    // Do something
}
```
