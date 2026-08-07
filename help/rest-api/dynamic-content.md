---
title: 動態內容
feature: REST API, Dynamic Content
description: 透過REST API使用區段來設定區段層級Marketo動態內容，以個人化電子郵件、登入頁面以及包含端點和範例的片段
exl-id: 8ab97624-5fb5-4a41-911f-ec8616dd43c9
TQID: https://experienceleague.adobe.com/MwfPxu74qk0bPZMr6yuxQi--e3gMvP1tXQZ5iMil02o
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 325
ht-degree: 3%

---

# 動態內容

使用銷售機會區段，以這些資產型別提供動態內容：

- 電子郵件
- 登陸頁面
- 程式碼片段

## 概觀

動態內容在區段層級運作。 每個區段都可以提供選定區段中區段的變數。

當潛在客戶檢視資產時，Marketo會顯示潛在客戶區段的變數。 如果潛在客戶不符合區段的資格，Marketo會顯示預設內容。

## 範例

此範例使用地區（美國）細分來顯示對西南區段中的潛在客戶的事件促銷。 此區段包含來自加州、內華達、猶他州、科羅拉多、亞利桑那州和新墨西哥州的銷售機會。

使用[更新電子郵件內容區段](https://developer.adobe.com/marketo-apis/api/asset#operation/updateEmailComponentContentUsingPOST)端點將識別碼為`Q1-promotion-banner`的可編輯區段變更為`DynamicContent`區段。 `value`引數指定分段ID。

電子郵件和登入頁面會遵循此模式。 程式碼片段使用程式碼片段API檔案中所述的不同模式。

以下範例將區段設為動態內容區段，並依區段1001分段。

```http
POST /rest/asset/v1/email/{id}/content/Q1-promotion-banner.json
```

```text
type=DynamicContent&value=1001
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1909
    }
  ]
}
```

呼叫[更新電子郵件動態內容區段](https://developer.adobe.com/marketo-apis/api/asset#operation/updateEmailDynamicContentUsingPOST)端點以新增特定區段中的區段內容。

以下請求會顯示特殊橫幅，而非西南區段中銷售機會的預設內容。 若要建立更多變數，請呼叫每個區段和區段的端點。

```http
POST /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json
```

```text
segment=Southwest&type=HTML&value=<img src='//www.example.com/SuperSpecialBannerForAmericanSouthwestLeads.jpg'/>
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1637
    }
  ]
}
```

## 細分

區段是使用者定義的規則集清單，Marketo會根據潛在客戶資料庫從上到下評估。 每個細分中，一個潛在客戶只能屬於一個區段。 潛在客戶會加入其符合資格的第一個區段。

如果潛在客戶不符合另一個區段的資格，則會加入預設區段並接收區段的預設內容。

### 清單

使用清單端點來擷取可用的區段。

```http
GET /rest/asset/v1/segmentation.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "78eb#14e9de95868",
  "result": [
    {
      "id": 1001,
      "name": "My Industry Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:10Z+0000",
      "url": "https://app-abm.marketo.com/#SG1001A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    },
    {
      "id": 1002,
      "name": "My Country Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:28:23Z+0000",
      "updatedAt": "2015-04-06T18:37:18Z+0000",
      "url": "https://app-abm.marketo.com/#SG1002A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    }
  ]
}
```

使用區段端點可擷取父分段中的區段。

```http
GET /rest/asset/v1/segmentation/1001/segments.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "2031#14e9df08796",
  "result": [
    {
      "id": 1001,
      "name": "Manufacturing",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1002,
      "name": "Healthcare",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769688A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1003,
      "name": "Financial",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769690A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1004,
      "name": "Technology",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769692A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1005,
      "name": "Default",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769694A1",
      "status": "approved",
      "segmentationId": 1001
    }
  ]
}
```
