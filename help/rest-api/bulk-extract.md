---
title: 大量擷取
feature: REST API
description: 瞭解如何使用Marketo大量擷取REST API匯出銷售機會、活動、方案成員和自訂物件，並具備OAuth、工作佇列和500MB每日限制。
exl-id: 6a15c8a9-fd85-4c7d-9f65-8b2e2cba22ff
TQID: https://experienceleague.adobe.com/ECSchsjqp8fyxXbUGl5DgXHUkXuN0sIUc3yJfVaIe1E
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1549
ht-degree: 0%

---

# 大量擷取

Marketo大量擷取提供可擷取大型人員和人員相關資料集的介面。 目前有四種物件型別可使用介面：

- 銷售機會（人員）
- 活動
- 計畫成員
- 自訂物件

若要執行大量擷取：

1. 建立工作並定義要擷取的資料。
1. 將工作排入佇列。
1. 等候工作完成寫入檔案。
1. 透過HTTP擷取檔案。

大量擷取工作會以非同步方式執行。 輪詢工作以擷取匯出狀態。

`Note:`大量API端點未像其他端點一樣加上前置詞「/rest」。

## Authentication

大量擷取API會使用與其他Marketo REST API相同的OAuth 2.0驗證方法。 在`Authorization: Bearer {_AccessToken_}` HTTP標頭中傳送有效的存取權杖。

>[!IMPORTANT]
>
>自2026年8月31日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

## 限制

- 最大並行匯出工作數：2
- 已排入佇列的匯出作業上限（包括目前正在匯出的作業）： 10
- 檔案保留期：7天
- 預設每日匯出配置： 500MB。 此配置每天於中午12:00重設。 可購買加值。
- 日期範圍篩選器的最大時間範圍（`createdAt`或`updatedAt`）： 31天

UpdatedAt和智慧清單的大量潛在客戶擷取篩選器不適用於某些訂閱型別。 如果這些篩選器無法使用，建立匯出潛在客戶作業端點會傳回錯誤「1035，目標訂閱不受支援的篩選器型別」。 請聯絡Marketo支援，為您的訂閱啟用此功能。

### 佇列

大量擷取API會使用一個在銷售機會、活動、方案成員和自訂物件之間共用的工作佇列。 首先，呼叫「建立匯出銷售機會/活動/方案成員工作」端點以建立擷取工作。 然後，呼叫對應的「排入佇列的匯出銷售機會/活動/方案成員工作」端點，將工作排入佇列。 當運算資源可用時，工作就會開始。

佇列最多可包含10個工作。 如果您嘗試在佇列已滿時將工作排入佇列，「排入佇列匯出工作」端點會傳回錯誤「1029，佇列中有太多工作」。 最多有兩個工作的狀態可以是「正在處理」並同時執行。

### 檔案大小

大量擷取API是根據大量擷取作業所擷取的資料在磁碟上的大小來計量。 若要判斷檔案大小（位元組），請讀取匯出作業之已完成狀態回應中的`fileSize`屬性。

