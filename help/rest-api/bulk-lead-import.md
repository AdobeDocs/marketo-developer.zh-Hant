---
title: 大量潛在客戶匯入
feature: REST API
description: 使用CSV TSV或SSV在Marketo中建立並監視非同步的大量潛在客戶匯入。
exl-id: 615f158b-35f9-425a-b568-0a7041262504
TQID: https://experienceleague.adobe.com/UamXYWis5J1ERqnp5lAnfUf3pFcgfSOLfKRXRB-Yg4I
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 623
ht-degree: 0%

---

# 大量潛在客戶匯入

[大量潛在客戶匯入端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads)

使用[大量API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)以非同步方式匯入大量潛在客戶記錄。 以逗號、定位字元或分號分隔的平面檔案提供小於10 MB的記錄。

大量潛在客戶匯入僅支援「插入或更新」記錄作業。

## 處理限制

每個大量匯入請求都會新增為先進先出(FIFO)佇列的工作。 下列限制適用：

- 最多可同時處理兩個工作。
- 佇列中最多可有10個工作，包括正在處理的兩個工作。

如果您超過10個作業的上限，API會傳回`1016, Too many imports`錯誤。

## 匯入檔案

檔案的第一列必須是標頭，該標頭會列出每列對應值的REST API欄位。 典型的檔案會遵循以下模式：

```csv
email,firstName,lastName
test@example.com,John,Doe
```

使用`externalCompanyId`將潛在客戶記錄連結至公司記錄。 使用`externalSalesPersonId`將潛在客戶記錄連結至銷售人員記錄。

使用`multipart/form-data`內容型別傳送要求。 使用現有的程式庫實作來建構多部分要求。

## 建立工作

若要建立大量匯入工作，請將內容型別設定為`multipart/form-data`並包含這些引數：

- `file`：匯入檔案內容。
- `format`：檔案格式。 有效值為`csv`、`tsv`和`ssv`。

```http
POST /bulk/v1/leads.json?format=csv
```

```text
Content-Type: multipart/form-data; boundary=------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```text
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email,company
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

此端點使用[multipart/form-data做為content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。 使用您偏好語言的HTTP支援程式庫來正確建構要求。 以下範例使用命令列中的cURL：

```bash
curl -i -F format=csv -F file=@lead_data.csv -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/leads.json
```

在此範例中，`lead_data.csv`匯入檔案包含下列資料：

```text
firstName,lastName,email,company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
```

您也可以包含下列選用引數：

- `lookupField`：選取用於重複資料刪除的欄位，並預設為`email`。 指定`id`以執行「僅更新」作業。
- `listId`：選取靜態清單。 除了匯入所建立或更新的任何記錄外，匯入的潛在客戶也會成為此清單的成員。
- `partitionName`：選取要匯入的分割區。 如需詳細資訊，請參閱工作區和分割區區段。

由於API為非同步，因此回應包含`batchId`和`status`欄位，而非個別的成功和失敗。 狀態可以是`Queued`、`Importing`或`Failed`。

保留`batchId`以檢查工作狀態，並在完成後擷取失敗或警告。 `batchId`的有效期限為七天。

## 輪詢工作狀態

根據延遲需求和API呼叫限制，使用「取得匯入銷售機會狀態API」每5-30秒輪詢一次作業。

```http
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

此回應會顯示已完成的匯入。 狀態可以是下列其中一個值：

- 完成
- 已排入佇列
- 正在匯入
- 失敗

當工作完成時，回應會列出已處理、失敗和已處理但出現警告的列數。 當狀態為`Failed`時，`message`引數也可以提供失敗訊息。

## 失敗

Get Import Lead Status回應中的`numOfRowsFailed`屬性表示失敗的資料列數目。 值大於零表示發生失敗。

若要擷取失敗的記錄及其原因，請要求失敗檔案：

```http
GET /bulk/v1/leads/batch/{id}/failures.json
```

此API會傳回一個檔案，用於識別每個失敗的列，並說明記錄失敗的原因。 檔案在建立工作期間使用`format`引數指定的格式。 每個記錄上的額外欄位說明了失敗。

## 警告

Get Import Lead Status回應中的`numOfRowsWithWarning`屬性指出有警告的列數。 大於零的值表示發生警告。

若要擷取受影響的記錄及其原因，請要求警告檔案：

```http
GET /bulk/v1/leads/batch/{id}/warnings.json
```

此API會傳回一個檔案，用於識別含有警告的每一列，並解釋警告發生的原因。 檔案在建立工作期間使用`format`引數指定的格式。 每個記錄上的額外欄位會說明警告。
