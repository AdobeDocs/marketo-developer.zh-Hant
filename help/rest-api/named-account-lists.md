---
title: 「具名帳戶清單」
feature: REST API
description: 「設定具名帳戶清單。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '696'
ht-degree: 2%

---


# 具名帳戶清單

[具名帳戶列出端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Account-Lists)

[具名帳戶清單](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/target-account-management/target/account-lists) 在Marketo中，代表具名帳戶的集合。 它們可用於多種情況，包括分類、資料擴充和智慧型行銷活動篩選。 具名帳戶清單API允許從遠端管理這些清單資產及其成員資格。
`Content`

## 權限

若要查詢具名帳戶清單，需要唯讀具名帳戶清單或讀寫具名帳戶清單許可權。 若要建立、更新或刪除清單，需要讀寫具名帳戶清單許可權。 查詢清單成員資格需要唯讀具名帳戶或讀寫具名帳戶許可權，而管理成員資格則需要讀寫具名帳戶許可權。

## 模型

具名帳戶清單的標準欄位數量有限，且無法與自訂欄位一起擴充。
`Named Account List Field`

| 名稱 | 資料類型 | 可更新 | 附註 |
|---|---|---|---|
| marketoGUID | 字串 | 假 | 具名帳戶清單的唯一字串識別碼。 此欄位由系統管理，不允許在建立記錄時作為欄位。 「dedupeBy」：「idField」執行建立或更新時所使用的欄位。 |
| 名稱 | 字串 | 真 | 清單的名稱。 「dedupeBy」在執行建立或更新時所使用的欄位：「dedupeFields」。 |
| createdAt | 日期時間 | 假 | 建立清單的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 更新時間 | 日期時間 | 假 | 清單最近更新的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 類型 | 字串 | 假 | 清單型別。 的值可能是「預設」或「外部」。 外部清單是由CRM帳戶檢視建立的清單。 |


## 查詢

查詢帳戶清單簡單明瞭。 目前，查詢具名帳戶清單的有效filterTypes只有兩種：&quot;dedupeFields&quot;和&quot;idField&quot;。 要篩選的欄位設定在 `filterType` 引數，而值設定於 `filterValues as` 逗號分隔清單。 此 `nextPageToken` 和 `batchSize` 篩選也是選用引數。

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

建立和更新具名帳戶清單記錄會遵循其他Lead Database建立和更新作業的已建立模式。 請記住，具名帳戶清單只有一個可更新的欄位， `name`.

端點允許兩種標準動作型別：「createOnly」和「updateOnly」。  此 `action defaults` 至「createOnly」。

選填 `dedupeBy parameter` 可以指定動作是否為 `updateOnly`.  允許的值是&quot;dedupeFields&quot; （對應至&quot;name&quot;）或&quot;idField&quot; （對應至&quot;marketoGUID&quot;）。  在 `createOnly` 模式，僅允許以「name」作為 `dedupeBy` 欄位。 您一次最多可以提交300筆記錄。

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

刪除具名帳戶清單非常簡單，您可以根據 `name`，或 `marketoGUID` 清單的。 若要選取您要使用的金鑰，請在「 」中將「dedupeFields」傳遞給name，或將「idField」傳遞給marketoGUID`deleteB` 請求的成員。 如果未設定，則預設為dedupeFields。 您一次最多可以刪除300筆記錄。

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

在找不到指定索引鍵的記錄的情況下，對應的結果專案將具有`status` 以及說明失敗的程式碼和訊息的原因，如上述範例所示。

## 管理成員資格

### 查詢成員資格

查詢具名帳戶清單的成員資格非常簡單，只需指定`i` 帳戶清單的。 可選引數包括：

-`field`  — 回應記錄中包含的逗號分隔欄位清單 — `nextPageToke`  — 用於分頁結果集 — `batchSiz`  — 指定要傳回的記錄數

如果`field` 未設定，則`marketoGUI`，`nam`， `createdA`、和`updatedA` 將會傳回。 `batchSiz` 具有最大和預設值300。

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

從帳戶清單中移除記錄具有不同的路徑，但需要相同的介面，`marketoGUI` 每個要刪除的記錄。 您一次最多可以移除300筆記錄。

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
