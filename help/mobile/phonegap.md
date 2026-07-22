---
title: PhoneGap
feature: Mobile Marketing
description: 使用Cordova設定Marketo PhoneGap外掛程式、設定Firebase雲端訊息、啟用iOS和Android推播、追蹤通知，以及初始化SDK。
exl-id: 99f14c76-9438-4942-9309-643bca434d07
TQID: https://experienceleague.adobe.com/eFAwR7r5IE6vKigsEWrJdCmC3VrfB-nl0h8x7Vgt1VY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 758
ht-degree: 2%

---

# PhoneGap

將Marketo PhoneGap外掛程式與Cordova應用程式整合。

## 先決條件

1. [在Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中新增應用程式，並取得應用程式秘密金鑰和Munchkin ID。
1. 設定[iOS](push-notifications.md)或[Android](push-notifications.md)的推播通知。
1. [安裝PhoneGap/Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安裝指示

1. 設定Marketo PhoneGap外掛程式。

   前往PhoneGap應用程式目錄，然後執行下列命令以新增Marketo外掛程式：

   `$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 安裝FCM外掛程式。

   `$ cordova plugin add cordova-plugin-fcm`

   執行以下命令，確認外掛程式已新增：

   `$ cordova plugin ls com.marketo.plugin 0.X.0 "MarketoPlugin" cordova-plugin-fcm 2.1.2 "FCMPlugin"`

**移轉至較新版本（選擇性）**

若要移除現有外掛程式，請執行以下命令：

`$ cordova plugin remove com.marketo.plugin`

若要再次新增外掛程式，請執行以下命令：

`$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

**Cordova 8.0.0版(Cordova@Android7.0.0)及更高版本**

建置Cordova Android平台後，請在Android Studio中開啟應用程式。 更新`com.marketo.plugin`資料夾中`Marketo.gradle`檔案的`dirs`值。

```groovy
repositories{
  jcenter()
  flatDir{
      dirs '../app/src/main/aar'
   }
}
```

新增應用程式的目標平台： `$cordova platform add android` `$ cordova platform add ios`

檢查新增的平台： `$cordova platform ls`

1. Firebase Cloud Messaging支援

1. 在Firebase主控台中設定Firebase應用程式。
   1. 在[&#128279;](https://console.firebase.google.com/)Firebase主控台中建立或新增專案。
      1. 在[Firebase主控台](https://console.firebase.google.com/)中，選取&#x200B;**[!UICONTROL Add Project]**。
      1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase歡迎畫面中，選取「將Firebase新增至Android應用程式」。
      1. 提供您的封裝名稱和SHA-1，然後選取&#x200B;**[!UICONTROL Add App]**。 已下載您Firebase應用程式的新`google-services.json`檔案。
   1. 在[!UICONTROL Project Overview]中移至&#x200B;**[!UICONTROL Project Settings]**。
      1. 選取「**[!UICONTROL General]**」索引標籤並下載「google-services.json」檔案。
      1. 選取 **[!UICONTROL Cloud Messaging]** 索引標籤。 複製[!UICONTROL Server Key]和[!UICONTROL Sender ID]，並將其提供給Marketo。
   1. 在PhoneGap應用程式中設定FCM。
      1. 將下載的「google-services.json」檔案移至PhoneGap應用程式模組的根目錄中。
      1. 從位置`platforms/android/app/src/main/java/com/gae/scaffolder/plugin`移除檔案&#39;MyFirebaseInstanceIDService&#39; （已棄用）
      1. 修改位置`platforms/android/app/src/main/java/com/gae/scaffolder/plugin`中的檔案&#39;MyFirebaseMessagingService&#39;，如下所示：

         ```
         import com.marketo.Marketo;
         
         public class MyFirebaseMessagingService extends FirebaseMessagingService{
         
         @Override
         public void onNewToken(String s){
           super.onNewToken(s);
           MarketoExtension.setPushNotificaitonTokens(s);
           //Add your code here
         }
         
         @Override
         public void onMessageReceived(RemoteMessage remoteMessage) {
           MarketoExtension.showPushNotificaiton(remoteMessage);
           //Add your code here
         }
         }
         ```

         1. 修改位置外掛程式/cordova-plugin-fcm/scripts中的「fcm_config_files_process.js」檔案，如下所示

            ```
            //change
            var strings = fs.readFileSync("platforms/android/res/values/strings.xml").toString();
            //to
            var strings = fs.readFileSync("platforms/android/app/src/main/res/values/strings.xml").toString();
            
            //AND change
            fs.writeFileSync("platforms/android/res/values/strings.xml", strings);
            //to
            fs.writeFileSync("platforms/android/app/src/main/res/values/strings.xml", strings);
            ```

### &#x200B;3. 在xCode中啟用推播通知

在xCode專案中開啟推播通知功能。

### &#x200B;4. 追蹤推播通知

將下列程式碼貼入`application:didFinishLaunchingWithOptions:`函式中。

>[!BEGINTABS]

>[!TAB 目標C]

更新`applicationDidBecomeActive`方法，如下所示。

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

更新`applicationDidBecomeActive`方法，如下所示。

```swift
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotification(launchOptions)
```

>[!ENDTABS]

### &#x200B;5. 初始化Marketo框架

若要在應用程式啟動時初始化Marketo架構，請在JavaScript主檔案的`onDeviceReady`函式下新增下列程式碼。

傳遞`phonegap`作為PhoneGap應用程式的架構型別。

### 語法

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

### 參數

- Success回呼：Marketo架構成功初始化時要執行的函式。
- 失敗回呼：當Marketo架構初始化失敗時要執行的函式。
- Munchkin ID：註冊期間從Marketo收到的Munchkin ID。
- 秘密金鑰：註冊期間從Marketo收到的秘密金鑰。

### &#x200B;6. 初始化Marketo推播通知

若要初始化Marketo推播通知，請在主要JavaScript檔案中的初始化函式後新增下列程式碼。

### 語法

```javascript
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

### 參數

- Success回呼：Marketo推播通知成功初始化時要執行的函式。
- 失敗回呼：當Marketo推播通知初始化失敗時要執行的函式。
- GCM_PROJECT_ID：建立應用程式後，在[Google開發人員主控台](https://console.developers.google.com/)中找到GCM專案ID。

您也可以在登出時取消註冊Token。

```javascript
marketo. uninitializeMarketoPush(
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
lead[marketo.KEY_FIRST_NAME] = "Phone";
lead[marketo.KEY_LAST_NAME] = "Gap";
lead[marketo.KEY_EMAIL] = email;
lead[marketo.KEY_ADDRESS] = "demo address";
lead[marketo.KEY_CITY] = "city";
lead[marketo.KEY_STATE] = "state";
lead[marketo.KEY_COUNTRY] = "country";
lead[marketo.KEY_POSTAL_CODE] = "postalCode";
lead[marketo.KEY_GENDER] = "gender";
// To use lead custom field, use the REST API NAME as key
lead["REST API NAME"] = "value";

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
