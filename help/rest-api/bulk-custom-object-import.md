---
title: 大量自訂物件匯入
feature: Custom Objects
description: 瞭解如何使用CSV、TSV或SSV檔案，透過REST大量匯入Marketo自訂物件。
exl-id: e795476c-14bc-4e8c-b611-1f0941a65825
TQID: https://experienceleague.adobe.com/C1LKLZDEvv95XXH3AEoxIXsLK55tgKTrvyxvs4LnYWw
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 736
ht-degree: 0%

---

# 大量自訂物件匯入

[大量自訂物件匯入端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Custom-Objects)

使用大量API以非同步方式匯入大量自訂物件記錄。 以逗號、定位字元或分號分隔的平面檔案提供小於10 MB的記錄。 如果檔案較大，API會傳回HTTP 413狀態代碼。

檔案內容取決於自訂物件定義。 第一列必須為標題，且每個標題欄位都必須符合API名稱。 其餘的每一列包含一個記錄。

大量自訂物件匯入僅支援「插入或更新」記錄作業。

## 處理限制

每個大量匯入請求都會新增為先進先出(FIFO)佇列的工作。 下列限制適用：

- 最多可同時處理兩個工作。
- 佇列中最多可有10個工作，包括正在處理的兩個工作。

如果您超過10個作業的上限，API會傳回`1016, Too many imports`錯誤。

## 自訂物件範例

在使用大量API之前，請使用Marketo管理UI來[建立您的自訂物件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)。

此範例使用具有`Color`、`Make`、`Model`和`VIN`欄位的`Car`自訂物件。 VIN欄位用於重複資料刪除。 管理員UI畫面會醒目顯示批次API端點所需的API名稱。

![插入自訂物件](assets/bulk-insert-co-car-1.png)

以下是管理UI中顯示的自訂物件欄位。

![插入自訂物件欄位](assets/bulk-insert-co-car-fields.png)

### API名稱

