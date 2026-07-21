---
title: 應用程式內訊息
feature: Mobile Marketing
description: 使用Mobile SDK設定Marketo應用程式內訊息、設定自訂事件觸發器、追蹤點選活動，以及修正首次開啟應用程式的初始化問題。
exl-id: 73c9f862-d154-4b37-94ce-92311aa756e8
TQID: https://experienceleague.adobe.com/RVkEUBaFb-PHd0gE9ngzYc5zOojINwSI7ic2TmcU7-8
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: c1579802-ddd4-4214-8a91-97b2066abe11
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 321
ht-degree: 2%

---

# 應用程式內訊息

完成下列步驟即可使用Marketo應用程式內傳訊：

1. 依照[行動安裝](installation.md)中的說明安裝Marketo Mobile SDK。
1. 依照[新增行動應用程式](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中所述，將行動應用程式新增至Marketo。
1. 可選：新增程式碼至您的行動應用程式以擷取[自訂動作](custom-actions.md)。

安裝Marketo Mobile SDK並將應用程式新增至Marketo後，您就可以傳送當使用者開啟您的應用程式時所顯示的應用程式內訊息。

依預設，應用程式開啟時會觸發應用程式內訊息。 若要針對其他事件觸發訊息，例如檢視特定頁面或選取特定按鈕，請在程式碼中新增自訂動作。 如需程式碼範例，請參閱[自訂動作](custom-actions.md)。

## 疑難排解

**應用程式內訊息未顯示**

Marketo只有在使用Marketo平台初始化Marketo Mobile SDK後，才會回應應用程式觸發程式。 當您首次安裝並開啟應用程式時，就會進行初始化。

由於初始化會在第一個應用程式開啟後進行，因此要等到您第二次開啟應用程式後，才會觸發「應用程式開啟」事件。 關閉並重新開啟應用程式。 「應用程式開啟」所觸發的訊息應隨即顯示在您的裝置上。

自訂事件在應用程式開啟後由使用者互動觸發。 自訂事件在第一個工作階段中會由Marketo識別。

**應用程式內點選活動追蹤**

若要追蹤點選活動並以點選次數為基礎顯示頻率，請將「關閉」以外的動作指派給主要或次要按鈕。

如需詳細資訊，請參閱產品檔案中的[應用程式內訊息](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message)。
