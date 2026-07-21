---
title: 大量程式成員匯入
feature: REST API
description: 瞭解如何使用10MB以下的CSV TSV或SSV檔案、佇列限制、必要引數和輪詢工作狀態，透過Marketo REST API大量匯入方案成員。
exl-id: b0e1039a-fe9b-4fb7-9aa6-9980a06da673
TQID: https://experienceleague.adobe.com/T1PAzLN1mnp38kJ0jwh6kPv6r1Uvxc7-o9zeTHetIV0
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 771
ht-degree: 0%

---

# 大量程式成員匯入

[大量程式成員匯入端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members)

使用[大量API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members)以非同步方式匯入大量程式成員記錄。 以逗號、定位字元或分號分隔的平面檔案提供小於10 MB的記錄。

大量程式成員匯入僅支援「插入或更新」記錄作業。

## 處理限制

每個大量匯入請求都會新增為先進先出(FIFO)佇列的工作。 下列限制適用：

- 最多可同時處理兩個工作。
- 佇列中最多可有10個工作，包括正在處理的兩個工作。

如果您超過10個作業的上限，API會傳回`1016, Too many imports`錯誤。

## 匯入檔案

檔案的第一列必須是標頭，該標頭會列出每列對應值的REST API欄位名稱。 使用[Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2)和[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeProgramMemberUsingGET)端點擷取這些名稱。

記錄可包含潛在客戶欄位、自訂潛在客戶欄位和自訂方案成員欄位。

典型的檔案會遵循以下模式：

```text
email,firstName,lastName
test@example.com,John,Doe
```

使用`multipart/form-data`內容型別傳送要求。 使用現有的程式庫實作來建構多部分要求。

## 建立工作

[匯入程式成員](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/importProgramMemberUsingPOST)端點會從檔案讀取程式成員記錄，並將它們新增至具有指定狀態的程式。 記錄可包含潛在客戶欄位和自訂方案成員欄位。

每個記錄都必須包含電子郵件欄位，用於重複資料刪除。

`programId`路徑引數指定成員加入的程式。

此請求需要三個查詢引數：

- `format`：匯入檔案格式（`CSV`、`TSV`或`SSV`）。
- `programMemberStatus`：指派給匯入成員的方案狀態。
- `file`：包含程式成員記錄的檔案名稱。

```http
POST /bulk/v1/program/{programId}/members/import.json?format=csv&programMemberStatus=On List
```

```text
Content-Type: multipart/form-data; boundary=--------------------------118046853683028616211319
Content-Length: 772
Host: <munchkinId>.mktorest.com
```

```text
----------------------------118046853683028616211319
Content-Disposition: form-data; name="file"; filename="Lead-House-Lannister.csv"
Content-Type: text/csv

firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0

----------------------------118046853683028616211319--
```

```json
{
    "requestId": "17f4a#16f87f87325",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Queued"
        }
    ],
    "success": true
}
```

因為端點非同步，所以回應包含`batchId`和`status`欄位。 狀態可以是`Queued`、`Importing`或`Failed`。

保留`batchId`以檢查匯入狀態，並在完成後擷取失敗或警告。 `batchId`的有效期限為七天。

以下命令列cURL請求會提交範例作業：

```bash
curl -i -F format='csv' -F programMemberStatus='On List' -F file='@Lead-House-Lannister.csv' -F access_token='<Access Token>' <REST API Endpoint Base URL>/bulk/v1/program/{programId}/members/import.json
```

在此範例中，`Lead-House-Lannister.csv`匯入檔案包含下列資料：

```text
firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0
```

## 輪詢工作狀態

建立匯入工作後，每5到30秒輪詢一次。 將`batchId`路徑引數傳遞至[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)端點。

```http
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "e0cb#16f87f8b177",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Complete",
            "numOfLeadsProcessed": 8,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 8 records imported (8 members)"
        }
    ],
    "success": true
}
```

此回應會顯示已完成的匯入。 狀態可以是`Complete`、`Queued`、`Importing`或`Failed`。

當工作完成時，回應會列出已處理、失敗和已處理但出現警告的列數。 當狀態為`Failed`時，`message`引數也可以提供失敗訊息。

## 失敗

[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)回應中的`numOfRowsFailed`屬性表示失敗的資料列數目。 值大於零表示發生失敗。

將`batchId`路徑引數傳遞至[取得匯入程式成員失敗]端點，以擷取失敗的記錄及其原因。

```http
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

端點會傳回可識別每個失敗列的檔案，並說明記錄失敗的原因。 檔案在建立工作期間使用`format`引數指定的格式。 每個記錄上的額外欄位說明了失敗。

例如，假設您匯入下列潛在客戶分數無效的檔案：

```text
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD
```

工作狀態傳回`numOfRowsFailed`為1，表示發生失敗：

```http
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "4c2d#16f8b32c8ef",
    "result": [
        {
            "batchId": 1046,
            "importId": "1046",
            "status": "Complete",
            "numOfLeadsProcessed": 0,
            "numOfRowsFailed": 1,
            "numOfRowsWithWarning": 0,
            "message": "Import completed with errors, 0 records imported (0 members), 1 failed"
        }
    ],
    "success": true
}
```

擷取失敗檔案以取得詳細資訊：

```http
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

```text
firstName,lastName,email,title,company,leadScore,Import Failure Reason
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD,Invalid data type in field Lead Score
```

## 警告

[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)回應中的`numOfRowsWithWarning`屬性表示含有警告的資料列數目。 大於零的值表示發生警告。

將`batchId`路徑引數傳遞至[取得匯入程式成員警告](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberWarningsUsingGET)端點，以擷取受影響的記錄及其原因。

```http
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

端點會傳回檔案，此檔案會識別每個具有警告的列，並說明警告發生的原因。 檔案在建立工作期間使用`format`引數指定的格式。 每個記錄上的額外欄位會說明警告。

例如，假設您匯入下列電子郵件地址無效的檔案：

```text
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0
```

工作狀態傳回`numOfRowsWithWarning`為1，表示發生警告：

```http
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
   "requestId":"4ca1#16f883c2003",
   "result":[
      {
         "batchId":1041,
         "importId":"1041",
         "status":"Complete",
         "numOfLeadsProcessed":1,
         "numOfRowsFailed":0,
         "numOfRowsWithWarning":1,
         "message":"Import succeeded, 1 records imported (1 members), 1 warning."
      }
   ],
   "success":true
}
```

擷取警告檔案以取得詳細資訊：

```http
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

```text
firstName,lastName,email,title,company,leadScore,Import Warning Reason
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0,Invalid email address
```
