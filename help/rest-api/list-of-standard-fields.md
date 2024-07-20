---
title: 標準欄位
feature: REST API, Field Management
description: 標準Marketo欄位表格。
exl-id: 147dbdff-4bc9-4ab3-8918-c4de3e1aa97a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1140'
ht-degree: 11%

---

# 標準欄位

以下是可透過API存取的Marketo中可用標準欄位清單。

您可以使用REST [Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi/)端點來擷取潛在客戶記錄上所有支援的欄位名稱清單。

| REST API名稱 | SOAP API名稱 | 易記標籤 | 說明 |
| --- | --- | --- | --- |
| 地址 | 地址 | 地址 | 潛在客戶的地址 |
| annualRevenue | 年收入 | 年收入 | 潛在客戶公司的年收入 |
| anonymousIP | 匿名IP | 匿名 IP | 潛在客戶第一個有紀錄的網頁造訪的IP位址 |
| 帳單城市 | 帳單城市 | 帳單城市 | 潛在客戶帳單地址的城市 |
| billingCountry | 帳單國家/地區 | 帳單國家 | 潛在客戶帳單地址的國家/地區 |
| billingPostalCode | 帳單郵遞區號 | 帳單郵遞區號 | 潛在客戶帳單地址的郵遞區號 |
| billingState | BillingState | 帳單州別 | 潛在客戶帳單地址的州或省 |
| billingStreet | 帳單街道 | 帳單地址 | 潛在客戶公司的帳單街道地址 |
| 城市 | 城市 | 城市 | 潛在客戶所在城市 |
| 公司 | 公司 | 公司名稱 | 潛在客戶的公司名稱 |
| 國家/地區 | 國家 | 國家 | 潛在客戶的國家/地區 |
| dateOfBirth | DateofBirth | 出生日期 | 潛在客戶出生日期 |
| 部門 | 部門 | 部門 | 公司中的潛在客戶部門 |
| doNotCall | DoNotCall | 請勿來電 | 潛在客戶的「請勿致電」偏好設定 |
| doNotCallReason | DoNotCallReason | 請勿來電理由 | 潛在客戶的「請勿致電」偏好設定說明 |
| 電子郵件 | 電子郵件 | 電子郵件地址 | 潛在客戶的電子郵件地址。 潛在客戶記錄的標準Marketo索引鍵欄位 |
| 傳真 | 傳真 | 傳真號碼 | 潛在客戶的傳真號碼 |
| 名字 | 名字 | 名字 | 潛在客戶名字 |
| industry | 產業 | 產業 | 潛在客戶產業 |
| inferredCompany | 推斷的公司 | 推斷的公司 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的公司名稱 |
| inferredCountry | 推斷的國家 | 推斷的國家 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的國家/地區 |
| 姓氏 | 姓氏 | 姓氏 | 潛在客戶姓氏 |
| leadRole | 潛在客戶角色 | 角色 | 潛在客戶在其公司中的角色 |
| 銷售機會分數 | 潛在客戶分數 | 銷售機會分數 | 透過評分行銷活動和方案授予潛在客戶的整數分數 |
| leadSource | 潛在客戶來源 | 銷售機會來源 | 記錄潛在客戶來源的欄位 |
| 潛在客戶狀態 | 潛在客戶狀態 | 銷售機會狀態 | 記錄潛在客戶目前行銷/銷售狀態的欄位 |
| mainPhone | 主要電話 | 主要電話 | 潛在客戶公司的主要電話號碼 |
| jigsawContactId | Marketo拼圖連絡人ID | MARKETO Data.com ID | 潛在客戶的Data.com ID （若有） |
| Jigsawcontactstatus | Marketo拼圖連絡人狀態 | Marketo Data.com狀態 | 潛在客戶的Data.com狀態（若有） |
| facebookDisplayName | MarketoSocialFacebookDisplayName | Marketo Social Facebook顯示名稱 | 潛在客戶的Facebook顯示名稱。 系統已在社交登入期間填入 |
| facebookId | MarketoSocialFacebookId | Marketo Social Facebook Id | 潛在客戶的Facebook ID。 系統已在社交登入期間填入 |
| facebookPhotoURL | MarketoSocialFacebookPhotoURL | Marketo Social Facebook像片URL | 潛在客戶的Facebook設定檔像片的URL。 系統已在社交登入期間填入 |
| facebookProfileURL | MarketoSocialFacebookProfileURL | Marketo社交Facebook設定檔URL | 潛在客戶的Facebook設定檔的URL。 系統已在社交登入期間填入 |
| facebookReach | MarketoSocialFacebookReach | Marketo Social Facebook觸及 | 潛在客戶的Facebook觸及範圍。 系統已在社交登入期間填入 |
| facebookReferredEnrollments | MarketoSocialFacebookReferredEnrollments | Marketo Social Facebook轉介註冊 | 透過Facebook歸因於潛在客戶的轉介註冊數。 系統管理 |
| facebookReferredVisits | MarketoSocialFacebookReferredVisits | Marketo Social Facebook轉介造訪 | 透過Facebook歸因於潛在客戶的轉介造訪次數。 系統管理 |
| 性別 | MarketoSocialGender | Marketo社交性別 | 潛在客戶的性別。 系統已在社交登入期間填入 |
| lastReferredEnrollment | MarketoSocialLastReferredEnrollment | Marketo社交上次反向連結註冊 | 上次完成轉介的日期。 系統管理 |
| lastReferredVisit | MarketoSocialLastReferredVisit | Marketo Social上次轉介的造訪 | 上次參照造訪的日期。 系統管理 |
| linkedInDisplayName | MarketoSocialLinkedInDisplayName | Marketo Social LinkedIn顯示名稱 | 潛在客戶的LinkedIn顯示名稱。 系統已在社交登入期間填入 |
| linkedInId | MarketoSocialLinkedInId | Marketo Social LinkedIn Id | 潛在客戶的LinkedIn ID。 系統已在社交登入期間填入 |
| linkedInPhotoURL | MarketoSocialLinkedInPhotoURL | Marketo Social LinkedIn像片URL | 潛在客戶的LinkedIn像片URL。 系統已在社交登入期間填入 |
| linkedinprofileURL | MarketoSocialLinkedInProfileURL | Marketo社交LinkedIn設定檔URL | 潛在客戶的LinkedIn設定檔。 系統已在社交登入期間填入 |
| linkedInReach | MarketoSocialLinkedInReach | Marketo Social LinkedIn觸及 | 潛在客戶的LinkedIn觸角。 系統已在社交登入期間填入 |
| linkedInReferredEnrollments | MarketoSocialLinkedInReferredEnrollments | Marketo Social LinkedIn轉介註冊 | 透過LinkedIn歸因於潛在客戶的轉介註冊數。 系統管理 |
| linkedInReferredVisits | MarketoSocialLinkedInReferredVisits | Marketo Social LinkedIn轉介造訪 | 透過LinkedIn歸因於潛在客戶的轉介造訪次數。 系統管理 |
| syndicationId |  - | Marketo社交整合ID | 潛在客戶的內部Marketo社交ID。 系統管理 |
| totalReferredEnrollments | MarketoSocialTotalReferredEnrollments | Marketo 社交轉介註冊總次數 | 歸因於潛在客戶的已完成轉介註冊總數 |
| totalReferredVisits | MarketoSocialTotalReferredVisits | Marketo 社交轉介造訪總次數 | 歸因於潛在客戶的被轉介造訪總數 |
| twitterDisplayName | MarketoSocialTwitterDisplayName | Marketo社交Twitter顯示名稱 | 潛在客戶的Twitter顯示名稱。 系統已在社交登入期間填入 |
| twitterId | MarketoSocialTwitterId | Marketo社交TwitterID | 潛在客戶的TwitterID。 系統已在社交登入期間填入 |
| twitterPhotoURL | MarketoSocialTwitterPhotoURL | Marketo社交Twitter像片URL | 潛在客戶的Twitter像片URL。 系統已在社交登入期間填入 |
| twitterProfileURL | MarketoSocialTwitterProfileURL | Marketo社交Twitter設定檔URL | 潛在客戶的Twitter設定檔URL。 系統已在社交登入期間填入 |
| twitterReach | MarketoSocialTwitterReach | Marketo社交Twitter觸及 | 潛在客戶的Twitter觸及。 系統已在社交登入期間填入 |
| twitterReferredEnrollments | MarketoSocialTwitterReferredEnrollments | Marketo社交Twitter反向註冊 | 透過Twitter歸因於潛在客戶的轉介註冊數。 系統管理 |
| twitterReferredVisits | MarketoSocialTwitterReferredVisits | Marketo社交Twitter轉介造訪 | 透過Twitter歸因於潛在客戶的被轉介造訪次數。 系統管理 |
| 中間名 | 中間名 | 中間名 | 潛在客戶的中間名 |
| 行動電話 | 行動電話 | 手機號碼 | 潛在客戶的行動電話號碼 |
| numberOfEmployees | 員工人數 | 員工人數 | 潛在客戶公司的員工人數 |
| 電話 | 電話 | 電話號碼 | 潛在客戶的電話號碼 |
| postalCode | 郵遞區號 | 郵遞區號 | 潛在客戶的郵遞區號 |
| 評等 | 評等 | 銷售機會評等 | 潛在客戶的行銷/銷售評等 |
| salutation | 問候語 | 問候語 | 潛在客戶偏好的稱呼，例如「先生」、「未接電話」等。 |
| sicCode | SICCode | SIC 代碼 | 潛在客戶公司的標準產業分類代碼 |
| 網站 | 地點 | 地點 |  |
| state | 州別 | 州別 | 潛在客戶狀態 |
| 標題 | 標題 | 職稱 | 潛在客戶職稱 |
| 已取消訂閱 | 退訂 | 退訂 | 潛在客戶的電子郵件取消訂閱狀態。 部分系統受管理。 若設為true，將防止接收非營運電子郵件。 |
| unsubscriptedReason | UnsubscriptedReason | 取消訂閱原因 | 潛在客戶取消訂閱狀態的原因。 部分系統受管理。 如果潛在客戶已直接從Marketo電子郵件取消訂閱，則填入電子郵件資訊。 |
| 網站 | 網站 | 網站 | 潛在客戶公司的網站URL |
| createdAt |  - | 建立時間 | 潛在客戶記錄的初始建立時間。 系統管理 |
| 更新時間 |  - | 更新時間 | 上次更新潛在客戶記錄的時間。 系統管理 |
| emailInvalid |  - | 電子郵件無效 | 電子郵件無效狀態。 若設為true，則會封鎖所有傳送至該位址的電子郵件。 指出電子郵件無效的退信會自動將此欄位設為true。 |
| emailInvalidCause |  - | 電子郵件無效原因 | 造成電子郵件狀態無效的原因。 當電子郵件無效設為true時，初始化退信訊息將記錄在此欄位中。 |
| inferredCity |  - | 推斷的城市 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶城市。 |
| inferredMetropolitanArea |  - | 推斷的大都會區 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶大都會區域。 |
| inferredPhoneAreaCode |  - | 推斷的電話區碼 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶電話區碼。 |
| inferredPostalCode |  - | 推斷的郵遞區號 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶的郵遞區號。 |
| inferredStateRegion |  - | 推斷的州別區域 | 透過潛在客戶第一個有紀錄的網頁造訪進行的反向IP查詢所推斷出的潛在客戶州別。 |
| isAnonymous |  - | 匿名 | 潛在客戶記錄的匿名狀態。 系統已管理。 |
| 優先順序 |  - | 優先順序 | 潛在客戶的銷售分析優先順序。 系統已管理。 |
| 相對分數 |  - | 相對分數 | 潛在客戶的銷售分析相對分數。 系統已管理。 |
| 急迫性 |  - | 急迫性 | 潛在客戶的銷售分析急迫性。 系統已管理。 |
