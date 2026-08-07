---
title: 異動電子郵件
feature: REST API
description: 瞭解如何針對異動電子郵件設定Marketo，並透過REST API Request Campaign觸發，包含設定步驟和Java程式碼範例。
exl-id: 057bc342-53f3-4624-a3c0-ae619e0c81a5
TQID: https://experienceleague.adobe.com/eUw2THnwDdIuEO3MsuG4cSaoPnKVvdZ0ZTV-gxP-pJQ
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 891
ht-degree: 1%

---

# 異動電子郵件

使用[請求行銷活動](https://developer.adobe.com/marketo-apis/api/mapi#operation/triggerCampaignUsingPOST) API傳送異動電子郵件給特定Marketo記錄。 提出要求之前，先設定電子郵件並觸發行銷活動。

- 確定收件者有Marketo記錄。
- 在Marketo例項中建立和核准交易式電子郵件。
- 啟用使用「已請求行銷活動， 1. Source：網站服務API」並傳送電子郵件。

首先，[建立並核准電子郵件](https://experienceleague.adobe.com/docs/marketo/using/home.html)。 如果電子郵件在法律上符合運作資格，請在「電子郵件動作>電子郵件設定」中將它設定為可運作：

![Request-Campaign-Email-Settings](assets/request-campaign-email-settings.png)

![Request-Campaign-Operational](assets/request-campaign-operational.png)

在建立行銷活動之前核准電子郵件：

![RequestCampaign-Approve-Draft](assets/request-campaign-approve-draft.png)

如有需要，請參閱[建立新的Smart Campaign](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/creating-a-smart-campaign/create-a-new-smart-campaign.html)。 使用「已請求促銷活動」觸發器設定促銷活動的智慧清單：

![Request-Campaign-Smart-List](assets/request-campaign-smart-list.png)

設定參考交易式電子郵件的傳送電子郵件流程步驟：

![Request-Campaign-Flow](assets/request-campaign-flow.png)

啟動之前，請在「排程」標籤上設定資格設定。 如果每個記錄只接收一次電子郵件，則保留預設設定。 否則，每次或按可用步調允許收件者符合資格。

啟動行銷活動：

![Request-Campaign-Schedule](assets/request-campaign-schedule.png)

## 傳送API呼叫

Java範例使用[minimal-json套件](https://github.com/ralfstx/minimal-json)來處理JSON表示。

在傳送電子郵件之前，請確認電子郵件地址存在Marketo記錄，並擷取其潛在客戶ID。 此範例假設電子郵件地址已存在。

使用[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByFilterUsingGET)取得銷售機會，以擷取識別碼。 然後以下主要方法會要求行銷活動：

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
        //Create an instance of Auth so that we can authenticate with our Marketo instance
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("requestCampaign.test@marketo.com");

        //Create and parameterize an instance of Leads
        //Set your email filterValue appropriately
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("test.requestCamapign@example.com");

        //Get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //Get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of your campaign from Marketo
        int campaignId = 0;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Send the request to Marketo
        rc.postData();
    }
}
```

從`JsonObject`回應中擷取結果陣列，並在索引0擷取物件：

```java
JsonArray leadsResult = leadsRequest.getData().get("result").asArray();
int leadId = leadsResult.get(0).asObject().get("id").asInt();
```

使用請求URL中的促銷活動ID呼叫請求促銷活動。 要求內文包含具有`id`成員的JSON物件陣列：

```java
package dev.marketo.blog_request_campaign;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class RequestCampaign {
    private String endpoint;
    private Auth auth;
    public ArrayList leads = new ArrayList();
    public ArrayList tokens = new ArrayList();

    public RequestCampaign(Auth auth, int campaignId) {
        this.auth = auth;
        this.endpoint = this.auth.marketoInstance + "/rest/v1/campaigns/" + campaignId + "/trigger.json";
    }
    public RequestCampaign setLeads(ArrayList leads) {
        this.leads = leads;
        return this;
    }
    public RequestCampaign addLead(int lead){
        leads.add(lead);
        return this;
    }
    public RequestCampaign setTokens(ArrayList tokens) {
        this.tokens = tokens;
        return this;
    }
    public RequestCampaign addToken(String tokenKey, String val){
        JsonObject jo = new JsonObject().add("name", tokenKey);
        jo.add("value", val);
        tokens.add(jo);
        return this;
    }
    public JsonObject postData(){
        JsonObject result = null;
        try {
            JsonObject requestBody = buildRequest(); //builds the Json Request Body
            System.out.println("Executing RequestCampaign call\n" + "Endpoint: " + endpoint + "\nRequest Body:\n"  + requestBody);
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
            urlConn.setRequestMethod("POST");
            urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
            OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
            wr.write(requestBody.toString());
            wr.flush();
            InputStream inStream = urlConn.getInputStream(); //get the inputStream from the URL connection
            Reader reader = new InputStreamReader(inStream);
            result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
            System.out.println("Result:\n" + result);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return result;
    }

    private JsonObject buildRequest(){
        JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
        JsonObject input = new JsonObject();
        JsonArray leadsArray = new JsonArray();
        for (int lead : leads) {
            JsonObject jo = new JsonObject().add("id", lead);
            leadsArray.add(jo);
        }
        input.add("leads", leadsArray);
        JsonArray tokensArray = new JsonArray();
        for (JsonObject jo : tokens) {
            tokensArray.add(jo);
        }
        input.add("tokens", tokensArray);
        requestBody.add("input", input);
        return requestBody;
    }

}
```

此類別有一個接受驗證的建構函式，以及促銷活動的ID。 透過傳遞包含記錄的ID的`ArrayList<Integer>`給setLeads，或使用addLead （取一個整數並將其附加到leads屬性中的現有ArrayList），將潛在客戶新增到物件。 若要觸發API呼叫以將潛在客戶記錄傳遞至促銷活動，必須呼叫postData，其會傳回包含來自請求的回應資料的JsonObject。 呼叫要求促銷活動時，Marketo中的目標觸發程式促銷活動會處理每個傳遞至呼叫的潛在客戶，並傳送先前建立的電子郵件。 恭喜，您已透過Marketo REST API觸發電子郵件。 請留意第2部分，我們將透過「請求行銷活動」以動態方式自訂電子郵件內容。

### 建立電子郵件

若要自訂我們的內容，我們必須先在Marketo中設定[程式](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/create-a-program.html)和[電子郵件](https://experienceleague.adobe.com/docs/marketo/using/home.html)。 若要產生自訂內容，我們必須在程式中建立權杖，然後將它們放入要傳送的電子郵件中。 為了簡單起見，在此範例中，我們僅使用一個權杖，但您可以取代電子郵件、寄件者電子郵件、寄件者姓名、回覆或電子郵件中任何內容的任何數量權杖。 所以讓我們建立一個Token Rich Text作為取代，並將其稱為「bodyReplacement」。 RTF可讓我們使用想要輸入的任意HTML來取代權杖中的任何內容。

![New-Token](assets/New-Token.png)

Token在空白時無法儲存，因此請在此插入一些預留位置文字。 現在必須將代號插入電子郵件中：

![新增Token](assets/Add-Token.png)

此Token現在可透過「請求行銷活動」呼叫進行取代。 此代號可以很簡單，只需一行文字，但必須根據每封電子郵件進行取代，也可以包含電子郵件的幾乎整個版面。

### 代碼

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
        //Create an instance of Auth so that we can authenticate with our Marketo instance
        Auth auth = new Auth("Client ID - CHANGE ME", "Client Secret - CHANGE ME", "Host - CHANGE ME");

        //Create and parameterize an instance of Leads
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("requestCampaign.test@marketo.com");

        //get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of our campaign from Marketo
        int campaignId = 1578;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Create the content of the token here, and add it to the request
        String bodyReplacement = "<div class=\"replacedContent\"><p>This content has been replaced</p></div>";
        rc.addToken("{{my.bodyReplacement}}", bodyReplacement);
        rc.postData();
    }
}
```

如果程式碼看起來很熟悉，那是因為它只有上述主要方法的另外兩行。 這次我們要在bodyReplacement變數中建立權杖的內容，然後使用addToken方法將其新增至請求。 addToken會取得索引鍵和值，然後建立JsonObject表示法並將其新增至內部Token陣列。 然後在postData方法期間將其序列化，並建立如下所示的主體：

```json
{
    "input":
    {
        "leads": [
            {
                "id": 1
            }
        ],
        "tokens": [
            {
                "name": "{{my.bodyReplacement}}",
                "value": "<div class=\"replacedContent\"><p>This content has been replaced</p></div>"
            }
        ]
    }
}
```

結合之後，我們的主控台輸出如下所示：

```bash
Token is empty or expired. Trying new authentication
Trying to authenticate with ...
Got Authentication Response: {"access_token":"19d51b9a-ff60-4222-bbd5-be8b206f1d40:st","token_type":"bearer","expires_in":3565,"scope":"apiuser@mktosupport.com"}
Executing RequestCampaign call
Endpoint: .../rest/v1/campaigns/1578/trigger.json
Request Body:
{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class=\"replacedContent\"><p>This content has been replaced</p></div>"}]}}
Result:
{"requestId":"1e8d#14eadc5143d","result":[{"id":1578}],"success":true}
```

## 正在結束

此方法可透過多種方式擴充，可變更個別版面區段內或外部電子郵件中的內容，讓自訂值可傳遞至任務或有趣的時刻。 在程式中可以使用代號的任何地方，都可以使用此方法自訂。 [排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi#operation/scheduleCampaignUsingPOST)呼叫也有類似的功能，可讓您處理整個批次行銷活動中的權杖。 這些無法根據潛在客戶進行自訂，但可用於在廣泛的潛在客戶集合中自訂內容。
