---
title: "標籤"
feature: REST API, Tags
description: 「在Marketo中管理方案的標籤。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '205'
ht-degree: 1%

---


# 標記

[標籤端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tags)

標籤是使用者為方案定義的欄位。 每個標籤可套用至一或多個程式型別，且可以是必要或選用的，視標籤的定義方式而定。 標籤也可以提供允許值的清單，必須從中選擇這些值才能使用。

## 查詢

使用標準資產模式查詢標籤，但沒有「依ID」的端點。 只有在依名稱查詢標籤時，才會傳回標籤的可允許值清單。

### 取得標籤

```
GET /rest/asset/v1/tagTypes.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1488a#1504ecfccf8",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true
        },
        {
            "tagType": "AAA2 Required Event Tag Type",
            "applicableProgramTypes": "[event]",
            "required": true
        },
        {
            "tagType": "AAA3 Not Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": false
        }
    ]
}
```

### 依名稱

```
GET /rest/asset/v1/tagType/byName.json?name=AAA1 Required Tag Type
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "8a44#1504ed0da2f",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true,
            "allowableValues": "[AAA1 RT1, AAA1 RT2, AAA1 RT3, AAA1 RT4]"
        }
    ]
}
```

## 更新

此 [更新計畫標籤](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) 端點可讓您更新指定標籤型別的值。 端點會採用 `id` 和 `tagType` 指定程式id的路徑引數，以及要更新的標籤型別。 A `tagValue` 查詢引數可用來指定標籤型別的新值。 所有引數皆為必要項。

```
POST /rest/asset/v1/program/{id}/tag/{tagType}.json?tagValue=David
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "fd84#17f84a885a6",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```

您可以使用大量更新標籤 [更新程式中繼資料](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) 端點。 這方面的範例可以找到 [此處](programs.md#update).

## 刪除

此 [刪除計畫標籤](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/deleteProgramUsingPOST) 端點可讓您刪除不需要的標籤型別。 端點取用 `id` 和 `tagType` 指定程式id的路徑引數，以及要刪除的標籤型別。

```
POST /rest/asset/v1/program/{id}/tag/{tagType}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d998#17f84ad36a7",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```
