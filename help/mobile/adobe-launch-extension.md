---
title: ' [!DNL Adobe Launch]的Marketo Mobile擴充功能'
feature: Mobile Marketing
description: 在適用於Marketo和Android的Adobe Launch中安裝並設定iOS Mobile SDK擴充功能，包括推播通知和應用程式內訊息的設定。
exl-id: 2f8691ff-0442-45a5-aeba-c91c3af5c711
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '287'
ht-degree: 1%

---

# [!DNL Adobe Launch]的Marketo Mobile擴充功能

[!DNL Adobe Launch]中Marketo Mobile SDK擴充功能的安裝指示。 傳送推播通知及/或應用程式內訊息需執行以下步驟。

## 先決條件

- [在Marketo Admin中新增應用程式](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (取得您的應用程式秘密金鑰和Munchkin ID)
- 依照[!DNL Adobe Launch]入口網站提供的指示進行安裝
- [設定推播通知](push-notifications.md) （選擇性）

## iOS

### 設定Swift橋接標題

1. 前往「檔案>新增>檔案」，然後選取「標頭檔案」。
1. 將檔案命名為「&lt;_ProjectName_>-Bridging-Header」。
1. 前往「專案>目標>建置階段> Swift編譯器>程式碼產生」。 將下列路徑新增至Objective-Bridging標頭：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

Swift使用者：移除下列匯入陳述式，因為在上述步驟中新增了橋接標頭。

`import Marketo/ALMarketo`

### iOS測試裝置

遵循[新增iOS測試裝置](installation.md#ios_test_devices)的指示

### 在AppDelegate中處理自訂Url型別

在[這裡](installation.md#ios_test_devices)遵循指示

### 在iOS上設定推播通知

依照指示[這裡](push-notifications.md)並使用類別名稱「ALMarketo」而不是「Marketo」

## Android

### 設定許可權

開啟`AndroidManifest.xml`並新增下列許可權。 您的應用程式必須要求「網際網路」和「ACCESS_NETWORK_STATE」許可權。 如果您的應用程式已要求這些許可權，請略過此步驟。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard組態（選購）

如果您正在使用應用程式的ProGuard，請在`proguard.cfg`檔案中新增下列行。 檔案位於您的專案資料夾中。 新增此程式碼會將Marketo SDK排除在模糊化程式之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android測試裝置

在[這裡](installation.md#android_test_devices)遵循指示

## 在Android上設定推播通知

依照指示[這裡](installation.md#android_firebase_cloud_messaging_support)並使用類別名稱「ALMarketo」而不是「Marketo」

若要設定使用者設定檔，請遵循指示[這裡](user-profiles.md)，若要自訂動作，請遵循指示[這裡](custom-actions.md#android_custom_action)。 在下列指示中，使用類別名稱「ALMarketo」而非「Marketo」
