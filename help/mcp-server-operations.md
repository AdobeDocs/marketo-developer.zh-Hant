---
title: Marketo Engage MCP作業
description: 瞭解哪些Marketo Engage MCP作業可搭配AI助理使用。
autotag-review: '2026-06-02T13:31:42.084Z'
TQID: 'https://experienceleague.adobe.com/qvrWbHOCsCCHctduNDxMhkE8JAKxZk8FCYfKvzxfcYA'
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: a7170d27-32ab-462b-a333-269abc654483id: b0bb9048-d951-48d8-8232-45cf248a7e27id: dca84292-69e9-4116-a575-667d31fa060did: e64968b2-4ee5-47f9-8cae-0588f184b9eb
topic_v2: id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
source-git-commit: c631b7c3d571f29083673f9b97d22230d109abfc
workflow-type: tm+mt
source-wordcount: 1228
ht-degree: 25%

---


# [!DNL Marketo Engage] MCP作業

下列作業可透過[!DNL Marketo Engage] MCP伺服器使用。 伺服器提供唯讀或非破壞性端點。 AI系統無法使用`Delete`或其他破壞性作業。

>[!NOTE]
>
>智慧清單和智慧行銷活動`create`和`update`工具預計於2026年9月發行。

如需有關如何使用Marketo AI和Marketo Engage MCP伺服器處理資料的資訊，請參閱[資料資訊](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-ai/data-information)頁面。

## 大量匯出

