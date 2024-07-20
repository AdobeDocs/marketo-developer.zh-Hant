---
title: 大量潛在客戶擷取
feature: REST API
description: 潛在客戶資料的批次擷取。
exl-id: 42796e89-5468-463e-9b67-cce7e798677b
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1173'
ht-degree: 2%

---

# 大量潛在客戶擷取

[大量潛在客戶擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads)

REST API的「大量銷售機會擷取」集提供程式設計介面，可從Marketo擷取大量銷售機會/人員記錄。 此外，它可用於根據記錄的建立日期、最近更新、靜態清單成員資格或智慧清單成員資格，以增量方式擷取潛在客戶。 使用案例的建議介面，此介面需要在Marketo和一個或多個外部系統之間持續交換資料，用於ETL、資料倉儲和封存目的。

## 權限

「大量銷售機會擷取API」要求擁有的API使用者必須具備一個或兩個唯讀銷售機會或讀寫銷售機會許可權的角色。

## 篩選器

潛在客戶支援各種篩選器選項。 某些篩選器（包括`updatedAt`、`smartListName`和`smartListId`）需要額外的基礎結構元件，這些元件尚未推出至所有訂閱。 每個匯出作業只能指定一個篩選器型別。

| 篩選器型別 | 資料類型 | 附註 |
|---|---|---|
| createdAt | 日期範圍 | 接受具有成員`startAt`和`endAt`的JSON物件。 `startAt`接受代表低浮水印的日期時間，而`endAt`接受代表高浮水印的日期時間。 範圍必須為31天或更少。 日期時間應採用ISO-8601格式，不含毫秒。 具有此篩選型別的工作會傳回在日期範圍內建立的所有可存取記錄。 |
| 更新時間* | 日期範圍 | 接受具有成員`startAt`和`endAt`的JSON物件。 `startAt`接受代表低浮水印的日期時間，而`endAt`接受代表高浮水印的日期時間。 範圍必須為31天或更少。 日期時間應採用ISO-8601格式，不含毫秒。 注意：此篩選器不會篩選可見的「updatedAt」欄位，這僅反映標準欄位的更新。 它會根據上次對潛在客戶記錄進行欄位更新的時間進行篩選。具有此篩選型別的工作會傳回日期範圍內最近更新的所有可存取記錄。 |
| staticListName | 字串 | 接受靜態清單的名稱。 具有此篩選型別的工作會在工作開始處理時，傳回屬於靜態清單成員的所有可存取記錄。 使用取得清單端點擷取靜態清單名稱。 |
| staticListId | 整數 | 接受靜態清單的識別碼。 具有此篩選型別的工作會在工作開始處理時，傳回屬於靜態清單成員的所有可存取記錄。 使用「取得清單」端點擷取靜態清單ID。 |
| 智慧型清單名稱* | 字串 | 接受智慧清單的名稱。 具有此篩選型別的工作會傳回工作開始處理時屬於智慧列示成員的所有可存取記錄。 使用「取得智慧列示」端點擷取智慧列示名稱。 |
| smartListId* | 整數 | 接受智慧清單的識別碼。 具有此篩選型別的工作會傳回工作開始處理時屬於智慧列示成員的所有可存取記錄。 使用「取得智慧清單」端點擷取智慧清單ID。 |


部分訂閱無法使用篩選器型別。 如果您的訂閱無法使用，您在呼叫「建立匯出潛在客戶工作」端點時會收到錯誤（「1035，目標訂閱不受支援的篩選器型別」）。 客戶可以聯絡Marketo支援，以便在他們的訂閱中啟用此功能。

## 選項

「建立匯出潛在客戶工作」端點提供數個格式選項，讓使用者能在匯出的檔案中包含特定欄位、重新命名這些欄位的欄標題，以及匯出的檔案格式。

