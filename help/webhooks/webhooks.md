---
title: Webhooks
feature: Webhooks
description: 瞭解如何設定Marketo Webhook以呼叫協力廠商服務、設定裝載範本、編碼、回應對應、Token、自訂標題和提示。
exl-id: fd283c66-05a1-4aa4-8412-0d41b8d1e3c8
TQID: https://experienceleague.adobe.com/r-GpAqhYPKvlDtMw5l23jeJWzlSqycP65eYJPA3m9EM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
subfeature_v2:
  - id: ad89fb33-8541-4339-afe7-bb13d1633714
  - id: fc9b09fe-b844-4544-887b-e420c3b82065
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 590
ht-degree: 3%

---

# Webhooks

Marketo webhook與協力廠商Web服務通訊。 webhook會使用GET或POST HTTP動詞來傳送資料至特定URL或從中擷取資料。

如需建立webhook並將其新增至Smart Campaign的說明，請參閱：

- [建立 Webhook](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook)
- [呼叫 Webhook](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/call-webhook)
- [在智慧行銷活動中使用 Webhook](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/use-a-webhook-in-a-smart-campaign)

使用下列屬性設定每個webhook：

- **[!UICONTROL URL]** — 您提交Web服務要求的URL。
- **[!UICONTROL Request Type]** - HTTP方法。
- **[!UICONTROL Payload Template]** - POST本文中傳送之資訊的範本。 使用支援HTTP POST的任何資料格式，包括XML、JSON或SOAP。 序列化格式必須在字串周圍加上雙引號。 若要插入權杖，請選取&#x200B;**[!UICONTROL Insert Token]**。 Marketo會自動將字串型別的Token括在雙引號中。
- **[!UICONTROL Request Token Encoding]** — 請求格式（JSON或表單/URL），用於編碼包含&amp;等特殊字元的權杖值。 選取正確的主體編碼，讓webhook可正確與Web服務通訊。
- **[!UICONTROL Response Type]** — 回應格式，JSON或XML。 選取正確的型別，將回應屬性對應至Marketo中的潛在客戶欄位。
- **[!UICONTROL Custom Headers]** — 透過&#x200B;**[!UICONTROL Webhooks Actions]** > **[!UICONTROL Set Custom Header]**&#x200B;新增為HTTP標題的機碼值組。 您可以新增任意數量的自訂標頭。

使用[回應對應](response-mappings.md)將資料從Web服務回應寫入回潛在客戶。

## 權杖

所有傳出webhook欄位（包括URL、範本和自訂標題）都會將權杖內容填入與流程步驟相同的內容中。

銷售機會和系統代號一律可用。 觸發器、行銷活動和方案代號可在各自的範圍中使用。 如需詳細資訊，請參閱：

- [權杖概觀](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/demand-generation/landing-pages/personalizing-landing-pages/tokens-overview)
- [系統權杖字彙表](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/email-marketing/general/using-tokens/system-tokens-glossary)
- [有趣時刻的Token](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-sales-insight/msi-for-salesforce/features/tabs-in-the-msi-panel/interesting-moments/trigger-tokens-for-interesting-moments)

例如，當方案或促銷活動對應至協力廠商資源時，請在方案層級將ID設定為`My Token`。 然後將ID當做權杖傳遞到webhook請求中。

## 自訂標頭

Webhook可使用傳出請求傳送任意數量的自訂標題欄位。 透過&#x200B;**[!UICONTROL Webhooks Actions]** > **[!UICONTROL Set Custom Header]**&#x200B;新增標題。

每個標頭都是一個索引鍵/值組，可包含代號。

![自訂標頭](assets/custom-headers.png)

## 提示

- 僅在觸發行銷活動中使用「呼叫Webhook」流程步驟。
- 只有在Web服務傳回2xx HTTP回應代碼時，回應對應才會更新記錄。
- 您可以使用Web服務，從內部或外部服務執行自訂資料擴充、驗證或標準化。
- Webhook執行時間取決於服務的回應時間，並可能導致較長的行銷活動執行延遲。 即使服務只需要50毫秒即可執行，100,000次執行仍需要1.5小時。
- Marketo在終止呼叫（也稱為逾時）之前，最多會等待指定的服務呼叫30秒。
- Marketo會以寫入的方式傳遞URL欄位中的字元。 例如，&#39;&amp;&#39;是以&#39;&amp;&#39;傳送，而&#39;%26&#39;是以&#39;%26&#39;傳送。
  - 若要將百分比編碼的字元傳送至收件者伺服器，請明確傳遞代表該字元的字串。
