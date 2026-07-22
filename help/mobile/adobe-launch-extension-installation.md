---
title: '[!DNL Adobe Launch]延伸模組安裝'
feature: Mobile Marketing
description: 安裝行動裝置專用的Adobe Launch Marketo擴充功能。 請依照iOS和Android的設定、測試裝置、許可權，以及FCM步驟，執行推播和應用程式內訊息。
exl-id: d71b7cd7-309b-4882-9bba-7daaaa5ef32d
TQID: https://experienceleague.adobe.com/UZRHaRBISIZsE6E25Ee7CnnYwyZwi6w2YgOQJ-JL00U
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 696
ht-degree: 1%

---

# [!DNL Adobe Launch]延伸模組安裝

安裝[!DNL Adobe Launch] Marketo擴充功能以傳送推播通知、應用程式內訊息，或兩者皆傳送。

## 先決條件

1. [在Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中新增應用程式，並取得應用程式秘密金鑰和Munchkin ID。
1. [在 [!DNL Adobe Launch] 入口網站](https://experience.adobe.com/#/@amc/data-collection/home)中設定屬性。
1. 設定[!DNL Adobe Launch]入口網站中屬性的應用程式秘密金鑰和Munchkin ID。
1. 選用： [設定推播通知](push-notifications.md)。

## 如何在iOS上安裝Marketo擴充功能

### 設定Swift橋接標題

1. 前往「[!UICONTROL File] > [!UICONTROL New] > [!UICONTROL File]」並選取「**[!UICONTROL Header File]**」。

1. 將檔案命名為「&lt;_ProjectName_>-Bridging-Header」。

1. 前往「[!UICONTROL Project] > [!UICONTROL Target] > [!UICONTROL Build Settings] > [!UICONTROL Swift Compiler] > [!UICONTROL Code Generation]」。
1. 將下列路徑新增至「目標橋接」標頭：

`$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

## 初始化擴充功能

>[!BEGINTABS]

>[!TAB 目標C]

更新`applicationDidBecomeActive`方法，如下所示。

```objectivec
(void)applicationDidBecomeActive:(UIApplication*) application
{
 [[ALMarketo sharedInstance] initializeMarketo:nil];
}
```

>[!TAB Swift]

更新`applicationDidBecomeActive`方法，如下所示。

```objectivec
func applicationDidBecomeActive(_ application: UIApplication)
{
 ALMarketo.sharedInstance().initializeMarketo(nil)
}
```

>[!ENDTABS]

## iOS測試裝置

1. 選取&#x200B;**[!UICONTROL Project]** > **[!UICONTROL Target]** > **[!UICONTROL Info]** > **[!UICONTROL URL Types]**。
1. 新增識別碼${PRODUCT_NAME}。
1. 將URL配置設定為mkto-&lt;S_Ecret Key_>。
1. 將Objective-C的`application:openURL:sourceApplication:annotation:`新增至`AppDelegate.m file`。

### 在AppDelegate中處理自訂Url型別

>[!BEGINTABS]

>[!TAB 目標C]

```objectivec
#ifdef __IPHONE_10_0
-(BOOL)application:(UIApplication *)application
           openURL:(NSURL *)url
           options:(NSDictionary *)options{
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
#endif

- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation {
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
```

>[!TAB Swift]

```objectivec
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    return ALMarketo.sharedInstance().application(application, open: url, sourceApplication: nil, annotation: nil)
}
```

>[!ENDTABS]

## 如何在Android上安裝Marketo SDK

### Android擴充功能設定

遵循[!DNL Adobe Launch]入口網站中的指示。

### 設定許可權

開啟`AndroidManifest.xml`並新增下列許可權。 您的應用程式必須要求「網際網路」和「ACCESS_NETWORK_STATE」許可權。 如果應用程式已要求，請略過此步驟。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

## 初始化擴充功能

ProGuard組態（選購）

如果您的應用程式使用ProGuard，請將下列行加入`project`資料夾中的`proguard.cfg`檔案。 此設定會排除Marketo SDK的模糊化功能。

```text
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

## Android測試裝置

將「MarketoActivity」新增至應用程式標籤內的`AndroidManifest.xml`。

```xml
<activity android:name="com.marketo.MarketoActivity"  android:configChanges="orientation|screenSize" >
    <intent-filter android:label="MarketoActivity" >
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto" />
    </intent-filter>
</activity>
```

## Firebase Cloud Messaging支援

適用於Android的MME SDK支援直接使用Google的[Firebase雲端通訊](https://firebase.google.com/docs/cloud-messaging/) (FCM)。

### 新增FCM至您的應用程式

1. 將最新的Marketo Android SDK整合至Android應用程式。 檢視[GitHub](https://github.com/Marketo/android-sdk)上的步驟。
1. 在Firebase主控台中設定Firebase應用程式。
   1. 在[&#128279;](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase主控台中建立或新增專案。
      1. 在[Firebase主控台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中，選取&#x200B;**[!UICONTROL Add Project]**。
      1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase歡迎畫面中，選取&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
      1. 提供您的封裝名稱和SHA-1，然後選取&#x200B;**[!UICONTROL Add App]**。 已下載您Firebase應用程式的新`google-services.json`檔案。
      1. 選取&#x200B;**[!UICONTROL Continue]**&#x200B;並依照在Android Studio中新增Google Services外掛程式的詳細指示操作。

   1. 在[!UICONTROL Project Overview]中移至&#x200B;**[!UICONTROL Project Settings]**。
      1. 選取「**[!UICONTROL General]**」索引標籤並下載`google-services.json`。
      1. 選取 **[!UICONTROL Cloud Messaging]** 索引標籤。 複製[!UICONTROL Server Key]和[!UICONTROL Sender ID]，並將其提供給Marketo。
   1. 在Android應用程式中設定FCM。
      1. 切換至Android Studio中的專案檢視，以顯示專案根目錄。
         1. 將下載的`google-services.json`檔案移至Android應用程式模組的根目錄中。
         1. 在專案層級`build.gradle`中新增下列專案：

            ```
            buildscript {
              dependencies {
                classpath 'com.google.gms:google-services:4.0.0'
              }
            }
            ```

         1. 在應用程式層級build.gradle中，新增下列內容：

            ```
            dependencies {
              compile 'com.google.firebase:firebase-core:17.4.0'
            }
            // Add to the bottom of the file
            apply plugin: 'com.google.gms.google-services'
            ```

         1. 在IDE中顯示的列中選取&#x200B;**[!UICONTROL Sync now]**。
   1. 編輯應用程式資訊清單。 FCM SDK會自動新增必要的許可權和接收者功能。 移除下列過時元素，這些元素可能會導致訊息重複：

      ```xml
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
      <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />
      
      ...
      
      <receiver>
        android:name="com.google.android.gms.gcm.GcmReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="<your-package-name> />
        </intent-filter>
      </receiver>
      ```

### FCM常見問題集

這些問題涵蓋Firebase Cloud Messaging支援。

**問：我可以在哪裡找到更新至MME SDK最新版本的指示？** 請參閱Marketo開發人員網站上的[安裝指示](installation.md)。

**問：更新至SDK最新版本需要我將Android應用程式的更新版本發佈給現有的使用者嗎？** 否。

**問：它如何影響擁有使用Marketo Android SDK的已發佈Android應用程式的現有MME客戶？** 將現有的Android GCM使用者端應用程式移轉至Firebase Cloud Messaging (FCM)，如下所示：

1. 在[Firebase主控台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中，選取&#x200B;**[!UICONTROL Add Project]**。
1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取&#x200B;**[!UICONTROL Add Firebase]**。
1. 在Firebase歡迎畫面中，選取&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
1. 提供您的封裝名稱和SHA-1，然後選取&#x200B;**[!UICONTROL Add App]**。 已下載適用於您Firebase應用程式的新google-services.json檔案。
1. 選取&#x200B;**[!UICONTROL Continue]**&#x200B;並依照在Android Studio中新增Google Services外掛程式的詳細指示操作。

**問：我們可以鎖定使用GCM應用程式的舊版Marketo SDK所建立的潛在客戶嗎？** 是的。 您可以鎖定使用Marketo SDK建立的所有銷售機會，以取得推播通知。
