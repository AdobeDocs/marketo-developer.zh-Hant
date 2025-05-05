---
title: 自訂動作
feature: Mobile Marketing
description: 自訂動作概觀
exl-id: 8c2698ce-4e39-4b2b-9d36-0864c55be17a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '291'
ht-degree: 0%

---

# 自訂動作

您可以透過傳送自訂動作來追蹤使用者互動。 當您的行動應用程式呼叫Marketo SDK以傳送自訂動作時，自訂動作最初會儲存至裝置。 Marketo SDK在傳送自訂動作之前，會先檢查是否有足夠的網際網路連線。 因此，自訂動作的傳送時間與Marketo接收時間之間可能會有延遲。

自訂動作可作為Smart Campaigns中的觸發器和篩選器。 如需詳細資訊，請參閱[行動應用程式活動](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/triggers-and-filters-for-mobile-smart-campaigns)。

## 在iOS上傳送自訂動作

傳送自訂動作。

>[!BEGINTABS]

>[!TAB 目標C]

```
Marketo *sharedInstance = [Marketo sharedInstance];
[sharedInstance reportAction:@"Login" withMetaData:nil];
```

>[!TAB Swift]

```
sharedInstance.reportAction("Login", withMetaData:nil);
```

>[!ENDTABS]

傳送包含中繼資料的自訂動作。

>[!BEGINTABS]

>[!TAB 目標C]

```
MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
[meta setType:@"Shopping"];
[meta setDetails:@"RedShirt"];
[meta setLength:20];
[meta setMetric:30];

[sharedInstance reportAction:@"Bought Shirt" withMetaData:meta];
```

>[!TAB Swift]

```
let meta = MarketoActionMetaData()
meta.setType("Shopping");
meta.setDetails("RedShirt");
meta.setLength(20);
meta.setMetric(30);

sharedInstance.reportAction("Bought Shirt", withMetaData:meta);
```

>[!ENDTABS]

立即報告所有動作（傳送所有儲存的動作）。

>[!BEGINTABS]

>[!TAB 目標C]

```
[sharedInstance reportAll];
```

>[!TAB Swift]

```
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

1. 立即報告所有自訂動作（傳送所有儲存的動作）。

   ```
   Marketo.reportAll();
   ```

## 疑難排解自訂動作

設定行動自訂動作很簡單，但您可從行動SDK傳送至Marketo的字元數存在限制。 確保透過行動SDK傳回Marketo的所有自訂動作長度少於20個字元。

**共用裝置上的多使用者使用案例注意事項：**&#x200B;當使用者登入與Marketo SDK整合的行動應用程式時，會進行第一次呼叫，以將潛在客戶與應用程式安裝建立關聯。 成功完成此呼叫後，可在潛在客戶的活動記錄中看到應用程式中的其他使用者活動。 請注意，如果有任何自訂動作在登入後立即記錄，則這屬於非同步呼叫，這些動作可能會與先前已登入的使用者建立關聯，直到關聯呼叫成功為止。
