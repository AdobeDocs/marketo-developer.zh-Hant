---
title: 使用者內容
feature: REST API
description: 瞭解如何啟用並使用Marketo RTP User Context API來設定自訂變數、跨造訪讀取使用者資料，以及追蹤已檢視和已點按的行銷活動。
exl-id: b8daace2-07a5-4621-aa3a-03fa9f66ea73
TQID: https://experienceleague.adobe.com/Ph0Tw-C9jzWaR4bYyUIXyzzoa2yjHQk2gt6tNA8H2mA
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
subfeature_v2:
  - id: a1d50dda-6d94-4e16-8c30-5eb7181c4650
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 273
ht-degree: 5%

---

# 使用者內容

使用者內容JavaScript API會公開多個工作階段中的使用者層級和訪客層級資料。 使用歷史行為和資料來建立進階個人化。

此API還提供自訂變數，用於將資料和事件傳送到RTP後端以進行細分和個人化。 檢視相關的[觸發器](../javascript-api/triggers.md)和[模式比對](../javascript-api/pattern-match.md)功能。

- 您必須是Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)。
- 您必須要求Marketo支援以啟用使用者內容API。 啟用後，userContext物件會顯示在RTP全域物件下。

## 使用者內容屬性

| 名稱 | 類型 | 說明 |
| --- | --- | --- |
| `customVar[1-5]` | 字串 | 儲存在使用者內容上的自訂資料。 |
| `viewedCampaigns` | 促銷活動ID為逗號分隔字串 | 已在目前或先前的造訪中檢視促銷活動。 |
| `clickedCampaigns` | 促銷活動ID為逗號分隔字串 | 在目前或先前的造訪中點進促銷活動。 |

## 設定自訂變數

設定自訂變數以新增資料至使用者內容。

### 使用情況

`rtp('set', 'customVar'[1-5], my_custom_value);`

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| `'set'` | 必要 | 字串 | 方法動作。 |
| `customVar` | 必要 | 字串 | 自訂變數名稱。 |
| `my_custom_value` | 必要 | 字串 | 要儲存在索引1-5中的自訂變數上的自訂值。 |

自訂變數只有在檢視呼叫中才會傳送至RTP。 在檢視呼叫之前設定自訂變數。 否則，變數會在下次檢視呼叫中傳送。

自訂變數有下列限制：

- 自訂變數不可超過100個字元。
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
