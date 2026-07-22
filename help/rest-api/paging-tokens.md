---
title: 分頁權杖
feature: REST API
description: 使用Marketo REST API分頁權杖來擷取活動和銷售機會，包括以日期和位置為基礎的權杖、ISO 8601 sinceDatetime和414錯誤。
exl-id: 63fbbf03-8daf-4add-85b0-a8546c825e5b
TQID: https://experienceleague.adobe.com/Ut05n-Y-qPJnvcNRs9liwE3NVBMbJlvaGyv-nExRsek
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 387
ht-degree: 0%

---

# 分頁權杖

Marketo提供分頁權杖，用以逐頁瀏覽結果或擷取相對於特定日期更新的資料。

有些回應會傳回長分頁權杖字串，這可能會造成HTTP 414錯誤。 檢視有關處理這些[錯誤](error-codes.md)的資訊。

請參閱[分頁Token API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getActivitiesPagingTokenUsingGET)檔案。

## 權杖型別

Marketo提供兩種相關但相異的分頁權杖：

- 以日期為基準的Token會擷取指定日期時間之後發生的記錄。
- 以位置為基礎的Token會周遊結果集中的記錄。

## 基於日期

以日期為基礎的分頁權杖代表日期時間。 使用它來擷取在該日期時間之後發生的活動、資料值變更和刪除的潛在客戶。

透過呼叫具有日期時間的[取得分頁權杖](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getActivitiesPagingTokenUsingGET)端點來產生日期型權杖：

```http
GET /rest/v1/activities/pagingtoken.json?sinceDatetime=2014-10-06T13:22:17-08:00
```

```json
{
    "requestId": "1607c#14884f3e74e",
    "success": true,
    "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
}
```

`sinceDateTime`引數必須使用[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)標準日期標籤法。 為了獲得最佳結果，請提供包含時區的完整日期時間。

以下列格式將時區表示為自GMT的位移：

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

或者，使用大寫字母「Z」表示UTC：

`yyyy-mm-ddThh:mm:ssZ`

例如：

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

因為`sinceDateTime`是查詢引數，所以對其值進行URL編碼。

將傳回的`nextPageToken`字串傳遞至[取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET)、[取得潛在客戶變更](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadChangesUsingGET)或[取得已刪除的潛在客戶](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getDeletedLeadsUsingGET)呼叫。 呼叫會擷取在提供給Get Paging Token API的日期時間之後發生的記錄。

```http
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## 基於位置

對Lead Database API的任何批次擷取呼叫都可以傳回以位置為基礎的分頁權杖。 權杖的運作方式與資料庫游標類似，可讓您周遊記錄。

例如，依篩選型別取得銷售機會呼叫可傳回大於請求批次大小的結果集，該批次大小通常具有最大值和預設值300。 如果有更多結果可用，回應會將moreResult欄位設為true並傳回`nextPageToken`。

若要擷取下一個頁面，請再次呼叫，並從上一個回應傳遞`nextPageToken`值。 回應會傳回結果集的下一頁。
