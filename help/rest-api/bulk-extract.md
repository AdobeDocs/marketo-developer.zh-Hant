---
title: 大量擷取
feature: REST API
description: 擷取Marketo資料的批次作業。
exl-id: 6a15c8a9-fd85-4c7d-9f65-8b2e2cba22ff
source-git-commit: e7d893a81d3ed95e34eefac1ee8f1ddd6852f5cc
workflow-type: tm+mt
source-wordcount: '1683'
ht-degree: 0%

---

# 大量擷取

Marketo提供的介面可擷取大量人員和人員相關資料，稱為大量擷取。 目前提供三種物件型別的介面：

- 銷售機會（人員）
- 活動
- 計畫成員
- 自訂物件

大量擷取是透過建立作業、定義要擷取的資料集、將作業排入佇列、等待作業完成寫入檔案，然後透過HTTP擷取檔案來執行。 這些作業會以非同步方式執行，且可以輪詢以擷取匯出的狀態。

`Note:`大量API端點未像其他端點一樣加上前置詞「/rest」。

## 驗證

大量擷取API會使用與其他Marketo REST API相同的OAuth 2.0驗證方法。 這需要有效的存取權杖才能作為HTTP標頭`Authorization: Bearer {_AccessToken_}`傳送。

>[!IMPORTANT]
>
>自2025年6月30日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

## 限制

- 最大並行匯出作業數：2
- 已排入佇列的匯出作業數上限（包含目前匯出的作業）：10
- 檔案保留期：7天
- 預設每日匯出配置：500MB （於每日中部時間午夜12:00重設）。 增加可購買專案。
- 日期範圍篩選器的最大時間範圍（createdAt或updatedAt）： 31天

UpdatedAt和智慧清單的大量潛在客戶擷取篩選器不適用於某些訂閱型別。 如果無法取得，對Create Export Lead Job端點的呼叫會傳回錯誤「1035， Unsupported filter type for target subscription」。 客戶可以聯絡Marketo支援，以便在他們的訂閱中啟用此功能。

### 佇列

大量擷取API會使用工作佇列（在銷售機會、活動、方案成員和自訂物件之間共用）。 擷取工作必須先建立，然後呼叫「建立匯出銷售機會/活動/方案成員工作」和「排入匯出銷售機會/活動/方案成員工作」端點以排入佇列。 排入佇列後，工作就會從佇列中提取，並在運算資源可用時啟動。

佇列中的工作數量上限為10個。 如果您嘗試在佇列已滿時將工作排入佇列，「排入佇列匯出工作」端點會傳回錯誤「1029，佇列中有太多工作」。 最多可同時執行兩個工作（狀態為「處理中」）。

### 檔案大小

大量擷取API是根據大量擷取作業所擷取的資料磁碟大小來計量。 從匯出作業的已完成狀態回應中讀取`fileSize`屬性，即可決定作業的明確位元組大小。

