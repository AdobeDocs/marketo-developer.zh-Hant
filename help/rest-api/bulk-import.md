---
title: 大量匯入
feature: REST API
description: Marketo大量匯入，用於透過多部分上傳載入銷售機會、自訂物件和程式成員，建立非同步工作、輪詢狀態和處理失敗。
exl-id: f7922fd2-8408-4d04-8955-0f8f58914d24
TQID: https://experienceleague.adobe.com/lr9dyX-fY-oJ2LM5P0zE1m24HtFYKQYYbxMkVe--PkE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 526
ht-degree: 2%

---

# 大量匯入

大量匯入提供介面，用於插入大量人員及人員相關資料。 您可以匯入三種物件型別：

- 銷售機會（人員）
- 自訂物件
- 計畫成員

若要執行大量匯入，請建立讀取已上傳檔案的工作。 工作以非同步方式執行，因此輪詢它以擷取匯入狀態。

每個RFC 2399使用HTTP `multipart/form-data`上傳檔案。

與其他端點不同，大量API端點沒有前置詞`/rest`。

## Authentication

大量匯入API使用與其他Marketo REST API相同的OAuth 2.0驗證方法。 在`Authorization: Bearer {_AccessToken_}` HTTP標頭中傳送有效的存取權杖。

>[!IMPORTANT]
>
>自2025年6月30日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

## 限制

- 最大並行匯入工單：2
- 已排入佇列的匯入作業上限（包括目前匯入的作業）： 10
- 最大匯入檔案大小： 10 MB

## 權限

大量匯入使用與Marketo REST API相同的許可權模型。 它不需要額外的許可權，但每組端點需要特定的許可權。

## 記錄作業

大量匯入是「插入或更新」記錄作業。 如果資料庫包含相符的記錄，作業會更新該記錄。 否則，作業會建立記錄。

大量匯入回應不會指出個別記錄是否已更新或插入。

## 建立工作

呼叫[匯入銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/importLeadUsingPOST)端點以建立銷售機會匯入工作。 此端點使用[multipart/form-data做為content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。

使用您慣用語言的HTTP支援程式庫來建構多部分要求。 您也可以使用[curl](https://curl.se/)開始使用。

```http
POST /bulk/v1/leads.json?format=csv
```

```text
Content-Type: multipart/form-data; boundary=--------------------------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```text
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email
Able,Baker,ablebaker@marketo.com
Charlie,Dog,charliedog@marketo.com
Easy,Fox,easyfox@marketo.com
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

此請求會建立從名為`leads.csv`的CSV檔案匯入值的工作。

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

回應傳回`batchId`。 使用此值來檢查工作狀態。

### 通用引數

每個工作建立端點會共用引數以設定匯入檔案。 匯入子型別也可支援其他引數。

| 參數 | 資料類型 | 附註 |
| --- | --- | --- |
| 格式 | 字串 | 使用逗號分隔值、定位字元分隔值和分號分隔值的選項，決定匯入資料的檔案格式。 接受以下其中之一：CSV、SSV、TSV。 格式預設為CSV。 |
| 檔案 | 字串 | 透過檔案中的多部分表單資料指定資料。 |

## 輪詢工作狀態

將`batchId`傳遞至[取得匯入銷售機會狀態](https://developer.adobe.com/marketo-apis/api/mapi#operation/getImportLeadStatusUsingGET)端點以擷取工作狀態。

```http
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

`status`成員表示工作進度。 它的值可以是`Queued`、`Importing`、`Complete`或`Failed`。

在此範例中，工作已完成，因此輪詢可以停止。

## 失敗

Get Import Lead Status回應中的`numOfRowsFailed`屬性表示失敗的資料列數目。 值大於零表示發生失敗。

若要擷取失敗的記錄及其原因，請使用[取得匯入潛在客戶失敗](https://developer.adobe.com/marketo-apis/api/mapi#operation/getImportLeadFailuresUsingGET)端點。

```http
GET /bulk/v1/leads/batch/{batchId}/failures.json
```

失敗檔案會識別每個失敗的資料列，並解釋記錄失敗的原因。
