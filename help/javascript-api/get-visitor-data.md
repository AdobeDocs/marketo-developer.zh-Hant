---
title: 取得訪客資料
description: 使用RTP User Context API搭配引數、回呼範例及區段、ABM和位置的範例回應，取得即時訪客身分識別。
feature: Javascript
exl-id: 39a2446d-8a31-461e-bbe6-a7edf24b4d52
TQID: https://experienceleague.adobe.com/B-JMACtMs3aRVsb1eJKaRoQGgVKB6MTbd0KBoZ7Ay6k
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 223
ht-degree: 4%

---

# 取得訪客資料

此方法用於取得即時訪客身分識別資料。

- 您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。
- RTP不支援以帳戶為根據的行銷指定帳戶清單。 ABM清單和程式碼只與在RTP內管理的已上傳帳戶清單（CSV檔案）有關。

如果發生錯誤，回應JSON中將會出現錯誤訊息。 如果傳回500程式碼，請聯絡支援人員並提出請求。

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| `get` | 必要 | 字串 | 方法動作。 |
| `visitor` | 必要 | 字串 | 方法名稱。 |
| `callback` | 必要 | 功能 | 針對每個傳回的行銷活動觸發的回呼函式。 |

## 範例

取得訪客身分識別資料：

```javascript
function callbackFunction() {
    console.log('RTP is awesome!');
}
rtp('get', 'visitor', callbackFunction);
```

具有區段比對的回應：

以下是訪客在取得訪客資料API呼叫前符合即時區段時傳回的回應範例。

```json
{
    "status": 200,
    "results": {
        "matchedSegments": [
            {
                "name": "first click",
                "id": 177
            }
        ],
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```

沒有區段比對的回應：

以下是訪客在取得訪客資料API呼叫前不符合任何即時區段時傳回的回應範例。

```json
{
    "status": 200,
    "results": {
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```
