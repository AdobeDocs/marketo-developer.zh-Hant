---
title: 啟用深層連結
feature: Mobile Marketing
description: 啟用深層連結的說明
exl-id: c3647416-d81d-4f15-b660-bcb3e54cb9bc
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '376'
ht-degree: 0%

---

# 啟用深層連結

深層連結可讓您將使用者重新導向至應用程式內的特定內容（資源）。 例如，當有人點按廣告宣傳紫色T恤的行動推送訊息時，您可以直接開啟紫色T恤內容的應用程式（而非首頁）。

程式的運作方式如下：

1. Marketo使用者會在其推送訊息的點選動作中放置自訂URI。
1. 當使用者在裝置上點選推送訊息時，Marketo MME SDK會觸發具有自訂URI的事件。
1. 您的應用程式接著會處理事件，並將人員重新導向至您應用程式內的適當內容。

這要求您為應用程式定義自訂URI結構；在應用程式的資訊清單中註冊配置；然後新增程式碼以處理深層連結事件並路由至應用程式中的適當位置。

若為iOS，請參閱有關[為您的應用程式定義自訂URL配置](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)的Apple檔案。

如需Android的相關資訊，請參閱有關[啟用應用程式內容的深層連結](https://developer.android.com/training/app-links/deep-linking)的Google檔案。

若為PhoneGap應用程式，深層連結不如原生iOS或Android應用程式直接連結，但外掛程式可讓您的混合式應用程式回應iOS和Android上的深層連結自訂URL配置和通用/應用程式連結。 考慮[這些外掛程式](https://cordova.apache.org/plugins/?q=deeplink)。

當您已在應用程式中啟用深層連結時，請將自訂URI與Marketo使用者共用，好讓他們可以將這些URI插入推送訊息的「點選動作」 。

設定測試裝置時，Marketo會使用預先定義的URI結構。 如需詳細資訊，請參閱[安裝指南](installation.md)的「測試裝置」一節。

## 定義URI結構的最佳做法

如果您的品牌已有行動網站，最佳做法是也遵循其深層連結URI的URL結構。 例如，如果`https://myappname.com/products/purple-shirt`是您相關產品的網站位址，則`myappname://products/purple-shirt`會是在您的應用程式中使用的良好深層連結URI結構。

一般而言，您的方案應該對您的品牌而言是唯一的。 雖然目前沒有規範可讓方案在全球範圍內是唯一的，但若要協助確保您的方案是唯一的，其中一個方法是反轉您的網域名稱（例如，`org.companyname`）。
