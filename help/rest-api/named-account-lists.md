---
title: 具名帳戶清單
feature: REST API
description: 瞭解如何使用REST API管理Marketo具名帳戶清單，包括查詢、建立、更新和刪除的許可權、欄位、篩選條件和端點。
exl-id: 98f42780-8329-42fb-9cd8-58e5dbea3809
TQID: https://experienceleague.adobe.com/18lMhheW21Gz1-3TMHwleHhmLTOqJsZSQ5aqkbbchhM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 686
ht-degree: 2%

---

# 具名帳戶清單

[具名帳戶列出端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Account-Lists)

[具名帳戶清單](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/target-account-management/target/account-lists)是Marketo中的具名帳戶集合。 將其用於分類、資料擴充和智慧型行銷活動篩選。

具名帳戶清單API可讓您從遠端管理清單資產及其成員資格。
`Content`

## 權限

所需的許可權取決於作業：

- 查詢具名帳戶清單：唯讀具名帳戶清單或讀寫具名帳戶清單。
- 建立、更新或刪除清單：讀寫具名帳戶清單。
- 查詢清單成員資格：唯讀指定帳戶或讀寫指定帳戶。
- 管理清單成員資格：讀寫具名帳戶。

## 模型

具名帳戶清單的標準欄位集有限，不支援自訂欄位。
`Named Account List Field`

| 名稱 | 資料類型 | 可更新 | 附註 |
| --- | --- | --- | --- |
| marketoGUID | 字串 | False | 具名帳戶清單的唯一字串識別碼。 此欄位由系統管理，不允許在建立記錄時作為欄位。 「dedupeBy」：「idField」執行建立或更新時所使用的欄位。 |
| 名稱 | 字串 | True | 清單的名稱。 「dedupeBy」在執行建立或更新時所使用的欄位：「dedupeFields」。 |
| createdAt | 日期時間 | False | 建立清單的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 更新時間 | 日期時間 | False | 清單最近更新的日期時間。 此欄位由系統管理，在建立或更新記錄時不允許作為欄位。 |
| 類型 | 字串 | False | 清單型別。 的值可能是「預設」或「外部」。 外部清單是由CRM帳戶檢視建立的清單。 |

## 查詢

具名帳戶清單查詢支援兩種filterTypes：&quot;dedupeFields&quot;和&quot;idField&quot;。 設定`filterType`查詢引數中的欄位，並提供逗號分隔清單`filterValues as`中的值。

`nextPageToken`和`batchSize`篩選器為選用。

```http
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

使用標準Lead Database模式來建立與更新具名帳戶清單記錄。 具名帳戶清單只有一個可更新的欄位： `name`。

端點支援兩種標準動作型別：「createOnly」和「updateOnly」。 `action defaults`為「createOnly」。

您可以在動作為`updateOnly`時指定選擇性`dedupeBy parameter`。 允許的值為「dedupeFields」（與「name」相對應）和「idField」（與「marketoGUID」相對應）。

在`createOnly`模式中，`dedupeBy`欄位僅允許&quot;name&quot;。 您一次最多可以提交300筆記錄。

```http
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

使用清單的`name`或`marketoGUID`刪除具名帳戶清單。 若要選取金鑰，請在請求的`deleteB`成員中，為name傳遞「dedupeFields」，或為marketoGUID傳遞「idField」。

如果未設定，該值會預設為dedupeFields。 您一次最多可以刪除300筆記錄。

```http
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

如果找不到索引鍵的記錄，則對應的結果專案會有`status`個「已略過」。 此外，也包含原因及說明失敗的程式碼和訊息。

## 管理成員資格

### 查詢成員資格

提供帳戶清單的`i`來查詢具名帳戶清單成員資格。 選用的引數包括：

-`field` — 要包含在回應記錄中的逗號分隔欄位清單
-`nextPageToke` — 用於分頁結果集
-`batchSiz` — 指定要傳回的記錄數

如果未設定`field`，則會傳回`marketoGUI`、`nam`、`createdA`和`updatedA`。 `batchSiz`的最大值和預設值為300。

```http
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

使用其marketoGUID將具名帳戶新增至具名帳戶清單。 您一次最多可以新增300筆記錄。

```http
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

從帳戶清單中移除記錄時，會使用不同的路徑但相同的介面。 為每個要移除的記錄提供`marketoGUI`。 您一次最多可以移除300筆記錄。

```http
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

- 除非另有說明，否則具名帳戶清單端點的逾時值為30秒。
- 同步具名帳戶清單的逾時時間為60秒。
- 刪除具名帳戶清單的逾時時間為60秒。
- 取得具名帳戶清單的逾時時間為60秒。
- 新增具名帳戶清單成員的逾時時間為60秒。
- 移除具名帳戶清單成員的逾時時間為60秒。
- 取得具名帳戶清單成員的逾時時間為60秒。
