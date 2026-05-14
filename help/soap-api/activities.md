---
title: 活動
feature: SOAP
description: 瞭解如何使用SOAP與活動互動、擷取潛在客戶活動，以及使用getLeadActivities和getLeadChanges追蹤潛在客戶變更
exl-id: fd695ab6-e7be-4ced-89c9-c4cd2d4c2ab0
TQID: https://experienceleague.adobe.com/6zUkvoDCqlRmblFDPWzLjdwITsyWxcXrJBKbLux76WI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: e71bcf289229867bc969345d79c8f014761aaaf9
workflow-type: tm+mt
source-wordcount: 79
ht-degree: 2%

---

# 活動

下列SOAP呼叫可用來與活動互動。

- [getLeadActivities](getleadactivity.md)
- [getleadchanges](getleadchanges.md)

>[!CAUTION]
>
>從2026-12-30開始，如果目標清單包含10,000個或更多銷售機會，對`Get Lead Activities`和包含`listId`引數的`Get Lead Changes`端點的呼叫將失敗（錯誤碼1003）。 為避免服務中斷，請確保呼叫的範圍已設定妥當，以避免此限制。 請參閱[移轉指南](migration.md)。
