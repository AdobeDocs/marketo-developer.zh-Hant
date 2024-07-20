---
title: SOAP常見問題集
feature: SOAP
description: SOAP常見問題集
exl-id: a2d8f144-cd5f-41bc-8231-29c42af935b8
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '333'
ht-degree: 0%

---

# SOAP常見問題集

**問：**&#x200B;如何取得Marketo中所有程式的清單及其中繼資料？

**A：**&#x200B;若要擷取所有程式的清單，您可以使用[getMObjects](./getmobjects.md)，傳遞等於「Program」的型別，並將includeDetails設定為true。

**問：**&#x200B;是否有辦法加快getMultipleLeads的效能？

**A：**&#x200B;有幾個選項可以加快getMultipleLeads呼叫的效能。 第一個是減少您對每個呼叫請求的batchSize。 200是建議的批次大小。 第二個選項是使用includeAttributes篩選條件來指定您感興趣的欄位。 這可加快查詢速度，並減少您收到的回應負載。 最後的方法是使用LastUpdateAtSelector並指定oldestUpdatedAt和latestUpdatedAt。 您可以指定不同的日期範圍，然後同時執行多個請求。 如果使用執行緒方法，請確定您的SOAP/WSDL使用者端支援[持續連線](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html)。

**問：**&#x200B;未與SalesForce或Microsoft Dynamics等CRM整合時，如何透過SOAP API建立商機？

**A：**&#x200B;您可以使用寫入OpportunityPersonRole和機會[MObject](marketo-objects.md)型別的[syncMObjects](syncmobjects.md)呼叫，以SOAP API建立機會。

**問：**&#x200B;我是否可透過Marketo以程式設計方式傳送電子郵件？ 如果是，如何傳送每個電子郵件收件者的自訂內容？

**A：**，絕對是。 您可以使用[requestCampaign](requestcampaign.md)或[importToList](importtolist.md)與[scheduleCampaign](schedulecampaign.md) SOAP API的組合，要求從Marketo傳送電子郵件。 若要立即傳送電子郵件給一或多位人員，請使用[requestCampaign](requestcampaign.md)。 如果您想要排程在指定的日期和時間傳送電子郵件，您可以使用[importToList](importtolist.md)來指定電子郵件的收件者，並使用[scheduleCampaign](schedulecampaign.md)來指定這些收件者傳送該電子郵件的時間。

如果您想要自訂每個電子郵件收件者的內容，您可以覆寫在電子郵件範本中設定的[程式權杖](../rest-api/tokens.md)的值，以執行此操作。
