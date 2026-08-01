---
title: '[!DNL Ionic]'
feature: Mobile Marketing
description: 將Marketo Cordova外掛程式與Ionic整合的逐步指南，可啟用推播通知、初始化SDK、追蹤工作階段並關聯銷售機會。
exl-id: 204e5fb4-c9d6-43a6-9d77-0b2a67ddbed3
TQID: https://experienceleague.adobe.com/UTNWd69NliR896RcO-XM2GG35liuLeNNhTXo9GRtB4o
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 581
ht-degree: 2%

---

# 離子型

將Marketo Cordova外掛程式與[!DNL Ionic]應用程式整合。[!DNL Ionic] 目前不支援電容器。

## 先決條件

1. [在Marketo Admin](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中新增應用程式，並取得應用程式秘密金鑰和Munchkin ID。
1. 設定[iOS](push-notifications.md)或[Android](push-notifications.md)的推播通知。
1. 安裝[[!DNL Ionic]](https://ionicframework.com/getting-started/)與[Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安裝指示

### 設定Marketo [!DNL Ionic]外掛程式

1. 移至[!DNL Ionic]應用程式目錄，然後執行下列命令以新增Marketo外掛程式：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 執行以下命令，確認外掛程式已新增：

   `$ ionic plugin list com.marketo.plugin 0.X.0 "MarketoPlugin"`

### 遷移到較新版本（可選）

1. 若要移除現有外掛程式，請執行以下命令：

   `$ ionic plugin remove com.marketo.plugin`

1. 若要再次新增外掛程式，請執行以下命令：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

### 在xCode中啟用推播通知

1. 在xCode專案中開啟推播通知功能。![通知功能](assets/notification-capability.png)

### 追蹤推播通知

將下列程式碼貼入`application:didFinishLaunchingWithOptions:`函式中。

>[!BEGINTABS]

>[!TAB 目標C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

```swift
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotfication(launchOptions)
```

>[!ENDTABS]

### 初始化Marketo框架

若要在應用程式啟動時初始化Marketo架構，請在JavaScript主檔案的`onDeviceReady`函式下新增下列程式碼。

傳遞`ionicCordova`做為[!DNL Ionic] Cordova應用程式的架構型別。

#### 語法

```javascript
// This method will Initialize the Marketo Framework using Your MunchkinId and Secret Key
marketo.initialize(
  function() { console.log("MarketoSDK Init done."); },
  function(error) { console.log("an error occurred:" + error); },
  'YOUR_MUNCHKIN_ID',
  'YOUR_SECRET_KEY',
  'FRAMEWORK_TYPE'
);

// For session tracking, add following.
marketo.onStart(
  function(){ console.log("onStart."); },
  function(error){ console.log("Failed to report onStart." + error); }
);
```

#### 參數

- Success回呼：Marketo架構成功初始化時要執行的函式。
- 失敗回呼：當Marketo架構初始化失敗時要執行的函式。
- Munchkin ID：註冊期間從Marketo收到的Munchkin ID。
- 秘密金鑰：註冊期間從Marketo收到的秘密金鑰。

### 初始化Marketo推播通知

若要初始化Marketo推播通知，請在主要JavaScript檔案中的初始化函式後新增下列程式碼。

#### 語法

```javascript
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

#### 參數

- Success回呼：Marketo推播通知成功初始化時要執行的函式。
- 失敗回呼：當Marketo推播通知初始化失敗時要執行的函式。
- GCM_PROJECT_ID：建立應用程式後，在[Google開發人員主控台](https://accounts.google.com/ServiceLogin?service=cloudconsole&passive=1209600&osid=1&continue=https://console.cloud.google.com/apis/dashboard&followup=https://console.cloud.google.com/apis/dashboard)中找到GCM專案ID。

您也可以在登出時取消註冊Token。

```javascript
marketo.uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## 關聯銷售機會

呼叫associateLead函式以建立Marketo銷售機會。

### 語法

```javascript
marketo.associateLead(
  function(){ console.log("MarketoSDK : Lead Added"); },
  function(error){ console.log("an error occurred:" + error); },
  'Lead_Data_JSON_String'
);
```

### 參數

- Success回呼：在Marketo架構成功建立潛在客戶關聯時要執行的函式。
- 失敗回呼：Marketo架構無法關聯潛在客戶時要執行的函式。
- 潛在客戶資料： JSON字串格式的潛在客戶資料。

### 範例

```javascript
// First create a lead as shown below
var lead = {};
lead[marketo.KEY_FIRST_NAME] = "Ionic";
lead[marketo.KEY_LAST_NAME] = "App";
lead[marketo.KEY_EMAIL] = email;
lead[marketo.KEY_ADDRESS] = "demo address";
lead[marketo.KEY_CITY] = "city";
lead[marketo.KEY_STATE] = "state";
lead[marketo.KEY_COUNTRY] = "country";
lead[marketo.KEY_POSTAL_CODE] = "postalCode";
lead[marketo.KEY_GENDER] = "gender";

// Use associateLead function to associate it.
marketo.associateLead(
  function() { console.log("MarketoSDK : Lead Associated"); },
  function(error) { console.log("an error occurred:" + error); },
  JSON.stringify(lead)
);
```

## 報表動作

呼叫`reportaction`函式以報告使用者動作。

### 語法

```javascript
marketo.reportaction(
  function(){ console.log("MarketoSDK : New event sent "); },
  function(error){ console.log("an error occurred:" + error); },
  'Action_Name',
  'Action_Data_JSON_String'
);
```

### 參數

- Success回呼：在Marketo架構成功回報動作時執行的函式。
- 失敗回呼：Marketo架構無法回報動作時要執行的函式。
- 動作名稱：動作名稱。
- 動作資料： JSON字串格式的動作資料。

### 範例

```javascript
// First create an event as below
var event = {
    "Action Type":"Add To Cart",
    "Action Details":"Adding Product in cart",
    "Action Metric":"10",
    "Action Length":"1"
}

marketo.reportaction(
    function(){ console.log("Reported action successfully."); },
    function(error){ console.log("Failed to report action." + error); },
    "Add To Cart",
    JSON.stringify(event)
);
```

## 工作階段報告

繫結「暫停」和「繼續」事件型別以報告「開始」和「停止」事件。 這些事件會追蹤使用者在行動應用程式中花費的時間，並且在Android上為必要專案。

```javascript
//Add the following code in your www/js/index.js

bindEvents: function() {
   document.addEventListener('pause', this.onStop, false);
   document.addEventListener('resume', this.onStart, false);
},
onStop: function() {
   marketo.onStop(
       function(){ console.log("onStop"); },
       function(error){ console.log("Failed to report onStop." + error); }
   );
},
onStart: function() {
   marketo.onStart(
       function(){ console.log("onStart."); },
       function(error){console.log( "Failed to report onStart." + error); }
   );
},
```

## 建立銷售機會

從混合應用程式建立銷售機會有三種方式：

1. MARKETO MME SDK
1. MARKETO REST API
1. 表單提交

識別新潛在客戶的觸發器和篩選器取決於建立方法：

- 使用MME SDK或REST API建立的銷售機會會出現在「建立的銷售機會」觸發器和篩選器中。
- 由表單提交建立的潛在客戶會出現在「填寫表單」觸發器和篩選器中。

在混合式應用程式和網頁應用程式中使用相同的銷售機會建立方法。 如果網頁應用程式使用表單提交或REST API，請在混合式應用程式中使用該方法。 如果網頁應用程式未使用其中一種方法，請考慮使用MME SDK在Marketo中建立銷售機會。
