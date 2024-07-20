---
title: 分頁權杖
feature: REST API
description: 檢視分頁權杖資料。
exl-id: 63fbbf03-8daf-4add-85b0-a8546c825e5b
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '345'
ht-degree: 0%

---

# 分頁權杖

為了逐頁瀏覽結果，或擷取相對於指定資料更新的資料，Marketo提供分頁權杖。

在某些情況下，可能會傳回長分頁Token字串。 這可能會造成您遇到HTTP 414錯誤碼。 您可以找到有關如何處理這些[錯誤](error-codes.md)的詳細資訊。

## 權杖型別

Marketo提供兩種相關但相異的分頁權杖型別：

- 基於日期
- 基於位置

## 基於日期

第一個是代表日期的分頁權杖。 這些可用來擷取活動、資料值變更，以及分頁Token所代表的日期之後發生的已刪除潛在客戶。 此型別的分頁權杖是透過呼叫[取得分頁權杖](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET)端點並包含日期時間所產生。

```
GET /rest/v1/activities/pagingtoken.json?sinceDatetime=2014-10-06T13:22:17-08:00
```

```json
{
    "requestId": "1607c#14884f3e74e",
    "success": true,
    "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
}
```

`sinceDateTime`引數的格式必須符合[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)標準日期標籤法。 為了獲得最佳結果，請使用包含時區的完整日期時間。 時區可使用以下格式表示為GMT時差：

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

或者使用大寫字母「Z」作為簡寫來表示UTC，如下所示：

`yyyy-mm-ddThh:mm:ssZ`

範例

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

因為`sinceDateTime`是查詢引數，所以它必須經過URL編碼。

然後會將`nextPageToken`字串提供給[取得銷售機會活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET)、[取得銷售機會變更](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET)或[取得已刪除的銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET)呼叫，而活動會在提供給Get Paging Token API的日期時間之後擷取。

```
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## 基於位置

對Lead Database API的任何批次擷取呼叫都可以傳回第二種型別的分頁權杖。 這種分頁Token在概念上與啟用記錄周遊的資料庫游標類似。 例如，依篩選型別取得銷售機會呼叫可能代表大於指定批次大小的集合，通常最大和預設為300。 如果有更多結果，回應中的moreResult欄位會是true，且會傳回`nextPageToken`。 若要擷取結果集中的其他記錄，另一個呼叫包含`nextPageToken`，其中包含新呼叫中先前回應的接收值。 產生的回應將傳回結果集中的下一個頁面。
