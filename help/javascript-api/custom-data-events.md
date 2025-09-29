---
title: 自訂資料事件
description: 使用適用於Web Personalization的RTP JavaScript API傳送自訂事件，其中包含引數、字串或陣列資料（最多4個專案），以及點選型觸發器。
feature: Javascript
exl-id: ef7cab9c-3bd0-450e-9247-9324b1e6f9ab
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '243'
ht-degree: 3%

---

# 自訂資料事件

此方法會傳送自訂事件以進行追蹤和即時個人化。 它可用來傳送第三方資料，或根據訪客行為觸發您自己的自訂事件。 自訂資料事件在訪客的工作階段中會計為一次。

您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

| 參數 | 選用/必要 | 類型 | 說明 |
|---|---|---|---|
| `send` | 必要 | 字串 | 方法動作。 |
| `event` | 必要 | 字串 | 方法名稱。 |
| `customData` | 必要 | 字串或陣列 | 自訂資料。 |

## 範例

### 使用自訂資料的字串傳送事件

```javascript
var customData = {value: 'MyEvent'};
rtp('send', 'event', customData);
```

### 使用自訂資料的字串陣列傳送事件

自訂資料陣列最多可包含四個元素。  如果您必須傳送超過四個元素，請重複呼叫「傳送事件API」 （最多四個專案），直到傳送所有專案為止。

```javascript
var customData = {value: ['MyEvent', 'download - example whitepaper']};
rtp('send', 'event', customData);
```

### 根據按鈕點選傳送事件

Marketo會將網站上的內容個人化給下載特定白皮書的網頁訪客。 他們透過擷取訪客的點選白皮書下載按鈕來執行此操作，該按鈕會傳送自訂資料事件。 RTP區段會即時顯示點選了下載白皮書按鈕的所有訪客，為每位訪客顯示稍後可點選2次的個人化行銷活動。 這是藉由顯示另一份與所下載的白皮書相關的內容來達成。

```html
<button id="download-whitepaper" onclick="rtp('send', 'event', {value :'download - example whitepaper'})">Download</button>
```
