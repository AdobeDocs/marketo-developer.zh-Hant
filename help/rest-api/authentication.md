---
title: Authentication
feature: REST API
description: 使用2條腿OAuth 2.0驗證Marketo REST API、建立和使用存取權杖、切換至授權標頭、管理過期、處理601和602錯誤。
exl-id: f89a8389-b50c-4e86-a9e4-6f6acfa98e7e
TQID: https://experienceleague.adobe.com/cIeI0m61CyIWq4HEosZ-QAsxzZb0WcrQRpCud2qysfY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 6d9408d07557d4b7426ad72d2a886220d622fb78
workflow-type: tm+mt
source-wordcount: 517
ht-degree: 0%

---

# Authentication

Marketo REST API使用2腿OAuth 2.0進行驗證。 自訂服務提供用於取得存取權杖的使用者端ID和使用者端密碼。

每個自訂服務都屬於僅限API的使用者。 使用者的角色和許可權可授權服務執行特定動作。 存取權杖屬於單一自訂服務，其到期日與執行個體中其他自訂服務的權杖無關。

## 建立存取Token

若要尋找`Client ID`和`Client Secret`，請移至&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL LaunchPoint]**。 選取自訂服務，然後選取&#x200B;**[!UICONTROL View Details]**。

![取得REST服務詳細資料](assets/authentication-service-view-details.png)

![啟動點認證](assets/admin-launchpoint-credentials.png)

若要尋找`Identity URL`，請前往&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**。 URL會顯示在REST API區段中。

使用HTTP GET或POST要求建立存取權杖：

```http
GET <Identity URL>/oauth/token?grant_type=client_credentials&client_id=<Client Id>&client_secret=<Client Secret>
```

如果您的請求有效，您會收到類似以下的JSON回應：

```json
{
    "access_token": "cdf01657-110d-4155-99a7-f986b2ff13a0:int",
    "token_type": "bearer",
    "expires_in": 3599,
    "scope": "apis@acmeinc.com"
}
```

回應包含下列欄位：

- `access_token`：您在後續呼叫中傳遞的Token，以驗證目標執行個體。
- `token_type`： OAuth驗證方法。
- `expires_in`：目前權杖的剩餘有效期限（以秒為單位）。 新的存取權杖的有效期限為3,600秒或1小時。
- `scope`：擁有用於驗證的自訂服務的使用者。

## 使用存取權杖

每個REST API呼叫都必須在HTTP標頭中包含存取權杖。

>[!IMPORTANT]
>
>2026年8月31日將移除使用`access_token`查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，應儘快更新以使用[授權標頭](https://experienceleague.adobe.com/zh-hant/docs/marketo-developer/marketo/rest/authentication#using-an-access-token)。 新開發應僅使用`Authorization`標頭。

### 切換至Authorization標題

若要以Authorization標頭取代`access_token`查詢引數，請更新要求傳送權杖的方式。

下列cURL範例將`access_token`值傳送為含有`-F`旗標的表單引數：

```bash
curl ...  -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/apiCall.json
```

下列範例會在`Authorization: Bearer` HTTP標頭中傳送具有`-H`旗標的相同值：

```bash
curl ... -H 'Authorization: Bearer <Access Token>' <REST API Endpoint Base URL>/bulk/v1/apiCall.json
```

## 提示和最佳實務

儲存身分回應的存取權杖和到期日。 管理權杖到期有助於防止在正常操作期間發生未預期的驗證錯誤。

在進行REST呼叫之前，請檢查權杖的剩餘期限。 如果權杖已過期，請呼叫[身分](https://developer.adobe.com/marketo-apis/api/identity#tag/Identity)端點以更新權杖。 主動更新可防止權杖過期所導致的失敗，並讓REST呼叫延遲更可預測，這對於面向使用者的應用程式非常重要。

驗證錯誤傳回下列程式碼：

- `602`：存取權杖已過期。
- `601`：存取權杖無效。

如果使用者端收到任一程式碼，請呼叫身分識別端點以續約權杖。

如果您在權杖過期之前呼叫身分端點，則回應會傳回相同的權杖及其剩餘期限。

存取權杖屬於自訂服務，而非使用者。 如果來自兩個不同服務的憑證產生範圍設定為相同使用者的身分回應，則其存取權杖和到期日將維持獨立。

當應用程式使用多個認證集時，請使用使用者端ID作為金鑰，以獨立管理每個權杖。
