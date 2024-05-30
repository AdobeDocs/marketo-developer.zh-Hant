---
title: 「SOAP常見問題集」
feature: SOAP
description: 「SOAP常見問題集」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '333'
ht-degree: 0%

---


# SOAP常見問題集

**問：** 如何取得Marketo中所有計畫的清單及其中繼資料？

**答：** 若要擷取所有程式的清單，您可以使用 [getMObjects](./getmobjects.md) 將型別傳遞等於「Program」並將includeDetails設定為true。

**問：** 有辦法提高getMultipleLeads的效能嗎？

**答：** 有幾個選項可以加快getMultipleLeads呼叫的效能。 第一個是減少您對每個呼叫請求的batchSize。 200是建議的批次大小。 第二個選項是使用includeAttributes篩選條件來指定您感興趣的欄位。 這可加快查詢速度，並減少您收到的回應負載。 最後的方法是使用LastUpdateAtSelector並指定oldestUpdatedAt和latestUpdatedAt。 您可以指定不同的日期範圍，然後同時執行多個請求。 如果使用執行緒方法，請確定您的SOAP/WSDL使用者端支援 [持續連線](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html).

**問：** 與SalesForce或Microsoft Dynamics等CRM未整合時，如何透過SOAP API建立機會？

**答：** 您可以使用SOAP API透過以下方式建立商機 [syncMObjects](syncmobjects.md) 呼叫寫入OpportunityPersonRole和Opportunity [物件](marketo-objects.md) 型別。

**問：** 我是否能夠以程式設計方式從Marketo傳送電子郵件？ 如果是，如何傳送每個電子郵件收件者的自訂內容？

**答：** 當然可以。 您可以使用以下任一項，請求從Marketo傳送電子郵件 [requestCampaign](requestcampaign.md) 或組合 [importToList](importtolist.md) 和 [scheduleCampaign](schedulecampaign.md) SOAP API。 若要立即傳送電子郵件給一或多人，請使用 [requestCampaign](requestcampaign.md). 如果您想要排程在指定的日期和時間傳送電子郵件，您會使用 [importToList](importtolist.md) 指定電子郵件的收件者，以及 [scheduleCampaign](schedulecampaign.md) 以指定這些收件者傳送該電子郵件的時間。

如果您想要自訂每個電子郵件收件者的內容，可以覆寫的值 [計畫權杖](../rest-api/tokens.md) 在電子郵件範本中設定的。
