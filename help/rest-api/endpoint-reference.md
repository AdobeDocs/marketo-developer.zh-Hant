---
title: 端點參考
feature: REST API
description: Marketo API端點參考
exl-id: 27d16b6f-865a-4e40-ab9c-cbabe2927472
source-git-commit: 8a019985fc9ce7e1aa690ca26bfa263cd3c48cfc
workflow-type: tm+mt
source-wordcount: '4676'
ht-degree: 3%

---

# 端點參考

以下是Marketo REST API參考資料的連結。

- [資產](https://developer.adobe.com/marketo-apis/api/asset/)
- [身分識別](https://developer.adobe.com/marketo-apis/api/identity/)
- [潛在客戶資料庫](https://developer.adobe.com/marketo-apis/api/mapi/)
- [用戶管理](https://developer.adobe.com/marketo-apis/api/user/)

## 端點清單

以下是REST API端點的完整清單。

| 名稱 | 群組 | 方法 | URI | 必要許可權 |
|---|---|---|---|---|
| 新增自訂活動 | 活動 | POST | /rest/v1/activities/external.json | 讀寫活動 |
| 核准自訂活動型別 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/approve.json | 讀寫活動中繼資料 |
| 建立自訂活動型別屬性 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/attributes/create.json | 讀寫活動中繼資料 |
| 建立自訂活動型別 | 活動 | POST | /rest/v1/activities/external/type.json | 讀寫活動中繼資料 |
| 刪除自訂活動型別 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/delete.json | 讀寫活動中繼資料 |
| 刪除自訂活動型別屬性 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/attributes/delete.json | 讀寫活動中繼資料 |
| 說明自訂活動型別 | 活動 | GET | /rest/v1/activities/external/type/{apiName}/describe.json | 唯讀活動中繼資料 |
| 捨棄自訂活動型別草稿 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/discardDraft.json | 讀寫活動中繼資料 |
| 取得活動型別 | 活動 | GET | /rest/v1/activities/types.json | 唯讀活動 |
| 取得自訂活動型別 | 活動 | GET | /rest/v1/activities/external/types.json | 唯讀活動中繼資料 |
| 取得已刪除的銷售機會 | 活動 | GET | /rest/v1/activities/deletedleads.json | 唯讀活動 |
| 取得潛在客戶活動 | 活動 | GET | /rest/v1/activities.json | 唯讀活動 |
| 取得銷售機會變更 | 活動 | GET | /rest/v1/activities/leadchanges.json | 唯讀活動 |
| 取得分頁Token | 活動 | GET | /rest/v1/activities/pagingtoken.json | 唯讀活動 |
| 更新自訂活動型別 | 活動 | POST | /rest/v1/activities/external/type/{apiName}.json | 讀寫活動中繼資料 |
| 更新自訂活動型別屬性 | 活動 | POST | /rest/v1/activities/external/type/{apiName}/attributes/update.json | 讀寫活動中繼資料 |
| 身分識別 | 驗證 | GET或POST | /identity/oauth/token | None |
| 取消匯出活動工作 | 大量匯出活動 | POST | /bulk/v1/activities/export/{exportid}/cancel.json | 唯讀活動 |
| 建立匯出活動工作 | 大量匯出活動 | POST | /bulk/v1/activities/export/create.json | 唯讀活動 |
| 將匯出活動工作排入佇列 | 大量匯出活動 | POST | /bulk/v1/activities/export/{exportid}/enqueue.json | 唯讀活動 |
| 取得匯出活動檔案 | 大量匯出活動 | GET | /bulk/v1/activities/export/{exportid}/file.json | 唯讀活動 |
| 取得匯出活動工作狀態 | 大量匯出活動 | GET | /bulk/v1/activities/export/{exportid}/status.json | 唯讀活動 |
| 取得匯出活動工作 | 大量匯出活動 | GET | /bulk/v1/activities/export.json | 唯讀活動 |
| 取消匯出自訂物件工作 | 大量匯出自訂物件 | POST | /bulk/v1/customobjects/export/{exportid}/cancel.json | 唯讀自訂物件 |
| 建立匯出自訂物件工作 | 大量匯出自訂物件 | POST | /bulk/v1/customobjects/export/create.json | 唯讀自訂物件 |
| 將匯出自訂物件工作排入佇列 | 大量匯出自訂物件 | POST | /bulk/v1/customobjects/export/{exportid}/enqueue.json | 唯讀自訂物件 |
| 取得匯出自訂物件檔案 | 大量匯出自訂物件 | GET | /bulk/v1/customobjects/export/{exportid}/file.json | 唯讀自訂物件 |
| 取得匯出自訂物件工作狀態 | 大量匯出自訂物件 | GET | /bulk/v1/customobjects/export/{exportid}/status.json | 唯讀自訂物件 |
| 取得匯出自訂物件工作 | 大量匯出自訂物件 | GET | /bulk/v1/customobjects/export.json | 唯讀自訂物件 |
| 取消匯出潛在客戶工作 | 大量匯出銷售機會 | POST | /bulk/v1/leads/export/{exportid}/cancel.json | 唯讀銷售機會 |
| 建立匯出潛在客戶工作 | 大量匯出銷售機會 | POST | /bulk/v1/leads/export/create.json | 唯讀銷售機會 |
| 將匯出潛在客戶工作排入佇列 | 大量匯出銷售機會 | POST | /bulk/v1/leads/export/{exportid}/enqueue.json | 唯讀銷售機會 |
| 取得匯出潛在客戶檔案 | 大量匯出銷售機會 | GET | /bulk/v1/leads/export/{exportid}/file.json | 唯讀銷售機會 |
| 取得匯出潛在客戶工作狀態 | 大量匯出銷售機會 | GET | /bulk/v1/leads/export/{exportid}/status.json | 唯讀銷售機會 |
| 取得匯出潛在客戶工作 | 大量匯出銷售機會 | GET | /bulk/v1/leads/export.json | 唯讀銷售機會 |
| 取消匯出方案成員工作 | 大量匯出計畫成員 | POST | /bulk/v1/program/members/export/{exportid}/cancel.json | 唯讀銷售機會 |
| 建立匯出程式成員工作 | 大量匯出計畫成員 | POST | /bulk/v1/program/members/export/create.json | 唯讀銷售機會 |
| 將匯出程式成員工作排入佇列 | 大量匯出計畫成員 | POST | /bulk/v1/program/members/export/{exportid}/enqueue.json | 唯讀銷售機會 |
| 取得匯出程式成員檔案 | 大量匯出計畫成員 | GET | /bulk/v1/program/members/export/{exportid}/file.json | 唯讀銷售機會 |
| 取得匯出程式成員工作狀態 | 大量匯出計畫成員 | GET | /bulk/v1/program/members/export/{exportid}/status.json | 唯讀銷售機會 |
| 取得匯出程式成員工作 | 大量匯出計畫成員 | GET | /bulk/v1/program/members/export.json | 唯讀銷售機會 |
| 取得匯入自訂物件失敗 | 大量匯入自訂物件 | GET | /bulk/v1/customobjects/import/{id}/failures.json | 讀寫自訂物件 |
| 取得匯入自訂物件狀態 | 大量匯入自訂物件 | GET | /bulk/v1/customobjects/import/{id}/status.json | 讀寫自訂物件 |
| 取得匯入自訂物件警告 | 大量匯入自訂物件 | GET | /bulk/v1/customobjects/import/{id}/warnings.json | 讀寫自訂物件 |
| 匯入自訂物件 | 大量匯入自訂物件 | POST | /bulk/v1/customobjects/{apiName}/import.json | 讀寫自訂物件 |
| 取得匯入銷售機會失敗 | 大量匯入銷售機會 | GET | /bulk/v1/leads/batch/{id}/failures.json | 讀寫潛在客戶 |
| 取得匯入銷售機會狀態 | 大量匯入銷售機會 | GET | /bulk/v1/leads/batch/{id}.json | 讀寫潛在客戶 |
| 取得匯入銷售機會警告 | 大量匯入銷售機會 | GET | /bulk/v1/leads/batch/{id}/warnings.json | 讀寫潛在客戶 |
| 匯入銷售機會 | 大量匯入銷售機會 | POST | /bulk/v1/leads.json | 讀寫潛在客戶 |
| 取得匯入程式成員失敗 | 大量匯入計畫成員 | GET | /bulk/v1/program/members/import/{id}/failures.json | 讀寫潛在客戶 |
| 取得匯入計畫成員狀態 | 大量匯入計畫成員 | GET | /bulk/v1/program/members/import/{id}/status.json | 讀寫潛在客戶 |
| 取得匯入程式成員警告 | 大量匯入計畫成員 | GET | /bulk/v1/program/members/import/{id}/warnings.json | 讀寫潛在客戶 |
| 匯入方案成員 | 大量匯入計畫成員 | POST | /bulk/v1/program/{programId}/members/import.json | 讀寫潛在客戶 |
| 依ID取得行銷活動 | 行銷活動 | GET | /rest/v1/campaigns/{id}.json | 唯讀行銷活動 |
| 取得行銷活動 | 行銷活動 | GET | /rest/v1/campaigns.json | 唯讀行銷活動 |
| 請求行銷活動 | 行銷活動 | POST | /rest/v1/campaigns/{id}/trigger.json | 讀寫行銷活動 |
| 排程行銷活動 | 行銷活動 | POST | /rest/v1/campaigns/{id}/schedule.json | 讀寫行銷活動 |
| 依名稱取得頻道 | 頻道 | GET | /rest/asset/v1/channel/byName.json | 唯讀資產 |
| 取得頻道 | 頻道 | GET | /rest/asset/v1/channels.json | 唯讀資產 |
| 刪除公司 | 公司 | POST | /rest/v1/companies/delete.json | 讀寫公司 |
| 說明公司 | 公司 | GET | /rest/v1/companies/describe.json | 唯讀公司 |
| 取得公司 | 公司 | GET | /rest/v1/companies.json | 唯讀公司 |
| 同步公司 | 公司 | POST | /rest/v1/companies.json | 讀寫公司 |
| 依名稱取得公司欄位 | 公司 | GET | /rest/v1/companies/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 取得公司欄位 | 公司 | GET | /rest/v1/companies/schema/fields.json | 讀寫結構描述自訂欄位 |
| 新增自訂物件型別欄位 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/addField.json | 讀寫自訂物件型別 |
| 核准自訂物件型別 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/approve.json | 讀寫自訂物件型別 |
| 刪除自訂物件 | 自訂物件 | POST | /rest/v1/customobjects/{name}/delete.json | 讀寫自訂物件 |
| 刪除自訂物件型別 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/delete.json | 讀寫自訂物件型別 |
| 刪除自訂物件型別欄位 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/deleteField.json | 讀寫自訂物件型別 |
| 說明自訂物件 | 自訂物件 | GET | /rest/v1/customobjects/{name}/describe.json | 唯讀自訂物件 |
| 說明自訂物件型別 | 自訂物件 | GET | /rest/v1/customobjects/schema/{apiName}/describe.json | 唯讀自訂物件型別 |
| 捨棄自訂物件型別草稿 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/discardDraft.json | 讀寫自訂物件型別 |
| 取得自訂物件 | 自訂物件 | GET | /rest/v1/customobjects/{name}.json | 唯讀自訂物件 |
| 取得自訂物件可連結物件 | 自訂物件 | GET | /rest/v1/customobjects/schema/linkableObjects.json | 唯讀自訂物件型別 |
| 取得自訂物件相依的Assets | 自訂物件 | GET | /rest/v1/customobjects/schema/{apiName}/dependentAssets.json | 唯讀自訂物件型別 |
| 取得自訂物件型別欄位資料型別 | 自訂物件 | GET | /rest/v1/customobjects/schema/fieldDataTypes.json | 唯讀自訂物件型別 |
| 自訂物件清單 | 自訂物件 | GET | /rest/v1/customobjects.json | 唯讀自訂物件 |
| 自訂物件型別清單 | 自訂物件 | GET | /rest/v1/customobjects/schema.json | 唯讀自訂物件型別 |
| 同步自訂物件 | 自訂物件 | POST | /rest/v1/customobjects/{name}.json | 讀寫自訂物件 |
| 同步自訂物件型別 | 自訂物件 | POST | /rest/v1/customobjects/schema.json | 讀寫自訂物件型別 |
| 更新自訂物件型別欄位 | 自訂物件 | POST | /rest/v1/customobjects/schema/{apiName}/updateField.json | 讀寫自訂物件型別 |
| 核准電子郵件範本草稿 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/approveDraft.json | 讀寫資產 |
| 複製電子郵件範本 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/clone.json | 讀寫資產 |
| 建立電子郵件範本 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplates.json | 讀寫資產 |
| 刪除電子郵件範本 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/delete.json | 讀寫資產 |
| 捨棄電子郵件範本草稿 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/discardDraft.json | 讀寫資產 |
| 依ID取得電子郵件範本 | 電子郵件範本 | GET | /rest/asset/v1/emailTemplate/{id}.json | 唯讀資產 |
| 依名稱取得電子郵件範本 | 電子郵件範本 | GET | /rest/asset/v1/emailTemplate/byName.json | 唯讀資產 |
| 依ID取得電子郵件範本內容 | 電子郵件範本 | GET | /rest/asset/v1/emailTemplate/{id}/content.json | 唯讀資產 |
| 取得使用的電子郵件範本 | 電子郵件範本 | GET | /rest/asset/v1/emailTemplates/{id}/usedBy.json | 唯讀資產 |
| 取得電子郵件範本 | 電子郵件範本 | GET | /rest/asset/v1/emailTemplates.json | 唯讀資產 |
| 取消核准電子郵件範本草稿 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/unapprove.json | 讀寫資產 |
| 更新電子郵件範本內容 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}/content.json | 讀寫資產 |
| 更新電子郵件範本中繼資料 | 電子郵件範本 | POST | /rest/asset/v1/emailTemplate/{id}.json | 讀寫資產 |
| 新增電子郵件模組 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/add.json | 讀寫資產 |
| 核准電子郵件草稿 | 電子郵件 | POST | /rest/asset/v1/email/{id}/approveDraft.json | 讀寫資產 |
| 複製電子郵件 | 電子郵件 | POST | /rest/asset/v1/email/{id}/clone.json | 讀寫資產 |
| 建立電子郵件 | 電子郵件 | POST | /rest/asset/v1/emails.json | 讀寫資產 |
| 刪除電子郵件 | 電子郵件 | POST | /rest/asset/v1/email/{id}/delete.json | 讀寫資產 |
| 刪除模組 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/delete.json | 讀寫資產 |
| 捨棄電子郵件草稿 | 電子郵件 | POST | /rest/asset/v1/email/{id}/discardDraft.json | 讀寫資產 |
| 重複的電子郵件模組 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json | 讀寫資產 |
| 依ID取得電子郵件 | 電子郵件 | GET | /rest/asset/v1/email/{id}.json | 唯讀資產 |
| 依名稱取得電子郵件 | 電子郵件 | GET | /rest/asset/v1/email/byName.json | 唯讀資產 |
| 取得電子郵件內容 | 電子郵件 | GET | /rest/asset/v1/email/{id}/content.json | 唯讀資產 |
| 取得電子郵件動態內容 | 電子郵件 | GET | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | 唯讀資產 |
| 取得電子郵件完整內容 | 電子郵件 | GET | /rest/asset/v1/email/{id}/fullContent.json | 唯讀資產 |
| 取得電子郵件變數 | 電子郵件 | GET | /rest/asset/v1/email/{id}/variables.json | 唯讀資產 |
| 取得電子郵件副本欄位 | 電子郵件 | GET | /rest/asset/v1/email/ccFields.json | 唯讀資產 |
| 取得電子郵件 | 電子郵件 | GET | /rest/asset/v1/emails.json | 唯讀資產 |
| 重新排列電子郵件模組 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/rearrange.json | 讀寫資產 |
| 重新命名電子郵件模組 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/rename.json | 讀寫資產 |
| 傳送範例電子郵件 | 電子郵件 | POST | /rest/asset/v1/email/{id}/sendSample.json | 讀寫資產 |
| 取消核准電子郵件 | 電子郵件 | POST | /rest/asset/v1/email/{id}/unapprove.json | 讀寫資產 |
| 更新電子郵件內容 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content.json | 讀寫資產 |
| 更新電子郵件內容區段 | 電子郵件 | POST | /rest/asset/v1/email/{id}/content/{htmlId}.json | 讀寫資產 |
| 更新電子郵件動態內容區段 | 電子郵件 | POST | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | 讀寫資產 |
| 更新電子郵件完整內容 | 電子郵件 | POST | /rest/asset/v1/emails/{id}/fullContent.json | 讀寫資產 |
| 更新電子郵件中繼資料 | 電子郵件 | POST | /rest/asset/v1/email/{id}.json | 讀寫資產 |
| 更新電子郵件變數 | 電子郵件 | POST | /rest/asset/v1/email/{id}/variable/{name}.json | 讀寫資產 |
| 建立檔案 | 檔案 | POST | /rest/asset/v1/files.json | 讀寫資產 |
| 依ID取得檔案 | 檔案 | GET | /rest/asset/v1/file/{id}.json | 唯讀資產 |
| 依名稱取得檔案 | 檔案 | GET | /rest/asset/v1/file/byName.json | 唯讀資產 |
| 取得檔案 | 檔案 | GET | /rest/asset/v1/files.json | 唯讀資產 |
| 更新檔案內容 | 檔案 | POST | /rest/asset/v1/file/{id}/content.json | 讀寫資產 |
| 建立資料夾 | 資料夾 | POST | /rest/asset/v1/folders.json | 讀寫資產 |
| 刪除資料夾 | 資料夾 | POST | /rest/asset/v1/folder/{id}/delete.json | 讀寫資產 |
| 依ID取得資料夾 | 資料夾 | GET | /rest/asset/v1/folder/{id}.json | 唯讀資產 |
| 依名稱取得資料夾 | 資料夾 | GET | /rest/asset/v1/folder/byName.json | 唯讀資產 |
| 取得資料夾內容 | 資料夾 | GET | /rest/asset/v1/folder/{id}/content.json | 唯讀資產 |
| 取得資料夾 | 資料夾 | GET | /rest/asset/v1/folders.json | 唯讀資產 |
| 更新資料夾中繼資料 | 資料夾 | POST | /rest/asset/v1/folder/{id}.json | 讀寫資產 |
| 新增欄位至表單 | 表單欄位 | POST | /rest/asset/v1/form/{id}/fields.json | 讀寫資產 |
| 將欄位集新增至表單 | 表單欄位 | POST | /rest/asset/v1/form/{id}/fieldSet.json | 讀寫資產 |
| 新增表單欄位可見性規則 | 表單欄位 | POST | /rest/asset/v1/form/{formId}/field/{fieldId}/visibility.json | 讀寫資產 |
| 新增RTF文字欄位 | 表單欄位 | POST | /rest/asset/v1/form/{id}/richText.json | 讀寫資產 |
| 從欄位集中刪除欄位 | 表單欄位 | POST | /rest/asset/v1/form/{id}/fieldSet/{fieldSetId}/field/{fieldId}/delete.json | 讀寫資產 |
| 刪除表單欄位 | 表單欄位 | POST | /rest/asset/v1/form/{id}/field/{fieldId}/delete.json | 讀寫資產 |
| 取得可用的表單欄位 | 表單欄位 | GET | /rest/asset/v1/form/fields.json | 唯讀資產 |
| 取得可用的表單方案成員欄位 | 表單欄位 | GET | /rest/asset/v1/form/programMemberFields.json | 唯讀資產 |
| 取得表單欄位 | 表單欄位 | GET | /rest/asset/v1/form/{id}/fields.json | 唯讀資產 |
| 更新欄位位置 | 表單欄位 | POST | /rest/asset/v1/form/{id}/reArrange.json | 讀寫資產 |
| 更新表單欄位 | 表單欄位 | POST | /rest/asset/v1/form/{id}/field/{fieldId}.json | 讀寫資產 |
| 核准表單草稿 | Forms | POST | /rest/asset/v1/form/{id}/approveDraft.json | 讀寫資產 |
| 複製表單 | Forms | POST | /rest/asset/v1/form/{id}/clone.json | 讀寫資產 |
| 建立表單 | Forms | POST | /rest/asset/v1/forms.json | 讀寫資產 |
| 取得使用的表單 | Forms | GET | /rest/asset/v1/form/{id}/usedBy.json | 讀寫資產 |
| 刪除表單 | Forms | POST | /rest/asset/v1/form/{id}/delete.json | 讀寫資產 |
| 捨棄表單草稿 | Forms | POST | /rest/asset/v1/form/{id}/discardDraft.json | 讀寫資產 |
| 依ID取得表單 | Forms | GET | /rest/asset/v1/form/{id}.json | 唯讀資產 |
| 依名稱取得表單 | Forms | GET | /rest/asset/v1/form/byName.json | 唯讀資產 |
| 取得Forms | Forms | GET | /rest/asset/v1/forms.json | 唯讀資產 |
| 依表單ID取得感謝頁面 | Forms | GET | /rest/asset/v1/form/{id}/thankYouPage.json | 唯讀資產 |
| 更新表單中繼資料 | Forms | POST | /rest/asset/v1/form/{id}.json | 讀寫資產 |
| 更新提交按鈕 | Forms | POST | /rest/asset/v1/{id}/submitButton.json | 讀寫資產 |
| 更新感謝頁面 | Forms | POST | /rest/asset/v1/form/{id}/thankYouPage.json | 讀寫資產 |
| 新增登陸頁面內容區段 | 登陸頁面內容 | POST | /rest/asset/v1/landingPage/{id}/content.json | 讀寫資產 |
| 刪除登陸頁面內容區段 | 登陸頁面內容 | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}/delete.json | 讀寫資產 |
| 取得登陸頁面內容 | 登陸頁面內容 | GET | /rest/asset/v1/landingPage/{id}/content.json | 唯讀資產 |
| 取得登陸頁面動態內容 | 登陸頁面內容 | GET | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | 唯讀資產 |
| 更新登陸頁面內容區段 | 登陸頁面內容 | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}.json | 讀寫資產 |
| 更新登陸頁面動態內容區段 | 登陸頁面內容 | POST | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | 讀寫資產 |
| 核准登陸頁面範本草稿 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/approveDraft.json | 讀寫資產 |
| 原地複製登陸頁面範本 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/clone.json | 讀寫資產 |
| 建立登入頁面範本 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate.json | 讀寫資產 |
| 刪除登入頁面範本 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/delete.json | 讀寫資產 |
| 捨棄登入頁面範本草稿 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/discardDraft.json | 讀寫資產 |
| 依ID取得登陸頁面範本 | 登陸頁面範本 | GET | /rest/asset/v1/landingPageTemplate/{id}.json | 唯讀資產 |
| 依名稱取得登陸頁面範本 | 登陸頁面範本 | GET | /rest/asset/v1/landingPageTemplates/byName.json | 唯讀資產 |
| 取得登入頁面範本內容 | 登陸頁面範本 | GET | /rest/asset/v1/landingPageTemplate/{id}/content.json | 唯讀資產 |
| 取得登入頁面範本 | 登陸頁面範本 | GET | /rest/asset/v1/landingPageTemplates.json | 唯讀資產 |
| 取消核准登陸頁面範本 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/unapprove.json | 讀寫資產 |
| 更新登入頁面範本內容 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}/content.json | 讀寫資產 |
| 更新登入頁面範本中繼資料 | 登陸頁面範本 | POST | /rest/asset/v1/landingPageTemplate/{id}.json | 讀寫資產 |
| 核准登陸頁面草稿 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/approveDraft.json | 讀寫資產 |
| 原地複製登陸頁面 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/clone.json | 讀寫資產 |
| 建立登陸頁面 | 登陸頁面 | POST | /rest/asset/v1/landingPages.json | 讀寫資產 |
| 刪除登陸頁面 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/delete.json | 讀寫資產 |
| 捨棄登陸頁面草稿 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/discardDraft.json | 讀寫資產 |
| 依ID取得登陸頁面 | 登陸頁面 | GET | /rest/asset/v1/landingPage/{id}.json | 唯讀資產 |
| 依名稱取得登陸頁面 | 登陸頁面 | GET | /rest/asset/v1/landingPage/byName.json | 唯讀資產 |
| 取得登陸頁面變數 | 登陸頁面 | GET | /rest/asset/v1/landingPage/{id}/variables.json | 唯讀資產 |
| 取得登陸頁面 | 登陸頁面 | GET | /rest/asset/v1/landingPages.json | 唯讀資產 |
| 預覽登陸頁面 | 登陸頁面 | GET | /rest/asset/v1/landingPage/{id}/preview.json | 唯讀資產 |
| 取消核准登陸頁面 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/unapprove.json | 讀寫資產 |
| 更新登陸頁面中繼資料 | 登陸頁面 | POST | /rest/asset/v1/{id}.json | 讀寫資產 |
| 更新登陸頁面變數 | 登陸頁面 | POST | /rest/asset/v1/landingPage/{id}/variable/{variableId}.json | 讀寫資產 |
| 建立登陸頁面重新導向規則 | 登陸頁面 | POST | /rest/asset/v1/redirectRules.json | 讀寫重新導向規則 |
| 刪除登陸頁面重新導向規則 | 登陸頁面 | POST | /rest/asset/v1/redirectRule/{id}/delete.json | 讀寫重新導向規則 |
| 取得登陸頁面重新導向規則 | 登陸頁面 | GET | /rest/asset/v1/redirectRules.json | 唯讀重新導向規則 |
| 依ID取得登陸頁面重新導向規則 | 登陸頁面 | GET | /rest/asset/v1/redirectRule/{id}.json | 唯讀重新導向規則 |
| 更新登陸頁面重新導向規則 | 登陸頁面 | POST | /rest/asset/v1/redirectRule/{id}.json | 讀寫重新導向規則 |
| 取得登陸頁面網域 | 登陸頁面 | GET | /rest/asset/v1/landingPageDomains.json | 唯讀重新導向規則 |
| 關聯銷售機會 | 銷售機會 | POST | /rest/v1/leads/{id}/associate.json | 讀寫潛在客戶 |
| 變更潛在客戶計畫狀態 | 銷售機會 | POST | /rest/v1/leads/programs/{programId}/status.json | 讀寫潛在客戶 |
| 刪除銷售機會 | 銷售機會 | POST | /rest/v1/leads.json | 讀寫潛在客戶 |
| 描述銷售機會 | 銷售機會 | GET | /rest/v1/leads/describe.json | 唯讀銷售機會 |
| 說明銷售機會2 | 銷售機會 | GET | /rest/v1/leads/describe2.json | 唯讀銷售機會 |
| 描述方案成員 | 銷售機會 | GET | /rest/v1/program/members/describe.json | 唯讀銷售機會 |
| 依ID取得銷售機會 | 銷售機會 | GET | /rest/v1/lead/{id}.json | 唯讀銷售機會 |
| 取得潛在客戶分割 | 銷售機會 | GET | /rest/v1/leads/partitions.json | 唯讀銷售機會 |
| 依篩選器型別取得銷售機會 | 銷售機會 | GET | /rest/v1/leads.json | 唯讀銷售機會 |
| 依計畫ID取得銷售機會 | 銷售機會 | GET | /rest/v1/leads/programs/{programId}.json | 唯讀銷售機會 |
| 合併銷售機會 | 銷售機會 | POST | /rest/v1/leads/{id}/merge.json | 讀寫潛在客戶 |
| 依銷售機會ID取得清單 | 銷售機會 | GET | /rest/v1/leads/{leadId}.json | 唯讀資產 |
| 依銷售機會ID取得計畫 | 銷售機會 | GET | /rest/v1/leads/{leadId}programMembership.json | 唯讀資產 |
| 依銷售機會ID取得智慧行銷活動 | 銷售機會 | GET | /rest/v1/leads/{leadId}/smartCampaignMembership.json | 唯讀資產 |
| 將銷售機會推送至Marketo | 銷售機會 | POST | /rest/v1/leads/partitions.json | 讀寫潛在客戶 |
| 提交表單 | 銷售機會 | POST | /rest/v1/leads/submitForm.json | 讀寫潛在客戶 |
| 同步銷售機會 | 銷售機會 | POST | /rest/v1/leads.json | 讀寫潛在客戶 |
| 更新潛在客戶分割 | 銷售機會 | POST | /rest/v1/leads/partitions.json | 讀寫潛在客戶 |
| 依名稱取得銷售機會欄位 | 銷售機會 | GET | /rest/v1/leads/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 取得潛在客戶欄位 | 銷售機會 | GET | /rest/v1/leads/schema/fields.json | 讀寫結構描述自訂欄位 |
| 建立潛在客戶欄位 | 銷售機會 | POST | /rest/v1/leads/schema/fields.json | 讀寫結構描述自訂欄位 |
| 更新潛在客戶欄位 | 銷售機會 | POST | /rest/v1/leads/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 新增具名帳戶清單成員 | 具名帳戶清單 | POST | /rest/v1/namedaccountlist/{id}/namedaccounts.json | 讀寫具名帳戶 |
| 刪除具名帳戶清單 | 具名帳戶清單 | POST | /rest/v1/namedaccountlists/delete.json | 讀寫具名帳戶清單 |
| 取得具名帳戶清單成員 | 具名帳戶清單 | GET | /rest/v1/namedaccountlist/{id}/namedaccounts.json | 唯讀具名帳戶 |
| 取得具名帳戶清單 | 具名帳戶清單 | GET | /rest/v1/namedaccountlists.json | 唯讀具名帳戶清單 |
| 移除具名帳戶清單成員 | 具名帳戶清單 | POST | /rest/v1/namedaccountlist/{id}/namedaccounts/remove.json | 讀寫具名帳戶 |
| 同步具名帳戶清單 | 具名帳戶清單 | POST | /rest/v1/namedaccountlists.json | 讀寫具名帳戶清單 |
| 刪除具名帳戶 | 具名帳戶 | POST | /rest/v1/namedaccounts/delete.json | 讀寫具名帳戶 |
| 說明具名帳戶 | 具名帳戶 | GET | /rest/v1/namedaccounts/describe.json | 唯讀具名帳戶 |
| 取得具名帳戶 | 具名帳戶 | GET | /rest/v1/namedaccounts.json | 唯讀具名帳戶 |
| 同步已命名的帳戶 | 具名帳戶 | POST | /rest/v1/namedaccounts.json | 讀寫具名帳戶 |
| 依名稱取得具名帳戶欄位 | 具名帳戶 | GET | /rest/v1/namedaccounts/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 取得具名帳戶欄位 | 具名帳戶 | GET | /rest/v1/namedaccounts/schema/fields.json | 讀寫結構描述自訂欄位 |
| 刪除機會 | 機會 | POST | /rest/v1/opportunities/delete.json | 讀寫機會 |
| 刪除機會角色 | 機會 | POST | /rest/v1/opportunities/roles/delete.json | 讀寫機會 |
| 描述商機 | 機會 | GET | /rest/v1/opportunities/describe.json | 唯讀機會 |
| 描述機會角色 | 機會 | GET | /rest/v1/opportunities/roles/describe.json | 唯讀機會 |
| 取得機會 | 機會 | GET | /rest/v1/opportunities.json | 唯讀機會 |
| 取得機會角色 | 機會 | GET | /rest/v1/opportunities/roles.json | 唯讀機會 |
| 同步機會 | 機會 | POST | /rest/v1/opportunities.json | 讀寫機會 |
| 同步機會角色 | 機會 | POST | /rest/v1/opportunities/roles.json | 讀寫機會 |
| 依名稱取得機會欄位 | 機會 | GET | /rest/v1/opportunities/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 取得機會欄位 | 機會 | GET | /rest/v1/opportunities/schema/fields.json | 讀寫結構描述自訂欄位 |
| 刪除計畫成員 | 計畫成員 | POST | /rest/v1/programs/{programId}/members/delete.json | 讀寫潛在客戶 |
| 描述方案成員 | 計畫成員 | GET | /rest/v1/programs/members/describe.json | 唯讀銷售機會 |
| 取得計畫成員 | 計畫成員 | GET | /rest/v1/programs/{programId}/members.json | 唯讀銷售機會 |
| 同步程式成員資料 | 計畫成員 | POST | /rest/v1/programs/{programId}/members.json | 讀寫潛在客戶 |
| 同步程式成員狀態 | 計畫成員 | POST | /rest/v1/programs/{programId}/members/status.json | 讀寫潛在客戶 |
| 依名稱取得方案成員欄位 | 計畫成員 | GET | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 取得方案成員欄位 | 計畫成員 | GET | /rest/v1/programs/members/schema/fields.json | 讀寫結構描述自訂欄位 |
| 建立方案成員欄位 | 計畫成員 | POST | /rest/v1/programs/members/schema/fields.json | 讀寫結構描述自訂欄位 |
| 更新計畫成員欄位 | 計畫成員 | POST | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | 讀寫結構描述自訂欄位 |
| 核准計畫 | 計畫 | POST | /rest/asset/v1/program/{id}/approve.json | 讀寫資產 |
| 復製程式 | 計畫 | POST | /rest/asset/v1/program/{id}/clone.json | 讀寫資產 |
| 建立方案 | 計畫 | POST | /rest/asset/v1/programs.json | 讀寫資產 |
| 刪除程式 | 計畫 | POST | /rest/asset/v1/program/{id}/delete.json | 讀寫資產 |
| 依ID取得計畫 | 計畫 | GET | /rest/asset/v1/program/{id}.json | 唯讀資產 |
| 依名稱取得計畫 | 計畫 | GET | /rest/asset/v1/program/byName.json | 唯讀資產 |
| 取得計畫 | 計畫 | GET | /rest/asset/v1/programs.json | 唯讀資產 |
| 按標籤取得計畫 | 計畫 | GET | /rest/asset/v1/program/byTag.json | 唯讀資產 |
| 依程式ID取得智慧清單 | 計畫 | GET | /rest/asset/v1/program/{id}/smartList.json | 唯讀資產 |
| 取消核准計畫 | 計畫 | POST | /rest/asset/v1/program/{id}/unapprove.json | 讀寫資產 |
| 更新程式中繼資料 | 計畫 | POST | /rest/asset/v1/program/{id}.json | 讀寫資產 |
| 更新計畫標籤 | 計畫 | POST | /rest/asset/v1/program/{id}/tag/{tagType}.json | 讀寫資產 |
| 刪除計畫標籤 | 計畫 | POST | /rest/asset/v1/program/{id}/tag/{tagType}/delete.json | 讀寫資產 |
| 刪除SalesPerson | 銷售人員 | POST | /rest/v1/salespersons/delete.json | 讀寫銷售人員 |
| 描述SalesPerson | 銷售人員 | GET | /rest/v1/salespersons/describe.json | 唯讀銷售人員 |
| 取得SalesPerson | 銷售人員 | GET | /rest/v1/salespersons.json | 唯讀銷售人員 |
| 同步SalesPerson | 銷售人員 | POST | /rest/v1/salespersons.json | 讀寫銷售人員 |
| 取得區段 | 區段 | GET | /rest/asset/v1/segmentation.json | 唯讀資產 |
| 取得區段的區段 | 區段 | GET | /rest/asset/v1/segmentation/{id}/segments.json | 唯讀資產 |
| 啟動Smart Campaign | Smart Campaign | POST | /rest/asset/v1/smartCampaign/{id}/activate.json | 啟動行銷活動 |
| 仿製智慧型行銷活動 | Smart Campaign | POST | /rest/asset/v1/smartCampaign/{id}/clone.json | 讀寫資產 |
| 建立智慧型行銷活動 | Smart Campaign | POST | /rest/asset/v1/smartCampaigns.json | 讀寫資產 |
| 停用Smart Campaign | Smart Campaign | POST | /rest/asset/v1/smartCampaign/{id}/deactivate.json | 停用行銷活動 |
| 刪除Smart Campaign | Smart Campaign | POST | /rest/asset/v1/smartCampaign/{id}/delete.json | 讀寫資產 |
| 取得智慧行銷活動 | Smart Campaign | GET | /rest/asset/v1/smartCampaigns.json | 唯讀資產 |
| 依ID取得Smart Campaign | Smart Campaign | GET | /rest/asset/v1/smartCampaign/{id}.json | 唯讀資產 |
| 依名稱取得智慧行銷活動 | Smart Campaign | GET | /rest/asset/v1/smartCampaign/byName.json | 唯讀資產 |
| 依Smart Campaign ID取得智慧清單 | Smart Campaign | GET | /rest/asset/v1/smartCampaign/{id}/smartList.json | 唯讀資產 |
| 更新Smart Campaign | Smart Campaign | POST | /rest/asset/v1/smartCampaign/{id}.json | 讀寫資產 |
| 複製智慧清單 | 智慧清單 | POST | /rest/asset/v1/smartList/{id}/clone.json | 讀寫資產 |
| 刪除智慧清單 | 智慧清單 | POST | /rest/asset/v1/smartList/{id}/delete.json | 讀寫資產 |
| 依ID取得智慧清單 | 智慧清單 | GET | /rest/asset/v1/smartList/{id}.json | 唯讀資產 |
| 依名稱取得智慧清單 | 智慧清單 | GET | /rest/asset/v1/smartList/byName.json | 唯讀資產 |
| 取得智慧清單 | 智慧清單 | GET | /rest/asset/v1/smartLists.json | 唯讀資產 |
| 核准程式碼片段草稿 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/approveDraft.json | 讀寫資產 |
| 復製程式碼片段 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/clone.json | 讀寫資產 |
| 建立代碼片段 | 程式碼片段 | POST | /rest/asset/v1/snippets.json | 讀寫資產 |
| 刪除程式碼片段 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/delete.json | 讀寫資產 |
| 捨棄片段草稿 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/discardDraft.json | 讀寫資產 |
| 取得動態內容 | 程式碼片段 | GET | /rest/asset/v1/snippet/{id}/dynamicContent.json | 唯讀資產 |
| 依ID取得程式碼片段 | 程式碼片段 | GET | /rest/asset/v1/snippet/{id}.json | 唯讀資產 |
| 取得程式碼片段內容 | 程式碼片段 | GET | /rest/asset/v1/snippet/{id}/content.json | 唯讀資產 |
| 取得代碼片段 | 程式碼片段 | GET | /rest/asset/v1/snippets.json | 唯讀資產 |
| 取消核准程式碼片段 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/unapprove.json | 讀寫資產 |
| 更新程式碼片段內容 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/content.json | 讀寫資產 |
| 更新程式碼片段動態內容 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}/dynamicContent/{segmentId}.json | 讀寫資產 |
| 更新程式碼片段中繼資料 | 程式碼片段 | POST | /rest/asset/v1/snippet/{id}.json | 讀寫資產 |
| 新增至清單 | 靜態清單 | POST | /rest/v1/lists/{listId}/leads.json | 讀寫潛在客戶 |
| 建立靜態清單 | 靜態清單 | POST | /asset/v1/staticLists.json | 讀寫資產 |
| 刪除靜態清單 | 靜態清單 | POST | /asset/v1/staticList/{id}/delete.json | 讀寫資產 |
| 依清單ID取得銷售機會 | 靜態清單 | GET | /rest/v1/lists/{listId}/leads.json | 唯讀銷售機會 |
| 依ID取得清單 | 靜態清單 | GET | /rest/v1/lists/{id}.json | 唯讀銷售機會 |
| 取得清單 | 靜態清單 | GET | /rest/v1/lists.json | 唯讀銷售機會 |
| 依ID取得靜態清單 | 靜態清單 | GET | /asset/v1/staticList/{id}.json | 唯讀資產 |
| 依名稱取得靜態清單 | 靜態清單 | GET | /asset/v1/staticList/byName.json | 唯讀資產 |
| 取得靜態清單 | 靜態清單 | GET | /asset/v1/staticLists.json | 唯讀資產 |
| 清單成員 | 靜態清單 | GET | /rest/v1/lists/{listId}/leads/ismember.json | 唯讀銷售機會 |
| 從清單移除 | 靜態清單 | DELETE | /rest/v1/lists/{listId}/leads.json | 讀寫潛在客戶 |
| 更新靜態清單中繼資料 | 靜態清單 | POST | /asset/v1/staticList/{id}.json | 讀寫資產 |
| 依名稱取得標籤 | 標記 | GET | /rest/asset/v1/tagType/byName.json | 唯讀資產 |
| 取得標籤型別 | 標記 | GET | /rest/asset/v1/tagTypes.json | 唯讀資產 |
| 建立Token | Token | POST | /rest/asset/v1/folder/{id}/tokens.json | 讀寫資產 |
| 依名稱刪除Token | Token | POST | /rest/asset/v1/folder/{id}/tokens/delete.json | 讀寫資產 |
| 依資料夾ID取得權杖 | Token | GET | /rest/asset/v1/folder/{id}/tokens.json | 唯讀資產 |
| 新增角色 | User Management | POST | /userservice/management/v1/users/{userid}/roles/create.json | 存取使用者管理Api |
| 刪除受邀使用者 | User Management | POST | /userservice/management/v1/users/{userId}/invite/delete.json | 存取使用者管理Api |
| 刪除角色 | User Management | POST | /userservice/management/v1/users/{userid}/roles/delete.json | 存取使用者管理Api |
| 刪除使用者 | User Management | POST | /userservice/management/v1/users/{userId}/delete.json | 存取使用者管理Api |
| 依Id取得受邀使用者 | User Management | GET | /userservice/management/v1/users/{userid}/invite.json | 存取使用者管理Api |
| 取得角色 | User Management | GET | /userservice/management/v1/users/roles.json | 存取使用者管理Api |
| 依Id取得角色和工作區 | User Management | GET | /userservice/management/v1/users/{userid}/roles.json | 存取使用者管理Api |
| 取得使用者 | User Management | GET | /userservice/management/v1/users/allusers.json | 存取使用者管理Api |
| 依ID取得使用者 | User Management | GET | /userservice/management/v1/users/{userid}/user.json | 存取使用者管理Api |
| 取得工作區 | User Management | GET | /userservice/management/v1/users/workspaces.json | 存取使用者管理Api |
| 邀請使用者 | User Management | POST | /userservice/management/v1/users/invite.json | 存取使用者管理Api |
| 更新使用者屬性 | User Management | POST | /userservice/management/v1/users/{userId}/update.json | 存取使用者管理Api |