每日配額為500MB，在潛在客戶、活動、方案成員及自訂物件之間共用。 超過配額時，您必須等到配額於[中部時間](https://en.wikipedia.org/wiki/Central_Time_Zone)午夜重設後，才能建立或排入其他工作。 在重設之前，API會傳回錯誤「1029，已超出匯出每日配額」。 除了每日配額，沒有檔案大小上限。

工作排入佇列或處理之後，除非發生錯誤或您取消工作，否則工作會執行到完成。 如果作業失敗，您必須重新建立它。

API只會在作業達到完成狀態時寫入完整檔案。 它不會寫入部分檔案。 若要驗證檔案，請計算其SHA-256雜湊，並將其與工作狀態端點傳回的總和檢查碼進行比較。

若要判斷當天使用的磁碟空間總數，請呼叫「取得匯出銷售機會/活動/程式成員工作」端點。 這些端點會傳回過去七天的所有工作。

使用`status`和`finishedAt`屬性，將清單篩選為當天完成的工作。 然後，為這些工作新增檔案大小。 您無法刪除檔案以回收磁碟空間。

## 權限

大量擷取使用與Marketo REST API相同的許可權模型。 它不需要額外的特殊許可權，但每組端點需要特定許可權。

只有建立大量擷取工作的API使用者可以存取它、輪詢其狀態或擷取其檔案內容。

大量擷取端點不瞭解Marketo工作區。 擷取請求包含來自所有工作區的資料，無論您如何為自訂服務定義「僅限API使用者」。

## 建立工作

Marketo大量擷取API會使用作業來啟動及執行資料擷取。 下列請求會建立潛在客戶匯出工作：

```http
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

此請求會建立一份工作，匯出2023年1月1日到2023年1月31日期間建立的每個銷售機會。 CSV檔案包含「firstName」和「lastName」欄位的值，並使用欄標題「First Name」和「Last Name」。

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

回應傳回`exportId`屬性中的工作ID。 使用此工作ID來排入佇列或取消工作、檢查其狀態或擷取完成的檔案。

### 通用引數

每個工作建立端點都有用於設定檔案格式、欄位名稱和篩選器的通用引數。 每個擷取工作子型別也可以有其他引數：

| 參數 | 資料類型 | 附註 |
| --- | --- | --- |
| 格式 | 字串 | 使用逗號分隔值、定位字元分隔值和分號分隔值的選項，決定擷取資料的檔案格式。 接受以下其中之一：CSV、SSV、TSV。 格式預設為CSV。 |
| columnHeaderName | 物件 | 允許設定傳回檔案中欄標題的名稱。 每個成員索引鍵都是要重新命名的欄標題名稱，值是欄標題的新名稱。 例如，「columnHeaderNames」：{ &quot;firstName&quot;： &quot;First Name&quot;， &quot;lastName&quot;： &quot;Last Name&quot; }， |
| 篩選 | 物件 | 套用至擷取作業的篩選器。 型別和選項因工作型別而異。 |

## 正在擷取作業

使用對應物件型別的「取得匯出作業」端點來擷取最近的作業。 每個「取得匯出作業」端點都支援下列引數：

- `status`會依匯出狀態篩選工作。 有效值為「已建立」、「已排入佇列」、「正在處理」、「已取消」、「已完成」和「失敗」。
- `batchSize`會限制傳回的工作數目。 預設值和最大值為300。
- `nextPageToken`頁通過大型結果集。

下列請求會擷取狀態為「已完成」或「失敗」的銷售機會匯出作業：

```http
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

結果陣列包含過去七天內為該物件型別建立的每個工作的狀態回應。 回應僅包含進行呼叫的API使用者擁有的工作。

## 開始工作

建立工作之後，請使用其工作ID將工作排入佇列並啟動：

```http
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

請求會啟動工作並傳回狀態回應。 由於匯出是以非同步方式執行，因此輪詢作業狀態以判斷匯出何時完成。

## 輪詢工作狀態

輪詢狀態端點以判斷工作的進度。 只有建立工作的API使用者可以輪詢其狀態。

工作狀態的更新頻率不會超過每60秒一次。 請勿更頻繁地輪詢。 在大多數的使用案例中，每5分鐘輪詢一次就足夠了。 每次成功匯出的資料都會保留10天。

```http
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

內部`status`成員表示工作進度。 其值可以是Created、Queued、Processing、Canceled、Completed或Failed。

在此範例中，工作已完成，因此您可以停止輪詢並擷取檔案。 針對已完成的工作，`fileSize`成員表示檔案總長度（位元組），而`fileChecksum`成員包含檔案的SHA-256雜湊。 工作狀態會在工作達到「已完成」或「失敗」狀態後的30天內有效。

## 正在擷取您的資料

工作完成後，擷取匯出的檔案：

```http
GET /bulk/v1/leads/export/{exportId}/file.json
```

回應包含為作業設定的格式的檔案。 如果作業不完整或請求包含無效的作業ID，檔案端點會傳回「404找不到」狀態和純文字錯誤訊息。 此回應與大部分其他Marketo REST端點回應不同。

為了支援部分且可恢復擷取，檔案端點支援[RFC 7233](https://datatracker.ietf.org/doc/html/rfc7233)中定義的具有`bytes`型別的選用HTTP `Range`標頭。 如果您未設定標頭，端點會傳回整個檔案。

若要擷取檔案的前10,000個位元組，請在GET要求中傳遞下列標頭。 範圍從位元組0開始：

```text
Range: bytes=0-9999
```

對於部分檔案，端點傳回狀態代碼206和Accept-ranges、Content-Length和Content-Range標頭：

```text
Accept-Ranges: bytes
Content-Length: 10000
Content-Range: bytes 0-9999/123424
```

### 部分擷取與恢復

使用`Range`標頭來擷取部分檔案或繼續擷取。 檔案範圍從位元組0開始，到值`fileSize`減去1結束。 Get Export File端點也會將檔案長度回報為`Content-Range`回應標頭中的分母。

如果擷取部分失敗，您可以恢復它。 例如，如果您嘗試擷取1000位元組的檔案，但只收到前725個位元組，請再次呼叫端點並傳遞新範圍：

```text
Range: bytes=725-999
```

此請求會傳回檔案的剩餘275個位元組。

#### 檔案完整性驗證

當`status`為「已完成」時，工作狀態端點會在`fileChecksum`屬性中傳回總和檢查碼。 總和檢查碼是匯出檔案的SHA-256雜湊。 將其與擷取檔案的SHA-256雜湊進行比較，以驗證檔案是否已完成。

下列回應包含總和檢查碼：

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

下列範例使用sha256sum命令列公用程式，為名為「bulk_lead_export.csv」的擷取檔案建立SHA-256雜湊：

```bash
$ sha256sum bulk_lead_export.csv
83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6 *bulk_lead_export.csv
```

## 取消工作

如果工作設定錯誤或不再需要該工作，請取消它：

```http
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

回應狀態表示工作已取消。
