---
title: 欄位
feature: REST API, Field Management
description: 支援的欄位名稱清單。
exl-id: 9033f32a-c7cb-4bbf-abcf-38ca4112139f
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '194'
ht-degree: 2%

---

# 欄位

REST API和SOAP API對潛在客戶欄位使用不同的命名慣例。

## 擷取欄位名稱清單

使用REST「描述銷售機會」端點擷取銷售機會記錄上所有支援的欄位名稱清單。

## 在何處使用哪個欄位名稱型別？

有時很難知道您運用特定整合相關功能時必須使用的欄位名稱型別。 以下為快速參照，其功能使用REST或SOAP欄位名稱型別。

| 功能 | 要使用的欄位名稱型別 |
|--- |--- |
| 潛在客戶追蹤API (Munchkin) | SOAP |
| Forms 2.0 API | SOAP |
| 清單匯入(UI) | SOAP |
| 清單匯入(REST API) | REST |
| Webhook回應對應 | SOAP |
| 電子郵件指令碼(Velocity) | SOAP |
| SOAP API | SOAP |
| REST API | REST |

### 為何REST API欄位sfdcId一律會傳回null值？

欄位`sfdcId`是公式欄位，錯誤包含在REST API的原始欄位對應中。 透過REST API擷取的記錄不會計算公式欄位的值，因此值將一律為空。 若要擷取真實的SFDC ID，您應該使用名為`sfdcLeadId`和`sfdcContactId`的欄位。
