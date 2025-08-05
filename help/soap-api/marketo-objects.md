---
title: Marketo物件
feature: SOAP
description: Marketo物件總覽
exl-id: 99b9aed4-94e8-46e8-84d9-2cc5215b0c13
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '249'
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

外部索引鍵是在Marketo物件（例如Lead或Opportunity）上定義的自訂欄位。 名稱是欄位名稱，值是在外部系統中產生的欄位值。 **Marketo不會對這些值強制執行唯一限制。** API使用者應負責確保值是唯一的。 如果發生重複，Marketo會使用最近新增的物件。 這類似於「電子郵件地址」標準欄位的行為。

### 可用的API

| API | 可以操作 |
|---|---|
| describeMObject | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
| getMObjects | 商機、OpportunityPersonRole、方案 |
| syncMObjects | 商機、OpportunityPersonRole、方案 |
| deleteMObjects | 商機，OpportunityPersonRole |
| listMObjects | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
