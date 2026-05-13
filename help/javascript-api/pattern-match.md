---
title: 模式比對
description: 使用RTP rtp.checkPattern公用程式來測試含百分比萬用字元的字串模式，請參閱同步限制、使用方式和URL範例，以及必要的RTP標籤設定。
feature: Javascript
exl-id: 4ebd13e3-375b-449b-850f-3b18f570ca75
TQID: https://experienceleague.adobe.com/-HopUg6-2EchL9kJrPDbz62mRlrqYaXYdufILjkvP1Y
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
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 171
ht-degree: 5%

---

# 模式比對

RTP會公開公用程式函式，以檢查模式是否符合特定字串。 此公用程式無法用於非同步處理，因為它會傳回是否有相符專案的指示。

您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。

## 使用情況

> rtp.checkPattern(check_about， pattern)；

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| check_about | 必要 | 字串 | 比對模式的字串。 例如：目前頁面URL、產品名稱。 |
| 圖樣 | 必要 | 字串 | 為萬用字元新增%。 模式可以是:start withend withcontainsfull match |

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
