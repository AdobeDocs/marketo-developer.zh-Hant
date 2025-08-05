---
title: 效能
feature: REST API
description: 使用Marketo API的效能秘訣。
exl-id: 173a398a-9d36-4e8d-9dd3-7d0d375b085a
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '122'
ht-degree: 0%

---

# 效能

此頁面包含效能相關主題的清單，可用來提高整合的效能。

## HTTP壓縮

Marketo REST API使用HTTP 1.1規格定義的標準，支援回應主體的HTTP壓縮。 建議啟用壓縮，因為這會減少頻寬使用量，以及擷取資料所花費的時間。

>[!NOTE]
>
>少於1024個位元組的裝載不會壓縮，且大量API不支援壓縮。

若要啟用壓縮，請在請求中包含以下HTTP標頭：

```html
Accept-Encoding: gzip
```

Marketo REST API會壓縮回應內文，並包含此標題：

```html
Content-Encoding: gzip
```

以下是使用Curl呼叫[Get Leads by Filter Type](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)端點以擷取5個銷售機會的範例：

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
