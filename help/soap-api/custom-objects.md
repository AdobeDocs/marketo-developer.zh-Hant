---
title: 「自訂物件」
feature: SOAP
description: 「建立自訂物件。」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '231'
ht-degree: 0%

---


# 自訂物件

Marketo自訂物件可讓您在Marketo銷售機會與自訂物件記錄之間建立一對多的關係。 例如，您可能會想要追蹤潛在客戶參加的所有路演。 由於潛在客戶可能會參加許多路演（多年），因此自訂物件更適合儲存這些資訊。

除Spark以外的所有Marketo版本都支援自訂物件。 當您帳戶中成功布建Marketo自訂物件時，您可以

- 透過Marketo SOAP API建立/讀取/更新/刪除自訂物件中的記錄
- 將新記錄新增至自訂物件時，使用智慧清單觸發器
- 使用自訂物件資料作為智慧清單中的濾鏡
- 使用Marketo電子郵件指令碼在電子郵件中使用自訂物件資料

## 自訂物件的結構

自訂物件包含：

- 所有自訂物件共有的一小組固定屬性：
   - 物件名稱（亦稱為物件型別名稱）
   - 物件說明
   - 自訂物件至Marketo銷售機會連結欄位名稱 — 這是自訂物件參考的銷售機會（人員）物件上的欄位
   - 物件金鑰欄位名稱 — 物件使用的主要金鑰
- 一或多個物件特定欄位（我們最多支援50個這類欄位）

## 自訂物件作業

下列呼叫可用來與CO互動。

- [getcustomobjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)
- [syncCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)
- [deleteCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)
