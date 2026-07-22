---
title: 取得銷售機會API更新
feature: REST API
description: 瞭解Get Lead Activities和Get Lead Changes端點的限制變更。
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: '311'
ht-degree: 0%

---

# 取得銷售機會API更新

自2026年9月30日起，如果目標清單包含10,000個或更多銷售機會，對[取得銷售機會活動](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadActivitiesUsingGET)或[取得銷售機會變更](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadChangesUsingGET)端點（包含`listId`引數）的呼叫將失敗。 端點會傳回1003錯誤代碼，指出目標靜態清單中有太多記錄。

此變更會影響一或多個最近的API呼叫。 為避免服務中斷，您可能需要在2026年9月30日前更新應用程式與Marketo的整合方式。

這些查詢通常會建立沒有潛在結果的搜尋，或在尋找結果之前逾時。 限制集合大小可改善查詢回應能力，並有助於及時完成搜尋。

## 如何判斷我是否受影響？

此變更只會影響少量Marketo Engage執行個體。 受影響訂閱的管理員會在套用變更之前收到應用程式內通知。

## 我需要做什麼？

與負責您Marketo Engage整合的人員或團隊共用此檔案。

根據您的使用案例，請使用下列其中一個移轉選項：

* 將用於活動擷取的靜態清單限製為10,000個成員。 將現有清單分割為較小的清單，以繼續輪詢活動的相同對象。
* 使用大量活動擷取或資料串流擷取活動或資料值變更。 以[getLeadByListId](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByListIdUsingGET_1)或[大量潛在客戶擷取](https://experienceleague.adobe.com/zh-hant/docs/marketo-developer/marketo/rest/bulk-extract/bulk-lead-extract)將結果加入靜態清單成員資格。

## 我若不採取任何動作，將會發生什麼事？

從具有大量成員的靜態清單查詢活動時，您的API整合可能會因未處理的錯誤而中斷。
