---
title: Marketo Engage MCP作業
description: 瞭解哪些Marketo Engage MCP作業可搭配AI助理使用。
autotag-review: '2026-06-02T13:31:42.084Z'
TQID: 'https://experienceleague.adobe.com/qvrWbHOCsCCHctduNDxMhkE8JAKxZk8FCYfKvzxfcYA'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
    internal-label: Marketo Engage
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
    internal-label: Smart Campaigns
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
    internal-label: Forms
  - id: dca84292-69e9-4116-a575-667d31fa060d
    internal-label: APIs
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
    internal-label: Programs
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
    internal-label: Artificial intelligence
source-git-commit: 1cdb28422994b772a0fa430e213facaf26817952
workflow-type: tm+mt
source-wordcount: '260'
ht-degree: 0%
---

# [!DNL Marketo Engage] MCP作業

[!DNL Marketo Engage] MCP伺服器支援182項作業，涵蓋資產管理、銷售機會資料和行銷活動自動化。



## 支援的作業

您可以透過連線的AI助理來探索可用的作業。

如果您的連線公開探索工具，請使用以下提示：

* 「使用Marketo MCP `list_tool_categories`工具列出可用的作業類別。」
* 「使用Marketo MCP `search_tools`工具尋找管理智慧行銷活動的作業。」

用您需要的操作取代第二個提示中的工作。 如果探索工具未公開，請要求您的AI助理列出[!DNL Marketo] MCP伺服器上可用的工具。

結果會反映您連線的許可權和功能可用性。 它們說明可用的MCP工具，而不是完整的[!DNL Marketo] REST API。

對於使用[!DNL Marketo]使用者端認證的連線，不支援下列功能：

* 簡訊工具
* 推播通知工具
* 應用程式內訊息和應用程式內程式工具
* 使用`schedule_smart_campaign`排程智慧行銷活動
* 使用者管理和大量服務工具

使用者端認證連線支援`schedule_campaign`作業。

可用的操作取決於您的API使用者許可權、[!DNL Marketo]執行個體開啟的功能，以及MCP使用者端中的任何限制。 有些變更或刪除資料的作業需要確認和額外的存取權。 類別支援並不意味著要存取每個REST端點。

如需連線指示和範例提示，請參閱[MCP伺服器](mcp-server.md#available-operations)頁面。 如需有關如何使用[!DNL Marketo] AI和[!DNL Marketo Engage] MCP伺服器處理資料的資訊，請參閱[資料資訊](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/marketo-ai/data-information)頁面。