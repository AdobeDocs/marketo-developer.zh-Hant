---
title: "觸發器"
description: "觸發器"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '80'
ht-degree: 8%

---


# 觸發器

新增在全域rtp物件的特定狀態上觸發函式的功能。

您必須是Web Personalization客戶並擁有 [已部署RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) 使用使用者內容API之前，先停留在您的網站上。

## 使用情況

`rtp('triggerName', function_to_trigger);`

| 引數 | 選用/必要 | 類型 | 說明 |
|---------------------|-------------------|----------|----------------------|
| &#39;triggerName&#39; | 必填 | 字串 | 方法名稱。 |
| function_to_trigger | 必填 | 函數 | 要觸發的函式。 |


### 使用者內容就緒觸發器

根據使用者位置設定自訂變數。 當「rtpUserContext」全域物件就緒時，會呼叫此函式。

```javascript
rtp('userContextReady', function() {
    if (rtpUserContext.location.state == 'CA') {
        rtp('set', 'custom1', 'productA');
    }
});
```