每日配額的上限為每天500MB，在潛在客戶、活動、方案成員和自訂物件之間共用。 超過配額時，除非每日配額在午夜[中部時間](https://en.wikipedia.org/wiki/Central_Time_Zone)重設，否則您無法建立或排入其他工作。 在此之前，會傳回錯誤「1029， Export daily quota exceeded」。 除了每日配額，沒有檔案大小上限。

工作排入佇列或處理之後，就會執行至完成（除非發生錯誤或工作取消）。 如果工作由於某個原因而失敗，您必須重新建立它。 只有當作業達到已完成狀態（永遠不寫入部分檔案）時，才會完全寫入檔案。 您可以計算檔案的SHA-256雜湊，並將其與工作狀態端點傳回的總和檢查碼進行比較，以確認檔案已完全寫入。

您可以呼叫Get Export Lead/Activity/Program Member Jobs來判斷當天使用的磁碟總數。 這些端點會傳回過去七天內的所有工作清單。 您可以篩選該清單，找出當天完成的工作（使用`status`和`finishedAt`屬性）。 然後加總這些工作的檔案大小，以產生使用的總數量。 無法刪除檔案以回收磁碟空間。

## 權限

大量擷取使用與Marketo REST API相同的許可權模型，不需要使用任何其他特殊許可權，不過每組端點需要特定許可權。

大量擷取作業僅供建立作業的API使用者存取，包括輪詢狀態和擷取檔案內容。

大量擷取端點不瞭解Marketo工作區。 擷取請求一律包含所有工作區的資料，無論您如何為自訂服務定義「僅限API使用者」。

## 建立工作

Marketo的大量擷取API會使用工作的概念來起始及執行資料擷取。 讓我們來看看如何建立簡單的銷售機會匯出工作。

```
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name"
   },
   "filter": {
      "createdAt": {
         "startAt": "2023-01-01T00:00:00Z",
         "endAt": "2023-01-31T00:00:00Z"
      }
   }
}
```

這個簡單請求會建構一個工作，其將傳回「firstName」和「lastName」欄位中包含的值，並將欄標題「First Name」和「Last Name」當作CSV檔案，包含2023年1月1日至2023年1月31日期間建立的每個潛在客戶。

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2023-01-21T11:47:30-08:00",
         "queuedAt": "2023-01-21T11:48:30-08:00",
         "format": "CSV",
      }
   ]
}
```

當我們建立工作時，它會傳回`exportId`屬性中的工作ID。 然後，我們可以使用此作業ID將作業排入佇列、取消作業、檢查其狀態或擷取完成的檔案。

### 通用引數

每個工作建立端點會共用一些通用引數，用於設定檔案格式、欄位名稱和大量擷取工作的篩選器。 每個擷取作業的子型別都可能有其他引數：

| 參數 | 資料類型 | 附註 |
|---|---|---|
| 格式 | 字串 | 使用逗號分隔值、定位字元分隔值和分號分隔值的選項，決定擷取資料的檔案格式。 接受以下其中之一：CSV、SSV、TSV。 格式預設為CSV。 |
| columnHeaderName | 物件 | 允許設定傳回檔案中欄標題的名稱。 每個成員索引鍵都是要重新命名的欄標題名稱，值是欄標題的新名稱。 例如，「columnHeaderNames」：{ &quot;firstName&quot;： &quot;First Name&quot;， &quot;lastName&quot;： &quot;Last Name&quot; }， |
| 篩選 | 物件 | 套用至擷取作業的篩選器。 型別和選項因工作型別而異。 |


## 正在擷取作業

有時候，您必須擷取最近的工作。 使用對應物件型別的「取得匯出作業」可輕鬆完成這項作業。 每個Get Export Jobs端點都支援`status`篩選欄位，  `batchSize`用來限制傳回的工作數目，以及`nextPageToken`用來分頁大型結果集。 狀態篩選器支援匯出作業的每個有效狀態：已建立、已排入佇列、正在處理、已取消、已完成和失敗。 batchSize的最大值和預設值是300。 讓我們取得潛在客戶匯出工作清單：

```
GET /bulk/v1/leads/export.json?status=Completed,Failed
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
      ...
   ]
}
```

端點會針對過去七天在結果陣列中針對該物件型別建立的每個工作，以`status`回應回應。 回應只會包含進行呼叫的API使用者所擁有之作業的結果。

## 開始工作

取得工作ID後，即可開始工作：

```
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

這會開始執行工作，並傳回狀態回應。 由於匯出一律以非同步方式完成，因此我們必須輪詢工作狀態以判斷工作是否已完成。 指定工作的狀態更新頻率不會高於每60秒一次，因此絕不應該超過此頻率輪詢狀態。 不過請記住，大部分的使用案例不應該要求輪詢頻率超過每5分鐘一次。 每次成功匯出的資料都會保留10天。

