---
title: "REST API"
feature: REST API
description: 「REST API總覽」
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '643'
ht-degree: 0%

---


# REST API

Marketo公開REST API，允許從遠端執行系統的許多功能。 從建立程式到大量潛在客戶匯入，有許多選項可讓您對Marketo執行個體進行微調控制。

這些API通常分為兩大類： [潛在客戶資料庫](https://developer.adobe.com/marketo-apis/api/mapi/)、和 [資產](https://developer.adobe.com/marketo-apis/api/asset/). 潛在客戶資料庫API可擷取Marketo個人記錄及相關物件型別（例如商機和公司）並與其互動。 資產API可與行銷宣傳品和工作流程相關記錄互動。

- **每日配額：** 訂閱每天會分配50,000個API呼叫（這會在每日中午12:00CST重設）。 您可以透過帳戶管理員增加每日配額。
- **速率限制：** 每個執行個體的API存取限製為每20秒100次呼叫。
- **並行限制：**  最多十個同時進行的API呼叫。

標準呼叫的大小限製為8KB的URI長度，而內文大小為1MB，不過大量API的內文可以是10MB。 如果您的呼叫發生錯誤，API通常仍會傳回狀態代碼200，但JSON回應將包含值為「 」的「成功」成員 `false`和「錯誤」成員中的錯誤陣列。 錯誤詳細資訊 [此處](error-codes.md).

## 快速入門

下列步驟需要您Marketo執行個體的管理員許可權。

第一次致電Marketo時，您將擷取潛在客戶記錄。 若要開始使用Marketo，您必須取得API認證，才能對執行個體進行已驗證呼叫。 登入您的執行個體並前往 **[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**.

![管理員使用者和角色](assets/admin-users-and-roles.png)

按一下 [!UICONTROL Roles] 索引標籤，然後新增角色，並至少將「唯讀銷售機會」（或「唯讀人員」）許可權指派給存取API群組中的角色。 請務必提供描述性名稱，然後按一下 [!UICONTROL Create].

![新角色](assets/new-role.png)

現在返回使用者索引標籤，然後按一下邀請新使用者。 為您的使用者提供描述性名稱（表示其為API使用者）、電子郵件地址並按一下 **[!UICONTROL Next]**.

![新使用者資訊](assets/new-user-info.png)

接著，核取「僅限API」選項，並為您的使用者授與您建立的API角色，然後按一下 **[!UICONTROL Next]**.

![新使用者許可權](assets/new-user-permissions.png)

若要完成使用者建立程式，請按一下 **[!UICONTROL Send]**.

![新增使用者訊息](assets/new-user-message.png)

接下來，前往管理員功能表，然後按一下 **[!UICONTROL LaunchPoint]**.

![啟動點](assets/admin-launchpoint.png)

按一下「新增」功能表並選取 [!UICONTROL New Service]. 為您的服務提供描述性名稱，然後從「服務」下拉式選單中選取「自訂」。 提供說明，然後從「僅限API使用者」下拉式選單中選取新使用者，並按一下 [!UICONTROL Create].

![新啟動點服務](assets/admin-launchpoint-new-service.png)

按一下新服務的檢視詳細資訊，即可存取使用者端ID和使用者端密碼。 現在，您可以按一下 [!UICONTROL Get Token] 按鈕來產生一小時有效的存取權杖。 暫時將代號儲存在備註中。

![取得Token](assets/get-token.png)

接下來，前往「管理員」功能表，然後前往 **[!UICONTROL Web Services]**.

![網站服務](assets/admin-web-services.png)

目前在REST API方塊中找到「端點」，並儲存在附註中。

![REST端點](assets/admin-web-services-rest-endpoint-1.png)

開啟新的瀏覽器索引標籤，並使用要呼叫的適當資訊輸入以下內容 [依篩選器型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)：

```
<Your Endpoint URL>/rest/v1/leads.json?access_token=<Your Access Token>&filterType=email&filterValues=<Your Email Address>
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

每個API使用者會在API使用報告中個別報告，因此依使用者分割網站服務可讓您輕鬆說明每個整合的使用情況。 如果對您執行個體的API呼叫數量超過限制，並導致後續呼叫失敗，使用此作法可讓您計算每個服務的數量，並讓您評估如何解決問題。 請前往「 」檢視您的使用狀況 **[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 並按一下過去七天內的通話次數。
