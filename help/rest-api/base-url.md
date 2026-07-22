---
title: 基礎 URL
feature: REST API
description: 瞭解如何建立Marketo REST API要求、瞭解基本URL路徑資源和引數，並尋找您唯一的基本URL。
exl-id: 6c3f122c-3ace-4ed3-bed0-a6b89cedc99a
TQID: https://experienceleague.adobe.com/NZisV6V-FMPi0RHpdaFrc1kZc3nb15YomwRgohaQmEE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 142
ht-degree: 2%

---

# 基礎 URL

[端點參考](endpoint-reference.md)中的每個API呼叫都指定了REST方法、路徑、資源和引數。 將這些元件附加至基底URL以形成請求。

以下是格式正確的REST URL範例：

`https://284-RPR-133.mktorest.com/rest/v1/lead/318581.json?fields=email,firstName,lastName`

此範例包含下列元件：

- **基底URL：** `https://284-RPR-133.mktorest.com/rest`
- **路徑：** `/v1/lead/`
- **資源：** `318582.json`
- **查詢引數：** `fields=email,firstName,lastName`

基本URL包含帳戶ID （也稱為Munchkin ID），且為每個Marketo訂閱所專屬。

若要尋找基底URL，請登入Marketo並移至&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**。 基底URL在「REST API」區段中標示為「端點：」，如下圖所示。

![Web服務基底URL端點](assets/rest-api-base-url-web-services.png)

複製基底URL，並將其包含在每個REST API呼叫的URL中。
