---
title: REST API
feature: REST API
description: REST API總覽
exl-id: 4b9beaf0-fc04-41d7-b93a-a1ae3147ce67
source-git-commit: 8ad3e3f0958ea705375651b1c8a75967d807ca80
workflow-type: tm+mt
source-wordcount: '770'
ht-degree: 1%

---

# REST API

Marketo公開REST API，允許從遠端執行系統的許多功能。 從建立程式到大量潛在客戶匯入，有許多選項可讓您對Marketo執行個體進行微調控制。

這些API通常分為兩大類： [潛在客戶資料庫](https://developer.adobe.com/marketo-apis/api/mapi/)和[資產](https://developer.adobe.com/marketo-apis/api/asset/)。 潛在客戶資料庫API可擷取Marketo個人記錄和相關物件型別（例如商機和公司）並與其互動。 資產API可與行銷宣傳品和工作流程相關記錄互動。

>[!NOTE]
>SOAP API即將淘汰，自2025年10月31日起不再提供。 所有新的開發應使用Marketo [REST API](./rest-api.md)完成，而現有服務應於該日期前移轉，以避免服務中斷。 如果您有使用SOAP API的服務，請參閱SOAP API [移轉指南](../soap-api/migration.md)以瞭解如何移轉的資訊。
>

- **每日配額：**&#x200B;訂閱每天會配置50,000個API呼叫（這會在每日中午12:00CST重設）。 您可以透過帳戶管理員增加每日配額。
- **速率限制：**&#x200B;每個執行個體的API存取限製為每20秒100次呼叫。
- **並行限制：**  最多十個同時進行的API呼叫。

標準呼叫的大小限製為8KB的URI長度，而內文大小為1MB，不過大量API的內文可以是10MB。 如果您的呼叫發生錯誤，API通常仍會傳回狀態代碼200，但JSON回應將包含值為`false`的「success」成員，以及「errors」成員中的錯誤陣列。 錯誤詳細資訊[在此](error-codes.md)。

## 快速入門

下列步驟需要您Marketo執行個體的管理員許可權。

第一次致電Marketo時，您會擷取潛在客戶記錄。 若要開始使用Marketo，您必須取得API認證，才能對執行個體進行已驗證呼叫。 登入您的執行個體並移至&#x200B;**[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**。

![管理員使用者和角色](assets/admin-users-and-roles.png)

按一下「**[!UICONTROL Roles]**」標籤，然後按一下「新增角色」，並至少將「唯讀銷售機會」（或「唯讀人員」）許可權指派給Access API群組中的角色。 請務必提供描述性名稱，然後按一下&#x200B;**[!UICONTROL Create]**。

![新角色](assets/new-role.png)

現在，返回[!UICONTROL Users]標籤並按一下&#x200B;**[!UICONTROL Invite New User]**。 提供使用者描述性名稱（表示其為API使用者）和電子郵件地址，然後按一下&#x200B;**[!UICONTROL Next]**。

![新使用者資訊](assets/new-user-info.png)

然後，核取[!UICONTROL API Only]選項並授與您建立的API角色給您的使用者，然後按一下&#x200B;**[!UICONTROL Next]**。

![新使用者許可權](assets/new-user-permissions.png)

若要完成使用者建立程式，請按一下&#x200B;**[!UICONTROL Send]**。

![新使用者訊息](assets/new-user-message.png)

接著，前往[!UICONTROL Admin]功能表並按一下&#x200B;**[!UICONTROL LaunchPoint]**。

![啟動點](assets/admin-launchpoint.png)

按一下&#x200B;**[!UICONTROL New]**&#x200B;功能表並選取&#x200B;**[!UICONTROL New Service]**。 提供描述性服務名稱，並從[!UICONTROL Service]下拉式功能表中選取&#x200B;**[!UICONTROL Custom]**。 提供說明，然後從[!UICONTROL API Only User]下拉式功能表中選取您的新使用者，並按一下&#x200B;**[!UICONTROL Create]**。

![新的Launchpoint服務](assets/admin-launchpoint-new-service.png)

按一下您新服務的&#x200B;**[!UICONTROL View Details]**&#x200B;以存取使用者端識別碼和使用者端密碼。 現在，您可以按一下&#x200B;**[!UICONTROL Get Token]**&#x200B;按鈕來產生一小時有效的存取權杖。 暫時將代號儲存在備註中。

![取得Token](assets/get-token.png)

接下來，前往&#x200B;**[!UICONTROL Admin]**&#x200B;功能表，然後前往&#x200B;**[!UICONTROL Web Services]**。

![網站服務](assets/admin-web-services.png)

在REST API方塊中尋找[!UICONTROL Endpoint]並暫時儲存在備註中。

![REST端點](assets/admin-web-services-rest-endpoint-1.png)

呼叫REST API方法時，存取權杖必須包含在每個呼叫中，呼叫才能成功。 存取權杖必須以HTTP標頭傳送。

```
Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int
```

>[!IMPORTANT]
>
>自2025年6月30日起，將移除對使用&#x200B;**access_token**&#x200B;查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，則應儘快更新以使用&#x200B;**Authorization**&#x200B;標頭。 新開發應專門使用&#x200B;**Authorization**&#x200B;標頭。

開啟新的瀏覽器標籤並輸入下列內容，使用適當的資訊呼叫[依篩選型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)

```
<Your Endpoint URL>/rest/v1/leads.json?&filterType=email&filterValues=<Your Email Address>
```

如果您的資料庫中沒有潛在客戶記錄包含您的電子郵件地址，請將其取代為您知道存在的記錄。 按一下URL列中的Enter鍵，您應該會得到類似以下內容的JSON回應：

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

每個API使用者會在API使用報告中個別報告，因此依使用者分割網站服務可讓您輕鬆說明每個整合的使用情況。 如果對您執行個體的API呼叫數量超過限制，並導致後續呼叫失敗，使用此作法可讓您計算每個服務的數量，並讓您評估如何解決問題。 前往「**[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**」並按一下過去七天的通話次數，以檢視您的使用情形。
