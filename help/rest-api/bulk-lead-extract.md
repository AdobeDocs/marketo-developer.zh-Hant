---
title: 大量潛在客戶擷取
feature: REST API
description: 瞭解如何使用Marketo大量銷售機會擷取REST API，以大量匯出具有日期、清單和智慧清單篩選器、自訂欄位和CSV/TSV格式的銷售機會。
exl-id: 42796e89-5468-463e-9b67-cce7e798677b
TQID: https://experienceleague.adobe.com/4eMJR87fHDdccrVid3wHtspvBVQmrBGHYMlIwFCSdEI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 1017
ht-degree: 2%

---

# 大量潛在客戶擷取

[大量潛在客戶擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads)

大量銷售機會擷取REST API會從Marketo擷取大量銷售機會/人員記錄。 您也可以根據記錄建立日期、最近更新、靜態清單成員資格或智慧清單成員資格，以增量方式擷取銷售機會。

使用大量潛在客戶擷取，以在Marketo和外部系統（包括ETL、資料倉儲和封存工作流程）之間持續交換資料。

## 權限

擁有工作的API使用者必須具有角色具有唯讀銷售機會許可權、讀寫銷售機會許可權，或同時具有這兩項許可權。

## 篩選器

潛在客戶匯出作業支援多種篩選器型別。 每個匯出作業只能使用一個篩選型別。

`updatedAt`、`smartListName`和`smartListId`篩選器需要並非所有訂閱都可用的基礎結構。

| 篩選器型別 | 資料類型 | 附註 |
| --- | --- | --- |
| createdAt | 日期範圍 | 具有`startAt`和`endAt`成員的JSON物件。 `startAt`是低浮水印日期時間，`endAt`是高浮水印日期時間。 使用ISO-8601日期和時間值（不含毫秒）。 範圍必須為31天或更少。 此工作會傳回在日期範圍內建立的所有可存取記錄。 |
| 更新時間* | 日期範圍 | 具有`startAt`和`endAt`成員的JSON物件。 `startAt`是低浮水印日期時間，`endAt`是高浮水印日期時間。 使用ISO-8601日期和時間值（不含毫秒）。 範圍必須為31天或更少。 此篩選器未使用可見的`updatedAt`欄位，這僅反映標準欄位的更新。 相反地，它會使用潛在客戶記錄最近一次欄位更新的時間。 此作業會傳回日期範圍內最近更新的所有可存取記錄。 |
| staticListName | 字串 | 靜態清單的名稱。 作業開始處理時，會傳回屬於靜態清單成員的所有可存取記錄。 使用取得清單端點擷取靜態清單名稱。 |
| staticListId | 整數 | 靜態清單的ID。 作業開始處理時，會傳回屬於靜態清單成員的所有可存取記錄。 使用取得清單端點擷取靜態清單ID。 |
| 智慧型清單名稱* | 字串 | 智慧清單的名稱。 當工作開始處理時，工作會傳回智慧清單成員的所有可存取記錄。 使用取得智慧清單端點擷取智慧清單名稱。 |
| smartListId* | 整數 | 智慧清單的ID。 當工作開始處理時，工作會傳回智慧清單成員的所有可存取記錄。 使用「取得智慧列示」端點擷取智慧列示ID。 |

某些訂閱無法使用以星號標示的篩選型別。 如果您的訂閱無法使用篩選器型別，則「建立匯出潛在客戶工作」端點會傳回錯誤「1035，目標訂閱不支援的篩選器型別」。 請聯絡Marketo支援，為您的訂閱啟用此功能。

## 選項

「建立匯出潛在客戶作業」端點提供選項，用於選取匯出的欄位、重新命名欄標題及設定檔案格式。

| 參數 | 資料類型 | 必要 | 附註 |
| --- | --- | --- | --- |
| 欄位 | 陣列[字串] | 是 | 字串的JSON陣列。 每個字串都必須是Marketo潛在客戶欄位的REST API名稱。 匯出包含每個列出的欄位，並使用其REST API名稱作為欄標題，除非`columnHeaderNames`覆寫它。 [!DNL Adobe Experience Cloud Audience Sharing]功能啟用時，Cookie同步程式會將[!DNL Adobe Experience Cloud] ID (ECID)與Marketo銷售機會建立關聯。 指定`ecids`欄位，將ECID包含在匯出檔案中。 |
| columnHeaderName | 物件 | 無 | 欄位和欄標題索引鍵值配對的JSON物件。 每個金鑰必須是匯出作業中包含的欄位的API名稱。 呼叫Describe Lead以擷取API名稱。 每個值都是該欄位的匯出欄標題。 |
| 格式 | 字串 | 無 | 匯出檔案格式：CSV適用於逗號分隔值，TSV適用於定位字元分隔值，或SSV適用於空格分隔值。 預設值為CSV。 |

## 建立工作

使用[建立匯出潛在客戶作業](https://developer.adobe.com/marketo-apis/api/mapi#operation/createExportLeadsUsingPOST)端點來定義匯出作業。 指定要匯出的`fields`、一個`filter`型別及其引數、檔案`format`以及任何自訂資料行標頭名稱。

```http
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
         "endAt": "2017-01-31T00:00:00Z"
      }
   }
}
```

此請求會針對2017年1月1日至2017年1月31日期間建立的潛在客戶建立匯出工作。 匯出包含來自`firstName`、`lastName`、`id`及`email`欄位的值。

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

回應會確認工作已建立但尚未啟動。 若要啟動工作，請從建立回應呼叫具有`exportId`的[排入佇列匯出潛在客戶工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/enqueueExportLeadsUsingPOST)端點。

```http
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

排入佇列回應有`status`個「已排入佇列」。 當匯出槽可供使用時，狀態會變更為「正在處理」。

## 輪詢工作狀態

您只能擷取相同API使用者所建立之工作的狀態。

潛在客戶匯出工作會非同步執行。 輪詢[取得匯出潛在客戶工作狀態](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportLeadsStatusUsingGET)端點以追蹤工作進度。

狀態每60秒僅更新一次。 請勿更頻繁地輪詢；在幾乎所有情況下，該間隔仍然過大。

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
         "status": "Processing",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

此回應顯示工作仍在處理中，因此檔案無法使用。 當工作狀態變更為「已完成」時，檔案就可供下載。

`status`欄位可傳回下列任何值：

- 建立日期
- 已排入佇列
- 處理中
- 已取消
- 已完成
- 失敗

## 正在擷取您的資料

若要擷取已完成的銷售機會匯出，請使用`exportId`呼叫[取得匯出銷售機會檔案](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportLeadsFileUsingGET)端點。

```http
GET /bulk/v1/leads/export/{exportId}/file.json
```

回應內文包含為工作設定的格式的檔案。

如果要求的潛在客戶欄位不含任何資料，則匯出檔案中的對應欄位會包含`null`。 在以下範例中，傳回的潛在客戶具有空白電子郵件欄位。

```csv
firstName,lastName,email,cookies
Russell,Wilson,null,_mch-localhost-1536605780000-12105
```

對於部分或可恢復擷取，檔案端點支援具有`bytes`型別的選用HTTP `Range`標頭。 如果您未設定標頭，端點會傳回所有內容。 深入瞭解如何搭配Marketo [大量擷取](bulk-extract.md)使用`Range`標頭。

## 取消工作

若要取消設定錯誤或不必要的工作，請呼叫[取消匯出潛在客戶工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/cancelExportLeadsUsingPOST)端點。

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
         "format": "CSV"
      }
   ]
}
```

回應會確認工作已取消。
