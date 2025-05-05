---
title: 模式比對
description: 模式比對
feature: Javascript
exl-id: 4ebd13e3-375b-449b-850f-3b18f570ca75
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 5%

---

# 模式比對

RTP會公開公用程式函式，以檢查模式是否符合特定字串。 此公用程式無法用於非同步處理，因為它會傳回是否有相符專案的指示。

您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

## 使用量

> rtp.checkPattern(check_about， pattern)；

| 引數 | 選用/必要 | 類型 | 說明 |
|---|---|---|---|
| check_about | 必要 | 字串 | 比對模式的字串。 例如：目前頁面URL、產品名稱。 |
| 圖樣 | 必要 | 字串 | 為萬用字元新增%。 模式可以是：start withend withcontainsfull match |


## 範例

如果目前頁面URL結尾為&quot;productA&quot;，請在索引1中設定自訂變數。

```javascript
if (rtp.checkPattern(window.location.href, '%productA')) {
    rtp('set', 'custom1', 'productA');
}
```

目前的URL路徑為&#39;/products/productB&#39;。 此範例會檢查路徑是否包含「products」並設定自訂變數。

```javascript
var currentURLPath = '/products/productB';
if (rtp.checkPattern(currentURLPath, '%products%')) {
    rtp('set', 'custom1', 'products');
}
```
