---
title: 應用程式內訊息
feature: Mobile Marketing
description: 應用程式內訊息概觀
exl-id: 73c9f862-d154-4b37-94ce-92311aa756e8
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '293'
ht-degree: 0%

---

# 應用程式內訊息

若要使用Marketo的應用程式內傳訊功能，您必須執行下列步驟：

1. 依照[行動安裝](installation.md)中的說明安裝Marketo Mobile SDK。
1. 依照[新增行動應用程式](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中所述，將行動應用程式新增至Marketo。
1. 選擇性地新增程式碼至您的行動應用程式以擷取[自訂動作](custom-actions.md)。

安裝Marketo Mobile SDK並完成在Marketo中新增應用程式後，您就可以傳送應用程式內訊息，當使用者開啟您的應用程式時就會顯示。

依預設，應用程式會在開啟時觸發應用程式內訊息。 如果您想要針對其他事件（例如檢視特定頁面或按下特定按鈕時）觸發應用程式內訊息，您必須將自訂動作新增至程式碼。 如需此專案的程式碼範例，請參閱[自訂動作](custom-actions.md)區段。

## 疑難排解

**應用程式內訊息未顯示**

Marketo只有在使用Marketo平台初始化Marketo Mobile SDK後，才會回應應用程式的觸發器。 當您首次安裝並開啟應用程式時，就會進行初始化程式。 由於初始化會在第一個應用程式開啟後發生，因此要等到第二次開啟應用程式後才會觸發「應用程式開啟」事件。 關閉應用程式並再次開啟，裝置上應會顯示由「應用程式開啟」觸發的訊息。

自訂事件在應用程式開啟後由使用者互動觸發。 自訂事件在第一個工作階段中會由Marketo識別。

**應用程式內點選活動追蹤**

請務必指派除了主要或次要按鈕之一「關閉」以外的動作，以追蹤點選活動，並根據點選次數使用基本顯示頻率。

如需詳細資訊，請參閱產品檔案中的[應用程式內訊息](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message)區段。
