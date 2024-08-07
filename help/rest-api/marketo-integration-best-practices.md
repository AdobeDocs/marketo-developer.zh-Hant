---
title: Marketo整合最佳實務
feature: REST API
description: 使用Marketo API的最佳實務。
exl-id: 1e418008-a36b-4366-a044-dfa9fe4b5f82
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '952'
ht-degree: 0%

---

# Marketo整合最佳實務

## API限制

- **每日配額：**&#x200B;大部分的訂閱每天會配置50,000個API呼叫（這會在每日中午12:00CST重設）。 您可以透過帳戶管理員增加每日配額。
- **速率限制：**&#x200B;每個執行個體的API存取限製為每20秒100次呼叫。
- **並行限制：**  最多十個同時進行的API呼叫。
- **批次大小：**&#x200B;潛在客戶資料庫 — 300筆記錄；資產查詢 — 200筆記錄
- **REST API承載大小：** 1MB
- **大量匯入檔案大小：** 10MB
- **SOAP批次大小上限：** 300筆記錄
- **大量擷取工作：** 2個正在執行； 10個已排入佇列（含）

## 快速提示

- 假設您的應用程式會與其他應用程式競爭配額、比率及並行資源，並設定保守的使用量限制。
- 若有條件使用Marketo的大量和批次方法。 必要時僅使用單一記錄或單一結果呼叫。
- 使用[指數回退](https://en.wikipedia.org/wiki/Exponential_backoff)來重試由於速率或並行限制而失敗的API呼叫。
- 如果您的使用案例不能從中受益，請避免同時進行API呼叫。

## 批次處理程式

為了確保整合的最佳效能，在執行插入或更新時，應將記錄分組為儘可能少的交易。 從資料存放區擷取記錄以供提交時，應一律在提交前彙總記錄，而非針對每項個別變更提交請求。

## 可接受的延遲

決定您的延遲允差，或提交API呼叫之前可能經過的最大時間，會通知許多（如果不是大部分）您在設計與Marketo整合時所做的決定。 Marketo提供許多適合不同使用案例和不同延遲類別的不同方法和設定選項。 例如，即時整合可通知銷售人員某位使用者已註冊試用版，若需要立即跟進，該整合可能只會提交批次其中一批。 不過，大多數情況不需要這種處理，而且可以容許額外的延遲，而且可以透過佇列和批次呼叫更有效率地管理。

| 可接受的延遲 | 偏好方法 | 附註 |
|---|---|---|
| 低（&lt;10秒） | 同步API （批次或未批次） | 確保您的使用案例需要此專案。 針對大量使用案例傳送即時和同步呼叫可能會快速消耗每日API配額，並可能超過速率和並行限制。 |
| Medium（10秒 — 6000萬） | 同步API （批次） | 若要將傳入資料整合至Marketo，強烈建議使用同時具有年齡和大小限制的佇列。 達到任一限制時，請排清佇列並提交包含累積記錄的API請求。 這在速度與效率之間是一種強烈的折衷，可確保您的請求以所需的節奏進行，同時批次處理儘可能多的記錄，數量以佇列的年齡所允許。 |
| 高（>60米） | 大量匯入/匯出（如果支援） | 針對傳入資料整合，應透過Marketo大量API，將記錄排入佇列並隨時提交。 |

## 每日限制

Marketo每個啟用API的執行個體每天至少會配置10,000個REST API呼叫，但通常為50,000個或更多，以及500MB或更多「大量擷取」容量。 雖然額外的每日容量可作為Marketo訂閱的一部分購買，但您的應用程式設計應考慮Marketo訂閱的共同限制。

由於容量是在執行個體中的所有API服務和使用者之間共用，最佳實務是消除多餘的呼叫，並儘可能將記錄批次化為少數呼叫。 匯入記錄最有效率的呼叫方式是使用Marketo的大量匯入API，這些可供[銷售機會/人員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)和[自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Snippets/operation/createSnippetUsingPOST)使用。 Marketo也為[銷售機會](bulk-lead-extract.md)和[活動](bulk-activity-extract.md)提供大量擷取。

### 快取

下列作業的結果通常可以在使用者端快取一天以上，因為它們很少變更：

- 說明作業的結果
- [活動型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)
- [分割區](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadPartitionsUsingGET)

快取特定資產型別（如程式、電子郵件和資料夾）也適用於特定使用案例，例如潛在客戶或活動記錄的資料擴充。

## 速率限制

每個Marketo執行個體都有每20秒100次呼叫的速率限制，此速率限制由所有協力廠商API服務共用。 如果超過此限制，API會以606錯誤碼回應，指出已超過速率限制。 一般而言，協力廠商整合應該將其使用率限製為每20秒50次呼叫或更少，以允許多個API整合和使用者公平使用速率限制。 雖然在某些情況下，達到此限制可能是適當的，但一般而言，使用批次化並將其輸送量目標設定為低於此限制的應用程式在操作中會更快速回應且一致，而延遲增加的成本很低。

## 並行限制

每個Marketo例項都有可同時執行REST API呼叫的10個共用限制。 就像每日配額和速率限制一樣，它也是共用的，因此您不應該假設您的應用程式是此限制的專屬消費者。 Marketo會將同時進行的呼叫數計算為正在處理且尚未傳回的呼叫數，因此當呼叫傳回時，不再根據同時呼叫數限制來計算。

大部分整合使用案例不會受益於並行呼叫，因此在決定向Marketo提交並行請求之前，請考慮您的應用程式是否受益。 如果您確實要實作並行處理，您應將並行要求數限制在初始設計中的5個或更少，並在確定應用程式需要更多之後，才增加此數目。

## 錯誤次數

除了極少數情況外，API請求會傳回200的HTTP狀態代碼。 商業邏輯錯誤也會傳回200，但在回應本文中包含詳細資訊。 如需詳細說明，請參閱[錯誤碼](error-codes.md)。 不應評估HTTP原因片語，因為它是選用字詞，而且可能會有所變更。
