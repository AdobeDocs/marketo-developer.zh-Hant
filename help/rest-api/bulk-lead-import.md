---
title: 「大量銷售機會匯入」
feature: REST API
description: 「批次匯入潛在客戶資料。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '803'
ht-degree: 0%

---


# 大量潛在客戶匯入

[大量潛在客戶匯入端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads)

對於大量的銷售機會記錄，銷售機會可以透過以下方式非同步匯入： [大量API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST). 這可讓您使用含分隔字元（逗號、定位字元或分號）的平面檔案，將記錄清單匯入Marketo。 檔案可包含任意數量的記錄，只要檔案總計小於10MB即可。 記錄作業僅限「插入或更新」。

## 處理限制

您可以提交一個以上的大量匯入要求，但會有限制。 每個請求都會當作工作新增至FIFO佇列進行處理。 最多可同時處理兩個工作。 在任何指定時間，佇列中最多允許10個工作（包括目前處理的2個）。 如果超過十個作業的上限，則會傳回「1016，匯入次數過多」錯誤。

## 匯入檔案

檔案的第一列必須是標頭，其中列出要將每列的值對應到的對應REST API欄位。 典型的檔案會遵循此基本模式：

```
email,firstName,lastName
test@example.com,John,Doe
```

此 `externalCompanyId` 欄位可用來將潛在客戶記錄連結至公司記錄。 此 `externalSalesPersonId` 欄位可用來將潛在客戶記錄連結至銷售人員記錄。

呼叫本身會使用 `multipart/form-data` content-type。

此請求型別可能很難實作，因此強烈建議您使用現有程式庫實作。

## 建立工作

若要提出大量匯入請求，您必須將內容型別標頭設為「multipart/form-data」，並包含至少一個檔案引數及檔案內容，以及值為「csv」、「tsv」或「ssv」的格式引數，以表示檔案格式。

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

FirstName,LastName,Email,Company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

```json
{
    "requestId": "d01f#15d672f8560",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Queued"
        }
    ],
    "success": true
}
```

此端點使用 [content-type的多重部分/表單資料](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html). 這可能很難對付，因此最佳實務是為您選擇的語言使用HTTP支援程式庫。 從命令列對cURL執行此動作的簡單方法如下所示：

```
curl -i -F format=csv -F file=@lead_data.csv -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/leads.json
```

其中匯入檔案「lead_data.csv」包含以下內容：

```
FirstName,LastName,Email,Company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
```

您也可以選擇包含 `lookupField`， `listId`、和 `partitionName` 請求中的引數。 `lookupField` 可讓您選取要取消重複的特定欄位，例如「同步銷售機會」，並預設為電子郵件。 您可以指定 `id` 作為 `lookupField` 以指示「僅更新」作業。 `listId` 可讓您選取要匯入潛在客戶清單的靜態清單；這將導致清單中的潛在客戶成為此靜態清單的成員，以及匯入導致的任何建立或更新。 `partitionName` 選取要匯入的特定分割區。 如需詳細資訊，請參閱工作區和分割區區段。

請注意，在回應我們的呼叫時，沒有成功或失敗清單（例如同步銷售機會），而是結果陣列中記錄的batchId和狀態列位。 這是因為此API為非同步，且可能傳回「已排入佇列」、「匯入」或「失敗」狀態。 您必須保留batchId才能取得匯入工作的狀態，以及在完成時擷取失敗和/或警告。 batchId的有效期為七天。

## 輪詢工作狀態

最佳實務是每5到30秒輪詢一次工作，視所需的延遲和API呼叫限制而定，以檢視匯入工作的狀態。 您可以使用Get Import Lead Status API來執行此操作。

```
GET /bulk/v1/leads/batch/{id}.json
```

```json
{
   "requestId":"8136#146daebc2ed",
   "success":true,
   "result":[
      {
         "batchId":1022,
         "status":"Complete",
         "numOfLeadsProcessed":2,
         "numOfRowsFailed":1,
         "numOfRowsWithWarning":0,
         "message":"Import completed with errors, 2 records imported (2 members), 1 failed"
      }
   ]
}
```

此回應會顯示已完成的匯入，但狀態可為其中一項：

- 完成
- 已排入佇列
- 正在匯入
- 失敗

如果工作已完成，則會列出已處理、失敗、有警告的資料列數目。 如果狀態為「失敗」，則訊息引數也會提供失敗訊息。

## 失敗

失敗由Get Import Lead Status回應中的「numOfRowsFailed」屬性表示。 如果「numOfRowsFailed」大於零，則該值表示發生的失敗次數。

若要擷取失敗列的記錄和原因，您必須擷取失敗檔案：

```
GET /bulk/v1/leads/batch/{id}/failures.json
```

API會以指出哪些列失敗的檔案回應，並顯示指出記錄失敗原因的訊息。 檔案的格式與工作建立期間在「format」引數中指定的格式相同。 附加欄位會附加至每個記錄並附有失敗說明。

## 警告

在Get Import Lead Status回應中，警告由「numOfRowsWithWarning」屬性表示。 如果「numOfRowsWithWarning」大於零，則該值表示警告發生的次數。

若要擷取記錄以及警告列的原因，請擷取警告檔案：

```
GET /bulk/v1/leads/batch/{id}/warnings.json
```

API會以檔案回應，指出哪些列產生警告，以及指出記錄失敗原因的訊息。 檔案的格式與工作建立期間在「format」引數中指定的格式相同。 附加欄位會附加至每個記錄，並附有警告的說明。
