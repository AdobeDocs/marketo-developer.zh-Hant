---
title: 取得銷售機會API更新
feature: REST API
description: 瞭解Get Lead Activities和Get Lead Changes端點的限制變更。
source-git-commit: e71bcf289229867bc969345d79c8f014761aaaf9
workflow-type: tm+mt
source-wordcount: '356'
ht-degree: 0%

---

# 取得銷售機會API更新

從2026年9月30日開始，對[取得銷售機會活動](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadActivitiesUsingGET)或[取得銷售機會變更](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadChangesUsingGET)端點（包含`listId`引數）的呼叫將失敗，如果目標清單包含10,000個或更多銷售機會，且錯誤代碼為1003，表示目標靜態清單有太多記錄。 最近進行了一個或多個API呼叫，這些呼叫將受此變更影響。 為避免服務中斷，您可能需要在2026年9月30日前更新應用程式與Marketo整合的方式。

這些型別的查詢通常會建立沒有潛在結果的搜尋，或在找到任何結果之前逾時。 限制集合的大小可改善這些查詢型別的回應能力，並確保資料集的搜尋可以及時完成。

## 如何判斷我是否受影響？

此變更只會影響少量Marketo Engage執行個體。 受影響訂閱的管理員會在套用變更之前在應用程式中收到通知。

## 我需要做什麼？

您應該將此檔案與負責您的Marketo Engage整合的人員或團隊共用。

根據您的使用案例，有兩個基本選項可將您的應用程式移轉至：

* 將您從中擷取活動的靜態清單限製為最多10,000個成員。 您可以將任何現有清單分割為較小的清單，以繼續輪詢相同對象的活動。
* 使用大量活動擷取或資料串流擷取您的活動或資料值變更，並將這些結果加入具有[getLeadByListId](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByListIdUsingGET_1)或[大量潛在客戶擷取](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-extract/bulk-lead-extract)的靜態清單成員資格

## 我若不採取任何動作，將會發生什麼事？

在從具有大量成員的靜態清單中查詢活動時，由於未處理的錯誤，您的API整合功能可能會遇到中斷。
