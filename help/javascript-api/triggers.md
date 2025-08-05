---
title: Triggers
description: Triggers
feature: Javascript
exl-id: 588836fa-1e4d-41f3-aec5-5cd17eb16071
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '80'
ht-degree: 13%

---

# Triggers

新增在全域rtp物件的特定狀態上觸發函式的功能。

您必須是Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

## 使用情況

`rtp('triggerName', function_to_trigger);`

| 參數 | 選用/必要 | 類型 | 說明 |
|---------------------|-------------------|----------|----------------------|
| &#39;triggerName&#39; | 必要 | 字串 | 方法名稱。 |
| function_to_trigger | 必要 | 功能 | 要觸發的函式。 |

### 使用者內容就緒觸發器

根據使用者位置設定自訂變數。 當「rtpUserContext」全域物件就緒時，會呼叫此函式。

```javascript
rtp('userContextReady', function() {
    if (rtpUserContext.location.state == 'CA') {
        rtp('set', 'custom1', 'productA');
    }
});
```
