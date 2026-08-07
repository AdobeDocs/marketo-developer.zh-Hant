---
title: 標記
feature: REST API, Tags
description: 查詢標籤型別、依名稱取得允許的值、透過REST Asset API更新或刪除Marketo中的程式標籤，並附上請求範例。
exl-id: 64731d1a-a749-4d6f-b336-16c733d002f0
TQID: https://experienceleague.adobe.com/zjdyfoofVWytE0Q-K4lk598jmleTSFOD7tSRqeAHsjk
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 221
ht-degree: 2%

---

# 標記

[標籤端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Tags)

標籤是使用者為方案定義的欄位。 標籤可套用至一或多個程式型別，且可為必要或選用。 標籤也可以定義使用者必須從中選取的允許值清單。

## 查詢

使用標準資產模式查詢標籤。 標籤沒有「依據ID」端點。 若要擷取標籤的允許值，請依名稱查詢標籤。

### 取得標籤

```http
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

```http
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

使用[更新程式標籤](https://developer.adobe.com/marketo-apis/api/asset#operation/updateProgramUsingPOST)端點來更新標籤型別的值。 所有引數皆為必要：

- `id`路徑引數指定程式ID。
- `tagType`路徑引數指定要更新的標籤型別。
- `tagValue`查詢引數指定了新值。

```http
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

若要更新多個標籤，請使用[更新程式中繼資料](https://developer.adobe.com/marketo-apis/api/asset#operation/updateProgramUsingPOST)端點。 請參閱[程式更新區段](programs.md#update)中的範例。

## 刪除

使用[刪除程式標籤](https://developer.adobe.com/marketo-apis/api/asset#operation/deleteProgramUsingPOST)端點來刪除非必要的標籤型別。 `id`路徑引數指定程式ID，而`tagType`路徑引數指定要刪除的標籤型別。

```http
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
