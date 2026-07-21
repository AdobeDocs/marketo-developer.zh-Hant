---
title: 自訂資料事件
description: 使用適用於Web Personalization的RTP JavaScript API傳送自訂事件，其中包含引數、字串或陣列資料（最多4個專案），以及點選型觸發器。
feature: Javascript
exl-id: ef7cab9c-3bd0-450e-9247-9324b1e6f9ab
TQID: https://experienceleague.adobe.com/oWDmtMF94xG5HYXeTwkx5zF9PWo98bpwoVB6kAKLYDo
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 241
ht-degree: 3%

---

# 自訂資料事件

使用此方法來傳送自訂事件，以追蹤及即時個人化。 您可以傳送第三方資料，或根據訪客行為觸發自訂事件。

每個自訂資料事件在訪客工作階段期間會計為一次。

您必須是Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
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

自訂資料陣列最多可包含四個元素。 若要傳送超過四個元素，請重複呼叫傳送事件API，且每個呼叫中的專案不超過四個。

```javascript
var customData = {value: ['MyEvent', 'download - example whitepaper']};
rtp('send', 'event', customData);
```

### 根據按鈕點選傳送事件

此範例會在訪客選取下載特定白皮書的按鈕時傳送自訂資料事件。 RTP可以使用事件來即時劃分這些訪客。

之後，網站可以在再按兩下後顯示個人化行銷活動。 例如，行銷活動可呈現與下載的白皮書相關的另一項內容。

```html
<button id="download-whitepaper" onclick="rtp('send', 'event', {value :'download - example whitepaper'})">Download</button>
```
