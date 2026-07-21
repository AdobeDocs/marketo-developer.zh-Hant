---
title: 自訂動作
feature: Mobile Marketing
description: 瞭解如何使用適用於iOS和Android的Marketo Mobile SDK來傳送和報告自訂動作、將離線佇列、觸發Smart Campaigns，並符合20個字元的要求……
exl-id: 8c2698ce-4e39-4b2b-9d36-0864c55be17a
TQID: https://experienceleague.adobe.com/yZKzdm-dH0cYPGGKE-Z-4KcbhGIwyFl0Z9vEqcv1QXI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
  - id: c1579802-ddd4-4214-8a91-97b2066abe11
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 259
ht-degree: 2%

---

# 自訂動作

自訂動作會追蹤行動應用程式中的使用者互動。 當應用程式呼叫Marketo SDK以傳送自訂動作時，SDK會先將動作儲存至裝置。 SDK在偵測到足夠的網際網路連線能力後會傳送動作，因此Marketo可能會在延遲後收到動作。

自訂動作可作為Smart Campaigns中的觸發器和篩選器。 如需詳細資訊，請參閱[行動應用程式活動](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/triggers-and-filters-for-mobile-smart-campaigns)。

## 在iOS上傳送自訂動作

傳送自訂動作。

>[!BEGINTABS]

>[!TAB 目標C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];
[sharedInstance reportAction:@"Login" withMetaData:nil];
```

>[!TAB Swift]

```swift
sharedInstance.reportAction("Login", withMetaData:nil);
```

>[!ENDTABS]

傳送包含中繼資料的自訂動作。

>[!BEGINTABS]

>[!TAB 目標C]

```objectivec
MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
[meta setType:@"Shopping"];
[meta setDetails:@"RedShirt"];
[meta setLength:20];
[meta setMetric:30];

[sharedInstance reportAction:@"Bought Shirt" withMetaData:meta];
```

>[!TAB Swift]

```swift
let meta = MarketoActionMetaData()
meta.setType("Shopping");
meta.setDetails("RedShirt");
meta.setLength(20);
meta.setMetric(30);

sharedInstance.reportAction("Bought Shirt", withMetaData:meta);
```

>[!ENDTABS]

立即報告所有已儲存的動作。

>[!BEGINTABS]

>[!TAB 目標C]

```objectivec
[sharedInstance reportAll];
```

>[!TAB Swift]

```swift
sharedInstance.reportAll();
```

>[!ENDTABS]

## 在Android上傳送自訂動作

1. 傳送自訂動作。

   ```
   Marketo.reportAction("Login", null);
   ```

1. 傳送包含中繼資料的自訂動作。

   ```
   MarketoActionMetaData meta = new MarketoActionMetaData();
   meta.setActionType("Shopping");
   meta.setActionDetails("RedShirt");
   meta.setActionLength("20");
   meta.setActionMetric("30");
   
   Marketo.reportAction("Bought Shirt", meta);
   ```

1. 立即報告所有已儲存的自訂動作。

   ```
   Marketo.reportAll();
   ```

## 疑難排解自訂動作

從行動SDK傳送至Marketo的自訂動作名稱必須少於20個字元。

**共用裝置上的多使用者使用案例：**&#x200B;當使用者登入使用Marketo SDK的行動應用程式時，第一個呼叫會將潛在客戶與應用程式安裝建立關聯。 呼叫成功後，後續的使用者活動會出現在潛在客戶的活動記錄中。

關聯呼叫為非同步。 在登入後立即記錄的自訂動作可能會與先前登入的使用者建立關聯，直到呼叫成功為止。
