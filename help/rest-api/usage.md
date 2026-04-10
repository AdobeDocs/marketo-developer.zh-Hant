---
title: 使用情況
feature: REST API
description: 使用每日和過去7天的統計端點監視Marketo REST API使用情況和錯誤，包括每位使用者的計數和錯誤碼總計。
exl-id: 935a00a4-1e1e-4b48-ae9c-72c5e578312a
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '393'
ht-degree: 8%

---

# 使用情況

[使用方式端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Usage)

使用API提供您訂閱的REST API使用量和錯誤活動摘要。 這些端點對於監控整合、追蹤每日來電數量及識別一段時間的錯誤趨勢相當實用。

使用量資料包括API呼叫總數和每個使用者的劃分。 錯誤資料包括錯誤總數以及依錯誤碼的劃分。

「使用情況API」使用與其他Marketo REST API相同的驗證方法。 在`Authorization: Bearer {accessToken}`標頭中傳遞存取權杖。

## 端點

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/rest/v1/stats/usage.json` | 擷取當天API使用狀況。 |
| GET | `/rest/v1/stats/usage/last7days.json` | 擷取過去7天的API使用狀況。 |
| GET | `/rest/v1/stats/errors.json` | 擷取當天的API錯誤。 |
| GET | `/rest/v1/stats/errors/last7days.json` | 擷取過去7天的API錯誤。 |

## 每日使用情況

擷取當天API使用狀況。

```http
GET /rest/v1/stats/usage.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 1120,
         "users": [
            {
               "userId": "some.body@yahoo.com",
               "count": 200
            },
            {
               "userId": "some.body@marketo.com",
               "count": 200
            },
            {
               "userId": "some.body@gmail.com",
               "count": 720
            }
         ]
      }
   ]
}
```

`result`陣列中的每個物件包含一天的使用量總計和每個使用者的劃分。

## 過去7天的使用狀況

擷取過去7天的API使用狀況。 `result`陣列中的每個元素代表一天。

```http
GET /rest/v1/stats/usage/last7days.json
```

## 每日錯誤

擷取當天的API錯誤。

```http
GET /rest/v1/stats/errors.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 73,
         "errors": [
            {
               "errorCode": "604",
               "count": 1
            },
            {
               "errorCode": "609",
               "count": 56
            },
            {
               "errorCode": "610",
               "count": 16
            }
         ]
      }
   ]
}
```

`result`陣列中的每個物件都包含一天錯誤總計和依錯誤碼劃分的資訊。

## 過去7天的錯誤

擷取過去7天的API錯誤。 `result`陣列中的每個元素代表一天。

```http
GET /rest/v1/stats/errors/last7days.json
```

## 回應成員

### 使用結果物件

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| `date` | 字串 | `YYYY-MM-DD`格式的使用摘要日期。 |
| `total` | 整數 | 當天的API呼叫總數。 |
| `users` | 陣列 | 當天每位使用者使用次數的清單。 |

### 使用狀況使用者物件

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| `userId` | 字串 | API使用者識別碼。 |
| `count` | 整數 | 該使用者當天進行的API呼叫數。 |

### 錯誤結果物件

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| `date` | 字串 | 錯誤摘要的日期，格式為`YYYY-MM-DD`。 |
| `total` | 整數 | 當天的API錯誤總數。 |
| `errors` | 陣列 | 當天的每個錯誤碼計數清單。 |

### 錯誤物件

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| `errorCode` | 字串 | Marketo錯誤碼。 |
| `count` | 整數 | 當天發生錯誤的次數。 |

## 附註

您的每位API使用者都會在使用情況回應中個別回報。 將整合分割給不同的API使用者，可讓您更輕鬆地識別使用配額且產生錯誤的服務。
