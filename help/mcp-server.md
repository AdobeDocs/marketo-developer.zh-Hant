---
title: MCP伺服器
description: 瞭解如何使用MCP伺服器將AI助理連線到Marketo。 使用您的Marketo憑證設定Claude Desktop、Cursor、Claude Code或VS Code。
hidefromtoc: true
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
source-git-commit: 3fe1c3e9fe572ef68d20ba10f93535aac9a98602
workflow-type: tm+mt
source-wordcount: '1278'
ht-degree: 1%

---

# [!DNL Marketo] MCP伺服器

模型上下文通訊協定(MCP)是一種開放標準，可讓AI工具與外部服務通訊。 [!DNL Marketo] MCP伺服器可作為您的AI助理與[!DNL Marketo]之間的橋樑。 它會公開超過100項操作，涵蓋表單、方案、智慧行銷活動、銷售機會、電子郵件、代碼片段、清單和資料夾。

當您的AI工具呼叫MCP伺服器時，伺服器會使用您在每個請求中提供的認證，代表您執行相對應的REST API呼叫。 您不需要安裝、部署或執行任何伺服器端軟體。

## 先決條件

- 已啟用REST API存取的[!DNL Marketo]執行個體
- 在[!DNL Marketo] LaunchPoint中建立API認證的管理員存取權
- 下列AI工具之一： Claude Desktop、Cursor、Claude Code (CLI)或具有GitHub Copilot的VS Code
- 網路存取MCP伺服器URL： `https://marketo-mcp.adobe.io/mcp`

## 取得Marketo認證

您需要[!DNL Marketo]執行個體的下列值：

- **使用者端識別碼**
- **使用者端密碼**
- **Munchkin帳戶ID**
- **REST API端點**

如果您已經擁有這些工具，請跳到[設定您的AI工具](#configure-your-ai-tool)。

### 使用者端ID和使用者端密碼

1. 前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。
1. 按一下您的API服務。 如果您沒有這類服務，請選取&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**，選擇&#x200B;**[!UICONTROL Custom]**&#x200B;做為服務型別，並指派專用的API使用者。
1. 按一下&#x200B;**[!UICONTROL View Details]**&#x200B;並複製&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**&#x200B;值。

### Munchkin帳戶ID

1. 前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Munchkin]**。
1. 複製&#x200B;**[!UICONTROL Munchkin Account ID]**。 格式為`XXX-XXX-XXX`，且符合執行個體URL的前置詞。

### REST API端點

1. 前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Web Services]**。
1. 在&#x200B;**[!UICONTROL REST API]**&#x200B;底下，複製&#x200B;**[!UICONTROL Endpoint]** URL。 格式為`https://XXX-XXX-XXX.mktorest.com`。

## 設定您的AI工具

每個AI工具都會從不同的位置讀取MCP伺服器組態。 在下方尋找您的工具，並依照步驟新增[!DNL Marketo] MCP伺服器。

### 克勞德案頭

組態檔為`claude_desktop_config.json`。 從下列其中一個位置開啟：

- **macOS**： `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**： `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux**： `~/.config/Claude/claude_desktop_config.json`

如果檔案已包含其他MCP伺服器，請在`mcpServers`下新增`marketo`專案。 下列範例顯示完整的`mcpServers`區塊：

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
      }
    }
  }
}
```

儲存檔案、結束Claude Desktop，然後重新開啟檔案。

### 游標

如果您的游標MCP組態已經包含其他伺服器，請在`mcpServers`下新增`marketo`專案。 下列範例顯示專案目錄中&#x200B;**[!UICONTROL Settings]** > **[!UICONTROL MCP]**&#x200B;或`.cursor/mcp.json`中的完整`mcpServers`區塊：

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
      }
    }
  }
}
```

重新啟動游標。

### 克勞德程式碼(CLI)

在終端機中執行以下命令，取代您的認證：

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "X-Marketo-Client-Id: YOUR-CLIENT-ID" \
  --header "X-Marketo-Client-Secret: YOUR-CLIENT-SECRET" \
  --header "X-Marketo-Munchkin-Id: YOUR-MUNCHKIN-ID"
