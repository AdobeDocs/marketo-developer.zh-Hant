---
title: 資料流位置
feature: SOAP
description: 蒸汽位置概觀
exl-id: c3a3fc1e-086b-4822-b2c7-2a7959db557c
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 0%

---

# 資料流位置

串流位置元素包含一或多個時間順序資料邏輯串流的位置參考。 位置參考可能是近似的外部規格，例如時間戳記，或是先前API呼叫傳回的不透明內部位置規格。 串流位置可以定義為複雜的多元素型別，也可以是字串。

資料流位置是用來以批次擷取資料，並允許呼叫者分頁結果。 在API要求中傳遞的資料流位置是先前回應中傳回的資料流位置值。 不建議修改從先前API呼叫傳回的資料流位置，這麼做可能會造成非預期的API行為。

## 支援串流位置的API

- [getcustomobjects](getcustomobjects.md)
- [getleadchanges](getleadchanges.md)
- [getLeadActivity](getleadactivity.md)
- [getMObjects](getmobjects.md)
- [getMultipleLead](getmultipleleads.md)

## 簡單資料流位置

```
<streamPosition>8UJZetaMb1V6uUZl+L7DcPP2jG+PMmtpF</streamPosition>
```

## 複雜資料流位置

```xml
<startPosition>
  <latestCreatedAt  />
  <oldestCreatedAt>2013-08-01T00:13:13+00:00</oldestCreatedAt>
  <activityCreatedAt  />
  <offset>ID:1086173</offset>
</startPosition>
```
