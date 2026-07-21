---
title: Marketo物件
feature: Email Programs
description: 使用Marketo Velocity搭配銷售機會、商機和自訂物件、載入欄位、前10名清單存取權、SFDC關係和$TriggerObject的指南。
exl-id: 88c63d72-7aa5-4550-9e1a-887a479872e1
TQID: https://experienceleague.adobe.com/PvLJb-AOk6DKaNINycpzk5ojZiL8UNcanRg3vXmsGCI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 452
ht-degree: 0%

---

# Marketo物件

Marketo的Velocity實作可以使用來自以下Marketo來源的資料：

- 銷售機會
- 機會
- 自訂物件
- 行動應用程式
- 行動應用程式安裝

## 正在載入欄位

若要在指令碼中使用欄位，請選取指令碼權杖編輯器中對應清單下的欄位。

如果指令碼參考未載入的欄位，指令碼會在執行階段失敗。 將欄位從欄位選單拖曳到指令碼中以進行載入，並在游標處新增參照。

## 機會與自訂物件清單

若為「機會」和「自訂物件」，Marketo僅會載入每種型別最近更新的10個物件。 您可以依照此處所述的步驟增加可用自訂物件的數量。

Marketo在名為`<objectName>List`的清單中提供物件，從最近更新的記錄排序為最近更新的記錄。 若要從最近更新的商機存取「金額」欄位，請使用：

`${OpportunityList.get(0).Amount}`

此範例參考OpportunityList物件，使用get方法存取索引0處的記錄，並從該記錄擷取Amount屬性。

當您將「機會」或「自訂物件」欄位拖曳到編輯器時，Marketo會自動從索引0的記錄中擷取欄位。

## SFDC自訂物件關係

若要使用SFDC自訂物件，該物件必須與Marketo潛在客戶只有一個關係。 物件通常透過連絡人和帳戶連結。 僅同步已啟用潛在客戶/連絡人關係的物件。

## 觸發物件

當行銷活動使用「新增至機會」、「機會已更新」或「新增至`<Custom Object Name>`觸發器」時，`$TriggerObject`變數可用於編寫在觸發器行銷活動中執行的權杖指令碼。 `<Custom Object Name>` is Updated觸發器不支援此變數。

此變數會參考觸發行銷活動的物件。 它包含當您透過其他變數名稱存取物件時可用的相同記錄資料。

請勿在批次行銷活動中使用參照`$TriggerObject`的權杖。 批次行銷活動中無法使用物件，且電子郵件傳送失敗。

例如，如果產品訂單的自訂物件觸發促銷活動，`$TriggerObject`變數會公開銷售機會新增至的訂單。

下列範例顯示訂單後續追蹤電子郵件的指令碼：

```html
<div>
<strong>Your order information:</strong>
##pull information from the Triggering Order and format it in a list
<ul>
<li>Product Ordered: $!{TriggerObject.ProductName}</li>
<li>Product Quantity: $!{TriggerObject.Quanitity}</li>
<li>Shipping Address: $!{TriggerObject.ShippingAddress}</li>
<li>Billing Address: $!{TriggerObject.BillingAddress}</li>
<li>Order Total: $!{TriggerObject.Amount}</li>
</ul>
<p><a href="$!{TriggerObject.OrderURL}">View Your Order Online</a></p>
</div>
```

觸發動作會決定物件。 您不需要額外的程式碼來判斷哪些可用物件包含本機資料。 使用`$TriggerObject` （當它可用且適當時），因為它明確識別要參考的物件。

注意：當您使用`$TriggerObject`時，請在編輯窗格中選取物件的欄位，讓指令碼可以使用。

注意2： `$TriggerObject`僅適用於「已新增」觸發程式，不適用於「已更新」觸發程式。
