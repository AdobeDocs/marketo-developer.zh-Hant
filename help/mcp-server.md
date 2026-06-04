---
title: Marketo Engage MCP伺服器
description: 瞭解如何使用Marketo Engage MCP伺服器將AI助理連線到Marketo。 使用您的Marketo憑證設定Claude Desktop、Cursor、Claude Code或VS Code。
badgeBeta: label="有限可用性" type="informative" tooltip="此功能目前在有限測試版中提供"
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
autotag-review: '2026-06-02T13:31:15.329Z'
TQID: 'https://experienceleague.adobe.com/PJJm7yv8HmbwMB2fsnfDCXs8zprDJK5Q5z2uiiCJRZI'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: c2dbad80-0f5c-4d96-a798-2a65f93b8721
  - id: dca84292-69e9-4116-a575-667d31fa060d
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
source-git-commit: 72329b0ee08402c02604d2b6868fdef88c532548
workflow-type: tm+mt
source-wordcount: 1409
ht-degree: 1%

---

# [!DNL Marketo Engage] MCP伺服器

>[!AVAILABILITY]
>
> 此功能限時提供。 若要要求存取權，請填寫[此表單](https://forms.cloud.microsoft/Pages/ResponsePage.aspx?id=Wht7-jR7h0OUrtLBeN7O4Y-uSf63sAxCmWyqMJg8eMFUMVZSVExSNDA3T0I4SEcwRDFSVTBGWU01Uy4u&origin=QRCode){target="_blank"}。 請確定您已準備好訂閱的Munchkin ID。

模型上下文通訊協定(MCP)是一種開放標準，可讓AI工具與外部服務通訊。 [!DNL Marketo] MCP伺服器可作為您的AI助理與[!DNL Marketo]之間的橋樑。 它會公開超過100項操作，涵蓋表單、方案、智慧行銷活動、銷售機會、電子郵件、代碼片段、清單和資料夾。

當您的AI工具呼叫MCP伺服器時，伺服器會使用您在每個請求中提供的認證，代表您執行相對應的REST API呼叫。 您不需要安裝、部署或執行任何伺服器端軟體。


>[!IMPORTANT]
>
>模型內容通訊協定(MCP)是一種新興的開放原始碼標準，可能會帶來安全性或可靠性風險。Adobe MCP伺服器整合和相關檔案係依「現況」提供，不提供任何形式的保證。
>將MCP使用者端或伺服器連線至Adobe產品是客戶選擇的組態，客戶必須負責評估任何MCP整合的安全性和適用性。Adobe對於因設定錯誤、誤用MCP、協力廠商實作中的漏洞，或透過啟用MCP的工作流程執行的意外動作所引起的問題，概不負責。
>為了降低風險，Adobe鼓勵您在有效使用之前在沙箱環境中測試整合，並在確認或依賴之前，仔細檢閱及驗證所有MCP啟動的動作和回應。

## MCP基本需知

>將MCP想成是AI應用程式的USB-C連線埠。 正如USB-C提供標準化方式將裝置連線到各種周邊裝置和配件，MCP也提供標準化方式將AI模型連線到不同的資料來源和工具。 — [模型內容通訊協定](https://modelcontextprotocol.io/docs/getting-started/intro){target="_blank"}

MCP可讓AI工具同時連線至多個外部服務。 例如，AI助理可以：

* 連線至文書處理器，以產生AI輔助檔案
* 連線至3D模型應用程式（例如Blender）以建立動畫
* 連線至After Effects以進行視訊編輯

MCP是一種通訊協定，任何應用程式都可以實作，向AI工具公開其資料和動作。

## [!DNL Marketo Engage] MCP的功用和不功用

在連線AI工具之前，瞭解MCP的範圍有助於設定期望。

**MCP可以：**

* 透過標準REST API提供對[!DNL Marketo]資料和功能的存取
* 使用您在每個請求中提供的認證，代表您執行API呼叫
* 支援多個同時使用者，每個使用者都透過自己的憑證連線
* 自動處理OAuth權杖重新整理 — 您不需要管理權杖有效期
* 在租使用者隔離的環境中操作，因此您的資料絕不會與另一個使用者的工作階段相交

**MCP不是：**

* 使用、託管或執行任何AI或機器學習模型 — 所有AI處理都會在AI工具中進行，而不是MCP
* 訓練任何資料或從中學習，包括您的客戶資料
* 產生預測、建議或決策 — 決策是下遊人工智慧工具或使用者的責任
* 儲存或保留要求之間的認證、要求資料或工作階段狀態
* 需要您安裝、部署或管理任何伺服器端軟體

MCP可能會傳輸資料，包括可能的敏感欄位，視API使用方式而定，但B2B資料涉及客戶業務資料，不涉及PII資料。

## 先決條件

* 已啟用REST API存取的[!DNL Marketo]執行個體
* 在[!DNL Marketo] LaunchPoint中建立API認證的管理員存取權
* 下列AI工具之一： Claude Desktop、Cursor、Claude Code (CLI)或具有GitHub Copilot的VS Code
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

每個AI工具都會從不同的位置讀取MCP伺服器組態。 在下方尋找您的工具，並依照步驟新增[!DNL Marketo] MCP伺服器。

>[!TIP]
>
>若要連線到多個[!DNL Marketo]執行個體，請在您的MCP設定中新增具有唯一名稱的個別專案，例如`marketo-prod`和`marketo-staging`，每個專案都有對應的認證。

### 克勞德案頭

若要連線到Claude Desktop，請下載[marketo-mcp-bridge.zip](assets/marketo-mcp-bridge.zip)並將其解壓縮。 將`marketo-mcp-bridge.mjs`放入已知位置，以便在下一個步驟中參照。

您還需要：

* Node.js v18+
* npm

1. 開啟Claude Desktop
1. 移至&#x200B;**設定>開發人員>編輯設定**
1. 將下列專案新增至`claude_desktop_config.json`：

```json
{
  "preferences": {
    ...
  },
  "mcpServers": {
    "marketo-mcp": {
      "command": "node",
      "args": ["/path/to/marketo-bridge/bridge.mjs"],
      "env": {
        "MARKETO_MCP_PROD_CLIENT_ID": "<your-client-id>",
        "MARKETO_MCP_PROD_CLIENT_SECRET": "<your-client-secret>",
        "MARKETO_MCP_PROD_MUNCHKIN_ID": "<your-munchkin-id>"
      }
    }
  }
}
```

1. 重新啟動Claude Desktop

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

按&#x200B;**[!UICONTROL Ctrl+Shift+P]** （或macOS上的&#x200B;**[!UICONTROL Cmd+Shift+P]**），輸入&#x200B;**[!UICONTROL MCP: Open User Configuration]**，然後按Enter。 這會開啟`mcp.json`。 在`servers`物件中新增`marketo`專案：

```json
{
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
```

>[!NOTE]
>
>為安全起見，請在設定檔案中使用環境變數內插，而非直接貼上認證。 您可以使用如`${MARKETO_CLIENT_SECRET}`的語法參考變數，並在您的環境中設定它們。 這可防止認證以純文字形式儲存在可能認可至版本控制的檔案中。

## 可用的作業

連線之後，您可以要求AI助理執行下列類別的作業。 如需支援API參考的完整作業清單，請參閱[支援的MCP作業](mcp-server-operations.md)。

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

依電子郵件地址尋找銷售機會、建立或更新銷售機會記錄，以及管理靜態清單成員資格。

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

瀏覽資料夾、頻道、標籤型別和活動型別以瞭解您的[!DNL Marketo]設定。

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
| 「未提供Marketo認證」 | 遺失一或多個`X-Marketo-Client-Id`、`X-Marketo-Client-Secret`或`X-Marketo-Munchkin-Id`。 | 確認設定中出現全部四個標題。 |
| 「驗證錯誤」 | 您的認證無效或已過期。 | 在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**&#x200B;中重新檢查您的使用者端ID和使用者端密碼。 |
| 「403禁止存取」 | 您的Munchkin ID不在伺服器允許清單上。 | 請連絡您的[!DNL Marketo] MCP管理員以新增您的Munchkin ID。 |
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
* **讓認證不受版本控制。** 如果您的AI工具支援，請使用環境變數內插(`${MARKETO_CLIENT_SECRET}`)，因此認證不會以純文字儲存在提交至存放庫的檔案中。
