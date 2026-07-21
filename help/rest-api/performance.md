---
title: 績效
feature: REST API
description: 透過HTTP壓縮提升Marketo REST API效能。 啟用gzip以削減頻寬；大量不支援的API以及低於1024位元組的未壓縮。
exl-id: 173a398a-9d36-4e8d-9dd3-7d0d375b085a
TQID: https://experienceleague.adobe.com/foJCTd890HZtL-UzWx2cjRXwTxqgW56A79sB7FPEWis
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 131
ht-degree: 1%

---

# 績效

使用此頁面上的效能選項來提高整合的效率。

## HTTP壓縮

Marketo REST API支援HTTP 1.1規格所定義的HTTP回應本文壓縮。 啟用壓縮功能以減少頻寬使用量和資料擷取時間。

>[!NOTE]
>
>少於1024個位元組的裝載不會壓縮，且大量API不支援壓縮。

若要啟用壓縮，請在請求中包含以下HTTP標頭：

```html
Accept-Encoding: gzip
```

Marketo REST API會壓縮回應內文，並包含下列標頭：

```html
Content-Encoding: gzip
```

下列cURL範例會呼叫[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadsByFilterUsingGET)的取得銷售機會，以擷取五個銷售機會：

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
