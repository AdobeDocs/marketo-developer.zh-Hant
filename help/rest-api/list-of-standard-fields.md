---
title: 標準欄位
feature: REST API, Field Management
description: 瀏覽包含REST名稱、標籤和說明的Marketo標準銷售機會欄位完整清單，以及如何透過Describe Lead API擷取它們。
exl-id: 147dbdff-4bc9-4ab3-8918-c4de3e1aa97a
TQID: https://experienceleague.adobe.com/vu2wGk36XJ243vwavhfLE7Vc9vMIJKGx6vmVqMRgEDA
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e64968b2-4ee5-47f9-8cae-0588f184b9ebid: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: bcf56d2102f2f60eac5ad3318d348fd020391e6b
workflow-type: tm+mt
source-wordcount: 688
ht-degree: 16%

---

# 標準欄位

下表列出可透過API使用的標準Marketo欄位。 其中包含每個欄位的REST API名稱、標籤和說明。

使用REST [Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi)端點來擷取您的Lead記錄支援的所有欄位名稱。

| REST API名稱 | 易記標籤 | 說明 |
| --- | --- | --- |
| 地址 | 地址 | 潛在客戶的地址 |
| annualRevenue | 年收入 | 潛在客戶公司的年收入 |
| anonymousIP | 匿名 IP | 潛在客戶第一個有紀錄的網頁造訪的IP位址 |
| 帳單城市 | 帳單城市 | 潛在客戶帳單地址的城市 |
| billingCountry | 帳單國家 | 潛在客戶帳單地址的國家/地區 |
| billingPostalCode | 帳單郵遞區號 | 潛在客戶帳單地址的郵遞區號 |
| billingState | 帳單州別 | 潛在客戶帳單地址的州或省 |
| billingStreet | 帳單地址 | 潛在客戶公司的帳單街道地址 |
| 城市 | 城市 | 潛在客戶所在城市 |
| 公司 | 公司名稱 | 潛在客戶的公司名稱 |
| 國家/地區 | 國家/地區 | 潛在客戶的國家/地區 |
| dateOfBirth | 出生日期 | 潛在客戶出生日期 |
| 部門 | 部門 | 公司中的潛在客戶部門 |
| doNotCall | 請勿來電 | 潛在客戶的「請勿致電」偏好設定 |
| doNotCallReason | 請勿來電理由 | 潛在客戶的「請勿致電」偏好設定說明 |
| 電子郵件 | 電子郵件地址 | 潛在客戶的電子郵件地址。 潛在客戶記錄的標準Marketo索引鍵欄位 |
| 傳真 | 傳真號碼 | 潛在客戶的傳真號碼 |
| 名字 | 名字 | 潛在客戶名字 |
| industry | 行業 | 潛在客戶產業 |
| inferredCompany | 推斷的公司 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的公司名稱 |
| inferredCountry | 推斷的國家 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的國家/地區 |
| 姓氏 | 姓氏 | 潛在客戶姓氏 |
| leadRole | 角色 | 潛在客戶在其公司中的角色 |
| 銷售機會分數 | 銷售線索分數 | 透過評分行銷活動和方案授予潛在客戶的整數分數 |
| leadSource | 銷售線索來源 | 記錄潛在客戶來源的欄位 |
| 潛在客戶狀態 | 銷售線索狀態 | 記錄潛在客戶目前行銷/銷售狀態的欄位 |
| mainPhone | 主要電話 | 潛在客戶公司的主要電話號碼 |
| jigsawContactId | MARKETO Data.com ID | 潛在客戶的Data.com ID （若有） |
| Jigsawcontactstatus | Marketo Data.com狀態 | 潛在客戶的Data.com狀態（若有） |
| 中間名 | 中間名 | 潛在客戶的中間名 |
| 行動電話 | 手機號碼 | 潛在客戶的行動電話號碼 |
| numberOfEmployees | 員工人數 | 潛在客戶公司的員工人數 |
| 電話 | 電話號碼 | 潛在客戶的電話號碼 |
| postalCode | 郵遞區號 | 潛在客戶的郵遞區號 |
| 評等 | 銷售線索評等 | 潛在客戶的行銷/銷售評等 |
| salutation | 問候語 | 潛在客戶偏好的稱呼，即先生、未接電話……等等 |
| sicCode | SIC 代碼 | 潛在客戶公司的標準產業分類代碼 |
| 網站 | 地點 |  |
| state | 狀態 | 潛在客戶狀態 |
| 標題 | 職稱 | 潛在客戶職稱 |
| 已取消訂閱 | 已取消訂閱 | 潛在客戶的電子郵件取消訂閱狀態。 部分系統受管理。 若設為true，將防止接收非營運電子郵件。 |
| unsubscriptedReason | 取消訂閱原因 | 潛在客戶取消訂閱狀態的原因。 部分系統受管理。 如果潛在客戶已直接從Marketo電子郵件取消訂閱，則填入電子郵件資訊。 |
| 網站 | 網站 | 潛在客戶公司的網站URL |
| createdAt | 建立時間 | 潛在客戶記錄的初始建立時間。 系統管理 |
| 更新時間 | 更新時間 | 上次更新潛在客戶記錄的時間。 系統管理 |
| emailInvalid | 電子郵件無效 | 電子郵件無效狀態。 若設為true，則會封鎖所有傳送至該位址的電子郵件。 指出電子郵件無效的退信會自動將此欄位設為true。 |
| emailInvalidCause | 電子郵件無效原因 | 造成電子郵件狀態無效的原因。 當電子郵件無效設為true時，初始化退信訊息將記錄在此欄位中。 |
| inferredCity | 推斷的城市 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶城市。 |
| inferredMetropolitanArea | 推斷的大都會區 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶大都會區域。 |
| inferredPhoneAreaCode | 推斷的電話區碼 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶電話區碼。 |
| inferredPostalCode | 推斷的郵遞區號 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶的郵遞區號。 |
| inferredStateRegion | 推斷的州別區域 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶州別。 |
| isAnonymous | 匿名 | 潛在客戶記錄的匿名狀態。 系統已管理。 |
| 優先順序 | 優先順序 | 潛在客戶的銷售Insight優先順序。 系統已管理。 |
| 相對分數 | 相對分數 | 潛在客戶的Sales Insight相對分數。 系統已管理。 |
| 急迫性 | 急迫性 | 潛在客戶的銷售Insight急迫性。 系統已管理。 |
