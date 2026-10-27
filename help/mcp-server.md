---
title: Marketo Engage MCP 伺服器
description: 瞭解如何使用Marketo Engage MCP伺服器將AI助理連線到Marketo。 使用您的Marketo憑證設定Claude Desktop、Cursor、Claude Code或VS Code。
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
autotag-review: '2026-06-02T13:31:15.329Z'
TQID: 'https://experienceleague.adobe.com/PJJm7yv8HmbwMB2fsnfDCXs8zprDJK5Q5z2uiiCJRZI'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
    internal-label: Marketo Engage
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
    internal-label: Smart Campaigns
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
    internal-label: Forms
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
    internal-label: Integrations
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
    internal-label: Configuration
  - id: c2dbad80-0f5c-4d96-a798-2a65f93b8721
    internal-label: Assets
  - id: dca84292-69e9-4116-a575-667d31fa060d
    internal-label: APIs
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
    internal-label: Implementation
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
    internal-label: Programs
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
    internal-label: Templates
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
    internal-label: Developer
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
    internal-label: Artificial intelligence
source-git-commit: 1cdb28422994b772a0fa430e213facaf26817952
workflow-type: tm+mt
source-wordcount: '2556'
ht-degree: 0%
---

# [!DNL Marketo Engage] MCP伺服器

模型上下文通訊協定(MCP)是一種將AI工具連線到外部服務的開放標準。 [!DNL Marketo] MCP伺服器會將您的AI助理連線到[!DNL Marketo]。 支援資產管理、潛在客戶資料和行銷活動自動化的作業。 請參閱[支援的MCP作業](mcp-server-operations.md)，瞭解例外狀況與可用性需求。

當您的AI工具呼叫MCP伺服器時，伺服器會使用該請求中的憑證來執行相對應的REST API呼叫。 您不需要安裝、部署或執行伺服器端軟體。

如需有關如何使用Marketo AI和Marketo Engage MCP伺服器處理資料的詳細資訊，請參閱[資料資訊](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-ai/data-information)頁面。

>[!IMPORTANT]
>
>模型內容通訊協定(MCP)是一種新興的開放原始碼標準，可能會帶來安全性或可靠性風險。 Adobe MCP伺服器整合和相關檔案係依「現況」提供，不提供任何形式的保證。
>將MCP使用者端或伺服器連線至Adobe產品是客戶選擇的組態，客戶需負責評估任何MCP整合的安全性和適用性。 Adobe對於因設定錯誤、誤用MCP、協力廠商實作中的漏洞，或透過啟用MCP的工作流程執行的意外動作所引起的問題，概不負責。
>為了降低風險，Adobe鼓勵您在有效使用之前在沙箱環境中測試整合，並在確認或依賴之前，仔細檢閱及驗證所有MCP啟動的動作和回應。

## MCP基本需知

