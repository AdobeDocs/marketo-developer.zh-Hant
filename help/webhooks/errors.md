---
title: 錯誤次數
feature: Webhooks
description: 瞭解Marketo Webhook錯誤代碼、為何需要2xx回應來更新潛在客戶欄位，以及如何使用Webhook擷取和處理呼叫的錯誤。
exl-id: adce40c3-87b1-4f31-8995-eb64e8a72b55
TQID: https://experienceleague.adobe.com/N2jNA4EUMMTUFL9uJHZhOor6Tlz4-EXWciwoXrPml48
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 210
ht-degree: 2%

---

# 錯誤次數

本頁說明Marketo Webhook的錯誤回應程式碼，並說明如何處理Webhook錯誤。

Marketo產生錯誤代碼1000和1001。 Marketo webhook呼叫的系統會傳回2xx到5xx回應代碼。

只有在Web服務傳回2xx回應代碼時，Marketo才會將回應值對應至欄位。 如果webhook回應的目的是要變更Marketo潛在客戶記錄中的值，則所有其他回應代碼都會導致Marketo忽略欄位更新的回應。

| 回應代碼 | 說明 |
| --- | --- |
| 1000 | 這表示「呼叫Webhook」流程動作正存放於批次行銷活動中。 Webhook只能從觸發行銷活動觸發。 |
| 1001 | 這表示Web服務發出空的回應主體。 |

## 擷取Webhook錯誤

使用&#x200B;**[!UICONTROL Webhook is Called]**&#x200B;觸發器來擷取及處理webhook錯誤：

已呼叫![Webhook](assets/webhook-called.png)

* **回應** — 要求所接收的常值回應裝載。
* **錯誤型別** - HTTP狀態訊息的原因短語。

使用這些值來回應可預測的錯誤和例外。 視整合服務而定，您可以自動從某些錯誤類別復原，並針對未預期的錯誤建立警示。
