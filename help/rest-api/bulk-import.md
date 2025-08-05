---
title: 大量匯入
feature: REST API
description: 批次匯入人員資料。
exl-id: f7922fd2-8408-4d04-8955-0f8f58914d24
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '592'
ht-degree: 1%

---

# 大量匯入

Marketo提供介面，用於插入大型人員及人員相關資料集，稱為大量匯入。 目前提供三種物件型別的介面：

- 銷售機會（人員）
- 自訂物件
- 計畫成員

大量匯入是透過建立工作，然後等待工作完成讀取檔案來執行。 這些工作會以非同步方式執行，且可以輪詢以擷取匯入的狀態。 根據RFC 2399，檔案會使用HTTP多部分/表單資料上傳。

大量API端點沒有像其他端點一樣以「/rest」為前置詞。

## Authentication

大量匯入API使用與其他Marketo REST API相同的OAuth 2.0驗證方法。  這需要有效的存取權杖作為HTTP標頭`Authorization: Bearer {_AccessToken_}`傳送。

>[!IMPORTANT]
>
>自2025年6月30日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

## 限制

- 最大並行匯入工單：2
- 已排入佇列的匯入作業上限（包含目前匯入的作業）：10
- 匯入檔案大小上限： 10 MB

## 權限

大量匯入使用與Marketo REST API相同的許可權模型，而且不需要額外的特殊許可權即可使用，不過每個端點集都需要特定許可權。

## 記錄作業

大量匯入是「插入或更新」記錄作業。 如果在資料庫中找到相符的記錄，則會更新記錄。 否則，將建立新記錄。 大量匯入回應不會指出指定的記錄是否已更新或插入。

## 建立工作

Marketo的大量匯入API使用工作的概念來執行資料匯入。 讓我們來看看如何使用[匯入銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)端點來建立簡單的銷售機會匯入工作。  請注意，此端點使用[multipart/form-data做為content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。 這可能很難對付，因此最佳實務是為您選擇的語言使用HTTP支援程式庫。  如果您剛開始接觸，我們建議您使用[curl](https://curl.se/)。

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=--------------------------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email
Able,Baker,ablebaker@marketo.com
Charlie,Dog,charliedog@marketo.com
Easy,Fox,easyfox@marketo.com
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

此請求將建構一個工作，匯入名為「leads.csv」的CSV檔案中包含的值，其欄標題為「FirstName」、「LastName」、「Email」、「Company」。

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

當我們提交工作時，它會傳回batchId，然後我們可以使用它來檢查其狀態。

### 通用引數

每個工作建立端點會共用一些通用引數，用於設定檔案格式、欄位名稱和大量擷取工作的篩選器。  每個擷取作業的子型別都可能有其他引數：

| 參數 | 資料類型 | 附註 |
|---|---|---|
| 格式 | 字串 | 使用逗號分隔值、定位字元分隔值和分號分隔值的選項，決定匯入資料的檔案格式。 接受以下其中之一：CSV、SSV、TSV。 格式預設為CSV。 |
| 檔案 | 字串 | 透過檔案中的多部分表單資料指定資料。 |

## 輪詢工作狀態

使用[Get Import Lead Status](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadStatusUsingGET)端點可輕鬆判斷工作的狀態。

```
GET /bulk/v1/leads/batch/{batchId}.json
```

```json
{
    "requestId": "1f63#15d6738fd15",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Complete",
            "numOfLeadsProcessed": 3,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 3 records imported (3 members)"
        }
    ],
    "success": true
}
```

內部`status`成員會指出工作進度，可能是下列其中一個值：「已排入佇列」、「匯入」、「完成」、「失敗」。 在此情況下，我們的工作已完成，因此我們可以停止輪詢。

## 失敗

失敗由Get Import Lead Status回應中的`numOfRowsFailed`屬性指示。 如果`numOfRowsFailed`大於零，則該值表示發生的失敗次數。

若要擷取失敗資料列的記錄與原因，您必須使用[取得匯入潛在客戶失敗](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadFailuresUsingGET)端點來擷取失敗檔案。

```
GET /bulk/v1/leads/batch/{batchId}/failures.json
```

檔案會指出哪些列失敗，並顯示訊息指出記錄失敗的原因。