```

### VS程式碼與GitHub Copilot

在macOS上按&#x200B;**[!UICONTROL Ctrl+Shift+P]**&#x200B;或&#x200B;**[!UICONTROL Cmd+Shift+P]**，然後選取&#x200B;**[!UICONTROL Preferences: Open User Settings (JSON)]**，以開啟您的VS程式碼`settings.json`。 新增下列範例：

```json
{
  "mcp": {
    "servers": {
      "marketo": {
        "type": "http",
        "url": "https://marketo-mcp.adobe.io/mcp",
        "headers": {
          "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
          "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
          "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
        }
      }
    }
  }
}
```

按&#x200B;**[!UICONTROL Ctrl+Shift+P]** （或macOS上的&#x200B;**[!UICONTROL Cmd+Shift+P]**），輸入&#x200B;**[!UICONTROL Reload Window]**，然後按Enter。

>[!NOTE]
>
>為安全起見，請在設定檔案中使用環境變數內插，而非直接貼上認證。 您可以使用如`${MARKETO_CLIENT_SECRET}`的語法參考變數，並在您的環境中設定它們。 這可防止認證以純文字形式儲存在可能認可至版本控制的檔案中。

## 可用的作業

連線之後，您可以要求AI助理執行下列類別的作業。

### 表單

瀏覽、建立、複製和核准表單。 新增或移除欄位、設定欄位可見性規則，並識別表單的內嵌位置。

提示範例：

- &quot;顯示所有已核准的表單&quot;
- 「將「聯絡我們」表單原地複製至Q2 Campaign資料夾」
- 「將公司欄位新增至示範申請表單」

### 智慧行銷活動

建立智慧行銷活動、設定智慧清單篩選器、新增流程步驟，以及啟用或停用行銷活動。

提示範例：

- 「哪些智慧型行銷活動目前有效？」
- 「在Operations資料夾中建立名為Lead Scoring Update的新智慧行銷活動」
- 「在歡迎電子郵件行銷活動中顯示流程步驟」

### 潛在客戶與清單

依電子郵件地址尋找銷售機會、建立或更新銷售機會記錄，以及管理靜態清單成員資格。

提示範例：

- 「透過電子郵件jane@example.com尋找銷售機會」
- 「將銷售機會ID 12345新增至第2季MQL清單」
- 「建立稱為夏季活動出席者的新靜態清單」

### 方案

建立、複製和標籤程式。 依型別、管道或日期範圍瀏覽計畫。

提示範例：

- 「將Q4網路研討會程式原地複製至2026年事件資料夾」
- 「在Campaigns資料夾中建立名為Summer Sale的新電子郵件程式」
- 「顯示所有標籤為網路研討會的計畫」

### 電子郵件和代碼片段

瀏覽電子郵件、從範本建立電子郵件、更新內容區段及管理可重複使用的程式碼片段。

提示範例：

- 「顯示所有草稿電子郵件」
- 「更新歡迎電子郵件的標題區段」
- 「哪些資產使用假日促銷活動代碼片段？」

### 例項結構

瀏覽資料夾、頻道、標籤型別和活動型別以瞭解您的[!DNL Marketo]設定。

提示範例：

- 「列出Marketo中的所有資料夾」
- 「顯示所有可用的頻道」
- 「已設定哪些標籤型別？」

### 大量作業

大量匯出潛在客戶資料，並檢查匯入或匯出工作狀態。

提示範例：

- 「針對過去30天內建立的潛在客戶建立大量匯出」
- 「檢查匯出作業xx的狀態」

## 疑難排解

| 錯誤 | 原因 | 修正 |
| ------- | ------- | ----- |
| 「未提供Marketo端點」 | 您的設定中缺少`X-Marketo-Endpoint`標頭。 | 重新檢查您的MCP設定，並確認所有四個標頭都存在。 |
| 「未提供Marketo認證」 | 遺失一或多個`X-Marketo-Client-Id`、`X-Marketo-Client-Secret`或`X-Marketo-Munchkin-Id`。 | 確認設定中出現全部四個標題。 |
| 「驗證錯誤」 | 您的認證無效或已過期。 | 在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**&#x200B;中重新檢查您的使用者端ID和使用者端密碼。 |
| 「403禁止存取」 | 您的Munchkin ID不在伺服器允許清單上。 | 請連絡您的[!DNL Marketo] MCP管理員以新增您的Munchkin ID。 |
| 連線逾時或已拒絕 | 無法從您的網路連線MCP伺服器。 | 確認您可以從您的環境連線到伺服器URL。 檢查VPN需求（如果適用）。 |
| 工具呼叫傳回空白結果 | API使用者缺少請求資產型別的許可權。 | 請要求您的[!DNL Marketo]管理員檢閱API使用者角色和許可權。 |

## 常見問題

### 我的資料安全嗎？

認證會以每個個別請求的HTTP標頭傳送。 伺服器不會在工作階段之間儲存或快取認證，而且每個要求都是完全隔離的。

### 多人可同時使用嗎？

是的。 伺服器是多租使用者。 每個使用者都使用自己的認證連線，而且要求會彼此隔離。

### 如果我的存取Token過期，會發生什麼事？

當您使用使用者端ID和使用者端密碼進行驗證時，伺服器會自動處理權杖重新整理。 您不需要採取任何動作。

### 我需要安裝或執行任何專案嗎？

否。 MCP伺服器由Adobe代管。 您只需要設定AI工具即可連線。

### 我的API使用者需要哪些[!DNL Marketo]許可權？

API使用者需要存取您要管理的資產型別。 至少為瀏覽操作指派一個唯讀角色，並為建立或修改資產指派一個讀寫角色。 與您的[!DNL Marketo]管理員合作以指派適當的許可權。

### 有哪些速率限制？

MCP伺服器繼承Marketo例項的API速率限制。 使用專用的API使用者來追蹤及管理配額消耗。

### 支援哪些AI工具？

Claude Desktop、Cursor、Claude程式碼(CLI)和搭配GitHub Copilot的VS程式碼。 任何透過HTTP支援模型內容通訊協定的AI工具都應可運作。

### 我可以連線到多個[!DNL Marketo]執行個體嗎？

是的。 在您的AI工具的MCP設定中新增多個專案，每個專案都有唯一名稱和對應執行個體的認證。 例如，您可以將`marketo-prod`和`marketo-staging`設定為個別的伺服器。

## 安全性考量

>[!IMPORTANT]
>
>在[!DNL Marketo]中使用只有您工作所需許可權的專用API使用者。 請勿重複使用管理員認證來存取API。

- **每個要求的認證。** 使用者端ID、使用者端密碼、Munchkin ID和REST API端點會隨每個請求以HTTP標頭傳輸。 伺服器不會儲存或快取它們。
- **多租使用者隔離。** 每個請求都使用自己的認證集。 您的資料不會與任何其他使用者的工作階段相交。
- **Munchkin ID允許清單。** 伺服器只接受已核准[!DNL Marketo]執行個體的要求。 使用未經授權的Munchkin ID的請求會以403錯誤遭到拒絕。
- **讓認證不受版本控制。** 如果您的AI工具支援，請使用環境變數內插(`${MARKETO_CLIENT_SECRET}`)，因此認證不會以純文字儲存在提交至存放庫的檔案中。
