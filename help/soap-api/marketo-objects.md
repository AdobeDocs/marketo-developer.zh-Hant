---
title: Marketo物件
feature: SOAP
description: Marketo物件的總覽，包括型別、屬性、外部金鑰行為，以及支援的SOAP API （適用於機會、計畫和相關記錄）。
exl-id: 99b9aed4-94e8-46e8-84d9-2cc5215b0c13
TQID: https://experienceleague.adobe.com/-G3BW9oFhPZL68yBUy-aNYycmgADhQCo8OclQAierrU
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 274
ht-degree: 0%

---

# Marketo物件

Marketo使用Marketo物件(MObjects)來表示各種類別，例如Program、Opportunity和OpportunityPersonRole。

## MObject的結構

MObject可以是三種型別之一：標準、自訂或虛擬。 標準和自訂物件代表不同的實體，例如Lead或Company，而虛擬物件（例如LeadRecord）則由一或多個物件的欄位組成。 虛擬物件是API內使用的方便物件，但不存在於Marketo應用程式中。

MObject包含：

- 所有MObject共有的一小組固定屬性
   - 必要型別
   - 選用的外部金鑰
   - 唯讀id、createdAt、updatedAt
- 一或多個物件特定屬性的清單，以名稱/值配對的形式呈現，其中一些可能是必要專案。 例如，機會上的名稱。
- 關聯物件參考的清單，以物件名稱加號表示
   - Marketo ID或
   - 外部索引鍵做為屬性名稱/屬性值組。

### 外部索引鍵

外部索引鍵是在Marketo物件（例如Lead或Opportunity）上定義的自訂欄位。 名稱是欄位名稱，值是在外部系統中產生的欄位值。 **Marketo未對這些值強制唯一條件約束。** API使用者應負責確保值是唯一的。 如果發生重複，Marketo會使用最近新增的物件。 這類似於「電子郵件地址」標準欄位的行為。

### 可用的API

| API | 可以操作 |
| --- | --- |
| describeMObject | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
| getMObjects | 商機、OpportunityPersonRole、方案 |
| syncMObjects | 商機、OpportunityPersonRole、方案 |
| deleteMObjects | 商機，OpportunityPersonRole |
| listMObjects | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
