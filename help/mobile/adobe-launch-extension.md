---
title: "適用於的Marketo Mobile Extension [!DNL Adobe Launch]"
feature: Mobile Marketing
description: '"適用於的Marketo Mobile Extension [!DNL Adobe Launch] 概述」'
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '269'
ht-degree: 0%

---


# 適用於的Marketo Mobile Extension [!DNL Adobe Launch]

中的Marketo Mobile SDK擴充功能安裝指示 [!DNL Adobe Launch]. 傳送推播通知及/或應用程式內訊息需執行以下步驟。

## 先決條件

- [在Marketo管理中新增應用程式](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) （取得您的應用程式秘密金鑰和Munchkin ID）
- 請依照 [!DNL Adobe Launch] 安裝入口網站
- [設定推播通知](push-notifications.md) （選擇性）

## iOS

### 設定Swift橋接標題

1. 前往「檔案>新增>檔案」，然後選取「標頭檔案」。
1. 將檔案命名為「&lt;」_專案名稱_>-Bridging-Header」。
1. 前往「專案>目標>建置階段> Swift編譯器>程式碼產生」。 將下列路徑新增至Objective-Bridging標頭：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

Swift使用者：移除下列匯入陳述式，因為在上述步驟中新增了橋接標頭。

`import Marketo/ALMarketo`

### iOS測試裝置

請依照以下位置的指示操作： [新增iOS測試裝置](installation.md#ios_test_devices)

### 在AppDelegate中處理自訂Url型別

遵循指示 [此處](installation.md#ios_test_devices)

### 在iOS上設定推播通知

遵循指示 [此處](push-notifications.md) 並使用類別名稱「ALMarketo」而非「Marketo」

## Android

### 設定許可權

開啟 `AndroidManifest.xml` 並新增下列許可權。 您的應用程式必須要求「網際網路」和「ACCESS_NETWORK_STATE」許可權。 如果您的應用程式已要求這些許可權，請略過此步驟。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard組態（選購）

如果您正在使用應用程式的ProGuard，請在您的應用程式中新增下列行 `proguard.cfg` 檔案。 檔案位於您的專案資料夾中。 新增此程式碼會將Marketo SDK排除在模糊化程式之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android測試裝置

遵循指示 [此處](installation.md#android_test_devices)

## 在Android上設定推播通知

遵循指示 [此處](installation.md#android_firebase_cloud_messaging_support) 並使用類別名稱「ALMarketo」而非「Marketo」

若要設定使用者設定檔，請遵循指示 [此處](user-profiles.md) 自訂動作則依照指示進行 [此處](custom-actions.md#android_custom_action). 在下列指示中，使用類別名稱「ALMarketo」而非「Marketo」
