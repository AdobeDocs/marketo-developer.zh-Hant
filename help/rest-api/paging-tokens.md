---
title: 「分頁Token」
feature: REST API
description: 「檢視分頁Token資料。」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '345'
ht-degree: 0%

---


# 分頁權杖

為了逐頁瀏覽結果，或擷取相對於指定資料更新的資料，Marketo提供分頁權杖。

在某些情況下，可能會傳回長分頁Token字串。 這可能會造成您遇到HTTP 414錯誤碼。 您可以找到有關如何處理這些專案的詳細資訊 [錯誤](error-codes.md).

## 權杖型別

Marketo提供兩種相關但相異的分頁權杖型別：

- 基於日期
- 基於位置

## 基於日期

第一個是代表日期的分頁權杖。 這些可用來擷取活動、資料值變更，以及分頁Token所代表的日期之後發生的已刪除潛在客戶。 此型別的分頁權杖是透過呼叫 [取得分頁Token](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET) 端點並包含日期時間。

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

的格式 `sinceDateTime` 引數必須符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 標準日期標籤法。 為了獲得最佳結果，請使用包含時區的完整日期時間。 時區可使用以下格式表示為GMT時差：

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

或者使用大寫字母「Z」作為簡寫來表示UTC，如下所示：

`yyyy-mm-ddThh:mm:ssZ`

範例

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

因為 `sinceDateTime` 是查詢引數，必須經過URL編碼。

此 `nextPageToken` 然後字串會提供給 [取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET)， [取得銷售機會變更](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET)，或 [取得已刪除的銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET) 呼叫，並從提供給取得分頁Token API的日期時間之後擷取活動。

```
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## 基於位置

對Lead Database API的任何批次擷取呼叫都可以傳回第二種型別的分頁權杖。 這種分頁Token在概念上與啟用記錄周遊的資料庫游標類似。 例如，依篩選型別取得銷售機會呼叫可能代表大於指定批次大小的集合，通常最大和預設為300。 如果有更多結果，回應中的moreResult欄位會是true，而且 `nextPageToken` 會傳回。 若要擷取結果集中的其他記錄，另一個呼叫包括 `nextPageToken` ，其中包含新呼叫中前一個回應的已接收值。 產生的回應將傳回結果集中的下一個頁面。