若要以程式設計方式擷取API名稱，請將自訂物件API名稱傳遞至[描述自訂物件](#describe)端點。

```text
/rest/v1/customobjects/{apiName}/describe.json
```

```json
{
    "requestId": "46ff#15a686e66de",
    "result": [
        {
            "name": "car_c",
            "displayName": "Car",
            "description": "It is a car.",
            "createdAt": "2017-02-22T19:55:51Z",
            "updatedAt": "2017-02-22T19:55:51Z",
            "idField": "marketoGUID",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ]
            ],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false
                },
                {
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true
                }
            ]
        }
    ],
    "success": true
}
```

### 匯入檔案

下列CSV檔案包含三個`Car`自訂物件記錄：

```text
color,make,model,vin
red,bmw,2002,WBA4R7C55HK895912
yellow,bmw,320i,WBA4R7C30HK896061
blue,bmw,325i,WBS3U9C52HP970604
```

第一行是標題。 第2-4行包含自訂物件資料記錄。

## 建立工作

若要建立大量匯入工作，請在[匯入自訂物件](https://developer.adobe.com/marketo-apis/api/mapi#tag/Identity/operation/identityUsingPOST)端點的路徑中包含自訂物件API名稱。 包含下列引數：

- `file`：匯入檔案的名稱。
- `format`：檔案分隔符號格式（`csv`、`tsv`或`ssv`）。

```http
POST /bulk/v1/customobjects/{apiName}/import.json?format=csv
```

```text
Transfer-Encoding: chunked
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryXjWP6BP8Ciq6bPeo
Content-Length: 290
Host: <munchkinId>.mktorest.com
```

```text
------WebKitFormBoundaryXjWP6BP8Ciq6bPeo
Content-Disposition: form-data; name="file"; filename="custom_object_import.csv"
Content-Type: text/csv

color,make,model,vin
red,bmw,2002,WBA4R7C55HK895912
yellow,bmw,320i,WBA4R7C30HK896061
blue,bmw,325i,WBS3U9C52HP970604
------WebKitFormBoundaryXjWP6BP8Ciq6bPeo--
```

```json
{
    "requestId": "c015#15a68a23418",
    "result": [
        {
            "batchId": 1013,
            "status": "Queued",
            "objectApiName": "car_c"
        }
    ],
    "success": true
}
```

此範例指定`csv`格式並命名匯入檔案`custom_object_import.csv`。

因為呼叫為非同步，所以回應包含`batchId`，而不是同步自訂物件端點傳回的個別成功和失敗。 `status`可以是`Queued`、`Importing`或`Failed`。

保留`batchId`以檢查匯入狀態，並在完成後擷取失敗或警告。 `batchId`的有效期限為七天。

以下命令列cURL請求會提交範例作業：

```bash
curl -X POST -i -F format='csv' -F file='@custom_object_import.csv' -F access_token='<Access Token>' <REST API Endpoint URL>/bulk/v1/customobjects/car_c/import.json
```

在此範例中，`custom_object_import.csv`檔案包含下列資料：

```text
color,make,model,vin
red,bmw,2002,WBA4R7C55HK895912
yellow,bmw,320i,WBA4R7C30HK896061
blue,bmw,325i,WBS3U9C52HP970604
```

## 輪詢工作狀態

建立匯入工作後，每5到30秒輪詢一次。 傳遞自訂物件API名稱和`batchId`（在路徑中），以至[取得匯入自訂物件狀態](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Custom-Objects/operation/getImportCustomObjectStatusUsingGET)端點。

```http
GET /bulk/v1/customobjects/{apiName}/import/{batchId}/status.json
```

```json
{
    "requestId": "2a5#15a68dd9be1",
    "result": [
        {
            "batchId": 1013,
            "operation": "import",
            "status": "Complete",
            "objectApiName": "car_c",
            "numOfObjectsProcessed": 3,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "importTime": "2 second(s)",
            "message": "Import succeeded, 3 records imported (3 members)"
        }
    ],
    "success": true
}
```

此回應會顯示已完成的匯入。 `status`可以是`Complete`、`Queued`、`Importing`或`Failed`。

當工作完成時，回應會列出已處理、失敗和已處理但出現警告的列數。 `message`屬性可提供額外的作業資訊。

## 失敗

[取得匯入自訂物件狀態](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Custom-Objects/operation/getImportCustomObjectStatusUsingGET)回應中的`numOfRowsFailed`屬性表示失敗的資料列數目。 值大於零表示發生失敗。

將自訂物件API名稱和`batchId`在路徑中傳遞至[取得匯入自訂物件失敗](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Custom-Objects/operation/getImportCustomObjectFailuresUsingGET)端點。 端點會傳回包含失敗詳細資料的檔案。 如果不存在失敗檔案，則會傳回HTTP 404狀態代碼。

若要示範失敗，請將`vin`變更為` vin`，並在逗號和`vin`之間新增空格，以修改標題。

```text
color,make,model, vin
```

重新匯入檔案後，狀態回應會顯示`numRowsFailed`： 3，指出三個失敗。

```http
GET /bulk/v1/customobjects/car_c/import/{batchId}/status.json
```

```json
{
    "requestId": "12260#15a68f491ed",
    "result": [
        {
            "batchId": 1016,
            "operation": "import",
            "status": "Complete",
            "objectApiName": "car_c",
            "numOfObjectsProcessed": 0,
            "numOfRowsFailed": 3,
            "numOfRowsWithWarning": 0,
            "importTime": "1 second(s)",
            "message": "Import completed with errors, 0 records imported (0 members), 3 failed"
        }
    ],
    "success": true
}
```

如需詳細資訊，請呼叫Get Import Custom Object Failures端點：

```http
GET /bulk/v1/customobjects/car_c/import/{batchId}/failures.json
```

```text
color,make,model, vin,Import Failure Reason
red,bmw,2002,WBA4R7C55HK895912,missing.dedupe.fields
yellow,bmw,320i,WBA4R7C30HK896061,missing.dedupe.fields
blue,bmw,325i,WBS3U9C52HP970604,missing.dedupe.fields
```

回應顯示遺漏重複資料刪除欄位`vin`。

## 警告

「取得匯入自訂物件狀態」回應中的`numOfRowsWithWarning`屬性指出含有警告的列數。 大於零的值表示發生警告。

傳遞自訂物件API名稱和`batchId` （在[取得匯入自訂物件警告](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Custom-Objects/operation/getImportCustomObjectWarningsUsingGET)端點的路徑中）。 端點會傳回包含警告詳細資料的檔案。 如果警告檔案不存在，則會傳回HTTP 404狀態代碼。

```http
GET /bulk/v1/customobjects/car_c/import/{batchId}/warnings.json
```
