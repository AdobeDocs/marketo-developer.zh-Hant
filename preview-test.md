---
title: EXL預覽測試
description: 測試擴充功能預覽的Adobe EXL Markdown語法範例。
source-git-commit: 87d2584ed0ef2c1fa219f2a3ad120c91dc5491e0
workflow-type: tm+mt
source-wordcount: '348'
ht-degree: 11%

---


# EXL預覽測試

## 警示區塊

>[!NOTE]
>
>這是備註。 使用附註以取得讀者應該注意的補充資訊。

>[!TIP]
>
>此為秘訣。 使用秘訣取得可選但實用的資訊。

>[!IMPORTANT]
>
>此為重要警報。 用於讀取器不可忽略的資訊。

>[!WARNING]
>
>這是警告。 使用以取得潛在問題的相關資訊。

>[!CAUTION]
>
>這是警告。 用於取得潛在風險的相關資訊。

>[!ADMIN]
>
>這是管理員警報。 僅供管理員使用的內容。

>[!AVAILABILITY]
>
>這是可用性注意事項。 瞭解功能可用性詳細資訊。

>[!PREREQUISITES]
>
>這是必要條件區塊。 列出讀取器在啟動前所需的內容。

## 陰影方塊

>[!BEGINSHADEBOX 「選用標題」]

此內容會以灰色背景顯示。 使用陰影方塊以視覺化方式將相關內容分組。

您可以包含清單：

- 專案一
- 專案二
- 專案三

>[!ENDSHADEBOX]

>[!BEGINSHADEBOX]

沒有標題的陰影方塊。

>[!ENDSHADEBOX]

## 可摺疊區段

+++按一下以展開 — 基本範例

在使用者選取標題之前，此內容會一直隱藏。

您可以在此處包含任何內容，包括程式碼區塊：

```javascript
const example = 'hello world';
console.log(example);
```

+++

+++進階設定

針對選填或進階內容，請使用可摺疊區段，否則這些區段將會雜亂顯示主要流程。

| 設定 | 值 | 說明 |
| --- | --- | --- |
| timeout | 30 | 要求逾時前的秒數 |
| 重試 | 3 | 重試次數 |

{style="table-layout:auto"}

+++

## 內容說明

上下文說明在預覽中隱藏。 另請參閱！
>[!CONTEXTUALHELP]
>id="models_insights_undefinedchannels"
>title="未定義的管道"
>abstract="包含未定義的管道，但沒有歸因轉換。"

## 內嵌視訊

>[!VIDEO](https://video.tv.adobe.com/v/3427028/?quality=12&learn=on)

## 本地化巨集

使用[!DNL Marketo]將產品名稱換行，以免產品名稱本地化。

針對UI元素標籤使用&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。

結合範例：在[!DNL Adobe Analytics]中，選取&#x200B;**[!UICONTROL Workspace]** > **[!UICONTROL Create project]**。

## 徽章

[!BADGE Beta]{type=Informative}

[!BADGE 一般可用]{type=Positive}

[!BADGE 已棄用]{type=Negative}

[!BADGE 實驗性]{type=Caution}

## 索引標籤

>[!BEGINTABS]

>[!TAB 需求]

>[!IMPORTANT]
>
>您必須擁有管理員許可權才能完成此工作。

必填欄位：

| 欄位 | 類型 | 必要 |
| --- | --- | --- |
| 名稱 | 字串 | 是 |
| 電子郵件 | 字串 | 是 |
| 角色 | 列舉 | 無 |

>[!TAB 步驟]

1. 開啟Admin Console。
1. 選取&#x200B;**[!UICONTROL Users]** > **[!UICONTROL Add user]**。
1. 填寫必填欄位。
1. 選取「**[!UICONTROL Save]**」。

>[!NOTE]
>
>變更會立即生效。

>[!TAB 結果]

新使用者會收到一封歡迎電子郵件，其中包含設定其密碼的連結。

- 連結會在24小時後到期。
- 使用者可以從登入頁面請求新連結。

>[!ENDTABS]

## 程式碼片段

```json
{
  "name": "example",
  "version": "1.0.0",
  "enabled": true
}
```

```javascript
function greet(name) {
  return `Hello, ${name}!`;
}
```

## 表格

| 欄一 | 欄二 | 欄三 |
| --- | --- | --- |
| [!UICONTROL Row 1]，儲存格1 | 第1列，儲存格2 | [!DNL Row 1, cell 3] |
| 第2列，儲存格1 | 第2列，儲存格2 | 第2列，儲存格3 |