## 輪詢工作狀態

判斷工作狀態很簡單。

只能輪詢由建立作業的相同API使用者建立的作業狀態。

```
GET /bulk/v1/leads/export/{exportId}/status.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:d9c73f0b6960c71623c8bafe29603b3e8e20fd0e4eeaefd119c0227506ea9be4"
      }
   ]
}
```

內部`status`成員表示工作的進度，可能是下列其中一個值： Created、Queued、Processing、Canceled、Completed、Failed。 在此情況下，我們的工作已完成，因此我們可以停止輪詢並繼續擷取檔案。 完成時，`fileSize`成員會指出檔案的總長度（位元組），而`fileChecksum`成員包含檔案的SHA-256雜湊。 在達到「已完成」或「失敗」狀態後，工作狀態可使用30天。

## 正在擷取您的資料

工作完成後，您就可以輕鬆擷取檔案。

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

回應包含以設定作業方式格式化的檔案。 端點會以檔案內容回應。 與大多數其他Marketo REST端點不同，如果作業尚未完成或傳遞了錯誤的作業ID，檔案端點會以「404找不到」狀態回應，並以純文字錯誤訊息作為裝載。

為了支援擷取資料的部分和復原友好擷取，檔案端點可選擇性地支援型別`bytes` （每[RFC 7233](https://datatracker.ietf.org/doc/html/rfc7233)）的HTTP標頭`Range`。 如果未設定標頭，將會傳回所有內容。 若要擷取檔案的前10,000個位元組，您必須將下列標題作為GET要求的一部分傳遞給端點，從位元組0開始：

```
Range: bytes=0-9999
```

擷取部分檔案時，端點會以狀態代碼206回應，並傳回Accept-ranges、Content-Length和Content-Range標頭：

```
Accept-Ranges: bytes
Content-Length: 1000
Content-Range: bytes 0-9999/123424
```

### 部分擷取與恢復

檔案可以部分擷取，或稍後使用`Range`標頭繼續。 檔案的範圍從位元組0開始，到值`fileSize`減1結束。 呼叫Get Export File端點時，檔案的長度也會回報為`Content-Range`回應標頭值的分母。 如果擷取部分失敗，稍後可以繼續進行。 例如，如果您嘗試擷取長度為1000個位元組的檔案，但只收到前725個位元組，則可以從失敗點再次呼叫端點並傳遞新範圍來重試擷取：

```
Range: bytes 724-999
```

這會傳回檔案的剩餘275個位元組。

#### 檔案完整性驗證

當`status`為「已完成」時，工作狀態端點會在`fileChecksum`屬性中傳回總和檢查碼。 總和檢查碼是匯出檔案的SHA-256雜湊。 您可以將總和檢查碼與擷取之檔案的SHA-256雜湊進行比較，以確認其已完成。

以下是包含總和檢查碼的範例回應：

```json
{
    "exportId": "45547609-6732-418a-bb7b-17b0160b2317",
    "format": "CSV",
    "status": "Completed",
    "createdAt": "2019-06-04T23:13:12Z",
    "queuedAt": "2019-06-04T23:14:02Z",
    "startedAt": "2019-06-04T23:15:19Z",
    "finishedAt": "2019-06-04T23:36:40Z",
    "numberOfRecords": 1776,
    "fileSize": 400785,
    "fileChecksum": "sha256:83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6"
}
```

以下是使用sha256sum命令列公用程式建立已擷取檔案「bulk_lead_export.csv」的SHA-256雜湊的範例：

```
$ sha256sum bulk_lead_export.csv
83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6 *bulk_lead_export.csv
```

## 取消工作

如果工作設定錯誤或變得不必要，可以輕鬆地取消它：

```
POST /bulk/v1/leads/export/{exportId}/cancel.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Cancelled",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "format": "CSV",
      }
   ]
}
```

此回應會以指出工作已取消的狀態回應。
