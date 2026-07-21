---
title: 欄位
feature: REST API, Field Management
description: 瞭解REST和SOAP銷售機會欄位命名、透過REST列出欄位說明銷售機會、功能對應、為何sfdcId為Null，以及使用sfdcLeadId或sfdcContactId。
exl-id: 9033f32a-c7cb-4bbf-abcf-38ca4112139f
TQID: https://experienceleague.adobe.com/H2Bvhy-67U8JJ1V3JwYJ0O0vj4i11fwUCyYQtjxm8u0
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 194
ht-degree: 2%

---

# 欄位

REST API和SOAP API對潛在客戶欄位使用不同的命名慣例。 使用每個整合功能所需的欄位名稱慣例。

## 擷取欄位名稱清單

使用REST「描述銷售機會」端點來擷取銷售機會記錄的所有支援欄位名稱。

## 在何處使用哪個欄位名稱型別？

必要的欄位名稱型別取決於整合功能。 下表指出每個功能是使用REST或SOAP欄位名稱。

| 功能 | 要使用的欄位名稱型別 |
| --- | --- |
| 潛在客戶追蹤API (Munchkin) | SOAP |
| Forms 2.0 API | SOAP |
| 清單匯入(UI) | SOAP |
| 清單匯入(REST API) | REST |
| Webhook回應對應 | SOAP |
| 電子郵件指令碼(Velocity) | SOAP |
| SOAP API | SOAP |
| REST API | REST |

### 為何REST API欄位sfdcId一律會傳回null值？

`sfdcId`欄位是公式欄位，包含在REST API的原始欄位對應中。 透過REST API擷取的記錄不會計算公式欄位值，因此`sfdcId`一律會傳回null。

若要擷取SFDC ID，請使用`sfdcLeadId`和`sfdcContactId`欄位。