| 引數 | 資料類型 | 必要 | 附註 |
|---|---|---|---|
| 欄位 | 陣列[字串] | 是 | 欄位引數接受字串的JSON陣列。 每個字串都必須是Marketo潛在客戶欄位的REST API名稱。 列出的欄位會包含在匯出的檔案中。 除非以columnHeader覆寫，否則每個欄位的欄標題將是每個欄位的REST API名稱。 注意：啟用[!DNL Adobe Experience Cloud Audience Sharing]功能時，會發生Cookie同步程式，將[!DNL Adobe Experience Cloud] ID (ECID)與Marketo銷售機會建立關聯。 您可以指定「ecid」欄位，將ECID包含在匯出檔案中。 |
| columnHeaderName | 物件 | 否 | 包含欄位和欄標題名稱之索引鍵/值組的JSON物件。 索引鍵必須是匯出作業中包含的欄位名稱。 這是欄位的API名稱，可透過呼叫Describe Lead來擷取。 值是該欄位匯出的欄標題的名稱。 |
| 格式 | 字串 | 否 | 接受以下其中之一：CSV、TSV、SSV。 匯出的檔案會分別呈現為逗號分隔值、定位字元分隔值或空格分隔值檔案（如果設定）。 如果未設定，則預設為CSV。 |


## 建立工作

使用[建立匯出潛在客戶工作](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/createExportLeadsUsingPOST)端點開始匯出之前，會定義工作的引數。 我們必須定義匯出所需的`fields`、`filter`的引數型別、檔案的`format`以及欄標題名稱（若有的話）。

```
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName",
      "id",
      "email"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name",
      "id": "Marketo Id",
      "email": "Email Address"
   },
   "filter": {
      "createdAt": {
         "startAt": "2017-01-01T00:00:00Z",
         "`endAt`": "2017-01-31T00:00:00Z"
      }
   }
}
```

此請求將開始匯出在2017年1月1日至2017年1月31日期間建立的一組銷售機會，包括來自對應`firstName`、`lastName`、`id`和`email`欄位的值。

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

這會傳回狀態回應，指出工作已建立。 工作已定義並建立，但尚未開始。 若要這麼做，必須使用建立狀態回應中的exportId來呼叫[Enqueue Export Lead Job](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/enqueueExportLeadsUsingPOST)端點：

```
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "147e4#16b24d9b913",
    "result": [
        {
            "exportId": "fad2cd1b-e822-4025-be1e-9caa9cf1d4b8",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2019-06-04T23:35:43Z",
            "queuedAt": "2019-06-04T23:36:17Z"
        }
    ],
    "success": true
}
```

此回應會顯示`status`個「已排入佇列」，之後在有可用的匯出位置時，會將其設為「正在處理」。

## 輪詢工作狀態

只能為同一API使用者建立的作業擷取`Note:`狀態。

由於這是非同步端點，在建立作業後，我們必須輪詢其狀態以判斷其進度。 使用[取得匯出潛在客戶工作狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET)端點進行輪詢。 狀態只會每60秒更新一次，因此不建議使用低於此值的輪詢頻率，並且在幾乎所有情況下仍然會太高。 讓我們來快速瞭解輪詢。

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
         "status": "Processing",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

狀態端點回應指出工作仍在處理中，因此檔案尚不可擷取。 工作狀態變更為「已完成」後，即可開始下載。

狀態列位可能會以下列任一專案回應：

- 建立時間
- 已排入佇列
- 處理中
- 已取消
- 已完成
- 失敗

## 正在擷取您的資料

若要擷取已完成潛在客戶匯出的檔案，只要使用您的`exportId`呼叫[取得匯出潛在客戶檔案](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsFileUsingGET)端點即可。

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

回應包含以設定作業方式格式化的檔案。 端點會以檔案內容回應。

如果請求的潛在客戶欄位為空（不包含任何資料），則`null`會放置在匯出檔案中的對應欄位中。 在以下範例中，傳回潛在客戶的電子郵件欄位為空白。

```csv
firstName,lastName,email,cookies
Russell,Wilson,null,_mch-localhost-1536605780000-12105
```

為了支援擷取資料的部份擷取和便於恢復擷取，檔案端點可選擇性地支援型別位元組的HTTP標頭範圍。 如果未設定標頭，則會傳回整個內容。 深入瞭解如何搭配Marketo [大量擷取](bulk-extract.md)使用Range標頭。

## 取消工作

如果工作設定錯誤或變得不必要，可以使用[取消匯出潛在客戶工作](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/cancelExportLeadsUsingPOST)端點輕鬆取消工作：

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
         "format": "CSV"
      }
   ]
}
```

此回應會以指出工作已取消的狀態回應。