[大量匯出API參考](https://developer.adobe.com/marketo-apis/api/mapi){target="_blank"}

- `bulk_export_create`
- `bulk_export_enqueue`
- `bulk_export_file`
- `bulk_export_status`
- `get_import_status`

## 管道和標籤

[管道API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Channels){target="_blank"} | [標籤API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Tags){target="_blank"}

- `browse_channels`
- `browse_tag_types`
- `get_channel_by_name`
- `get_tag_type_by_name`

## 電子郵件

[電子郵件API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails){target="_blank"}

- `approve_email`
- `browse_emails`
- `create_email`
- `get_email_by_id`
- `get_email_by_name`
- `get_email_content`
- `update_email_content`

## 資料夾

[資料夾API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders){target="_blank"}

- `browse_folders`
- `create_folder`
- `delete_folder`
- `get_folder_by_id`
- `get_folder_by_name`
- `get_folder_content`
- `update_folder`

## 表單

[Forms API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms){target="_blank"}

- `add_field_set`
- `add_field_to_form`
- `add_field_visibility_rule`
- `add_rich_text_field`
- `approve_form`
- `browse_forms`
- `clone_form`
- `create_form`
- `delete_field_from_fieldset`
- `delete_form`
- `delete_form_field`
- `discard_form_draft`
- `get_form_by_id`
- `get_form_by_name`
- `get_form_field_metadata`
- `get_form_fields`
- `get_forms_used_by`
- `get_program_member_fields`
- `get_thank_you_page`
- `set_field_autofill`
- `update_field_positions`
- `update_form`
- `update_form_field`

## 銷售機會

[銷售機會API參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads){target="_blank"}

- `add_leads_to_list`
- `describe_lead`
- `get_activity_types`
- `get_lead_activities`
- `get_leads_by_filter`
- `get_leads_by_smart_list`
- `get_paging_token`

## 方案

[程式API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Programs){target="_blank"}

- `approve_program`
- `browse_email_batch_programs`
- `browse_nurture_programs`
- `browse_program_details`
- `browse_program_events`
- `browse_programs`
- `browse_scheduled_programs`
- `clone_program`
- `create_program`
- `delete_program_tag`
- `get_program_by_id`
- `get_program_by_name`
- `get_program_creation_options`
- `get_program_smart_list`
- `get_programs_by_tag`
- `unapprove_program`
- `update_program`
- `update_program_tag`

## 智慧行銷活動

[智慧行銷活動API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns){target="_blank"}

- `activate_smart_campaign`
- `add_flow_step`
- `browse_smart_campaigns`
- `create_smart_campaign`
- `facet_smart_campaigns`
- `get_smart_campaign_auto_suggest`
- `get_smart_campaign_by_id`
- `get_smart_campaign_by_name`
- `get_smart_campaign_flow_step_by_name`
- `get_smart_campaign_flow_step_type_by_name`
- `get_smart_campaign_flow_step_types`
- `get_smart_campaign_flow_steps`
- `get_smart_campaign_rule_by_name`
- `get_smart_campaign_rules`
- `get_smart_campaign_scheduled_runs`
- `get_smart_campaign_used_by`
- `get_smart_list_by_campaign_id`
- `schedule_campaign`
- `trigger_campaign`
- `update_flow_step_choice`
- `update_smart_campaign`

## 智慧清單

[智慧清單API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Lists){target="_blank"}

- `add_smart_list_rule`
- `browse_smart_lists`
- `clone_smart_list`
- `create_smart_list`
- `delete_all_smart_list_rules`
- `get_smart_list_auto_suggest`
- `get_smart_list_by_id`
- `get_smart_list_by_name`
- `get_smart_list_rule_by_name`
- `get_smart_list_rules`
- `get_smart_list_used_by`
- `remove_smart_list_rule_constraint`
- `reorder_smart_list_rules`
- `update_smart_list_filter_logic`
- `update_smart_list_rule`

## 程式碼片段

[程式碼片段API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Snippets){target="_blank"}

- `approve_snippet`
- `browse_snippets`
- `clone_snippet`
- `create_snippet`
- `delete_snippet`
- `discard_snippet_draft`
- `facet_snippets`
- `get_snippet_by_id`
- `get_snippet_content`
- `get_snippet_dynamic_content`
- `unapprove_snippet`
- `update_snippet`
- `update_snippet_content`
- `update_snippet_dynamic_content`

## 靜態清單

[靜態清單API參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists){target="_blank"}

- `browse_lists`
- `create_list`
- `get_list_by_id`
- `get_list_by_name`
- `get_list_members`
- `remove_from_list`
- `update_list`

## 權杖

[Token API參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens){target="_blank"}

- `create_calendar_token`
- `create_token`
- `delete_token`
- `get_calendar_tokens`
- `get_tokens_by_folder`

## 已啟用MCP流程步驟工具

<table style="table-layout:auto">
<tr>
<th>流程步驟</th>
<th>觸發程序</th>
<th>篩選器（活動）</th>
<th>篩選器（屬性）</th>
</tr>
<tr>
<td valign="top"><ul><li>新增到欄位集</li><li>新增至清單</li><li>新增到 Microsoft 行銷活動</li><li>新增至Nurture</li><li>新增至 SFDC 行銷活動</li><li>呼叫 Webhook</li><li>變更資料值</li><li>變更潛在客戶分割</li><li>變更Nurture步調</li><li>變更培養軌跡</li><li>變更所有者</li><li>在 Microsoft 中變更所有者</li><li>變更方案資料</li><li>變更方案成員資料</li><li>變更收入階段</li><li>變更分數</li><li>變更區段</li><li>進度中的變更狀態</li><li>在 SFDC 行銷活動中變更狀態</li><li>轉換潛在客戶</li><li>建立工作</li><li>在 Microsoft 中建立工作</li><li>刪除銷售機會</li><li>從Microsoft刪除銷售機會</li><li>從SFDC刪除銷售機會</li><li>執行行銷活動</li><li>關鍵時刻</li><li>從欄位集移除</li><li>從流程中移除</li><li>從清單中移除</li><li>從 Microsoft 行銷活動中移除</li><li>從 SFDC 行銷活動中移除</li><li>請求行銷活動</li><li>傳送警報</li><li>傳送電子郵件</li><li>將銷售機會同步至Microsoft</li><li>將銷售機會同步至SFDC</li><li>等待</li></ul></td>
<td valign="top"><ul><li>活動已記錄</li><li>活動已更新</li><li>已新增至清單</li><li>新增至Microsoft Campaign</li><li>新增至Nurture</li><li>新增至商機</li><li>已新增至機會（帳戶）</li><li>已新增至機會（連絡人）</li><li>新增至SFDC Campaign</li><li>在活動期間提出問題</li><li>出席活動</li><li>行銷活動已要求</li><li>點按連結</li><li>電子郵件中的點按連結</li><li>銷售電子郵件中的點按連結</li><li>SMS訊息中的點按連結</li><li>連結的點按次數</li><li>資料值變更</li><li>下載資產</li><li>電子郵件退回數</li><li>電子郵件軟退信</li><li>電子郵件已傳遞</li><li>使用對話流程</li><li>使用對話方塊</li><li>在交談流程中與代理程式互動</li><li>在對話方塊中與代理程式互動</li><li>填寫表單</li><li>有有趣的時刻</li><li>在對話流程中與檔案互動</li><li>在對話方塊中與檔案互動</li><li>已傳送銷售電子郵件</li><li>銷售機會已轉換</li><li>銷售機會已建立</li><li>銷售機會已從Microsoft中刪除</li><li>銷售機會已從SFDC中刪除</li><li>潛在客戶已推送至Marketo</li><li>銷售機會已同步至Microsoft</li><li>銷售機會已同步至SFDC</li><li>潛在客戶分割變更</li><li>手動階段變更</li><li>Nurture步調變更</li><li>Nurture追蹤變更</li><li>開啟電子郵件</li><li>開啟銷售電子郵件</li><li>機會（帳戶）已更新</li><li>機會（連絡人）已更新</li><li>機會已更新</li><li>所有者變更</li><li>Microsoft中的擁有者變更</li><li>程式成員資料已變更</li><li>進度狀態已變更</li><li>到達對話方塊目標</li><li>在對話流程中達到目標</li><li>已接收轉寄給朋友的電子郵件</li><li>已從清單移除</li><li>已從 Microsoft 行銷活動中移除</li><li>已從機會移除</li><li>從商機（帳戶）移除</li><li>從商機移除（連絡人）</li><li>已從SFDC Campaign中移除</li><li>回覆銷售電子郵件</li><li>回應投票</li><li>回覆問卷</li><li>收入階段已變更</li><li>銷售電子郵件退回數</li><li>已收到銷售電子郵件</li><li>以對話流程排程會議</li><li>排程對話方塊中的會議</li><li>分數已變更</li><li>區段變更</li><li>已傳送警報</li><li>轉寄給朋友的電子郵件</li><li>SMS訊息退信</li><li>SMS訊息已傳遞</li><li>SFDC Campaign中的狀態已變更</li><li>取消訂閱電子郵件</li><li>造訪網頁</li><li>已呼叫Webhook</li></ul></td>
<td valign="top"><ul><li>活動已記錄</li><li>活動已更新</li><li>已傳送警報</li><li>已執行行銷活動</li><li>已要求行銷活動</li><li>按一下連結</li><li>電子郵件中的點選連結</li><li>銷售電子郵件中的點選連結</li><li>SMS訊息中的點按連結</li><li>已點按連結</li><li>資料值已變更</li><li>已下載資產</li><li>電子郵件已退回</li><li>電子郵件已軟退回</li><li>已與對話流程互動</li><li>參與對話</li><li>在交談流程中與代理程式互動</li><li>在對話中與代理互動</li><li>已填寫的表單</li><li>曾有過關鍵時刻</li><li>已在事件期間提出問題</li><li>已參加事件</li><li>在交談流程中與檔案互動</li><li>在對話中與文件互動</li><li>潛在客戶分割已變更</li><li>潛在客戶已轉換</li><li>潛在客戶已建立</li><li>銷售機會已從Microsoft中刪除</li><li>銷售機會已從SFDC中刪除</li><li>潛在客戶已推送至Marketo</li><li>潛在客戶已同步至Microsoft</li><li>潛在客戶已同步至SFDC</li><li>Nurture步調已變更</li><li>Nurture追蹤已變更</li><li>已開啟的電子郵件</li><li>已開啟的銷售電子郵件</li><li>機會（帳戶）已更新</li><li>機會（連絡人）已更新</li><li>機會已更新</li><li>所有者已變更</li><li>已在Microsoft中變更擁有者</li><li>程式成員資料已變更</li><li>進度狀態已變更</li><li>達成對話目標</li><li>轉換流量中的達成目標</li><li>已接收轉寄給朋友的電子郵件</li><li>已回覆銷售電子郵件</li><li>已回覆問卷</li><li>已回覆一項調查</li><li>收入階段已變更</li><li>銷售電子郵件已退回</li><li>已收到銷售電子郵件</li><li>已排程的會議對話流程</li><li>在對話中安排會議</li><li>分數已變更</li><li>區段已變更</li><li>轉寄給朋友的電子郵件</li><li>SMS訊息已退回</li><li>已取消訂閱電子郵件</li><li>造訪的網頁</li><li>已新增至清單</li><li>已新增至Nurture</li><li>已新增至商機</li><li>已新增至機會（帳戶）</li><li>已新增至機會（連絡人）</li><li>已傳遞電子郵件</li><li>已傳遞簡訊訊息</li><li>已從清單中移除</li><li>已從機會中移除</li><li>已從機會（帳戶）移除</li><li>已從機會（連絡人）中移除</li><li>已傳送電子郵件</li><li>已傳送銷售電子郵件</li><li>已呼叫Webhook</li></ul></td>
<td valign="top"><ul><li>帳戶擁有者電子郵件地址</li><li>帳戶擁有者名字</li><li>帳戶擁有者姓氏</li><li>贏取日期</li><li>贏取方案</li><li>贏取計畫名稱</li><li>地址</li><li>年營業額</li><li>匿名IP</li><li>帳單寄送地址</li><li>帳單寄送城市</li><li>帳單國家</li><li>帳單郵遞區號</li><li>帳單州別</li><li>已加入封鎖清單</li><li>城市</li><li>公司 Microsoft 類型</li><li>公司名稱</li><li>國家/地區</li><li>建立時間</li><li>出生日期</li><li>部門</li><li>請勿來電</li><li>請勿來電的理由</li><li>重複欄位</li><li>電子郵件地址</li><li>電子郵件無效</li><li>電子郵件無效原因</li><li>電子郵件暫停</li><li>電子郵件暫停時間：</li><li>電子郵件暫停原因</li><li>傳真號碼</li><li>名字</li><li>全名</li><li>有機會</li><li>行業</li><li>推斷的城市</li><li>推斷公司</li><li>推斷國家</li><li>推斷的大都會區</li><li>推斷的電話區碼</li><li>推斷的郵遞區號</li><li>推斷的州別區域</li><li>是客戶</li><li>是合作夥伴</li><li>職稱</li><li>姓氏</li><li>潛在客戶擁有者電子郵件地址</li><li>潛在客戶擁有者名字</li><li>潛在客戶所有者職稱</li><li>潛在客戶擁有者姓氏</li><li>潛在客戶擁有者電話號碼</li><li>潛在客戶分割名稱</li><li>銷售線索評等</li><li>銷售線索分數</li><li>潛在客戶來源</li><li>潛在客戶狀態</li><li>主要電話</li><li>行銷活動暫停</li><li>欄位整合員</li><li>清單的成員</li><li>Nurture會員</li><li>方案成員</li><li>收入模式成員</li><li>收入階段的成員</li><li>SFDC 行銷活動的成員</li><li>智慧行銷活動的成員</li><li>智慧清單的成員</li><li>Microsoft 帳戶號碼</li><li>Microsoft 建立日期</li><li>Microsoft 已刪除</li><li>Microsoft 類型</li><li>中間名</li><li>手機號碼</li><li>附註</li><li>員工數</li><li>機會數量</li><li>原始反向連結</li><li>原始搜尋引擎</li><li>原始搜尋片語</li><li>原始來源資訊</li><li>原始來源類型</li><li>母公司名稱</li><li>人員時區</li><li>電話號碼</li><li>郵遞區號</li><li>隨機抽樣</li><li>註冊Source資訊</li><li>註冊Source型別</li><li>角色</li><li>稱謂</li><li>SFDC帳戶號碼</li><li>SFDC建立日期</li><li>SFDC 已刪除</li><li>SFDC 類型</li><li>標準產業分類（SIC）代碼</li><li>現場</li><li>狀態</li><li>機會數量總計</li><li>總機會預期收入</li><li>已取消訂閱</li><li>退訂原因</li><li>更新時間</li><li>網站</li></ul></td>
</tr>
</table>
