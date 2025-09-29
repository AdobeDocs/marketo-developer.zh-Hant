---
title: '[!DNL Adobe Launch]延伸模組安裝'
feature: Mobile Marketing
description: 安裝行動裝置專用的Adobe Launch Marketo擴充功能。 請依照iOS和Android的設定、測試裝置、許可權，以及FCM步驟，執行推播和應用程式內訊息。
exl-id: d71b7cd7-309b-4882-9bba-7daaaa5ef32d
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '684'
ht-degree: 0%

---

# [!DNL Adobe Launch]延伸模組安裝

[!DNL Adobe Launch] Marketo擴充功能的安裝指示。 傳送推播通知及/或應用程式內訊息需執行以下步驟。

## 先決條件

1. [在Marketo Admin中新增應用程式](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (取得您的應用程式秘密金鑰和Munchkin ID)
1. [在 [!DNL Adobe Launch] 入口網站](https://experience.adobe.com/#/@amc/data-collection/home)中設定屬性
1. 設定[!DNL Adobe Launch]入口網站中屬性的應用程式秘密金鑰和Munchkin ID
1. [設定推播通知](push-notifications.md) （選擇性）

## 如何在iOS上安裝Marketo擴充功能

### 設定Swift橋接標題

1. 前往「[!UICONTROL File] > [!UICONTROL New] > [!UICONTROL File]」並選取「**[!UICONTROL Header File]**」。

1. 將檔案命名為「&lt;_ProjectName_>-Bridging-Header」。

1. 前往「[!UICONTROL Project] > [!UICONTROL Target] > [!UICONTROL Build Settings] > [!UICONTROL Swift Compiler] > [!UICONTROL Code Generation]」。 將下列路徑新增至「目標橋接」標頭：

`$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

## 初始化擴充功能

>[!BEGINTABS]

>[!TAB 目標C]

更新`applicationDidBecomeActive`方法，如下所示

```
(void)applicationDidBecomeActive:(UIApplication*) application
{
 [[ALMarketo sharedInstance] initializeMarketo:nil];
}
```

>[!TAB Swift]

更新`applicationDidBecomeActive`方法，如下所示

```
func applicationDidBecomeActive(_ application: UIApplication)
{
 ALMarketo.sharedInstance().initializeMarketo(nil)
}
```

>[!ENDTABS]

## iOS測試裝置

1. 選取&#x200B;**[!UICONTROL Project]** > **[!UICONTROL Target]** > **[!UICONTROL Info]** > **[!UICONTROL URL Types]**。
1. 新增識別碼： ${PRODUCT_NAME}
1. 設定URL配置： mkto-&lt;S_Ecret Key_>
1. 包含`application:openURL:sourceApplication:annotation:`到`AppDelegate.m file` (Objective-C)

### 在AppDelegate中處理自訂Url型別

>[!BEGINTABS]

>[!TAB 目標C]

```
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

```
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    return ALMarketo.sharedInstance().application(application, open: url, sourceApplication: nil, annotation: nil)
}
```

>[!ENDTABS]

## 如何在Android上安裝Marketo SDK

### Android擴充功能設定

遵循[!DNL Adobe Launch]入口網站中的指示

### 設定許可權

開啟`AndroidManifest.xml`並新增下列許可權。 您的應用程式必須要求「網際網路」和「ACCESS_NETWORK_STATE」許可權。 如果您的應用程式已要求這些許可權，請略過此步驟。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

## 初始化擴充功能

ProGuard組態（選購）

如果您正在使用應用程式的ProGuard，請在`proguard.cfg`檔案中新增下列行。 檔案位於您的`project`資料夾中。 新增此程式碼會將Marketo SDK排除在模糊化程式之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

## Android  測試  裝置

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

適用於Android的MME Software Development Kit (SDK)已更新至更現代、穩定及可擴充的架構，其中包含更優異的彈性和全新的工程功能，適合您的Android應用程式開發人員使用。

Android應用程式開發人員現在可以直接將Google的[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM)與此SDK搭配使用。

### 新增FCM至您的應用程式

1. 在Android應用程式中整合最新的Marketo Android SDK 。  步驟可在[GitHub](https://github.com/Marketo/android-sdk)取得。
1. 在Firebase主控台上設定Firebase應用程式。
   1. 在[](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase主控台上建立/新增專案。
      1. 在[Firebase主控台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中，選取&#x200B;**[!UICONTROL Add Project]**。
      1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase歡迎畫面中，選取&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
      1. 提供您的封裝名稱和SHA-1，然後選取&#x200B;**[!UICONTROL Add App]**。 已下載您Firebase應用程式的新`google-services.json`檔案。
      1. 選取&#x200B;**[!UICONTROL Continue]**&#x200B;並依照在Android Studio中新增Google Services外掛程式的詳細指示操作。

   1. 在&#x200B;**[!UICONTROL Project Settings]**&#x200B;中導覽至[!UICONTROL Project Overview]
      1. 按一下「**[!UICONTROL General]**」標籤。 下載 `google-services.json` 檔案。
      1. 按一下「**[!UICONTROL Cloud Messaging]**」標籤。 複製[!UICONTROL Server Key]和[!UICONTROL Sender ID]。 提供這些[!UICONTROL Server Key]和[!UICONTROL Sender ID]給Marketo。
   1. 在Android應用程式中設定FCM變更
      1. 切換至Android Studio中的專案檢視，以檢視您的專案根目錄
         1. 將下載的`google-services.json`檔案移至您的Android應用程式模組根目錄
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

         1. 最後，按一下ID中所顯示列中的&#x200B;**[!UICONTROL Sync now]**
   1. 編輯應用程式的資訊清單FCM SDK會自動新增所有必要許可權和必要的接收器功能。 請務必從應用程式的資訊清單中移除下列過時的（且可能有害，因為它們可能會導致訊息重複）元素：

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

關於Firebase雲端訊息支援的常見問題。

**問：我可以在哪裡找到更新至MME SDK最新版本的指示？在Marketo開發人員網站**&#x200B;此處[找到](installation.md)指示。

**問：更新至SDK最新版本會要求我將Android應用程式的更新版本發佈給現有的使用者嗎？**&#x200B;否

**問：這對已發佈與Marketo Android SDK整合之Android應用程式的現有MME客戶有何影響？**&#x200B;他們可以將Android上的現有GCM使用者端應用程式移轉至Firebase Cloud Messaging (FCM)，如下所示：

1. 在[Firebase主控台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中，選取&#x200B;**[!UICONTROL Add Project]**。
1. 從現有Google Cloud專案清單中選取您的GCM專案，然後選取&#x200B;**[!UICONTROL Add Firebase]**。
1. 在Firebase歡迎畫面中，選取&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
1. 提供您的封裝名稱和SHA-1，然後選取&#x200B;**[!UICONTROL Add App]**。 適用於您的新google-services.json檔案
1. Firebase應用程式已下載。
1. 選取&#x200B;**[!UICONTROL Continue]**&#x200B;並依照在Android Studio中新增Google Services外掛程式的詳細指示操作。

**問：我們可以鎖定使用GCM應用程式的舊版Marketo SDK所建立的潛在客戶嗎？**&#x200B;是。 所有使用Marketo SDK建立的銷售機會都可以定位為傳送推播通知。
