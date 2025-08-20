---
title: Marketo物件
feature: Email Programs
description: 搭配Velocity指令碼使用Marketo物件的概觀
source-git-commit: 3ccb27a0d184e0c1314979d404022bc4e0794f7b
workflow-type: tm+mt
source-wordcount: '518'
ht-degree: 0%

---

# Marketo物件

Marketo的Velocity實作可針對Marketo內數個來源的資料操作：銷售機會、商機、自訂物件、行動應用程式、行動應用程式安裝。

## 正在載入欄位

若要載入要在指令碼中使用的欄位，必須在指令碼權杖編輯器中的對應清單下檢查該欄位。

如果您未載入欄位並在指令碼中參考欄位，則指令碼在執行階段執行失敗。 您可以從欄位選單將欄位拖放至指令碼中。 如此可讓游標載入，並在游標處新增欄位的參照。

## 機會與自訂物件清單

從「機會」或「自訂物件」擷取時，只會載入某型別的10個最近更新的物件。 可依照此處所述的步驟增加可用自訂物件的數量。 這些檔案是以清單形式提供，名稱為`<objectName>List`，其排列順序為最近更新的記錄從最近到最少。 因此，若要從最近更新的商機存取Amount欄位，請使用下列專案：

`${OpportunityList.get(0).Amount}`

在此範例中，您參照OpportunityList物件，使用get方法存取索引為0的記錄，然後從傳回的物件中擷取Amount屬性。 如果您將欄位從Opportunity或Custom Object拖曳到編輯器中，它會自動從索引為0的記錄中擷取欄位。

## SFDC自訂物件關係

SFDC自訂物件與Marketo潛在客戶必須只有一個關係，才能使用。 物件通常透過聯絡人和帳戶連結，因此重要的是在啟用銷售機會/聯絡人關係的情況下，僅將物件同步到Marketo。

## 觸發物件

當透過「新增至機會」、「機會已更新」或「新增至`<Custom Object Name>`個觸發程式」觸發促銷活動時，在觸發程式促銷活動內容內執行的指令碼Token中會提供一個特殊變數： `$TriggerObject ` （不支援`<Custom Object Name>`為「已更新」觸發程式）。  如果批次行銷活動中使用使用`$TriggerObject`參考的Token，則電子郵件傳送將失敗，因為此物件無法用於任何型別的批次行銷活動。  這是觸發行銷活動的物件參考。 物件包含透過不同變數名稱存取記錄時該記錄擁有的所有資料。

例如，如果促銷活動是透過產品訂單的自訂物件所觸發，則要新增銷售機會的訂單會顯示在`$TriggerObject`變數中。

以下是訂單後續追蹤電子郵件的範例指令碼：

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

使用`$TriggerObject`變數的優點在於，您不需要指定任何程式碼來決定要從哪一個可用物件提取本機資料。  物件由觸發動作決定。 這是選擇要參考的物件最明確的方式，只要可用且適當，就應該使用。

注意：使用`$TriggerObject`時，必須在編輯窗格中核取欄位，物件才能供指令碼使用。

注意2： `$TriggerObject`僅適用於「已新增」觸發程式，不適用於「已更新」觸發程式。
