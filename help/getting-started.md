---
title: 快速入門
description: 開始使用Marketo Engage API和資料模型，包括銷售機會、活動、方案、標籤、清單、REST指引和SOAP淘汰通知。
exl-id: 78c44c32-4e59-4d55-a45c-ef0d7dac814d
TQID: https://experienceleague.adobe.com/0lfzor5EQJ0VqIh4fqlK29OiPmRCy6fnEtncJ38r-OM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c954475c-8548-4e33-a0b8-6b550d956115
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1225
ht-degree: 2%

---

# 快速入門

Marketo Engage是行銷自動化平台，用於為潛在客戶和客戶管理個人化的多頻道方案和行銷活動。 您可以透過其整合點擴充平台。

本頁介紹核心Marketo Engage實體及其關係。

>[!NOTE]
>
>SOAP API即將淘汰，自2026年7月31日起將不再提供使用。 針對所有新開發使用Marketo [REST API](./rest-api/rest-api.md)。 在此日期之前移轉現有服務，以避免服務中斷。 如果服務使用SOAP API，請參閱SOAP API [移轉指南](./soap-api/migration.md)。
>

在Marketo Engage執行個體上啟用原生SFDC或MS Dynamics CRM連線時，這些物件為唯讀：

- 公司
- 機會
- 機會角色
- 銷售人員

![資料模型](assets/data_model.png)

## 人員（銷售機會）

人是行銷自動化的基礎。 Marketo將所有非銷售人員記錄稱為銷售機會，無論銷售人員是否將其視為銷售機會、潛在客戶、疑犯或聯絡人。

潛在客戶物件包含標準欄位，例如電子郵件、名字和姓氏。 您可以新增欄位以儲存其他資訊，也可以以與標準欄位相同的方式讀取和寫入自訂屬性。 在Marketo的&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Field Management]**&#x200B;下尋找完整的欄位清單。

Marketo會透過id欄位唯一識別銷售機會。 您必須在系統外強制實行其他唯一索引鍵。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads)，[JavaScript](javascript-api/lead-tracking.md#lead-tracking-api)

## 活動

潛在客戶可與貴組織透過多種方式互動，例如造訪網頁、參加貿易展會或下載白皮書。 Marketo會擷取這些動作作為活動，讓行銷人員可以瞭解潛在客戶做了什麼，以及何時發生。

活動一律與leadId的銷售機會相關。

您也可以定義自訂活動。 建立並發佈自訂活動後，您可以透過Marketo API新增其例項。 如需詳細資訊，請參閱[瞭解自訂活動](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/marketo-custom-activities/understanding-custom-activities)。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities)，[JavaScript](javascript-api/lead-tracking.md#munchkin-behavior)

## 方案與行銷活動

計畫在一個位置組織行銷人員的相關行銷工作。 例如，電子郵件爆炸可以是計畫。

潛在客戶可以執行與方案相關聯的多個動作或活動。 此程式稱為潛在客戶進度。 對於電子郵件快速啟動計畫，進度可以記錄Marketo傳送電子郵件的時間、使用者開啟電子郵件的時間，以及使用者是否按一下連結。

行銷活動在方案中用於特定目的和目標。 例如，行銷活動可以選取一組銷售機會並傳送電子郵件爆炸訊息。 當潛在客戶在電子郵件爆炸中點按連結時，另一個Campaign可以通知銷售代表。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns)

## 標記

標籤可為報表將程式資料分組和分類。 使用標籤來評估計畫成效和ROI。

身為Marketo管理員，您可以建立使用者在建立方案時選取的必要和選用標籤型別。 您可以根據公司的報告需求來定義每個標籤型別的可能值。

例如，使用值（例如Northeast和Southeast）建立自訂「區域」標籤型別，以分析哪個區域產生最多銷售機會。 建立「擁有者」標籤型別，比較哪些計畫擁有者（例如Maria、David或John）對建立銷售機會和機會的影響最大。 如需詳細資訊，請參閱[瞭解標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/understanding-tags)。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/asset)

## 清單

列出組織潛在客戶集合。 Marketo提供兩種型別：

- 靜態清單是行銷人員可以新增或移除潛在客戶的固定集合。
- 智慧清單是根據定義特性的動態集合。

例如，名為「所有造訪過我們網站定價頁面的潛在客戶」的智慧清單，會隨著更多潛在客戶造訪該頁面而持續成長。 如需詳細資訊，請參閱[Marketo Engage檔案](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists)

## 機會

商機代表行銷人員提供給銷售人員的潛在銷售交易。 在Marketo中，機會與潛在客戶或聯絡人和組織相關聯。

商機角色將潛在客戶與組織連線起來，並描述潛在客戶在該組織中的功能。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities)

## 公司

組織（有時稱為Marketo中的帳戶）是個人所屬的組織。

如需Marketo ROI報告或Revenue Cycle Analytics (RCA)中的準確ROI歸因，請將人員與其組織和機會建立關聯。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Companies)

## 資產

Assets包含程式中使用的登入頁面、電子郵件、表單和影像。 資產可以是特定計畫的本機資產，也可以是全域資產。 全球資產適用於每個計畫。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/asset)

## 權杖

Token可讓行銷人員使用資產個人化訊息，並在流程動作中新增邏輯。 Marketo提供整個系統、計畫、銷售機會和公司的代號。

例如，將潛在客戶代號`{{lead.First Name}}`放入電子郵件中，以顯示潛在客戶的名字。

在方案或資料夾層級定義的代號，在Marketo中稱為「我的代號」。 我的Token有三種型別：

- 本機：建立於特定的行銷活動資料夾或方案，並僅供該資料夾或方案使用。
- 繼承：在行銷活動資料夾層級建立，可用於該資料夾中的所有計畫。
- 已覆寫：在方案層級使用自訂值修改，而不在方案資料夾層級變更父級「我的Token」值。

我的Token使用命名慣例`{{my.My Token}}`，在Token名稱的開頭加上「my」這個字。 例如，名為EventDate的日期型別「我的Token」的Token名稱為`{{my.EventDate}}`。 如需詳細資訊，請參閱[瞭解程式中的我的代號](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program)。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens)

## 自訂物件

Marketo自訂物件會在Marketo銷售機會與自訂物件記錄之間建立一對多或多對多(Edge-Bridge-Edge)關係。

建立並發佈Marketo自訂物件後，您可以透過Marketo API對其執行CRUD操作。 新增記錄時，您可以使用智慧清單觸發器來回應。 您也可以使用自訂物件資料作為智慧清單篩選條件，以分段，或透過[電子郵件指令碼](email-scripting.md)在電子郵件中使用。 如需建立自訂物件的詳細資訊，請參閱[Marketo Engage檔案](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects)

## 銷售人員

若未啟用原生CRM整合，您可以在Marketo中管理銷售人員記錄及其銷售機會關係。 這些記錄包含名稱、電子郵件和職稱等資訊。 當銷售人員擁有銷售機會時，您可以使用此資訊來篩選和代號。

透過「externalSalesPersonId」欄位，在潛在客戶層級管理與銷售人員的關係。 透過[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/syncLeadUsingPOST) API更新此欄位。

相關API： [REST](https://developer.adobe.com/marketo-apis/api/mapi#tag/Sales-Persons)
