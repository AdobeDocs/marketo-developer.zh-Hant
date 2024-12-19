---
title: 移轉至REST API
feature: SOAP
description: 從SOAP移轉至REST API
source-git-commit: 8ad3e3f0958ea705375651b1c8a75967d807ca80
workflow-type: tm+mt
source-wordcount: '643'
ht-degree: 1%

---


# 概觀

Marketo Engage SOAP API將在2025年10月31日之後淘汰。 在此日期之前，所有使用SOAP API的現有整合應該已淘汰或移轉至[Marketo EngageREST API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/rest-api)，以避免服務中斷。

## 移轉

與[REST AP](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/rest-api)I相比，SOAP API支援有限的使用案例範圍。在決定要對應使用案例的端點時，您應該遵循[Marketo整合最佳實務](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/marketo-integration-best-practices)

[參考架構](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/reference-architectures)可用於[CRM同步處理](https://experienceleague.adobe.com/docs/marketo-developer/assets/sync-architecture-whitepaper.pdf?lang=en)和[Data Warehouse匯出](https://experienceleague.adobe.com/docs/marketo-developer/assets/reference_architecture.pdf?lang=en)使用案例。

## 驗證

[驗證檔案](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/authentication)

Marketo REST API會搭配使用者端憑證授權型別，使用OAuth 2.0型驗證。 存取權杖在建立後一小時內有效。

## 銷售機會

[潛在客戶API檔案](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads)

SOAP API支援銷售機會資料同步處理、[Munchkin Cookie關聯](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/javascriptapi/leadtracking/lead-tracking)以及銷售機會合併。 如果您的應用程式呼叫SOAP syncLead方法並設定`marketoCookie`引數，您可以透過下列其中一種方式移轉：

1. 使用[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) REST方法，接著使用[關聯銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST)
2. 您可以呼叫[提交表單](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads&quot;%20\l%20&quot;submit-form)，不過這需要設定一些行銷Assets以及與[Forms API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/forms)的某些互動

使用`foreignSysPersonId`金鑰型別的應用程式，應移轉至使用自訂銷售機會欄位來代表此外部識別碼，並使用[同步銷售機會](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads#create-and-update)或[大量銷售機會匯入](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import) REST方法。

| SOAP方法 | REST方法 |
| --- | --- |
| [getLead](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/getlead) | [依識別碼取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)，[依篩選型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) |
| [getMultipleLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/getmultipleleads) | [依識別碼取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)，[依篩選型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，[依計畫識別碼取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByProgramIdUsingGET)，[依清單識別碼取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByListIdUsingGET)，[大量銷售機會匯出](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads) |
| [mergeLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/mergeleads) | [合併潛在客戶](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST) |
| [syncLead](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/synclead) | [同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [提交表單](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST) [關聯銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST) |
| [syncMultipleLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/syncmultipleleads) | [同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [大量匯入](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |

## M個物件

M Objects是一個概念性的概念，可支援匯出Opportunity Attribution資料供外部分析使用，並處理三種物件型別：Opportunity、Opportunity Roles和Programs。

REST檔案：

- [商機](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/opportunities)
- [角色](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/opportunity-roles)
- [程式](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/programs)

| SOAP方法 | REST方法 |
| --- | --- |
| [deleteMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/deletemobjects) | [刪除商機](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST)，[刪除商機角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunityRolesUsingPOST) |
| [describeMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/describemobject) | [描述商機](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_4)，[描述商機角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [getMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/getmobjects) | [取得商機](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getOpportunitiesUsingGET)，[取得商機角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [listMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/listmobjects) | 不適用 |
| [syncMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/syncmobjects) | [同步處理商機](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunitiesUsingPOST)，[同步處理商機角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunityRolesUsingPOST) |
| [getChannels](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/programs/getchannels) | [取得頻道](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllChannelsUsingGET) |
| [getTags](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/programs/gettags) | [取得標籤型別](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagTypesUsingGET)，[依名稱取得標籤](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagByNameUsingGET) |

## 靜態清單

SOAP API中的靜態清單使用案例僅限於擷取成員資格和銷售機會資料，以及移除成員資格，其可透過[新增至清單](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST)、[大量匯入銷售機會](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import)或[從清單](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) REST方法移除。

| SOAP方法 | REST方法 |
| --- | --- |
| [getImportToListStatus](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/getimporttoliststatus) | [大量匯入銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [importToList](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/importtolist) | [新增至清單](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST) [大量匯入銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [listOperation](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/listoperation) | [從清單移除](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) |

## 活動

SOAP API僅支援活動擷取。

REST檔案：

- [同步活動](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/activities)
- [大量活動擷取](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-extract/bulk-activity-extract)

| SOAP方法 | REST方法 |
| --- | --- |
| [getLeadActivity](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activities/getleadactivity) | [大量匯出活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) |
| [getLeadChanges](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activities/getleadchanges) | [大量匯出活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [取得銷售機會變更](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadChangesUsingGET) |

## 行銷活動

REST檔案：

- [智慧行銷活動](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns&quot;%20\h%20HYPERLINK%20&quot;https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns)

SOAP API僅支援智慧行銷活動的三個使用案例：[觸發銷售機會以符合申請的Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns#trigger)、擷取這些申請的行銷活動，以及[排程未來執行Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns#schedule)。

| SOAP方法 | REST方法 |
| --- | --- |
| [getCampaignsForSource](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/getcampaignsforsource) | [取得智慧行銷活動](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllSmartCampaignsGET) |
| [requestCampaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/requestcampaign) | [要求行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST) |
| [scheduleCampaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/schedulecampaign) | [排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST) |

## 自訂物件

REST檔案：

- [自訂物件](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/custom-objects&quot;%20\h%20HYPERLINK%20&quot;https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/custom-objects)

SOAP API僅支援自訂物件的CRUD操作。

| SOAP方法 | REST方法 |
| --- | --- |
| [deleteCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/deletecustomobjects) | [刪除自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteCustomObjectsUsingPOST) |
| [getCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/getcustomobjects) | [取得自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCustomObjectsUsingGET) |
| [syncCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/synccustomobjects) | [同步自訂物件](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncCustomObjectsUsingPOST) [大量匯入自訂物件](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import) |