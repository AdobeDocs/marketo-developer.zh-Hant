---
title: Marketo整合最佳實務
feature: REST API
description: Marketo API整合的最佳實務涵蓋配額、速率和並行限制、批次、大量匯入和匯出、快取和延遲計畫。
exl-id: 1e418008-a36b-4366-a044-dfa9fe4b5f82
TQID: https://experienceleague.adobe.com/Ld-rmFCwKSx-0W2-ceYICu0FQHK8BKAC1QgqtiOWDn4
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b13bd2ad-8e65-49e5-9691-2a0d31067b35id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: e64968b2-4ee5-47f9-8cae-0588f184b9ebid: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: df401a2a-327d-468c-a5e4-b7b7ccd071a0
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 866
ht-degree: 0%

---

# Marketo整合最佳實務

圍繞您的Marketo執行個體的共用API限制設計整合。 使用批次處理程式、快取和保守的請求率來改善輸送量和可靠性。

## API限制

- **每日配額：**&#x200B;大部分的訂閱每天都會分配50,000個API呼叫。 配額會在每日中午12:00 CST重設。 請連絡您的帳戶管理員以增加每日配額。
- **速率限制：**&#x200B;每個執行個體限製為每20秒100次API呼叫。
- **並行限制：**&#x200B;每個執行個體最多允許10個並行API呼叫。
- **批次大小：**&#x200B;潛在客戶資料庫支援300筆記錄；資產查詢支援200筆記錄。
- **REST API承載大小：** 1 MB。
- **大量匯入檔案大小：** 10 MB。
- **大量擷取工作：**&#x200B;兩個正在執行，10個已排入佇列（含）。

## 快速提示

- 設定保守使用量限制，因為您的應用程式會與其他應用程式共用配額、比率及並行資源。
- 可用時，請使用Marketo大量和批次方法。 僅在必要時使用單一記錄或單一結果呼叫。
- 使用[指數回退](https://en.wikipedia.org/wiki/Exponential_backoff)，重試因速率或並行限制而失敗的API呼叫。
- 避免同時API呼叫，除非讓您的使用案例受益。

## 批次處理程式

對於插入和更新，請將記錄分組到儘可能少的交易中。 從資料存放區擷取記錄時，請在提交前彙總記錄，而非針對每次變更提交一個請求。

## 可接受的延遲

當您設計整合時，定義可接受的延遲時間，即提交API呼叫之前的最長時間。 此選項會決定哪些Marketo方法和設定選項適合使用案例。

例如，即時整合可在使用者開始試用時通知銷售人員，當需要立即後續追蹤時，可能會提交批次一個。 大部分的使用案例可以容許更多延遲，並透過佇列和批次呼叫來更有效率地運作。

| 可接受的延遲 | 偏好方法 | 附註 |
| --- | --- | --- |
| 低（&lt;10秒） | 同步API （批次或未批次） | 確保您的使用案例需要此專案。 針對大量使用案例傳送即時和同步呼叫可能會快速消耗每日API配額，並可能超過速率和並行限制。 |
| Medium（10秒 — 6000萬） | 同步API （批次） | 若要將傳入資料整合至Marketo，強烈建議使用同時具有年齡和大小限制的佇列。 達到任一限制時，請排清佇列並提交包含累積記錄的API請求。 這在速度與效率之間是一種強烈的折衷，可確保您的請求以所需的節奏進行，同時批次處理儘可能多的記錄，數量以佇列的年齡所允許。 |
| 高（>60米） | 大量匯入/匯出（如果支援） | 針對傳入資料整合，應透過Marketo大量API，將記錄排入佇列並隨時提交。 |

## 每日限制

每個啟用API的Marketo執行個體每天至少會配置10,000個REST API呼叫，但通常為50,000個或更多。 每個執行個體也有500 MB以上的大量擷取容量。 額外的每日容量可作為Marketo訂閱的一部分購買，但應用程式設計應考慮常見的訂閱限制。

容量由執行個體中的所有API服務和使用者共用。 儘可能將多餘的呼叫和批次記錄排除到少數呼叫中。

最有效率呼叫的匯入方法是Marketo大量匯入API，可供[銷售機會/人員](https://developer.adobe.com/marketo-apis/api/mapi#operation/importLeadUsingPOST)和[自訂物件](https://developer.adobe.com/marketo-apis/api/mapi#operation/importCustomObjectUsingPOST)使用。 Marketo也為[銷售機會](bulk-lead-extract.md)和[活動](bulk-activity-extract.md)提供大量擷取。

### 快取

下列作業的結果通常可以在使用者端快取一天以上，因為它們很少變更：

- 說明作業的結果
- [活動類型](https://developer.adobe.com/marketo-apis/api/mapi#operation/getAllActivityTypesUsingGET)
- [分割區](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadPartitionsUsingGET)

若是銷售機會或活動資料擴充等使用案例，您也可以快取方案、電子郵件和資料夾等資產型別。

## 速率限制

每個Marketo例項的所有協力廠商API服務具有每20秒100次呼叫的共用速率限制。 如果呼叫超過此限制，API會傳回606錯誤代碼。

一般而言，請將每個協力廠商整合限製為每20秒50次呼叫，以便多個API整合和使用者可以共用可用容量。 部分使用案例可能需要完整的限制。 不過，使用批次處理和目標較低輸送量的應用程式通常能更迅速回應、更一致，延遲也只會稍微增加。

## 並行限制

每個Marketo例項都有可同時執行REST API呼叫的10個共用限制。 請勿假設您的應用程式是此限制的唯一消費者。

Marketo會計算正在處理且尚未傳回的呼叫。 呼叫傳回時，不再計入並行限制。

大部分的整合功能不會受益於並行呼叫。 如果您實作並行處理，一開始會將應用程式限製為五個或更少的並行請求。 只有在您判斷應用程式需要更多專案後，才提高上限。

## 錯誤次數

除了極少數情況外，API請求會傳回HTTP狀態代碼200。 商業邏輯錯誤也會傳回200，但會在回應主體中包含詳細資訊。 如需詳細資訊，請參閱[錯誤碼](error-codes.md)。

請勿評估HTTP原因片語，因為它是選用字詞，而且可能會有所變更。
