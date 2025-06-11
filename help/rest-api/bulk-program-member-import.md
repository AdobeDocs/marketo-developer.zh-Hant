---
title: 大量程式成員匯入
feature: REST API
description: 批次匯入成員資料。
exl-id: b0e1039a-fe9b-4fb7-9aa6-9980a06da673
source-git-commit: 8a785b0719e08544ed1a87772faf90bd9dda3077
workflow-type: tm+mt
source-wordcount: '837'
ht-degree: 0%

---

# 大量程式成員匯入

[大量程式成員匯入端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members)

對於大量的程式成員記錄，可以使用[大量API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members)非同步匯入程式成員。 這可讓您使用含分隔字元（逗號、定位字元或分號）的平面檔案，將記錄清單匯入Marketo。 檔案可包含任意數量的記錄，只要檔案總計小於10MB即可。 記錄作業僅限「插入或更新」。

## 處理限制

您可以提交一個以上的大量匯入要求，但會有限制。 每個請求都會當作工作新增至FIFO佇列進行處理。 最多可同時處理兩個工作。 在任何指定時間，佇列中最多允許10個工作（包括目前處理的2個）。 如果超過十個作業的上限，則會傳回「1016，匯入次數過多」錯誤。

## 匯入檔案

檔案的第一列必須是標題，標題會列出對應的REST API名稱，作為要將每列的值對應到的欄位。 可以使用[描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2)和/或[描述方案成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeProgramMemberUsingGET)端點來擷取REST API名稱。 記錄可包含潛在客戶欄位、自訂潛在客戶欄位和自訂方案成員欄位。

典型的檔案會遵循此基本模式：

```
email,firstName,lastName
test@example.com,John,Doe
```

呼叫本身是使用`multipart/form-data`內容型別所執行。

此請求型別可能很難實作，因此強烈建議您使用現有程式庫實作。

## 建立工作

[匯入程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/importProgramMemberUsingPOST)端點會讀取包含程式成員記錄的檔案，並將它們新增至具有指定狀態的程式。 記錄可以同時包含潛在客戶欄位和方案成員自訂欄位。 所有記錄都必須包含電子郵件欄位，用於重複資料刪除目的。

`programId`路徑引數指定成員加入的程式。

有三個必要的查詢引數。 `format`引數指定匯入檔案格式（CSV、TSV或SSV），`programMemberStatus`引數指定正在加入程式的成員的程式狀態，而`file`引數包含包含包含程式成員記錄的匯入檔案名稱。

```
POST /bulk/v1/program/{programId}/members/import.json?format=csv&programMemberStatus=On List
```

```
Content-Type: multipart/form-data; boundary=--------------------------118046853683028616211319
Content-Length: 772
Host: <munchkinId>.mktorest.com
```

```
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

請注意，在回應我們的呼叫時，結果陣列中的記錄有`batchId`和`status`欄位。 由於此端點為非同步，因此它可以傳回「已排入佇列」、「匯入」或「失敗」狀態。 您必須保留`batchId`才能取得匯入工作的狀態，以及在完成時擷取失敗和/或警告。 `batchId`的有效期限為七天。

使用上面的範例，呼叫端點的簡單方法是從命令列使用cURL：

```bash
curl -i -F format='csv' -F programMemberStatus='On List' -F file='@Lead-House-Lannister.csv' -F access_token='<Access Token>' <REST API Endpoint Base URL>/bulk/v1/program/{programId}/members/import.json
```

其中匯入檔案「Lead-House-Lannister.csv」包含下列內容：

```
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

建立匯入工作之後，您必須查詢其狀態。 最佳實務是每5到30秒輪詢匯入工作。 若要這麼做，請將`batchId`路徑引數傳遞至[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)端點。

```
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

此回應會顯示已完成的匯入。 狀態可為下列其中之一：完成、已排入佇列、匯入、失敗。

如果工作已完成，則會列出已處理、失敗或有警告的列數。 如果狀態為「失敗」，則訊息引數也會提供失敗訊息。

## 失敗

[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)回應中的`numOfRowsFailed`屬性指出失敗。 如果numOfRowsFailed大於零，則該值表示發生的失敗次數。

使用Get Import Program Member Failures端點傳遞`batchId`路徑引數，以擷取失敗資料列的記錄和原因。

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

端點會以檔案回應，指出哪些資料列失敗，同時訊息指出記錄失敗的原因。 檔案的格式與工作建立期間在`format`引數中指定的格式相同。 附加欄位會附加至每個記錄並附有失敗說明。

例如，假設您匯入下列潛在客戶分數無效的檔案：

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD
```

當您檢查工作狀態時，您會看到`numOfRowsFailed`為1，表示發生失敗：

```
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

然後擷取失敗檔案以取得失敗的其他詳細資訊：

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

```
firstName,lastName,email,title,company,leadScore,Import Failure Reason
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD,Invalid data type in field Lead Score
```

## 警告

[取得匯入程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)回應中的`numOfRowsWithWarning`屬性表示警告。 如果`numOfRowsWithWarning`大於零，則該值表示發生的警告數。

使用[取得匯入程式成員警告](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberWarningsUsingGET)端點傳遞`batchId`路徑引數，以擷取記錄及警告資料列的原因。

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

端點會以檔案回應，指出哪些列產生警告，同時也會以訊息指出記錄產生警告的原因。 檔案的格式與工作建立期間在`format`引數中指定的格式相同。 附加欄位會附加至每個記錄，並附有警告的說明。

例如，假設您匯入下列電子郵件地址無效的檔案：

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0
```

當您檢查工作狀態時，您會看到`numOfRowsWithWarning`為1，表示發生警告：

```
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

然後，您可以擷取警告檔案，以取得有關警告的其他詳細資訊：

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

```
firstName,lastName,email,title,company,leadScore,Import Warning Reason
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0,Invalid email address
```
