---
title: 觸發程序
description: 在Web Personalization中使用RTP觸發程式，以對rtp狀態執行函式，包括userContextReady，以及語法、引數和位置範例。
feature: Javascript
exl-id: 588836fa-1e4d-41f3-aec5-5cd17eb16071
TQID: https://experienceleague.adobe.com/yTz9i4bnD4I0PDAmpnjdD1okYJzd40wriA-2ZzO5OMM
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87cid: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 117
ht-degree: 8%

---

# 觸發程序

全域`rtp`物件達到指定狀態時觸發執行函式。

您必須是Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

## 使用情況

`rtp('triggerName', function_to_trigger);`

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| &#39;triggerName&#39; | 必要 | 字串 | 方法名稱。 |
| function_to_trigger | 必要 | 功能 | 要觸發的函式。 |

### 使用者內容就緒觸發器

全域`rtpUserContext`物件就緒時，`userContextReady`觸發程式會呼叫函式。 以下範例會根據使用者的位置設定自訂變數。

```javascript
rtp('userContextReady', function() {
    if (rtpUserContext.location.state == 'CA') {
        rtp('set', 'custom1', 'productA');
    }
});
```
