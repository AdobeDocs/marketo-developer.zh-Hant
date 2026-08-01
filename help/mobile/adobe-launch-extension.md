---
title: ' [!DNL Adobe Launch]的Marketo Mobile擴充功能'
feature: Mobile Marketing
description: 在適用於Marketo和Android的Adobe Launch中安裝並設定iOS Mobile SDK擴充功能，包括推播通知和應用程式內訊息的設定。
exl-id: 2f8691ff-0442-45a5-aeba-c91c3af5c711
TQID: https://experienceleague.adobe.com/Bk5GTnQjm6NDosl5Iw6TS-NRjH8owNRUKoE0mZ-H3pY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 303
ht-degree: 0%

---

# [!DNL Adobe Launch]的Marketo Mobile擴充功能

在[!DNL Adobe Launch]中安裝Marketo Mobile SDK擴充功能，以傳送推播通知、應用程式內訊息或兩者。

## 先決條件

- [在Marketo Admin](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中新增應用程式，並取得應用程式秘密金鑰和Munchkin ID。
- 遵循[!DNL Adobe Launch]入口網站中的安裝指示。
- 選用： [設定推播通知](push-notifications.md)。

## iOS

### 設定Swift橋接標題

1. 前往「檔案>新增>檔案」，然後選取「標頭檔案」。
1. 將檔案命名為「&lt;_ProjectName_>-Bridging-Header」。
1. 前往「專案>目標>建置階段> Swift編譯器>程式碼產生」。
1. 將下列路徑新增至Objective-Bridging標頭：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

若為Swift，請移除下列匯入陳述式，因為上述步驟會新增橋接標頭。

`import Marketo/ALMarketo`

### iOS測試裝置

請依照[新增iOS測試裝置](installation.md#ios_test_devices)中的指示操作。

### 在AppDelegate中處理自訂Url型別

遵循[自訂URL指示](installation.md#ios_test_devices)。

### 在iOS上設定推播通知

遵循[推播通知指示](push-notifications.md)。 使用類別名稱「ALMarketo」而非「Marketo」。

## Android

### 設定許可權

開啟`AndroidManifest.xml`並新增下列許可權。 您的應用程式必須要求「網際網路」和「ACCESS_NETWORK_STATE」許可權。 如果應用程式已要求，請略過此步驟。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard組態（選購）

如果您的應用程式使用ProGuard，請在專案資料夾的`proguard.cfg`檔案中加入下列各行。 此設定會排除Marketo SDK的模糊化功能。

```text
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android測試裝置

請依照[Android測試裝置](installation.md#android_test_devices)中的指示操作。

## 在Android上設定推播通知

遵循[Android Firebase Cloud Messaging指示](installation.md#android_firebase_cloud_messaging_support)。 使用類別名稱「ALMarketo」而非「Marketo」。

若要設定使用者設定檔，請遵循[使用者設定檔指示](user-profiles.md)。 若要設定自訂動作，請依照[自訂動作指示](custom-actions.md#android_custom_action)執行。 在這兩組指示中，請使用類別名稱「ALMarketo」而非「Marketo」。
