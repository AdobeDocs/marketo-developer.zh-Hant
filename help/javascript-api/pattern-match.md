---
title: 「模式相符」
description: 「模式相符」
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 5%

---


# 模式比對

RTP會公開公用程式函式，以檢查模式是否符合特定字串。 此公用程式無法用於非同步處理，因為它會傳回是否有相符專案的指示。

您必須成為Web Personalization客戶，並擁有 [已部署RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) 使用使用者內容API之前的資訊。

## 使用情況

> rtp.checkPattern(check_about， pattern)；

| 引數 | 選用/必要 | 類型 | 說明 |
|---|---|---|---|
| check_about | 必填 | 字串 | 比對模式的字串。 例如：目前頁面URL、產品名稱。 |
| 圖樣 | 必填 | 字串 | 為萬用字元新增%。 模式可以是：start withend withcontainsfull match |


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
