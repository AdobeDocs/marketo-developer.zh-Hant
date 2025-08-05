---
title: 具名帳戶清單
feature: REST API
description: 設定具名帳戶清單。
exl-id: 98f42780-8329-42fb-9cd8-58e5dbea3809
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '696'
ht-degree: 2%

---

# 具名帳戶清單

[具名帳戶列出端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Account-Lists)

Marketo中的[具名帳戶清單](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/target-account-management/target/account-lists)代表具名帳戶的集合。 它們可用於多種情況，包括分類、資料擴充和智慧型行銷活動篩選。 具名帳戶清單API允許從遠端管理這些清單資產及其成員資格。
`Content`

## 權限

若要查詢具名帳戶清單，需要唯讀具名帳戶清單或讀寫具名帳戶清單許可權。 若要建立、更新或刪除清單，需要讀寫具名帳戶清單許可權。 查詢清單成員資格需要唯讀具名帳戶或讀寫具名帳戶許可權，而管理成員資格則需要讀寫具名帳戶許可權。

## 模型

具名帳戶清單的標準欄位數量有限，且無法與自訂欄位一起擴充。
`Named Account List Field`

| 名稱 | 資料類型 | 可更新 | 附註 |
|---|---|---|---|
| marketoGUID | 字串 | False | 具名帳戶清單的唯一字串識別碼。 此欄位由系統管理，不允許在建立記錄時作為欄位。 「dedupeBy」：「idField」執行建立或更新時所使用的欄位。 |
| 名稱 | 字串 | True | 清單的名稱。 「dedupeBy」在執行建立或更新時所使用的欄位：「dedupeFields」。 |
| createdAt | 日期時間 | False | 建立清單的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 更新時間 | 日期時間 | False | 清單最近更新的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 類型 | 字串 | False | 清單型別。 的值可能是「預設」或「外部」。 外部清單是由CRM帳戶檢視建立的清單。 |

## 查詢

查詢帳戶清單簡單明瞭。 目前，查詢具名帳戶清單的有效filterTypes只有兩種：&quot;dedupeFields&quot;和&quot;idField&quot;。 要篩選的欄位設定在查詢的`filterType`引數中，而值設定在`filterValues as`中以逗號分隔的清單中。 `nextPageToken`和`batchSize`篩選器也是選用引數。

```
GET /rest/v1/namedAccountLists.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fb,dff23271-f996-47d7-984f-f2676861b5fc
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      }
   ]
}
```

## 建立和更新

建立和更新具名帳戶清單記錄會遵循其他Lead Database建立和更新作業的已建立模式。 請記住，具名帳戶清單只有一個可更新的欄位`name`。

端點允許兩種標準動作型別：「createOnly」和「updateOnly」。  `action defaults`為「createOnly」。

如果動作為`dedupeBy parameter`，則可指定選擇性`updateOnly`。  允許的值是&quot;dedupeFields&quot; （對應至&quot;name&quot;）或&quot;idField&quot; （對應至&quot;marketoGUID&quot;）。  在`createOnly`模式中，`dedupeBy`欄位僅允許&quot;name&quot;。 您一次最多可以提交300筆記錄。

```
POST /rest/v1/namedAccountLists.json
```

```json
{
   "action": "createOnly",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "name": "SAAS List"
      },
      {
         "name": "Manufacturing (Domestic)"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

## 刪除

刪除具名帳戶清單非常簡單，您可以根據`name`或清單的`marketoGUID`完成。 若要選取您要使用的金鑰，請在請求的`deleteB`成員中，為name傳遞「dedupeFields」，或為marketoGUID傳遞「idField」。 如果未設定，則預設為dedupeFields。 您一次最多可以刪除300筆記錄。

```
POST /rest/v1/namedAccountLists/delete.json
```

```json
{
   "deleteBy": "dedupeFields",
   "input": [
      {
         "name": "Saas List"
      },
      {
         "name": "B2C List"
      },
      {
         "name": "Launchpoint Partner List"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq": 1,
         "id": "dff23271-f996-47d7-984f-f2676861b5fc",
         "status": "deleted"
      },
      {
         "seq": 2,
         "status": "skipped",
         "reasons": [
            {
               "code": "1013",
               "message": "Record not found"
            }
         ]
      }
   ]
}
```

在找不到指定索引鍵的記錄的情況下，對應的結果專案會有`status`個「已略過」，以及包含說明失敗之程式碼和訊息的原因，如上述範例所示。

## 管理成員資格

### 查詢成員資格

查詢具名帳戶清單的成員資格很簡單，只需要帳戶清單的`i`。 可選引數包括：

-`field` — 要包含在回應記錄中的逗號分隔欄位清單
-`nextPageToke` — 用於分頁結果集
-`batchSiz` — 指定要傳回的記錄數

如果未設定`field`，則會傳回`marketoGUI`、`nam`、`createdA`和`updatedA`。 `batchSiz`的最大值和預設值為300。

```
GET /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      }
   ]
}
```

### 新增成員

可將具名帳戶輕鬆新增至具名帳戶清單。 只能使用其marketoGUID新增帳戶。 您一次最多可以新增300筆記錄。

```
POST /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true,
}
```

### 移除成員

從帳戶清單移除記錄有不同的路徑，但介面相同，您想要刪除的每個記錄都需要`marketoGUI`。 您一次最多可以移除300筆記錄。

```
POST /rest/v1/namedAccountList/{id}/namedAccounts/remove.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true
}
```

## 逾時

- 具名帳戶清單端點的逾時為30秒，除非以下說明
   - 同步命名帳戶清單： 60秒
   - 刪除具名帳戶清單： 60秒
   - 取得具名帳戶清單：60秒
   - 新增具名帳戶清單成員：60秒
   - 移除具名帳戶清單成員：60秒
   - 取得具名帳戶清單成員：60秒
