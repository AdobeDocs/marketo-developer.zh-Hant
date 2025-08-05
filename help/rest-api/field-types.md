---
title: 欄位型別
feature: REST API
description: Marketo欄位型別清單
exl-id: a0ba9e02-ed42-4be3-9cdd-a97fee9a726e
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '307'
ht-degree: 8%

---

# 欄位型別

以下是Marketo中欄位型別的說明。 有關欄位型別的其他資訊可在[這裡](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/field-management/custom-field-type-glossary)找到。 有關欄位型別限制的其他資訊可在[這裡](https://nation.marketo.com/t5/knowledgebase/marketo-field-limits-by-field-type/ta-p/251613)找到。

| 欄位型別 | 說明 | 範例 |
| --- | --- | --- |
| 日期時間 | 用於輸入日期和時間。 遵循[W3C格式](https://www.w3.org/TR/NOTE-datetime) (ISO 8601)。 最佳作法是包含時區位移。 完成日期加上小時和分鐘： YYYY-MM-DDThh:mm:ssTZD，其中TZD為「+hh:mm」或「 — hh:mm」注意：某些資產API會針對`updatedAt`和`createdAt`傳回「Z+0000」作為TZD。 | 2010-05-07T15:41:32-05:00 |
| 電子郵件 | 接受電子郵件地址的字串欄位 | <example@example.com> |
| 浮點數 | 包含實數且可以使用小數位的數字欄位。 | 10.4 |
| 整數 | 整數 | 10 |
| 公式 | 透過操控潛在客戶記錄上呈現之其他欄位的資料而產生值的欄位。 它們不會匯出，且無法用於Smart Campaigns。 | 檢視此[文章](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/field-management/create-and-use-a-concatenated-string-formula-field) |
| 百分比 | 以整數表示的百分比 | 30 |
| URL | 限制輸入至URL （包括URL的通訊協定）的文字欄位。 | <http://example.com/> |
| 電話 | 電話號碼 | 111-111-1111 |
| 文字區域 | 較長的文字。 | 最多可支援30,000個位元組。 標準ASCII字元每個字元使用1個位元組（最多允許30,000個字元）。 Unicode字元每個字元最多可使用4個位元組(減少  字元數允許少於30,000個字元)。 |
| 字串 | 較短的文字 | 文字最多255個字元 |
| 分數 | 可使用「變更分數」流程步驟操作的整數欄位 | 10 |
| 布林值（舊稱Checkbox） | 允許使用者選取True （核取）或False （未核取）值。 | True |
| 貨幣 | 浮點欄位，代表為Marketo訂閱選取的預設貨幣型別 | 10.40 |
| 日期 | 用於日期。 遵循W3C格式。 | 2010-05-07 |
| 參考 | 字串欄位，其中包含其他記錄的索引鍵（外部索引鍵）。 | 聯絡人公司 |
