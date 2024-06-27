---
title: SOAP API
feature: SOAP
description: Marketo SOAP概觀
exl-id: 6618cc82-15ae-4030-aa00-438e635d8369
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '230'
ht-degree: 0%

---

# SOAP API

SOAP API不再處於作用中開發狀態。 呼叫仍可運作，但我們的開發重點為 [REST](https://developer.adobe.com/marketo-apis/) 至今以後。

Marketo SOAP API可建立、擷取和移除儲存在Marketo中的實體和資料。 您可找到 [Marketo-SOAP-SDK](https://github.com/Marketo/SOAP-API-Java-Client) 在GitHub上。 此外還有 [使用者端資料庫](https://github.com/Marketo/Community-Supported-Client-Libraries) 以節省您的時間。

最新API版本： 3_1

## SOAP WSDL

若要擷取SOAP SOAP WSDL檔案，請向 **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 功能表。

![SOAP端點](assets/endpoint-soap.png)

您的WSDL URL為：

`<SOAP API Endpoint> + ?WSDL`

請勿使用WSDL中定義的端點。 每個Marketo例項都有不重複的端點，可從中進行呼叫。

## 限制

- **每日配額：** 大部分的訂閱每天皆會分配10,000個API呼叫（這會在中部標準時間每天凌晨12:00重設）。 您可以透過帳戶管理員增加每日配額。
- **速率限制：** 每個執行個體的API存取限製為每20秒100次呼叫。
- **並行限制：**  最多十個同時進行的API呼叫。

我們建議批次大小不要超過300。 不支援較大尺寸，可能會導致逾時，並且在極端情況下會進行節流。

## Marketo中的SOAP API設定

1. 前往 **[!UICONTROL Admin]** 區段並按一下 **[!UICONTROL Web Services]**.

![admin-web-services2](assets/admin-web-services2.png)

1. 設定適當的 [!UICONTROL Encryption Key]，按一下 **[!UICONTROL Save Changes]** 和使用SOAP API [!UICONTROL Endpoint]， [!UICONTROL User ID]、和 [!UICONTROL Encryption Key] 值以產生正確的值 [驗證簽章](authentication-signature.md) 用於每個SOAP API呼叫。

![admin-web-services3](assets/admin-web-services3.png)