>將MCP想成是AI應用程式的USB-C連線埠。 USB-C提供標準化方式，將裝置連線至各種周邊裝置和配件，而MCP提供標準化方式，將AI模型連線至資料來源和工具。 — [模型內容通訊協定](https://modelcontextprotocol.io/docs/getting-started/intro){target="_blank"}

MCP可讓AI工具同時連線至多項外部服務。 例如，AI助理可以：

* 連線至文書處理器，以產生AI輔助檔案
* 連線至動畫工具（例如Blender），以建立視覺效果
* 連線至Adobe After Effects以進行視訊編輯

任何應用程式都可以實作MCP，以公開資料和動作給AI工具。

## [!DNL Marketo Engage] MCP的功用和不功用

在連線AI工具之前，瞭解MCP的範圍有助於設定期望。

**MCP可以：**

* 透過標準REST API提供對[!DNL Marketo]資料和功能的存取
* 使用您在每個請求中提供的認證，代表您執行API呼叫
* 支援多個同時使用者，每個使用者都透過自己的憑證連線
* 自動處理OAuth權杖重新整理。 您不需要管理權杖有效期
* 在租使用者隔離的環境中操作，因此您的資料絕不會與另一個使用者的工作階段相交

**MCP不是：**

* 使用、託管或執行任何AI或機器學習模型。 所有AI處理作業都會在AI工具中進行，而非MCP
* 訓練任何資料或從中學習，包括您的客戶資料
* 產生預測、建議或決定。 決策是下遊人工智慧工具或使用者的責任
* 儲存或保留要求之間的認證、要求資料或工作階段狀態
* 需要您安裝、部署或管理任何伺服器端軟體

MCP可能會傳輸資料，包括可能的敏感欄位，視API使用方式而定，但B2B資料涉及客戶業務資料，不涉及PII資料。

## 先決條件

* 已啟用REST API存取的[!DNL Marketo]執行個體
* 在[!DNL Marketo] LaunchPoint中建立API認證的管理員存取權
* 網路存取MCP伺服器URL： `https://marketo-mcp.adobe.io/mcp`

## 取得Marketo認證

您需要[!DNL Marketo]執行個體的下列值：

* **使用者端識別碼**
* **使用者端密碼**
* **Munchkin帳戶ID**

如果您已經擁有這些工具，請跳到[設定您的AI工具](#configure-your-ai-tool)。

### 使用者端ID和使用者端密碼

1. 前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。
1. 選取您的API服務。 如果您沒有這類服務，請選取&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**，選擇&#x200B;**[!UICONTROL Custom]**&#x200B;做為服務型別，並指派專用的API使用者。
1. 選取&#x200B;**[!UICONTROL View Details]**&#x200B;並複製&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**&#x200B;值。

### Munchkin帳戶ID

1. 前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Munchkin]**。
1. 複製&#x200B;**[!UICONTROL Munchkin Account ID]**。 格式為`XXX-XXX-XXX`，且符合執行個體URL的前置詞。

## 設定您的AI工具

設定會依工具而略有不同。 本節提供常用工具的連線範例。

>[!TIP]
>
>若要連線到多個[!DNL Marketo]執行個體，請在您的MCP設定中新增具有唯一名稱的個別專案： `marketo-prod`和`marketo-staging`，每個專案都有對應的認證。

>[!BEGINTABS]

>[!TAB 克勞德案頭]

### 克勞德案頭 {#claude-desktop}

若要連線到Claude Desktop，請下載[marketo-mcp-bridge.zip](assets/marketo-mcp-bridge.zip)並將其解壓縮。 將`marketo-mcp-bridge.mjs`置於已知位置，並在下列組態中使用其絕對路徑。

您還需要在電腦上安裝支援的LTS版本Node.js。 橋接器不需要額外的npm套件。

1. 開啟Claude Desktop。
1. 開啟`claude_desktop_config.json`。 在macOS上，檔案在`~/Library/Application Support/Claude/`中。 在Windows上，它在`%APPDATA%\Claude\`中。 在開發人員設定功能表的版本中，選取「**[!UICONTROL Settings]** > **[!UICONTROL Developer]** > **[!UICONTROL Edit Config]**」以開啟。
1. 在`mcpServers`下新增以下專案，保留任何現有的設定。 將路徑和認證預留位置取代為您的值：

```json
{
  "mcpServers": {
    "marketo-mcp": {
      "command": "node",
      "args": ["/path/to/marketo-bridge/marketo-mcp-bridge.mjs"],
      "env": {
        "MARKETO_MCP_PROD_CLIENT_ID": "<your-client-id>",
        "MARKETO_MCP_PROD_CLIENT_SECRET": "<your-client-secret>",
        "MARKETO_MCP_PROD_MUNCHKIN_ID": "<your-munchkin-id>"
      }
    }
  }
}
```

1. 重新啟動Claude Desktop。

如果Claude Desktop找不到Node.js，請將`command`中的`node`取代為Node.js可執行檔的絕對路徑。 如需詳細資訊，請參閱[連線到本機MCP伺服器](https://modelcontextprotocol.io/docs/develop/connect-local-servers){target="_blank"}。

>[!TAB 游標]

### 游標 {#cursor}

在`~/.cursor/mcp.json`的`mcpServers`下為您的使用者帳戶或專案目錄中的`.cursor/mcp.json`新增`marketo`專案。 保留任何現有的伺服器，並將認證預留位置取代為您的值：

```json
{
  "mcpServers": {
    "marketo": {
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

在游標中開啟&#x200B;**[!UICONTROL Customize]**&#x200B;並確認伺服器已開啟。 如需詳細資訊，請參閱[Cursor MCP組態](https://cursor.com/docs/context/mcp){target="_blank"}。

>[!TAB 克勞德程式碼CLI]

### 克勞德程式碼(CLI) {#claude-code}

在終端機中執行以下命令，取代您的認證：

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "X-Marketo-Client-Id: YOUR-CLIENT-ID" \
  --header "X-Marketo-Client-Secret: YOUR-CLIENT-SECRET" \
  --header "X-Marketo-Munchkin-Id: YOUR-MUNCHKIN-ID"
```

該命令會為目前的專案新增伺服器和您的使用者帳戶。 若要使其可在多個專案中使用，請新增`--scope user`。 在Claude程式碼中，執行`/mcp`以檢查連線。 如需詳細資訊，請參閱[克勞德程式碼MCP組態](https://code.claude.com/docs/en/mcp){target="_blank"}。

>[!TAB OpenAI程式碼]

### OpenAI程式碼 {#codex}

將下列專案新增至`~/.codex/config.toml`，並保留任何現有的組態：

```toml
[mcp_servers.marketo]
url = "https://marketo-mcp.adobe.io/mcp"

[mcp_servers.marketo.env_http_headers]
X-Marketo-Client-Id = "MARKETO_CLIENT_ID"
X-Marketo-Client-Secret = "MARKETO_CLIENT_SECRET"
X-Marketo-Munchkin-Id = "MARKETO_MUNCHKIN_ID"
```

在執行程式碼處理序的環境中設定`MARKETO_CLIENT_ID`、`MARKETO_CLIENT_SECRET`和`MARKETO_MUNCHKIN_ID`。 `env_http_headers`中的值是環境變數名稱，不是認證。

開始新的Codex工作階段並執行`/mcp`以檢查連線。 如需詳細資訊，請參閱[Codex MCP組態](https://developers.openai.com/codex/mcp){target="_blank"}。

>[!TAB Gemini CLI]

### Gemini CLI

若要將[!DNL Marketo Engage] MCP伺服器新增至Gemini CLI，請在您的專案目錄中將下列`mcpServers`專案新增至`.gemini/settings.json`或為您的使用者帳戶新增至`~/.gemini/settings.json`。 保留任何現有的設定，並以您的值取代認證預留位置。

```json
{
  "mcpServers": {
    "marketo": {
      "httpUrl": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
      }
    }
  }
}
```

或者，在您的殼層環境中設定`MARKETO_CLIENT_ID`、`MARKETO_CLIENT_SECRET`和`MARKETO_MUNCHKIN_ID`，然後執行下列命令。 殼層會在CLI將標題值儲存於您的使用者設定中之前展開變數：

```bash
gemini mcp add --transport http -s user marketo https://marketo-mcp.adobe.io/mcp \
  -H "X-Marketo-Client-Id: $MARKETO_CLIENT_ID" \
  -H "X-Marketo-Client-Secret: $MARKETO_CLIENT_SECRET" \
  -H "X-Marketo-Munchkin-Id: $MARKETO_MUNCHKIN_ID"
```

重新啟動工作階段並執行`/mcp`以檢查連線。 如需詳細資訊，請參閱[Gemini CLI MCP組態](https://geminicli.com/docs/tools/mcp-server/){target="_blank"}。

>[!TAB 使用GitHub Copilot的 VS程式碼]

### VS程式碼與GitHub Copilot {#vscode}

使用可攜式Copilot設定以取得最新版本的VS Code。 在`~/.copilot/mcp-config.json`中的`mcpServers`下新增下列專案，或如果您設定`COPILOT_HOME`，則在`$COPILOT_HOME/mcp-config.json`下新增專案。 保留任何現有的伺服器：

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "${MARKETO_CLIENT_ID}",
        "X-Marketo-Client-Secret": "${MARKETO_CLIENT_SECRET}",
        "X-Marketo-Munchkin-Id": "${MARKETO_MUNCHKIN_ID}"
      }
    }
  }
}
```

在執行VS Code或連線至伺服器的遠端主機的環境中設定三個環境變數。 啟動新的Copilot工作階段並檢閱任何伺服器信任提示。

若要改用引導式安裝，請執行&#x200B;**[!UICONTROL MCP: Add Server]**，選取&#x200B;**[!UICONTROL HTTP (HTTP or Server-Sent Events)]**，輸入伺服器URL，然後選擇&#x200B;**[!UICONTROL Global]** > **[!UICONTROL Copilot Global]**。 然後將標題新增到儲存的設定。

較舊版本也支援&#x200B;**[!UICONTROL MCP: Open User Configuration]**&#x200B;或`.vscode/mcp.json`。 這些檔案使用`servers`而不是環境變數的`mcpServers`和`${env:MARKETO_CLIENT_ID}`語法。 請勿混用這兩種格式。 如需詳細資訊，請參閱[VS程式碼MCP組態](https://code.visualstudio.com/docs/agents/reference/mcp-configuration){target="_blank"}。

>[!TAB Copilot Studio]

### Copilot Studio

在新增MCP工具之前，開啟代理程式的產生式協調流程。 使用自訂聯結器來定義所有三個必要的[!DNL Marketo]標題。

在您的代理程式的&#x200B;**[!UICONTROL Tools]**&#x200B;頁面上，選取&#x200B;**[!UICONTROL Add a tool]** > **[!UICONTROL New tool]** > **[!UICONTROL Custom connector]**。 在Power Apps中，建立自訂聯結器並使用&#x200B;**[!UICONTROL Swagger editor]**&#x200B;輸入下列定義：

```yaml
swagger: '2.0'
info:
  title: Marketo MCP Server
  description: Connect to the Marketo MCP server.
  version: 1.0.0
host: marketo-mcp.adobe.io
basePath: /
schemes:
  - https
paths:
  /mcp:
    post:
      summary: Marketo MCP Server
      description: Invoke the Marketo MCP server.
      operationId: InvokeServer
      x-ms-agentic-protocol: mcp-streamable-1.0
      parameters:
        - name: X-Marketo-Client-Id
          in: header
          description: Client ID.
          type: string
          required: true
        - name: X-Marketo-Client-Secret
          in: header
          description: Client secret.
          type: string
          required: true
        - name: X-Marketo-Munchkin-Id
          in: header
          description: Munchkin ID.
          type: string
          required: true
      responses:
        '200':
          description: Immediate Response
securityDefinitions: {}
security: []
```

儲存或更新聯結器，返回Copilot Studio，然後將其新增到您的代理程式。 建立連線，並在出現提示時提供三個標頭值。 將認證保留在OpenAPI定義和代理程式指示之外。 如需詳細資訊，請參閱[將代理程式連線到現有的MCP伺服器](https://learn.microsoft.com/en-us/microsoft-copilot-studio/mcp-add-existing-server-to-agent){target="_blank"}。

>[!TAB Glean]

### Grean {#glean}

若要將Glean連線到[!DNL Marketo Engage] MCP伺服器，請依照[Glean的遠端MCP設定指示](https://docs.glean.com/administration/tools/connect-remote-mcp-servers-to-glean){target="_blank"}新增`https://marketo-mcp.adobe.io/mcp`。 請先檢查&#x200B;**[!UICONTROL Connectors]**&#x200B;目錄。 若為自訂或未列出的伺服器，請使用&#x200B;**[!UICONTROL Admin console]** > **[!UICONTROL Platform]** > **[!UICONTROL Tools]** > **[!UICONTROL Import tools from MCP server]**。

[Glean支援團隊必須在下方設定自訂標頭](https://docs.glean.com/administration/tools/mcp-custom-headers){target="_blank"}。 在您的支援請求中包含伺服器名稱、URL和必要的標頭名稱，並協調認證值的安全處理。 僅標準API金鑰連線無法提供所有三個標頭。

| 標頭 | 值 |
| ------ | ----- |
| `X-Marketo-Client-Id` | 您的使用者端ID |
| `X-Marketo-Client-Secret` | 您的使用者端密碼 |
| `X-Marketo-Munchkin-Id` | 您的Munchkin帳戶ID |

在Glean確認標題作用中後，請先以唯讀請求測試連線，再將工具提供給其他使用者使用。

>[!TAB 其他工具]

### 其他工具 {#other-tools}

Adobe主控[!DNL Marketo] MCP伺服器，並在公用URL中公開。 任何透過可串流的HTTP傳輸支援遠端伺服器的MCP使用者端都可以連線到它。
支援直接遠端HTTP連線的使用者端不需要橋接器或本機安裝的伺服器軟體。 如果您的工具未在上方列出，請使用下方的連線詳細資料來手動設定。

**連線詳細資料：**

| 設定 | 值 |
| ------- | ----- |
| 傳輸 | HTTP （可串流HTTP） |
| 伺服器URL | `https://marketo-mcp.adobe.io/mcp` |

**驗證標頭：**

連同每個請求一起傳送全部三個標頭。 您在其中輸入伺服器URL和標題取決於您的工具，因此請參閱其MCP檔案。

| 標頭 | 值 |
| ------ | ----- |
| `X-Marketo-Client-Id` | 您的使用者端ID |
| `X-Marketo-Client-Secret` | 您的使用者端密碼 |
| `X-Marketo-Munchkin-Id` | 您的Munchkin帳戶ID |

如果您的工具接受JSON設定，請從[Cursor](#cursor)或[VS Code](#vscode)範例開始，並調整金鑰和環境變數語法以符合您工具的結構描述。

>[!ENDTABS]

連線之後，請要求您的AI助理使用[!DNL Marketo] MCP伺服器列出可用的通道，而不變更任何資料。 檢查工具呼叫及其回應，確認要求使用預期的伺服器，並在沒有連線或驗證錯誤的情況下傳回資料。

## 可用的作業

連線之後，您可以要求AI助理執行下列類別的作業。 如需例外狀況與可用性需求，請參閱[支援的MCP作業](mcp-server-operations.md)。

### 表單

瀏覽、建立、複製和核准表單。 新增或移除欄位、設定欄位可見性規則，並識別表單的內嵌位置。

提示範例：

* &quot;顯示所有已核准的表單&quot;
* 「將「聯絡我們」表單原地複製至Q2 Campaign資料夾」
* 「將公司欄位新增至示範申請表單」

### 智慧行銷活動

建立智慧行銷活動、設定智慧清單篩選器、新增流程步驟，以及啟用或停用行銷活動。

提示範例：

* 「哪些智慧型行銷活動目前有效？」
* 「在Operations資料夾中建立名為Lead Scoring Update的新智慧行銷活動」
* 「在歡迎電子郵件行銷活動中顯示流程步驟」

### 潛在客戶與清單

依電子郵件地址尋找銷售機會、建立銷售機會記錄，以及管理靜態清單成員資格。

提示範例：

* 「透過電子郵件jane@example.com尋找銷售機會」
* 「將銷售機會ID 12345新增至第2季MQL清單」
* 「建立稱為夏季活動出席者的新靜態清單」

### 方案

建立、複製和標籤程式。 依型別、管道或日期範圍瀏覽計畫。

提示範例：

* 「將Q4網路研討會程式原地複製至2026年事件資料夾」
* 「在Campaigns資料夾中建立名為Summer Sale的新電子郵件程式」
* 「顯示所有標籤為網路研討會的計畫」

### 電子郵件和代碼片段

瀏覽電子郵件、從範本建立電子郵件、更新內容區段及管理可重複使用的程式碼片段。

提示範例：

* 「顯示所有草稿電子郵件」
* 「更新歡迎電子郵件的標題區段」
* 「哪些資產使用假日促銷活動代碼片段？」

### 例項結構

若要瞭解您的[!DNL Marketo]設定，請瀏覽資料夾、頻道、標籤型別和活動型別。

提示範例：

* 「列出Marketo中的所有資料夾」
* 「顯示所有可用的頻道」
* 「已設定哪些標籤型別？」

### 大量作業

大量匯出潛在客戶資料，並檢查匯入或匯出工作狀態。

提示範例：

* 「針對過去30天內建立的潛在客戶建立大量匯出」
* 「檢查匯出作業xx的狀態」

## 疑難排解

| 錯誤 | 原因 | 修正 |
| ------- | ------- | ----- |
| 「未提供Marketo認證」 | 遺失一或多個`X-Marketo-Client-Id`、`X-Marketo-Client-Secret`或`X-Marketo-Munchkin-Id`。 | 確認您的設定中存在所有Marketo使用者端認證標頭。 |
| 「401未獲授權」 | 您的認證遺失、無效或過期。 若使用Marketo使用者端憑證，使用者端ID或使用者端密碼不正確。 若使用IMS權杖，該權杖無效或過期。 | 驗證驗證方法的認證。 若要取得使用者端認證，請在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**&#x200B;中重新檢查&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**。 針對IMS權杖，產生新權杖並更新`Authorization`標頭。 |
| 「403禁止存取」 | 您的認證有效，但您的[!DNL Marketo]執行個體未啟用MCP存取。 | 請連絡您的[!DNL Marketo] MCP管理員，為您的Munchkin帳戶ID啟用MCP存取權。 |
| 「請求太多」（速率限制） | 您在短時間內傳送過多請求，或同時傳送過多請求，且已達您的[!DNL Marketo]執行個體的API限制。 | 減少您一次傳送的頻率與要求數，並在重試之前等候一小段時間。 使用專用的API使用者來追蹤及管理您的配額。 |
| 連線逾時或已拒絕 | 無法從您的網路連線MCP伺服器。 | 確認您可以從您的環境連線到伺服器URL。 檢查VPN需求（如果適用）。 |
| 工具呼叫傳回空白結果 | API使用者缺少請求資產型別的許可權。 | 請要求您的[!DNL Marketo]管理員檢閱API使用者角色和許可權。 |

## 安全性考量

>[!IMPORTANT]
>
>在[!DNL Marketo]中使用只有您工作所需許可權的專用API使用者。 請勿重複使用管理員認證來存取API。

* **每個要求的認證。** 使用者端ID、使用者端密碼、Munchkin ID和REST API端點會隨每個請求以HTTP標頭傳輸。 伺服器不會儲存或快取它們。
* **多租使用者隔離。** 每個請求都使用自己的認證集。 您的資料不會與任何其他使用者的工作階段相交。
* **Munchkin ID允許清單。** 伺服器只接受已核准[!DNL Marketo]執行個體的要求。 使用未經授權的Munchkin ID的請求會以403錯誤遭到拒絕。
* **API速率限制。** MCP伺服器繼承[!DNL Marketo]執行個體的API速率限制。 使用專用的API使用者來追蹤及管理配額消耗。
* **讓認證不受版本控制。** 如果您的AI工具支援，請使用環境變數內插(`${MARKETO_CLIENT_SECRET}`)，因此認證不會以純文字儲存在存放庫檔案中。

## 治理和資料保留

### 認證處理

* 客戶認證不會儲存在伺服器端，而且由使用者端根據請求提供，這有助於限制服務內的認證曝光。

### API互動模型

* 代理程式使用：代理程式可能會使用MCP伺服器來叫用支援的Marketo API。
* 驗證模型校準：此服務使用針對Marketo API記錄的相同外部API驗證模型。

### 驗證和授權

* 最低許可權：有效許可權繼承自指派給客戶LaunchPoint服務的僅限Marketo API的使用者，以在客戶的Marketo設定中啟用最低許可權管理。
* 無伺服器端權杖持續性：此服務會繼續避免在伺服器端儲存客戶憑證或權杖。  

### 記錄與監視

* 安全性記錄：結構化JSON記錄會透過Fluent Bit路由至Splunk，並具備敏感資料遮罩和其他篩選功能，以支援法規遵循需求。
* 稽核支援：這些控制項可支援持續監控服務可用性、安全性相關事件和作業品質。
* 無伺服器端密碼儲存：客戶認證不會由MCP部署儲存，且必須由使用者端根據請求提供。
* 權杖處理：存取權杖為短期性，權杖回應會標籤為no-store，而權杖會透過標準授權機制（而非查詢字串傳輸）被接受。
* 角色型營運存取：管理部署存取許可權會透過Adobe基礎結構角色和群組型控制項進行管理，而資料平面許可權會繼承自客戶的Marketo API使用者設定。
* 稽核和可觀察性：已啟用安全性記錄、遮罩、監視和警示，以支援調查、服務健康情況追蹤和作業監督。
