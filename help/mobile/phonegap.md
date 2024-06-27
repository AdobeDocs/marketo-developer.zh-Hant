---
title: PhoneGap
feature: Mobile Marketing
description: 在行動裝置上搭配使用PhoneGap與Marketo
exl-id: 99f14c76-9438-4942-9309-643bca434d07
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '797'
ht-degree: 0%

---

# PhoneGap

整合Marketo PhoneGap外掛程式

## 先決條件

1. [在Marketo管理中新增應用程式](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) （取得您的應用程式秘密金鑰和Munchkin ID）。
1. 設定推播通知([iOS](push-notifications.md) | [Android](push-notifications.md))。
1. [安裝PhoneGap/Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/).

## 安裝指示

1. 設定Marketo PhoneGap外掛程式

   假設您已安裝Cordova CLI，請前往PhoneGap應用程式目錄，然後執行下列命令，將Marketo外掛程式新增至您的應用程式：

   `$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 安裝FCM外掛程式

   `$ cordova plugin add cordova-plugin-fcm`

   若要確認外掛程式已新增至應用程式，請執行以下命令並驗證

   `$ cordova plugin ls com.marketo.plugin 0.X.0 "MarketoPlugin" cordova-plugin-fcm 2.1.2 "FCMPlugin"`

**遷移到較新版本（可選）**

若要移除現有外掛程式，請執行以下命令：

`$ cordova plugin remove com.marketo.plugin`

若要重新新增外掛程式，請執行以下命令：

`$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

**Cordova 8.0.0版(Cordova@Android7.0.0)及更高版本**

建置Cordova Android平台後，請使用Android Studio開啟應用程式並更新 `dirs` 的值 `Marketo.gradle` 在下列位置找到檔案： `com.marketo.plugin` 資料夾。

```
repositories{    
  jcenter()
  flatDir{
      dirs '../app/src/main/aar'
   }
}
```

新增應用程式的目標平台 `$cordova platform add android` `$ cordova platform add ios`

檢查新增的平台清單 `$cordova platform ls`

1. Firebase Cloud Messaging支援

1. 在Firebase主控台上設定Firebase應用程式。
   1. 建立/新增專案於 [](https://console.firebase.google.com/)Firebase主控台。
      1. 在 [Firebase主控台](https://console.firebase.google.com/)，選取 **[!UICONTROL Add Project]**.
      1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取 **[!UICONTROL Add Firebase]**.
      1. 在Firebase歡迎畫面中，選取「將Firebase新增至Android應用程式」。
      1. 提供您的封裝名稱和SHA-1，然後選取 **[!UICONTROL Add App]**. 新 `google-services.json` 已下載您Firebase應用程式的檔案。
   1. 瀏覽至 **[!UICONTROL Project Settings]** 在 [!UICONTROL Project Overview]
      1. 按一下 **[!UICONTROL General]** 標籤。 下載「google-services.json」檔案。
      1. 按一下 **[!UICONTROL Cloud Messaging]** 標籤。 複製 [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID]. 提供這些 [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID] 前往Marketo。
   1. 在Phonegap應用程式中設定FCM變更
      1. 將下載的&#39;google-services.json&#39;檔案移動到Phonegap應用程式模組的根目錄中
      1. 從位置移除&#39;MyFirebaseInstanceIDService&#39;檔案 `platforms/android/app/src/main/java/com/gae/scaffolder/plugin` （已棄用）
      1. 修改位置中的&#39;MyFirebaseMessagingService&#39;檔案 `platforms/android/app/src/main/java/com/gae/scaffolder/plugin` 如下所示：

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


### 3.在xCode中啟用推播通知

在xCode專案中開啟推播通知功能。

### 4.追蹤推播通知

將下列程式碼貼入 `application:didFinishLaunchingWithOptions:` 函式。

>[!BEGINTABS]

>[!TAB 目標C]

更新 `applicationDidBecomeActive` 方法如下

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

更新 `applicationDidBecomeActive` 方法如下

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotification(launchOptions)
```

>[!ENDTABS]

### 5.初始化Marketo架構

若要確保在應用程式啟動時啟動Marketo架構，請在底下新增下列程式碼 `onDeviceReady` JavaScript函式中。

請注意，我們必須通過 `phonegap` 作為PhoneGap應用程式的框架型別。

### 語法

```
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

### 引數

- Success回呼：在Marketo架構成功初始化時要執行的函式。
- 失敗回呼：當Marketo架構初始化失敗時要執行的函式。
- MUNCHKIN ID ：註冊時從Marketo收到的Munchkin ID。
- 秘密金鑰：註冊時從Marketo收到的秘密金鑰。

### 6.初始化Marketo推播通知

若要確保Marketo推播通知已啟動，請在主要JavaScript檔案中的初始化函式後新增下列程式碼。

### 語法

```
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

### 引數

- Success回呼：Marketo推播通知成功初始化時要執行的函式。
- 失敗回呼：當Marketo推播通知無法初始化時要執行的函式。
- GCM_PROJECT_ID ：在中找到GCM專案ID [Google開發人員主控台](https://console.developers.google.com/) 建立應用程式之後。

登出時也可以取消註冊權杖。

```
marketo. uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## 關聯銷售機會

您可以呼叫associateLead函式來建立Marketo Lead。

### 語法

```
marketo.associateLead(
  function(){ console.log("MarketoSDK : Lead Added"); },
  function(error){ console.log("an error occurred:" + error); },
  'Lead_Data_JSON_String'
);
```

### 引數

- Success回呼：當Marketo架構成功建立潛在客戶關聯時要執行的函式。
- 失敗回呼：當Marketo架構無法關聯潛在客戶時要執行的函式。
- 潛在客戶資料：JSON字串格式的潛在客戶資料。

### 範例

```
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

您可以呼叫「 」，報告使用者執行的任何動作 `reportaction` 函式。

### 語法

```
marketo.reportaction(
  function(){ console.log("MarketoSDK : New event sent "); },
  function(error){ console.log("an error occurred:" + error); },
  'Action_Name',
  'Action_Data_JSON_String'
);
```

### 引數

- 成功回呼：Marketo框架成功回報動作時要執行的函式。
- 失敗回呼：Marketo框架無法回報動作時要執行的函式。
- 動作名稱：動作名稱。
- 動作資料： JSON字串格式的動作資料。

### 範例

```
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

繫結「暫停」和「繼續」事件型別，如下所示，以報告「開始」和「停止」事件。  這可用來追蹤您在行動應用程式中花費的時間。 注意：這在Android中是必要的。

```
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

根據使用的方法，新建立的潛在客戶將由不同的觸發器和篩選器識別。 使用MME SDK或REST API建立的潛在客戶會出現在「建立的潛在客戶」觸發器和篩選器中。 由表單提交建立的潛在客戶會出現在「填寫表單」觸發器和篩選器中。

最佳實務建議在建立銷售機會時，與網頁應用程式使用的方法保持一致。 如果您已有使用表單提交作為建立銷售機會的機制之網頁應用程式，請在混合式應用程式中建立銷售機會時，使用該相同的機制。 如果您已有使用我們REST API作為建立銷售機會的機制的網頁應用程式，請在混合式應用程式中建立銷售機會時，使用該相同的機制。 如果您未使用表單提交或REST API做為在網頁應用程式中建立銷售機會的機制，可以考慮使用MME SDK在Marketo中建立銷售機會。
