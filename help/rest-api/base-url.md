---
title: "基礎URL"
feature: REST API
description: 「說明如何設定Marketo的URL。」
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 0%

---


# 基礎URL

此 [端點參考](endpoint-reference.md) 每個API呼叫的檔案都會顯示REST方法、路徑、資源和引數，這些必須附加至基底URL才能形成請求。

以下是格式正確的REST URL範例：

`https://284-RPR-133.mktorest.com/rest/v1/lead/318581.json?fields=email,firstName,lastName`

由下列部分所組成：

- 基本URL： `https://284-RPR-133.mktorest.com/rest`
- 路徑： `/v1/lead/`
- 資源： `318582.json`
- 查詢引數： `fields=email,firstName,lastName`

基底URL包含帳戶ID （亦即Munchkin ID），因此在每個Marketo訂閱中都是唯一的。 您可以登入Marketo並導覽至 **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 功能表。 如下列熒幕擷取畫面所示，「REST API」區段底下標示為「端點：」。

![網站服務基底URL端點](assets/rest-api-base-url-web-services.png)

找到基本URL後，請複製該URL，並將其納入您呼叫任何REST API時使用的URL中。
