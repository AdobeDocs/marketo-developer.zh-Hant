---
title: REST API
feature: REST API
description: 瞭解如何使用Marketo REST API、設定API使用者和LaunchPoint、檢視配額和限制、使用授權標頭驗證以及擷取銷售機會。
exl-id: 4b9beaf0-fc04-41d7-b93a-a1ae3147ce67
TQID: https://experienceleague.adobe.com/GqhWI816wWX-2zf89wWj-GXpg9i615HRFVl2ljdYVj0
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 726
ht-degree: 2%

---

# REST API

Marketo REST API可讓您從遠端存取許多系統功能。 您可以用它來建立程式、大量匯入銷售機會，以及在詳細層級控制Marketo執行個體。

REST API分為兩大類：

- [潛在客戶資料庫](https://developer.adobe.com/marketo-apis/api/mapi) API會擷取並與Marketo人員記錄和相關物件型別（例如商機和公司）互動。
- [資產](https://developer.adobe.com/marketo-apis/api/asset) API與行銷宣傳品和工作流程相關記錄互動。

>[!NOTE]
>
>自2026年7月31日起，SOAP API已淘汰，不再提供。 所有新的開發應使用Marketo [REST API](./rest-api.md)完成。
>

>[!IMPORTANT]
>
>請參閱此[Nation貼文](https://nation.marketo.com/t5/product-blogs/rest-api-double-slash-deprecation/ba-p/358616)，瞭解API閘道URL中雙斜線是否被取代。
>

- **每日配額：**&#x200B;每個訂閱每天都會分配50,000個API呼叫。 配額會在每日中午12:00 CST重設。 請連絡您的帳戶管理員以增加每日配額。
- **速率限制：**&#x200B;每個執行個體限製為每20秒100次API呼叫。
- **並行限制：**&#x200B;每個執行個體最多允許10個並行API呼叫。

標準API呼叫的URI長度上限為8 KB，主體大小上限為1 MB。 大量API呼叫支援最大內文大小10 MB。

當呼叫包含錯誤時，API通常仍會傳回HTTP狀態代碼200。 JSON回應包含值為`false`的`success`成員，以及`errors`成員中的錯誤陣列。 有關錯誤的詳細資訊[在此](error-codes.md)。

## 快速入門

您需要Marketo執行個體的管理員許可權，才能完成下列步驟。 此工作流程會建立API認證，並使用這些認證來擷取潛在客戶記錄。

首先，建立API使用者並取得已驗證呼叫的認證。 登入您的執行個體並移至&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Users and Roles]**。

![管理員使用者和角色](assets/admin-users-and-roles.png)

選取&#x200B;**[!UICONTROL Roles]**&#x200B;標籤，然後選取[新增角色]。 從存取API群組至少指派角色的「唯讀銷售機會」（或「唯讀人員」）許可權。 為角色提供描述性名稱，並選取&#x200B;**[!UICONTROL Create]**。

![新角色](assets/new-role.png)

返回[!UICONTROL Users]標籤並選取&#x200B;**[!UICONTROL Invite New User]**。 輸入描述性名稱，將使用者識別為API使用者，輸入電子郵件地址，然後選取&#x200B;**[!UICONTROL Next]**。

![新使用者資訊](assets/new-user-info.png)

選取[!UICONTROL API Only]選項，指派您建立的API角色，然後選取&#x200B;**[!UICONTROL Next]**。

![新使用者許可權](assets/new-user-permissions.png)

選取&#x200B;**[!UICONTROL Send]**&#x200B;以建立使用者。

![新使用者訊息](assets/new-user-message.png)

接著，前往[!UICONTROL Admin]功能表並選取&#x200B;**[!UICONTROL LaunchPoint]**。

![啟動點](assets/admin-launchpoint.png)

選取&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**。 輸入描述性名稱和說明，然後從[!UICONTROL Service]功能表選取&#x200B;**[!UICONTROL Custom]**。 從[!UICONTROL API Only User]功能表選取您的新使用者，然後選取&#x200B;**[!UICONTROL Create]**。

![新的Launchpoint服務](assets/admin-launchpoint-new-service.png)

選取新服務的&#x200B;**[!UICONTROL View Details]**&#x200B;以存取使用者端ID和使用者端密碼。 選取&#x200B;**[!UICONTROL Get Token]**&#x200B;以產生一小時有效的存取權杖。 儲存第一個API呼叫的Token。

![取得Token](assets/get-token.png)

前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Web Services]**。

![網站服務](assets/admin-web-services.png)

在REST API方塊中尋找[!UICONTROL Endpoint]並儲存以供第一個API呼叫使用。

![REST端點](assets/admin-web-services-rest-endpoint-1.png)

每個REST API呼叫都必須在HTTP標頭中包含存取權杖。

```text
Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int
```

>[!IMPORTANT]
>
>自2025年6月30日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

開啟新的瀏覽器索引標籤，然後輸入下列URL。 將預留位置取代為您執行個體的端點和電子郵件地址，以呼叫[依篩選器型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByFilterUsingGET)。

```text
<Your Endpoint URL>/rest/v1/leads.json?&filterType=email&filterValues=<Your Email Address>
```

如果您的資料庫未包含具有電子郵件地址的潛在客戶記錄，請使用現有潛在客戶的電子郵件地址。 提交URL以接收JSON回應，類似於以下範例：

```json
{
    "requestId":"c493#1511ca2b184",
    "result":[
       {
           "id":1,
           "updatedAt":"2015-08-24T20:17:23Z",
           "lastName":"Elkington",
           "email":"developerfeedback@marketo.com",
           "createdAt":"2013-02-19T23:17:04Z",
           "firstName":"Kenneth"
        }
    ],
    "success":true
}
```

## API 使用情況

API使用報告會個別追蹤每個API使用者。 指派個別使用者至各個Web服務，可協助您識別每個整合的API使用方式。

如果呼叫超過您的執行個體限制，且後續呼叫失敗，請使用報告識別每個服務的呼叫量。 前往「**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**」，並選取過去七天進行的通話次數。

對於傳回每日和過去7天使用量和錯誤統計資料的REST端點，請參閱[使用量](usage.md)。
