---
title: 自訂服務
feature: REST API
description: 建立Marketo自訂服務、設定僅限API的角色和許可權、在LaunchPoint中取得使用者端ID和使用者端密碼，以及取得存取權杖。
exl-id: 38b05c4c-4404-4c30-a7cb-d31b28a3a72e
TQID: https://experienceleague.adobe.com/lvT-8bYucf-K5LYxb5jQ7BHc137W71SvsGg7cWJlxEs
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 868
ht-degree: 0%

---

# 自訂服務

自訂服務提供用來向Marketo進行驗證以及從Marketo [身分識別服務](https://developer.adobe.com/marketo-apis/api/identity#operation/identityUsingGET)取得存取權杖的認證。 每個自訂服務的範圍限定為一個「僅限API」使用者，並從該使用者衍生其許可權。

## 角色

在建立自訂服務之前，請先建立要指派給相關「僅限API」使用者的角色。 前往 **[!UICONTROL Admin]** > **[!UICONTROL Users & Roles]** > **[!UICONTROL Roles]**。

角色包含允許或限制存取特定功能的個別許可權。 在啟用Workspaces和Partitions的訂閱中，會針對每個工作區指派許可權。 使用者只能在使用者擁有這些許可權的工作區中執行允許的動作。

若要建立角色，請選取&#x200B;**[!UICONTROL New Role]**。

![使用者和角色](assets/admin-users-and-roles-roles.png)

為角色提供描述性名稱。 僅限API的使用者擁有一組與標準使用者許可權不同的特定許可權。 API許可權會顯示在「存取API」樹狀結構下自己的階層中。

![新角色許可權](assets/new-role-access-api-permissions.png)

### 角色許可權

只有「存取API」群組中的許可權適用於API使用者。 指派所有管理員許可權不會將API許可權授予使用者。

當您建構角色時，請確定應用程式必須執行的動作。 僅指派這些動作所需的最低許可權。 不必要的許可權可讓整合功能在您的訂閱中執行不需要的動作。

使用[許可權工具](endpoint-reference.md)來決定最小許可權集。 檢視[許可權](#permission_list)的完整清單。

## 使用者

建立角色後，請建立「僅限API」使用者。 其他使用者管理僅限API的使用者，而僅限API的使用者無法登入Marketo。 他們可以：

- 建立自訂服務
- 這些服務的範圍許可權
- 存取REST API

>[!MORELIKETHIS]
>
>若要建立僅限API的使用者，請前往「**[!UICONTROL Admin]** > **[!UICONTROL Users & Roles]** > **[!UICONTROL Users]**」功能表並選取「**[!UICONTROL Invite New User]**」。

![新使用者資訊](assets/new-user-info.png)

根據將使用該帳戶的服務和應用程式，為使用者提供描述性名稱和電子郵件地址。 電子郵件地址不一定有效。 完成必填欄位，選取&#x200B;**[!UICONTROL API Only]**&#x200B;核取方塊，然後將您的其中一個API角色指派給使用者。 此動作會將角色的許可權集指派給使用者。

![新使用者許可權](assets/new-user-permissions.png)

選取&#x200B;**[!UICONTROL Send]**&#x200B;以建立僅限API的使用者。

當您布建新應用程式的認證時，請考慮為服務建立個別的使用者，即使其他整合使用相同的許可權集。 系統會為每個使用者追蹤API呼叫使用量統計資料和錯誤。

每個應用程式的使用者可協助隔離特定應用程式的使用情況和問題。 當整合達到每日API呼叫限制或產生API錯誤時，此分離相當實用。

## 自訂服務

自訂服務會提供使用Marketo執行個體進行驗證所需的使用者端ID和使用者端密碼。 若要布建服務，請前往「**[!UICONTROL Admin]** > **[!UICONTROL Integrations]** > **[!UICONTROL LaunchPoint]**」，然後選取「**[!UICONTROL New Service]**」。

為服務提供描述性名稱。 從「服務」清單中選取「自訂」。 輸入詳細的說明，從[僅限API使用者]清單中選取適當的使用者，然後選取&#x200B;**[!UICONTROL Create]**。

![新的自訂服務](assets/admin-launchpoint-new-service.png)

此服務會顯示在LaunchPoint Services清單中，並包含「檢視詳細資料」選項。 選取「檢視詳細資料」以存取使用者端ID、使用者端密碼、擁有使用者以及取得權杖選項。

使用Get Token進行短期測試。 權杖的存留期與從[身分識別服務](https://developer.adobe.com/marketo-apis/api/identity#operation/identityUsingGET)取得的權杖相同，且在建立後3,600秒內有效。

![取得Token](assets/get-token.png)

## 工作區與分割區

在具有Workspaces和Partitions的訂閱中，使用者在Workspace中的角色許可權會決定記錄和資產的存取權。 每個工作區都可存取一或多個分割區，而且每個潛在客戶都屬於一個分割區。

如果API-Only使用者可以讀取或寫入工作區中的潛在客戶記錄，則使用者可以存取該工作區可用的分割中的所有記錄。

Assets屬於工作區。 當使用者在資產工作區中具有具有所需許可權的角色時，使用者可以讀取或寫入資產。

## 許可權清單

下表列出「僅限API」使用者可用的許可權，以及每個許可權授予的存取權。

| 角色許可權 | 授予存取權…… |
| --- | --- |
| 核准Assets | 核准資產 |
| 執行行銷活動 | 請求或排程行銷活動 |
| 唯讀活動 | 擷取潛在客戶活動 |
| 唯讀活動中繼資料 | 擷取潛在客戶活動中繼資料 |
| 唯讀Assets | 擷取資產詳細資訊 |
| 唯讀行銷活動 | 擷取行銷活動詳細資料 |
| 唯讀公司 | 擷取公司詳細資料 |
| 唯讀自訂物件 | 擷取自訂物件詳細資料 |
| 唯讀銷售機會 | 擷取銷售機會詳細資料 |
| 唯讀具名帳戶 | 擷取具名帳戶詳細資料 |
| 唯讀具名帳戶清單 | 擷取具名帳戶清單詳細資料 |
| 唯讀機會 | 擷取機會詳細資料 |
| 唯讀銷售人員 | 擷取銷售人員詳細資料 |
| 讀寫活動 | 擷取和建立潛在客戶活動 |
| 讀寫活動中繼資料 | 擷取和建立潛在客戶活動中繼資料 |
| 讀寫Assets | 擷取、建立和更新資產 |
| 讀寫行銷活動 | 擷取、建立和更新行銷活動 |
| 讀寫公司 | 擷取、建立和更新公司 |
| 讀寫自訂物件 | 擷取、建立和更新自訂物件 |
| 讀寫潛在客戶 | 擷取、建立和更新潛在客戶詳細資訊 |
| 讀寫具名帳戶 | 擷取、建立和更新具名帳戶 |
| 讀寫具名帳戶清單 | 擷取、建立和更新具名帳戶清單 |
| 讀寫機會 | 擷取、建立和更新商機 |
| 讀寫銷售人員 | 擷取、建立和更新銷售人員 |
