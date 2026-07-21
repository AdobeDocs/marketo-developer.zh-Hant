---
title: 啟用深層連結
feature: Mobile Marketing
description: 瞭解如何使用自訂URI配置，並搭配使用iOS、Android以及PhoneGap指引和最佳實務，在您的應用程式中為Marketo推送訊息啟用深層連結。
exl-id: c3647416-d81d-4f15-b660-bcb3e54cb9bc
TQID: https://experienceleague.adobe.com/UswOvHXGlfTrTUqr4Gsf3j2Z7Xpv2FF2luXeygT4qE0
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 363
ht-degree: 1%

---

# 啟用深層連結

深層連結會將使用者引導至您應用程式中的特定內容。 例如，當使用者選取廣告為紫色T恤的行動推送訊息時，應用程式可開啟紫色T恤內容而非首頁。

程式的運作方式如下：

1. Marketo使用者會在推送訊息的點選動作中放置自訂URI。
1. 當使用者在裝置上點選推送訊息時，Marketo MME SDK會觸發具有自訂URI的事件。
1. 您的應用程式會處理事件，並將人員導向對應的內容。

若要啟用此程式：

1. 為您的應用程式定義自訂URI結構。
1. 在應用程式資訊清單中註冊配置。
1. 新增處理深層連結事件並將人員路由至相應內容的程式碼。

若為iOS，請參閱有關[為您的應用程式定義自訂URL配置](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)的Apple檔案。

如需Android的相關資訊，請參閱有關[啟用應用程式內容的深層連結](https://developer.android.com/training/app-links/deep-linking)的Google檔案。

若為PhoneGap應用程式，請使用外掛程式讓您的混合式應用程式能夠回應自訂URL配置以及iOS和Android上的通用/應用程式連結。 檢視可用的[深層連結外掛程式](https://cordova.apache.org/plugins/?q=deeplink)。

當您已在應用程式中啟用深層連結時，請將自訂URI與Marketo使用者共用，好讓他們可以將這些URI插入推送訊息的「點選動作」 。

設定測試裝置時，Marketo會使用預先定義的URI結構。 如需詳細資訊，請參閱[安裝指南](installation.md)中的[測試裝置]。

## 定義URI結構的最佳做法

如果您的品牌有行動網站，請在定義深層連結URI時遵循其URL結構。 例如，如果產品URL是`https://myappname.com/products/purple-shirt`，請使用`myappname://products/purple-shirt`做為對應的深層連結URI。

使用您的品牌專屬的配置。 雖然沒有規則要求配置必須是全域唯一的，但您可以反轉您的網域名稱（例如`org.companyname`）來協助建立唯一配置。
