---
title: 「動態內容」
feature: REST API, Dynamic Content
description: 「使用Marketo API設定動態內容。」
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '426'
ht-degree: 1%

---


# 動態內容

Marketo透過對多種資產型別的潛在客戶細分來促進動態內容的使用：

- 電子郵件
- 登陸頁面
- 代碼片段

## 概觀

動態內容是透過根據在所選細分內區段中的資格指定要提供給潛在客戶的區段的特定變數，在區段層級實作。 如果內容片段設定為根據特定細分提供動態內容，則潛在客戶看到內容時，會收到符合其所在區段的內容變數，或者，如果他們不符合區段的資格，會收到預設內容。

## 範例

為了示範，讓我們檢視一個電子郵件範例，其中我們有地區（美國）分段，並且只想為落於西南區段的銷售機會顯示事件促銷活動，該區段包含加利福尼亞、內華達、猶他州、科羅拉多、亞利桑那州和新墨西哥的銷售機會。 為此，我們將ID為「Q1-promotion-banner」的電子郵件中的可編輯區段設為DynamicContent區段。 為此，我們必須使用 [更新電子郵件內容區段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailComponentContentUsingPOST) 我們電子郵件的端點。 此 `value` 引數可用來指定分段的ID。

注意：電子郵件和登入頁面都遵循此模式。 程式碼片段有不同的模式，詳細資訊請參閱程式碼片段API檔案。

以下範例將區段設為動態內容區段，並依區段1001分段。

```
POST /rest/asset/v1/email/{id}/content/Q1-promotion-banner.json
```

```
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

若要為個別區段新增內容，我們必須呼叫 [更新電子郵件動態內容區段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailDynamicContentUsingPOST) 特定區段的端點。

下列範例會設定區段，以針對西南區段中的銷售機會顯示我們的特殊橫幅影像，而非預設值。 如果我們想要為更多區段建立更多變數，我們會為每個區段和區段再次呼叫此端點。

```
POST /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json
```

```
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

## 區段

細分是Marketo動態內容的核心。 區段是使用者定義的個別規則集清單，系統會根據整個潛在客戶資料庫從上到下評估這些規則。 潛在客戶只能是每個細分中一個區段的成員，並且將成為每個細分中它符合資格的第一個區段的成員。 若不符合區段的資格，則會是預設區段的成員，且會收到使用該區段之任何指定動態內容的預設內容。

### 清單

區段有一個清單端點，會傳回包含可用區段清單的回應。

```
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

區段也有一個端點，可傳回包含父區段清單的回應。

```
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
