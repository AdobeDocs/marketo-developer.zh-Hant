---
title: 部落格封存
description: Marketo開發人員部落格2014-2023年封存
exl-id: d7ae88dd-9938-4957-9798-db43090dab4e
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '61715'
ht-degree: 0%

---

# 部落格封存

>[!INFO]
>
>這是Marketo部落格的封存，橫跨2014年至2023年。 此處僅提供歷史參考資料。
>>部分資訊可能已過期。  請務必檢視目前的檔案，以瞭解最新功能。
>

>[!IMPORTANT]
>SOAP API即將淘汰，2025年10月31日後將不再提供使用。 所有新開發應透過Marketo REST API完成，而現有服務應於該日期前移轉，以避免服務中斷。 如果您有使用SOAP API的服務，請參閱[SOAP API移轉指南](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/migration)以瞭解如何移轉的資訊。
>

>[!IMPORTANT]
>2025年10月31日將移除使用`access_token`查詢引數的驗證支援。 如果您的專案使用查詢引數來傳遞存取Token，應儘快更新以使用[授權標頭](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/authentication#using-an-access-token)。 新開發應專門使用Authorization標頭。
>

## 歡迎使用Marketo開發人員部落格

歡迎Marketo開發人員！ 我們在Marketo發佈新功能時相當忙碌，這些新功能可協助行銷人員更勝以往。 現今的行銷人員獲得傑出的開發人員社群的支援。 此部落格專供支援現代行銷人員迅速發展需求的網頁開發人員與軟體工程師使用。 隨著Marketo平台的發展，我們將於推出新整合選項和API版本更新時正式發佈。 我們也將推出一系列新的作法文章，分享程式碼範例和與Marketo平台整合的最佳作法。 本系列的第一篇文章將引導您瞭解如何使用API有效率地擷取儲存在Marketo中的人員（客戶/聯絡人/潛在客戶）的相關資訊。 請使用上述表格訂閱以取得最新消息。 新文章發佈時，我們會以電子郵件寄給您更新消息。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2014-03-06_

## 2014年1月版本更新

### Forms 2.0

Forms 2.0可讓行銷人員建立美觀、穩定且有彈性的網路表單，而不需要程式設計知識。 除了大幅改善的編輯器、條件式邏輯和強大的設計外，現在嵌入網站任何頁面的Marketo也比以往更容易。 開發人員能使用JavaScript擴充核心功能；使用案例包括：

* 提交成功後隱藏表單，讓訪客在填寫表單後仍留在頁面上
* 根據自訂商業邏輯在提交上顯示自訂錯誤訊息
* 在燈箱樣式對話方塊中顯示表單

開發人員檔案可在[這裡](/help/javascript-api/forms-api-reference.md)取得。

### SOAP API 2_3版現已可用

* [getLeadChanges：](/help/soap-api/getleadchanges.md)已引入要求欄位`activityNameFilter`
* [ListOperation：](/help/soap-api/listoperation.md)已移除要求欄位`skipActivityLog`

**注意：** SOAP API修訂版本可回溯相容

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-01-26_

## Zapier第二部分：Marketo整合公告

在上一篇文章中，我們討論了如何使用Zapier將外部資料來源與Marketo整合。 本文提供從頭開始建立您自己的Zapier工作流程（或「Zap」），以整合Marketo和其他應用程式的方法。 那麼，您是否樂見將Zapier與Marketo搭配使用，但需要入門協助？ 好消息！ Zapier已發佈多個Marketo的範例Zap，讓您快速上手：

* 擷取新的銷售機會
* 培養您的客戶
* 發佈連絡資訊
* 對新潛在客戶做出反應

除了這些範例，您還可以在Zapier上使用數百個其他應用程式瀏覽[Marketo整合](https://zapier.com/apps/marketo/integrations)頁面，並在幾分鐘內建立您自己的自動化工作流程。 不需要編碼。 節省時間，讓自動化完成您的手動工作。 發揮創意。 天空就是極限！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2016-06-01_

## 使用API從Marketo擷取客戶和潛在客戶資訊

您可以使用`getLead`和[`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads) SOAP API擷取儲存在Marketo中的客戶和潛在客戶相關資訊。 我們通常希望定期擷取這些資訊，以便隨著客戶和潛在客戶資訊的更新或在Marketo中建立新記錄而保持另一個系統的更新。 我們顯示定期執行的程式碼範例，用於輪詢Marketo以取得更新。 下圖說明在設定的定期計時器上執行的API呼叫。 根據使用案例，週期性計時器可設為每10分鐘執行一次以下程式碼。

第一次呼叫[`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads)將會設定時間範圍、batchSize以及要傳回的欄位。 當可用記錄數多於指定的batchSize時，Marketo中在指定時間範圍內更新的所有銷售機會都將與streamPosition一起傳回。

**SOAP要求第一次呼叫getMultipleLeads：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector>
  <batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```

**來自第一次呼叫getMultipleLeads的SOAP回應：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
 <returnCount>2</returnCount><remainingCount>24</remainingCount><newStreamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</newStreamPosition><leadRecordList>
      <leadRecord>
        <Id>84105</Id>
        <Email>eimang@marketo.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>French</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>Lead</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
      <leadRecord>
        <Id>1089965</Id>
        <Email>t@t.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>George</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>of the Jungle</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
    </leadRecordList>
  </result>
</ns2:successGetMultipleLeads>
```

只要`<remainingCount/>`值大於0，您就後續呼叫`getMultipleLeads`，將先前呼叫中傳回的`<newStreamPosition/>`值傳入`<streamPosition/>`引數，以分頁處理其餘的值。 **SOAP要求後續呼叫getMultipleLeads：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector><streamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</streamPosition><batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```


只要`<remainingCount/>`大於零，此邏輯就會繼續。 **請參閱下方執行上述案例的範例Java程式。**


```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.GregorianCalendar;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import javax.xml.datatype.DatatypeFactory;
import javax.xml.datatype.XMLGregorianCalendar;

public class GetMultipleLeads {
  public static void main(String[] args) {
    System.out.println("Executing GetMultipleLeads");
        try {
        URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
        String marketoUserId = "CHANGE ME";
        String marketoSecretKey = "CHANGE ME";
        QName serviceName = new QName("<http://www.marketo.com/mktows/>",
        "MktMktowsApiService");
        MktMktowsApiService service = new
        MktMktowsApiService(marketoSoapEndPoint, serviceName);
        MktowsPort port = service.getMktowsApiSoapPort();

        // Create Signature
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String text = df.format(new Date());
        String requestTimestamp = text.substring(0, 22) + ":" +         text.substring(22);
        String encryptString = requestTimestamp + marketoUserId ;

        SecretKeySpec secretKey = new         SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");

        Mac mac = Mac.getInstance("HmacSHA1");
        mac.init(secretKey);
        byte[] rawHmac = mac.doFinal(encryptString.getBytes());
        char[] hexChars = Hex.encodeHex(rawHmac);
        String signature = new String(hexChars);

        // Set Authentication Header
        AuthenticationHeader header = new AuthenticationHeader();
        header.setMktowsUserId(marketoUserId);
        header.setRequestTimestamp(requestTimestamp);
        header.setRequestSignature(signature);

        // Create Request
        ParamsGetMultipleLeads request = new ParamsGetMultipleLeads();

        // Build Request Using LastUpdateAtSelector
        LastUpdateAtSelector leadSelector = new LastUpdateAtSelector();
        GregorianCalendar gc = new GregorianCalendar();
        gc.setTimeInMillis(new Date().getTime());
        gc.add( GregorianCalendar.DAY_OF_YEAR, -20);
        DatatypeFactory factory = DatatypeFactory.newInstance();
        ObjectFactory objectFactory = new ObjectFactory();
        JAXBElement<XMLGregorianCalendar> until =         objectFactory.createLastUpdateAtSelectorLatestUpdatedAt(factory.newXMLGregorianCalendar(gc));

        GregorianCalendar since = new GregorianCalendar();
        since.setTimeInMillis(new Date().getTime());
        since.add( GregorianCalendar.DAY_OF_YEAR, -21);
        leadSelector.setOldestUpdatedAt(factory.newXMLGregorianCalendar(since));
        leadSelector.setLatestUpdatedAt(until);
        request.setLeadSelector(leadSelector);

        ArrayOfString attributes = new ArrayOfString();
        attributes.getStringItems().add("FirstName");
        attributes.getStringItems().add("LastName");
        attributes.getStringItems().add("Email");
        request.setIncludeAttributes(attributes);

        JAXBElement<Integer> batchSize = new
        ObjectFactory().createParamsGetMultipleLeadsBatchSize(2);
        request.setBatchSize(batchSize);

        SuccessGetMultipleLeads result = null;

        int count = 0;

        do {
        if (count > 0) {
        // Set the streamPosition on subsequent calls to paginate
        through large result sets
        String pos = result.getResult().getNewStreamPosition();
        JAXBElement<String> streamPos = new
        ObjectFactory().createParamsGetMultipleLeadsStreamPosition(pos);
        request.setStreamPosition(streamPos);
        }

        JAXBContext context =
        JAXBContext.newInstance(ParamsGetMultipleLeads.class);
        Marshaller m = context.createMarshaller();
        m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        System.out.println("REQUEST:");
        m.marshal(request, System.out);
        result = port.getMultipleLeads(request, header);
        System.out.println("RESPONSE:");
        m.marshal(result, System.out);
        count = result.getResult().getReturnCount();
        } while (result.getResult().getRemainingCount() > 0);
        }

        catch(Exception e) {
        // Update to include appropriate retry/error handling
        e.printStackTrace();
        }

  }

}
```

### 秘訣與技巧：

從Marketo擷取大量聯絡人時，建議沿著以下引數調整API請求：

* `<includeAttributes/>`：建議您只要求您想要與系統同步的欄位。 這樣會減少回應的裝載，並提高查詢效能。
* `<batchSize/>`： API支援在單一呼叫中最多可傳回1000筆記錄。 將此值調整為500筆記錄也可減少回應的裝載。
* `<LastUpdatedAtSelector/>`：建議同時設定`<oldestUpdatedAt/>`和`<latestUpdatedAt/>`引數以限制日期範圍。 例如，與其對一年的資料提出單一請求。 中斷API呼叫以請求較小的日期範圍。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-03-05_

## 2014年2月版本更新

### SOAP API更新：

* [syncMObjects](/help/soap-api/syncmobjects.md)：您現在可以為現有程式新增和更新標籤和頻道。

更新已合併至[2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL)。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-02-26_

## 2014年3月版本更新

### SOAP API更新：

* [syncLead](/help/soap-api/synclead.md)和[syncMultipleLeads](/help/soap-api/syncmultipleleads.md)的效能改善

更新已合併至[2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL)。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-03-20_

## 訪客填寫表單時合併匿名訪客活動

在標題為「根據商業邏輯擷取匿名訪客活動」的部落格中，我們討論了如何根據自訂事件在Marketo中建立匿名潛在客戶記錄。 在這篇部落格中，我們將建立在該文章上，並在您收到使用者的聯絡資訊後，將匿名潛在客戶記錄與已知使用者建立關聯。 Marketo的[Munchkin追蹤代碼](/help/javascript-api/lead-tracking.md)可協助您追蹤網站的造訪次數。 第一次有人瀏覽您網站上具有Munchkin追蹤代碼的頁面時，Marketo會建立匿名銷售機會，並使用瀏覽器Cookie來追蹤他們。 一旦他們自我識別，就會成為已知的潛在客戶，而且與其瀏覽器Cookie相關的歷史記錄會合併到其Marketo潛在客戶記錄中。 **匿名銷售機會是在以下人員時建立的：**

1. 首次造訪您的Marketo登陸頁面
1. 瀏覽具有Munchkin追蹤代碼的頁面
1. 按一下Marketo電子郵件中的「以網頁形式檢視」連結

**當某人出現下列情況時，匿名潛在客戶會合併到新的或現有的已知潛在客戶中：**

1. 按一下Marketo電子郵件的連結
1. 填寫Marketo表單
1. 使用下列其中一項技術，將匿名銷售機會與已知記錄建立關聯。

若要提交個人資料，或將瀏覽器網路追蹤Cookie與Marketo中對應的個人記錄建立關聯，請使用下列其中一項技術： **瀏覽器端提交**&#x200B;如果您的使用案例需要從瀏覽器提交個人資料，您應使用背景表單提交。 **伺服器端提交**&#x200B;如果您不需要瀏覽器端提交，REST API會提供多種個人資料提交方法，以及專門建立的Cookie與個人記錄關聯方法。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-04-22_

## 2014年4月版本更新

### Marketo Forms安全性更新：

我們對來自單一IP位址的表單貼文提交數量和頻率進行了限制。 此限制現在強製為每分鐘30個貼文，以保護我們的客戶免受惡意使用程式化表單提交的攻擊。 [syncLead API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/synclead)是建議的整合工具，可在Marketo中以程式化方式提交新連絡人。    

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-04-29_

## 以Marketo API取代您的表單發佈整合

使用Marketo的行銷人員通常會根據填寫特定網路表單的聯絡人/潛在客戶數量，為指定的行銷活動建立成功關聯。 行銷人員只需建立新的Marketo表單、將其放在登陸頁面上，然後在Marketo中設定觸發行銷活動，以將填寫該特定表單的所有聯絡人建立關聯，以成功顯示該行銷計畫。 （請參閱下方的熒幕擷圖）。 即使行銷活動成功是在他人填寫表單之外進行，在記錄方案成功時也應使用此相同方法，這是很自然的擴充功能。 例如，當行銷人員執行促銷優惠方案，而轉換是呼叫並排程約會時。 潛在客戶未在此過程中隨時填寫表單。 在以下文章中，我們將說明如何使用Marketo syncLead API建立新連絡人（若他們是新連絡人），然後使用requestCampaign API記錄指定行銷方案的成功案例。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_1970-01-01_

## 使用Marketo API刪除銷售機會

假設您想要透過Marketo API刪除銷售機會。 您可以在具有預先定義之流程動作的促銷活動上呼叫requestCampaign API來刪除銷售機會，以達成此目的。 我們會先說明如何建立Smart Campaign、如何設定觸發程式以透過API執行行銷活動、如何定義將刪除銷售機會作為流程動作的一部分，以及第四個用於執行此行銷活動的程式碼範例。 **如何在Marketo中建立新的Smart Campaign** Marketo中的Smart Campaign執行您的所有行銷活動。 在Smart Campaign中，您可以設定一系列自動化動作，以對聯絡人的智慧清單執行。 刪除銷售機會時，您會在行銷活動中設定觸發條件，如下所示。 首先設定Smart Campaign。

1. 在行銷活動中，選擇方案，然後在「新增」下拉式清單中，按一下「新增本機資產1」。 按一下Smart Campaign
1. 輸入智慧行銷活動名稱，然後按一下「建立新增觸發器至Smart Campaign」**將觸發器新增至Smart Campaign，可讓您根據即時事件（在此例中為透過requestCampaign API的請求），一次對一個人執行Smart Campaign。
1. 搜尋「已要求行銷活動」觸發程式，然後將其拖放至畫布。
1. 在觸發器中，選取「是」和「網站服務API」。

**如何在行銷活動上建立刪除銷售機會流量動作**&#x200B;在頂端功能表中按一下流量。 從右側的功能表中搜尋「刪除銷售機會」，然後將其拖曳至中心，以將其新增為促銷活動的觸發因子。 注意：如果您僅從Marketo中刪除潛在客戶並將其保留在您的CRM中，則當該潛在客戶的任何資料更新時，會在Marketo中重新建立該潛在客戶。  **呼叫requestCampaign API的程式碼範例**&#x200B;在Marketo介面中設定行銷活動和觸發器後，我們會示範如何使用API來執行此行銷活動。 第一個範例是XML要求，第二個範例是XML回應，最後一個範例是可用來產生XML要求的Java程式碼範例。 我們也會說明如何尋找在呼叫requestCampaign API時使用的促銷活動ID。 此API呼叫也要求您預先知道Marketo促銷活動的ID。 您可以使用下列其中一種方法來判斷促銷活動ID：

1. 使用[getCampaignsForSource](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET) API
1. 在瀏覽器中開啟Marketo促銷活動，並檢視URL位址列。 行銷活動ID （以4位數的整數表示）可在「SC」後面立即找到。 例如 `https://app-stage.marketo.com/#SC**1025**A1`。粗體部分是促銷活動ID - &quot;1025&quot;。 SOAP的requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

requestCampaign的SOAP回應

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

請參閱底下執行上述情境的範例Java程式。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-05-16_

## 使用Munchkin AP自訂活動追蹤 — 

假設您想要在Marketo中追蹤自訂活動。 例如，您有一個網頁上的影片，且您想要追蹤觀看影片中超過50%的訪客。 您可以使用Munchkin的自訂活動追蹤功能來達成此目的。 若要實作，請聆聽頁面上的事件（影片播放率達到50%），然後呼叫Munchkin API。 若要這麼做，我們必須在Marketo中設定自訂活動，以根據頁面上的此事件呼叫。 我們將YouTube用於視訊播放器，並使用他們的[YouTube Iframe API](https://developers.google.com/youtube/iframe_api_reference)來呼叫Munchkin API上的方法。

我們會先說明如何在Marketo中產生Munchkin追蹤程式碼，接著說明如何修改您的Munchkin範常式式碼以根據頁面事件觸發，接著說明如何使用由頁面上的動作所定義的智慧清單及流程步驟來設定行銷活動，最後說明如何確認在Marketo中記錄了匿名使用者的頁面造訪。 ====這篇部落格是說明程式碼的即時範例。 請填寫這張表格，這樣您就是Marketo中的已知使用者。 如此一來，當您觀看50%的影片時，就會傳送其餘的影片給您，而如果您觀看了100%的影片，就會傳送另一個部落格的連結給您。=== <https://developers.google.com/youtube/iframe_api_reference>

**如何產生Munchkin追蹤代碼** Munchkin追蹤代碼可讓您追蹤網站的造訪次數。 底下說明有三種型別的Munchkin程式碼，但在此範例中，我們使用非同步Munchkin追蹤程式碼。 A)簡單：程式碼行數最少，但無法最佳化網頁載入時間。 此程式碼會在每次載入網頁時載入jQuery程式庫。 B)非同步：減少網頁載入時間。 此程式碼會檢查jQuery程式庫是否已存在，如果缺少程式庫，則會將其載入，並在載入網頁其餘部分後用來執行追蹤程式碼。 C)非同步jQuery：減少網頁載入時間並改善系統效能。 此程式碼假設您已擁有jQuery，且未檢查以載入它。

1. 按一下應用程式右上角的管理員。
1. 按一下左側樹狀結構中的Munchkin 。
1. 選取「非同步」以追蹤程式碼型別。
1. 按一下並複製JavaScript追蹤程式碼，放置您的網站上。 **YouTube代碼** <https://developers.google.com/youtube/js_api_reference#EventHandlers> <https://developers.google.com/youtube/iframe_api_reference> 播放器。

`getCurrentTime()`傳回自視訊開始播放以來經過的時間（以秒為單位）。 `player.getDuration()`傳回目前播放視訊的持續時間（以秒為單位）。 請注意，載入視訊的中繼資料之前，`getDuration()`會傳回0，這通常是在視訊開始播放後立即發生。 當非Cookie使用者前往具有Munchkin追蹤代碼的頁面時，會在使用者的瀏覽器上建立新的Cookie，並在Marketo中建立新的匿名銷售機會。 如果使用者已經完成Cookie，且使用者是Marketo中的現有銷售機會，則對頁面的瀏覽將記錄在Marketo中使用者的活動記錄中。 **Cookie使用者和追蹤事件的程式碼範例**&#x200B;請將追蹤程式碼放置在網頁上的`</body>`標籤之前。 在Marketo中建立的登入頁面會自動包含追蹤程式碼，因此您不需要將此程式碼放在這些頁面上。 此程式碼範例會在指令碼載入後呼叫Munchkin API：

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```

此程式碼範例會在使用者在頁面上停留5秒鐘，且已向下捲動500畫素後向呼叫Munchkin API：

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
      }
     }

     var s = document.createElement('script');
     s.type = 'text/javascript';
     s.async = true;
    s.src = '//munchkin.marketo.net/munchkin.js';
     s.onreadystatechange = function() {
      if (this.readyState == 'complete' || this.readyState == 'loaded') {
          initMunchkin();
      }
     };
     s.onload = initMunchkin;
     document.getElementsByTagName('head')[0].appendChild(s);
   })();
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**如何確認匿名使用者的頁面造訪記錄於Marketo**

1. 按一下頂端功能表中的Analytics ，然後按一下「新增報表」 。 選擇「網頁活動」作為報表型別，然後為您的報表命名。
1. 建立報表後，按一下「智慧列示」。 然後從右側的方塊中選取「造訪的網頁」篩選器。 輸入放置Munchkin追蹤代碼的網頁。
1. 按一下「設定」。 選取「來自ISP的匿名訪客」，並將選項變更為「已顯示」。
1. 按一下「報表」 。 您現在會看到在您選取的網頁上追蹤的活動。
1. 按兩下潛在客戶記錄，然後將顯示活動記錄，您可以在其中檢視匿名使用者造訪的特定頁面。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

例如，對於包含多媒體內容的頁面，您可能想要進行自訂追蹤。 一個常見範例是將Munchkin追蹤程式碼新增至頁面，同時使用Munchkin API在Marketo執行個體中產生事件，以用於播放視訊或聆聽音訊片段等活動。 建議您在大部分或所有網頁上放置Munchkin追蹤程式碼。 Munchkin追蹤程式碼會自動包含在您使用Marketo建立的登陸頁面中。 使用此呼叫來記錄使用者做了某些事情，例如在Ajax、Flash或其他RIA環境中造訪頁面。 URL不可包含&#39;&#39;或任何網域，但可指向任何頁面 — 甚至不存在的頁面。 如果要新增URL引數，請使用params引數。
該事件會在使用者活動記錄（位於呼叫網頁的網域下）中顯示為「造訪網頁」事件。 注意：您第一次呼叫`mktoMunchkin()`時，一律會為目前頁面建立造訪網頁事件。 除非您想要追蹤其他網頁造訪，否則不需要呼叫`visitWebPage`。 `mktoMunchkinFunction('visitWebPage', { url: '/MyFlashMovie/Story1', params: 'x=y&2=3' });`注意事項請確定您有權存取經驗豐富的JavaScript開發人員。 Marketo技術支援未設定為協助疑難排解自訂JavaScript。 Munchkin JavaScript API可讓您將協力廠商網站系統與Marketo帳戶整合。 透過某些網站開發，您可以擷取新的銷售機會，或使用網站上現有應用程式更新目前的銷售機會。 假設您有一個客戶註冊的網頁應用程式，可擷取新的客戶資訊。 只要進行一點程式設計，您就能擁有在Marketo中擷取的使用者的潛在客戶資訊，以及用於未來網路追蹤的Marketo Cookie集。

此外，另一項功能可讓您的網頁開發人員從豐富的網頁環境（例如Flash或Ajax）擷取及追蹤網頁活動資訊。 注意：如果您有適當的開發資源，應考慮使用SOAP API進行整合，而非使用此API。 SOAP API比Munchkin API更健全，功能也更多。 Marketo SOAP API需求您必須在網頁上加入Munchkin JavaScript程式碼，這些程式碼才能運作。 您可以在Munchkin教學課程中找到所需的指令碼標籤。 同時啟用Munchkin API，教學課程中也對此進行說明。
在幕後執行Munchkin API呼叫後，系統會在使用者沒有Cookie時自動對使用者執行Cookie。 在Marketo中，這會在人員的活動記錄中記錄事件（按一下連結、造訪網頁或新的潛在客戶）。 如果您使用點選連結或造訪網頁呼叫，事件會新增到該潛在客戶的活動記錄（已知或匿名）。 如果這是新的潛在客戶，而您使用關聯潛在客戶通話，則該潛在客戶會變成已知潛在客戶，其活動歷史記錄將會保留。 如果這是現有的銷售機會（根據電子郵件地址相符），則任何變更或新的值都將更新在該銷售機會記錄中。

以下是`munchkinFunction`呼叫的一般形式。 無論您要在何處呼叫，都以標籤形式加入網頁。 您可以像叫用任何其他JavaScript函式一樣叫用此函式。 不過，您必須先呼叫Munchkin追蹤函式`mktoMunchkin()`，才能進行任何`mktoMunchkinFunction()`呼叫：

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"> mktoMunchkin("###-###-###"); mktoMunchkinFunction('function', { key: 'value', key2: 'value'}, 'hash');
```

其中： `###-###-###`是您帳戶函式的Munchkin帳戶ID是您要讓引數成為呼叫雜湊所需之引數陣列的呼叫，只有`associateLead`才需要

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_1970-01-01_

## 將資料帶入Marketo

下列簡報會向您說明將資料傳入Marketo的各種方式。 它著重於表單、自訂物件和整合。

[從](https://www.slideshare.net/MurtzaManzur/getting-data-into-marketo-35662408)Murtza Manzur[將資料匯入Marketo](https://www.slideshare.net/MurtzaManzur)

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-06-06_

## 在Workspace中建立銷售機會

假設您的公司有兩個部門：北美和歐洲。 您想要根據Marketo中的公司部門來細分潛在客戶。 您可以使用工作區來達到此目的，這是Marketo中的功能，可讓您限制對潛在客戶的存取。 為此，您需要為北美建立一個工作區，並為歐洲建立一個工作區。 然後，您可以使用[syncLead API](/help/soap-api/synclead.md)，在特定工作區中建立銷售機會。 如果您的組織具備以下條件，您應該考慮使用工作區和銷售機會分割：

1. 為多個產品線區分行銷團隊
1. 針對不同的地區或國家/地區設立不同的行銷團隊
1. 母公司和子公司
1. 母公司與經銷商

當您使用潛在客戶分割區和工作區時，您可以：

1. 限制存取組織中的潛在客戶
1. 限制存取組織中的資產
1. 在行銷團隊間共用資產

我們會先說明如何透過UI在Marketo中建立工作區，然後說明如何使用[syncLead API](/help/soap-api/synclead.md)將銷售機會寫入該工作區。 **建立Workspace**&#x200B;工作區是一組銷售機會和Marketo資產。 在工作區中，您只能看見該工作區的銷售機會以及該工作區中的資產（電子郵件、行銷活動、清單等）。 該工作區中的智慧行銷活動只會影響該工作區中的銷售機會。 若要檢視帳戶中的工作區：

1. 前往「管理員」區段的「工作區和銷售機會分割」頁面。 您的工作區會顯示在「工作區」標籤中。 1.若要建立新工作區，請按一下「工作區」標籤之功能表列中的「新增Workspace」按鈕。
1. 在對話方塊中，您需要新增一些有關新工作區的資訊：

* **Workspace名稱** — 此工作區在介面中顯示的名稱
* **描述** — 工作區的選擇性文字描述
* **潛在客戶分割** — 這些潛在客戶會顯示在此分割中。
* **所有潛在客戶分割** — 將會看見來自所有目前和未來分割的潛在客戶
* **選取個別的資料分割** — 只會顯示來自這些資料分割的銷售機會（而且不會顯示未來的資料分割）
* **主要潛在客戶分割** — 造訪您登入頁面的潛在客戶會依預設新增至此分割
* **語言** — 工作區的商務語言

我們說明如何使用API將銷售機會寫入特定工作區。 第一個範例是XML要求，第二個範例是XML回應，最後一個範例是Ruby程式碼範例，可用來產生XML要求。 1.建立銷售機會之後，「銷售機會分割」是「銷售機會資訊」上的欄位。 `requestCampaign`的SOAP請求

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

requestCampaign的SOAP回應

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

請參閱底下執行上述情境的範例Java程式。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

**如何註冊工作區和Lead資料分割？**&#x200B;對於Marketo Enterprise客戶，您可以免費存取工作區和Lead分割區。 如需啟用和實作的詳細資訊，請聯絡客戶啟用管理員。 若是其他客戶，請連絡您的Marketo銷售主管，或傳送電子郵件給我們的銷售團隊，以取得此產品的詳細資訊。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_1970-01-01_

## 2014年6月版本更新

### Marketo即時Personalization API

Marketo即時Personalization (RTP) JavaScript API擴充了平台的自動個人化功能。 它允許事件追蹤和網頁的動態自訂。 在[這裡](/help/javascript-api/web-personalization.md)參閱完整檔案。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-06-20_

## 在Marketo中儲存外部索引鍵

在例如專屬CRM或資料倉儲等系統之間同步處理聯絡人和潛在客戶記錄時，通常需要將潛在客戶記錄與唯一的系統識別碼建立關聯。 在Marketo中，您可以使用唯一的系統識別碼，透過[syncMultipleLeads API](/help/soap-api/syncmultipleleads.md)呼叫建立或更新潛在客戶記錄。 若要完成此操作，您可以將唯一的系統識別碼（主索引鍵）儲存為Marketo中的外部索引鍵。 此欄位在Marketo中儲存外部索引鍵的名稱為foreignSysPersonId。 以下是三個需要注意的重要事項：

1. externalSysPersonId在Marketo的UI中不可見。 因此，最佳實務是也使用此值填入自訂屬性欄位。
1. foreignSysPersonId是潛在客戶所獨有的，但潛在客戶可以有一個以上的foreignSysPersonId。
1. 無法更新或刪除foreignSysPersonId，但可以重新指派給其他記錄。

我們說明如何呼叫[syncMultipleLeads API](/help/soap-api/syncmultipleleads.md)，將foreignSysPersonId值寫入Marketo中的兩個現有潛在客戶記錄。 **如何使用syncMultipleLeads API寫入foreignSysPersonId**&#x200B;您可以插入新的潛在客戶記錄並指定foreignSysPersonId。 您也可以指定Marketo ID和foreignSysPersonId，將其新增至現有的銷售機會。 我們將向您介紹後一種情況。 **要求syncMultipleLeads SOAP API呼叫的XML**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>b5e21953ae9f1b263e644da5eccce9ff33802513</requestSignature>
      <requestTimestamp>2013-08-01T18:22:30-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncMultipleLeads>
      <leadRecordList>
        <leadRecord>
          <leadId>1090240</leadId>
          <foreignSysPersonId>1224191</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1000</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1000</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
        <leadRecord>
          <leadId>1090239</leadId>
          <foreignSysPersonId>1224192</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1001</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1001</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
      </leadRecordList>
      <dedupEnabled>true</dedupEnabled>
    </ns1:paramsSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**回應XML for syncMultipleLeads SOAP API呼叫**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncMultipleLeads>
      <result>
        <syncStatusList>
          <syncStatus>
            <leadId>1090240</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
          <syncStatus>
            <leadId>1090239</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
        </syncStatusList>
      </result>
    </ns1:successSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**請參閱下方將輸出上述要求XML的範例Ruby程式。**

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
        :lead_record_list => {
                :lead_record => {
                        :lead_id => "1090240",
                        :foreign_sys_person_id => "1224191",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1000" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1000" }
                }
        },
        :lead_record! => {
                        :lead_id => "1090239",
                        :foreign_sys_person_id => "1224192",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1001" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1001" }
                }
        }
        },
    :dedup_enabled => "True"
}

response = client.call(:sync_multiple_leads, message: request)

puts response
```
 

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-06-27_

## 更新潛在客戶的電子郵件地址

假設使用者在您的網站上填寫Marketo表單。 會發生什麼事？ Marketo會對使用者進行Cookie，並將他們與其提供的電子郵件建立關聯。 如果使用者下次造訪您的網站時，再次填寫相同的表格並使用不同的電子郵件，該怎麼做？ 會發生什麼事？ Marketo將建立新的潛在客戶記錄，並覆寫使用者瀏覽器上的第一個Cookie。 使用者現在是Marketo中的新銷售機會/其他銷售機會。 我們為您說明在Marketo中更新潛在客戶電子郵件地址的四種方式，包括[syncLead API方法](/help/soap-api/synclead.md)、表單方法中的自訂欄位、Marketo UI以及匯入清單。 **透過syncLead API**&#x200B;您可以使用[syncLead API](/help/soap-api/synclead.md)，使用其Marketo ID和新的電子郵件地址來更新潛在客戶記錄。 要求`syncMultipleLeads` SOAP API呼叫的XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>bigcorp1_461839624B16E06BA2D663</mktowsUserId>
      <requestSignature>92f05a7be4838ae1c0e5aafe814891ee72968a08</requestSignature>
      <requestTimestamp>2013-07-31T12:38:47-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncLead>
      <leadRecord>
        <leadId>1090240</leadId>
        <Email>t@t.com</Email>
      </leadRecord>
      <returnLead>false</returnLead>
    </ns1:paramsSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

回應syncMultipleLeads SOAP API呼叫的XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncLead>
      <result>
        <leadId>1090240</leadId>
        <syncStatus>
          <leadId>1090240</leadId>
          <status>UPDATED</status>
          <error xsi:nil="true" />
        </syncStatus>
        <leadRecord xsi:nil="true" />
      </result>
    </ns1:successSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

請參閱下方將輸出上述請求XML的範例Ruby程式。

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
 :lead_record => {
  :Email => "<t@t.com>",
  :lead_id => "1090240",
 :return_lead => "false"
}

response = client.call(:sync_lead, message: request)

puts response
```

**透過表單中的自訂欄位**&#x200B;您可以在Marketo中為「新電子郵件地址」建立自訂欄位。 然後要求使用者填寫包含此新欄位的表單。 接著，在Marketo中建立程式，當新自訂欄位「新電子郵件地址」中發生變更時，以語彙基元`{{lead.newEmailAddress}}`變更系統電子郵件位址列位的資料值。 **透過Marketo UI**&#x200B;您可以透過Marketo UI手動更新潛在客戶的電子郵件地址。 以下是[說明文章](https://nation.marketo.com/)，說明如何執行此動作(需要登入Marketo才能檢視文章)。 **透過匯入清單**&#x200B;您可以使用[此處](https://nation.marketo.com/)描述的Marketo中的匯入清單方法(需要登入Marketo才能檢視文章)，來更新潛在客戶的電子郵件地址。  

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_1970-01-01_

## 儲存潛在客戶的第二個電子郵件地址

假設您想使用API變更Marketo中的潛在客戶分數。 這可以透過建立/更新潛在客戶端點與REST API一起使用。 如果您想要在潛在客戶記錄上儲存多個電子郵件，您需要建立自訂欄位，並將第二封電子郵件儲存於此處。 以下提供詳細資訊的說明文章：以下是Ruby中的程式碼範例，說明如何進行此呼叫。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-20_

## 在Marketo中建立自訂欄位，並透過AP更新此欄位

假設您有關於潛在客戶的其他資料，這些資料不符合標準Marketo欄位。 例如，此自訂欄位可能是第三方分數。 您可以在Marketo中建立自訂欄位，以取得協力廠商分數，然後透過Marketo [REST API](https://developer.adobe.com/marketo-apis/)或[SOAP API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activity-type-filters)更新此欄位的值。 我們會先說明如何在Marketo中建立自訂欄位，然後說明如何使用REST API更新此欄位。

### 如何在Marketo中建立自訂欄位

1. 在「管理員」底下，按一下「欄位管理」。
1. 按一下新增自訂欄位按鈕。
1. 選擇欄位型別。 這會變更其在Marketo的智慧清單和Forms中的呈現方式。
1. 輸入您要在Marketo中顯示的「名稱」。 挑選名稱和API名稱時請務必謹慎，因為重新命名欄位可能會很困難，並且在某些情況下是不可能的。
1. 您建立的自訂欄位現在可透過API存取。

### 如何使用REST API更新自訂欄位

在上一節中，我們使用資料型別字串建立了一個名為`myCustomField`的自訂欄位。 若要更新該欄位的值，我們使用名為「建立/更新銷售機會」的REST API端點。 向REST API提出請求之前，您需要先進行驗證。 這不在本文範圍之內，但Marketo開發人員網站[提供深入資訊](/help/rest-api/authentication.md)。

**端點**

`/rest/v1/leads.json`

**要求內文**

```json
{
   "action":"createOrUpdate",
   "input":[
      {
         "email":"<example@example.com>",
         "myCustomField":"examplestring"
      }
   ]
}
```

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-08-19_

## 整合Unbounce和Marketo

**注意：這是Fab Capodicasa的訪客部落格。 他是[Hoosh Marketing](http://hooshmarketing.com.au/)的Marketo認證顧問，此為Marketo LaunchPoint代理合作夥伴，專長於B2C。 過去13年，他同時在SaaS和行銷部門工作。 他的背景是結合核心IT、直銷和企業銷售。 Fab也是前Marketo員工。**

**概述**在本文中，我們將示範如何整合熱門登陸頁面工具Unbounce與Marketo。 我們會先說明如何將Marketo追蹤插入「彈回」，然後說明如何修改「彈回」表單，將資料直接插入Marketo。 整合彈回與Marketo的挑戰在於「彈回」不允許重新命名預設欄位（例如，first_name無法變更為FirstName）。 也不允許欄位標籤與欄位名稱不同。 這項整合涉及JavaScript，它會調整現有表單，使其與Marketo相容。 建議您至少具備JavaScript初學者層級和中級的Marketo知識，才能完成本文所述工作。
**第1部分：將Marketo追蹤程式碼新增至退信**若要讓Analytics和表單整合運作，必須將Marketo的Munchkin追蹤指令碼新增至退信頁面。 請依照下列步驟操作：從Marketo複製Munchkin程式碼：導覽至「管理員 — > Munchkin」，並複製JavaScript的「簡單」版本。 開啟「反彈」登陸頁面，然後按一下「JavaScript->新增JavaScript」 。  按一下「新增」，呼叫指令碼「Munchkin」，選取「在Body結尾標籤之前」，然後貼上Munchkin程式碼。 按一下完成按鈕。 針對未來的退信頁面，您需前往JavaScript並啟用我們建立的Munchkin指令碼。 不需要重新建立。
**第2部分：將「退信」表單轉換為Marketo表單**&#x200B;現在我們需要修改「退信」表單，新增一些隱藏欄位和JavaScript以允許您的「退信」登陸頁面直接將潛在客戶資訊提交至Marketo。 我們會先建立Marketo預留位置表單。 在Marketo中建立空白表單並核准。

這是Marketo中代表「反彈」表單的Proxy表單。 新增隱藏欄位至取消退回表單。 Marketo需要這些隱藏欄位來決定此表單提交將套用至哪個Marketo例項、哪個表單和使用者工作階段。 在「退回」中，按兩下以開啟您的表單。 新增名為`_mkt_trk`的隱藏欄位。 新增名為`formid`的第二個隱藏欄位。  233需要以您表單的id取代，您可在Marketo的Marketo表單內嵌程式碼中找到。 在Marketo中，開啟您的表單，選取「表單動作 — >內嵌程式碼」。 新增名為`returnurl`的隱藏欄位。 `http://hooshmarketing.com.au/thank-you`需要取代為後續追蹤URL，這是您希望在提交表單後重新導向使用者的URL。 例如，這可能是您的感謝頁面。
**第3部分：直接跳出表單至Marketo**&#x200B;後續追蹤URL是潛在客戶提交至Marketo後，您會被重新導向的頁面。 在「退回」中，請依照下列步驟操作：按一下您的表單。 修改表單確認區段。 變更確認以張貼表單資料至URL。 將URL設定為您想要的後續追蹤頁面。 `fpmarkets`需要取代為您的Marketo帳戶字串，可在Marketo的「管理員 — >登陸頁面」下找到。
**第4部分：將JavaScript新增至「取消退回」頁面**&#x200B;此JavaScript會將表單轉換為相容於Marketo，並將它提交至Marketo。 在「退回」中，請依照下列步驟操作：開啟「退回」登陸頁面，然後按一下「JavaScript->新增JavaScript」 。 按一下「新增」，呼叫指令碼「Marketo表單轉換」，選取「Before Body結束標籤」。 在下方貼上JavaScript程式碼：

```javascript
var MARKETO_MUNCHKIN_ID='614-CGT-700';
var MARKETO_ACCOUNT_STRING = 'fpmarkets';

var UNBOUNCE_MARKETO_FIELD_MAP = new Object();

//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
UNBOUNCE_MARKETO_FIELD_MAP['last_name'] = 'LastName';
UNBOUNCE_MARKETO_FIELD_MAP['phone_number'] = 'Phone';

function getMarketoField(k) {
    return UNBOUNCE_MARKETO_FIELD_MAP[k];
}


var formFields = [];
var hiddenClonedFields = [];
var firstForm = document.forms[0];

//Convert Unbounce form names to Marketo field names
for(i=0; i<firstForm.elements.length; i++){
 var formField = firstForm.elements[i];
 var newFieldName = getMarketoField(formField.name);

  if(newFieldName != undefined) {


    //save original field as hidden field
    var hiddenField = document.createElement("input");
    hiddenField.setAttribute("type", "hidden");
    hiddenField.setAttribute("name", formField.name);
    hiddenField.setAttribute("id", formField.id);
    hiddenClonedFields.push(hiddenField);

    //change original field
    console.log ( 'Changed form field name: ' + formField.name + '=>' + newFieldName );
    formField.name = newFieldName;
    formField.id = newFieldName;
    formFields.push(formField);


  } else {
    console.log ( 'Couldn't map:' + formField.name );
  }
}

//Add hidden cloned Unbounce fields to form
//for Unbounce validation
for(i=0; i<hiddenClonedFields.length; i++){
    firstForm.appendChild(hiddenClonedFields[i]);
    formFields[i].onchange = (function(hf) {
        return function(event) {
            hf.value = event.target.value;
          console.log('Changed hidden cloned field:' + hf.name + '=>' + hf.value);
        };
    }(hiddenClonedFields[i]));
    console.log ( 'Added cloned field: ' + hiddenClonedFields[i].name );
}


//Add MunchkinId to form
var input = document.createElement("input");
input.setAttribute("type", "hidden");
input.setAttribute("name", "munchkinId");
input.setAttribute("value", MARKETO_MUNCHKIN_ID);
firstForm.appendChild(input);
console.log('Added hidden field:' + input.name + '=' + input.value);
```

如果您有自訂欄位的API名稱與「彈回」不相容，您可以將這些欄位新增到JavaScript中的對應中。 例如，如果您在Marketo中的其中一個自訂欄位是`Comments_c`，但您想要欄位標籤顯示為註解，則「取消退回」不會讓您將欄位名稱變更為`Comments_c`。

```
//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['comments'] = 'Comments_c';
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
```

_comments是彈回中的欄位名稱。 _Comments_c_是Marketo中的欄位名稱。 針對未來的反彈頁面，您只需前往JavaScript並啟用我們建立的Munchkin指令碼即可。 不需要重新建立。
**第5部分：測試**&#x200B;最後一個步驟是測試此表單整合正在運作。 在Marketo中建立在Marketo表單上啟用的觸發器，並填寫和確保銷售機會正確插入Marketo。 提交表單後，頁面應該會將您重新導向至後續追蹤URL。

由_在&#x200B;_2014-08-04_&#x200B;發佈

## 2014年7月版本更新

### Munchkin更新

Munchkin的新版本是141。 142版不受支援，並將於2011年9月初移除。 在142版中，有些公開存取的功能未在開發人員網站上記錄。 這些未記錄的功能無法再公開使用。 我們將繼續長期支援開發人員網站上已記錄的功能。

### RTP更新

RTP API有一個新函式，稱為「取得訪客資料」。 此RTP API呼叫可讓您取得即時訪客資料，例如組織、產業、地點和區段代碼相符。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-07-30_

## 在單一頁面上使用多個Munchkin追蹤程式碼

假設您有多個Marketo執行個體，且想傳送頁面瀏覽數或點選連結之類的網路追蹤事件給這些多個執行個體，在Munchkin中可執行此動作。 Marketo會依網域（例如「marketo.com」）追蹤您網站的訪客。 如果您在不同於主要網域的網域（例如「company.com」）上託管此Munchkin指令碼，這些訪客會顯示為匿名潛在客戶，直到他們在該其他網域上填寫表單為止。 若要完成此作業，請將`altIds`引數新增至您的`Munchkin.init`呼叫。 altIds引數包含其他Munchkin ID的陣列，這些ID應傳送網頁事件。 使用下列範例，將醒目提示的Munchkin ID （XXX-XXX-XXX、YYYY-YYY和ZZZ-ZZZ-ZZZ）取代為應傳送追蹤資訊的每個Marketo執行個體中的Munchkin ID。

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"></script>
<script>Munchkin.init('XXX-XXX-XXX', { altIds:['YYY-YYY-YYY', 'ZZZ-ZZZ-ZZZ'] });</script>
```

如需Munchkin初始化引數的詳細資訊，請參閱[此檔案](/help/javascript-api/configuration.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-08-08_

## 將Munchkin與Google Tag Manager整合

Google Tag Manger可讓您將標籤新增至您的網站。 您可以將[Google標籤管理員](https://marketingplatform.google.com/about/tag-manager/)放在您的網站上，然後透過Munchkin標籤管理員的UI新增如[Munchkin](/help/javascript-api/lead-tracking.md)的標籤，而不必手動將每個追蹤指令碼(例如Google)新增到您網站的原始程式碼。 在本文章中，我們會先說明如何在Marketo中產生Munchkin追蹤程式碼，然後說明如何將此Munchkin追蹤程式碼新增至Google Tag Manager。

### 如何產生Munchkin追蹤代碼

1. 按一下應用程式右上角的&#x200B;**管理員**。
1. 按一下左側樹狀結構中的&#x200B;**Munchkin**。
1. 選取&#x200B;**非同步**&#x200B;以追蹤程式碼型別。
1. 按一下並複製JavaScript追蹤程式碼。

**如何將Munchkin追蹤程式碼新增至Google Tag Manager**

1. 登入您的Google標籤管理員帳戶並&#x200B;**新增標籤。**
1. 建立新的&#x200B;**自訂HTML標籤**。
1. 將您的Munchkin程式碼複製並貼到&#x200B;**HTML**&#x200B;欄位中，然後按一下&#x200B;**繼續**。
1. 選取&#x200B;**在所有頁面上引發**，然後按一下&#x200B;**建立標籤。**&#x200B;注意：如果您的網站流量極高，可以使用&#x200B;**在某些頁面上觸發**&#x200B;來排除網站的區段。
1. 按一下「儲存」 ，然後確認Munchkin追蹤程式碼正在您的網站上載入。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-08-05_

## 提交表單後隱藏Lightbox

### 概觀

提交表單時，Marketo產生的目前Lightbox內嵌程式碼不會消失。 它會在表單提交後重新載入頁面，表單將再次出現。 如果您要在提交表單後建立隱藏的燈箱，請遵循下列步驟。

### 操作指南

1. 在Marketo中建立表單後，產生內嵌程式碼。 若要這麼做，請按一下[Form Actions]，然後按一下[Lightbox]作為程式碼型別。 將此程式碼複製並貼到文字編輯器中，以便您能在下一個步驟中進行編輯。
1. 將內嵌程式碼中「(form)」之後的所有程式碼取代為下列程式碼：

```javascript
{
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

在步驟2之後，完成的版本應該看起來像下面的程式碼。 程式碼現在已準備好在您的網站上使用。

```javascript
<script src="http://app-sj04.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_0000"></form>
<script>MktoForms2.loadForm("http://app-sj04.marketo.com", "AAA-BBB-CCC", 0000, function (form){
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-08-21_

## Marketo REST API快速入門手冊

本指南會示範如何在10分鐘內首次呼叫Marketo REST API。 我們說明如何使用[依ID取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) REST API端點擷取單一銷售機會。 為此，我們會逐步引導您完成驗證程式以產生存取權杖，您會使用它向[依ID取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)發出HTTP GET請求。 接著，我們提供您要求退貨銷售機會資訊的程式碼，格式為JSON。

### 如何產生驗證Token

>[!NOTE]
> 自2025年6月起，不再支援驗證Token。 您必須使用Authentication標題。
>


Marketo中的自訂服務可讓您說明和定義應用程式可存取的資料。 您必須以Marketo管理員身分登入，才能建立自訂服務，並將該服務與單一「僅限API」使用者建立關聯。

1. 導覽至Marketo應用程式的管理區域。
1. 按一下左側面板上的「使用者和角色」節點。
1. 建立新角色。 按一下「存取API」以顯示角色許可權清單。 現在，向下捲動並僅選取您需要的許可權。 在此情況下，我們只需選取「唯讀銷售機會」許可權。
1. 下一步是建立僅限API的使用者，並將其與您在上一步中建立的API角色建立關聯。 您可以在使用者建立時勾選僅限API的使用者核取方塊，以達成此目的。
1. 需要自訂服務來唯一識別您的使用者端應用程式。 若要建立自訂應用程式，請前往「管理員> LaunchPoint」畫面並建立新服務。
1. 提供顯示名稱、選擇「自訂」服務型別、提供說明，以及在步驟1中建立的使用者電子郵件地址。 我們建議您使用能代表此自訂REST API服務之公司或目的的描述性顯示名稱。
1. 按一下網格上的「檢視詳細資料」連結，即可取得使用者端ID和使用者端密碼。 您的使用者端應用程式可使用使用者端ID和使用者端密碼來產生存取權杖。
1. 將您的驗證Token複製並貼到文字編輯器中。 您的驗證Token看起來類似於以下範例：

`cdf01657-110d-4155-99a7-f986b2ff13a0:int`

### 如何判斷端點URL

向Marketo API提出請求時，您需要在端點URL中指定Marketo執行個體。 Marketo REST API的所有非大量API請求將遵循以下格式：

`<REST API Endpoint URL>/rest/`

您可以在「Marketo管理員>網站服務」面板中找到REST API端點URL。 您的Marketo端點URL結構應類似以下範例：

`https://100-AEK-913.mktorest.com/rest/v1/lead/{id}.json`

**注意：大量API URL的前置詞不是&#39;/rest/&#39;。 針對大量API，您應該只使用主機，並附加適當的路徑，如下所示：**

`https://100-AEK-913.mktorest.com/bulk/v1/leads/export/create.json`

### 如何使用驗證Token來呼叫Get Lead by Id AP

在前幾節中，我們產生驗證Token並找到端點URL。 我們現在會向名稱為[Get Lead by ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)的REST API端點提出要求。 向Marketo REST API提出請求的最簡單方法是，將URL貼入網頁瀏覽器位址列。 請遵循以下格式：

`https://<REST API Endpoint URL for your Marketo instance>/rest/v1/<API that you are calling>?access_token=<access_token>`

### 範例

`https://100-AEK-913.mktorest.com/rest/v1/lead/318581.json?access_token=cdf01657-110d-4155-99a7-f986b2ff13a0:int`

如果您的呼叫成功，則會傳回JSON，格式如下：

```json
{
    "requestId": "d82a#14e26755a9c",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2015-06-11T23:15:23Z",
            "lastName": "Doe",
            "email": "<jdoe@marketo.com>",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "John"
        }
    ],
    "success": true
}
```

如果您有興趣進一步瞭解Marketo REST API，這裡是[良好的起點](https://developer.adobe.com/marketo-apis/)。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-09-04_

## 如何刪除Marketo追蹤Cookie

問題：「我們已在網站上設定表格，讓銷售團隊取消訂閱口頭要求從任何電子郵件訊息中移除的個人。 我們的發現是，當他們在電子郵件中輸入，並提交他們已使用該電子郵件地址編碼，並開始在我們的網站上接收其活動的警示時。 我們目前的表單是內嵌表單。 有人想出辦法停用內嵌表單的烹飪追蹤功能嗎？我知道如何使用Marketo登陸頁面來達成此目的。」 我們可以這樣做。 若要實作，請加快Cookie有效期的速度。 表單建立Cookie後，您可以強制其立即過期。 由於Cookie已過期，瀏覽器會自動將其刪除。 若要開始此程式，我們將使用原生表單功能，在名為`deleteCookie`的函式下呼叫。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-08-26_

## 將Marketo登陸頁面與Wordpress整合

假設您的網站是使用Wordpress建置，而您想要在其中一個頁面上內嵌Marketo登陸頁面。 這是使用iframe時的可能方式。 iframe可讓您將頁面內嵌在其他頁面中。 這篇文章說明如何將Marketo登入頁面內嵌至Wordpress頁面。 **選擇Wordpress外掛程式** Wordpress這裡有Wordpress外掛程式可供您使用： `http://wordpress.org/plugins/iframe/`這裡有幾個使用此方法的專業和竅門： `http://www.elixiter.com/marketo-landing-page-and-form-hosting-options/`Murtza貼文精彩絕倫！ Colby，iframe會保留提交表單後您被重新導向至PDF的部分。 我們在網站上使用iframe已有很長時間。 Murtza貼文好看！ Colby，iframe會保留提交表單後您被重新導向至PDF的部分。 我們在網站上使用iframe已有很長時間。 請注意，如果您想要將URL引數從主要URL傳遞至iframe，您需要進行一些編碼。 此外，請確認您的Google Analytics已正確設定。 您不希望在每次使用iframe造訪頁面時，頁面檢視次數計算兩次。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_1970-01-01_

## 以最佳方式整合Marketo登陸頁面

[最佳化](https://www.optimizely.com/)讓您能夠在您的網站上進行A/B測試、多頁面測試和多變數測試。 您可以最佳化地搭配Marketo登陸頁面使用。 以下是其操作方式：

1. 尋找並複製您的最佳化程式碼片段。**以最佳方式前往您的控制面板，然後按一下「專案程式碼」連結。 複製快顯視窗中顯示的程式碼行。
1. 登入Marketo，並選取您的登入頁面範本。 然後按一下「編輯草稿」。**
1. 按一下「登陸頁面動作」 。 然後按一下「編輯頁面中繼標籤」**
1. 將您的最佳化程式碼片段貼到自訂HEAD HTML區段，然後按一下儲存。
1. 測試您的登入頁面，確認「最佳化」程式碼片段正常運作

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-18_

## 透過Marketo REST API依全名搜尋

**問題：**是否只要使用潛在客戶的全名，就能使用Marketo API來查詢潛在客戶？
**答案：**&#x200B;無法直接進行。 不過，下述的因應措施可讓您進行此作業。

1. 在Marketo中建立名為「Fullname」的自訂欄位。
1. 使用[getMultipleLeads](/help/soap-api/getmultipleleads.md) SOAP API或[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)取得多個銷售機會，以查詢您的銷售機會資料庫。 在向REST或SOAP API提出的請求中，將您的名字和姓氏加入為屬性。
1. 查詢潛在客戶資料庫之後，請為每個潛在客戶串連「名字」和「姓氏」，並將此資料儲存在「全名」欄中。 1.使用[syncMultipleLeads](/help/soap-api/syncmultipleleads.md) SOAP API將此資料推送到「Fullname」自訂欄位。 或者，您可以使用[Import Lead](/help/rest-api/leads.md) API，或使用Marketo UI匯入CSV或XLS。
1. 現在，您可以使用[依篩選型別API取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)以全名查詢，以搜尋此自訂欄位。 指定「Fullname」為「filterType」，「filterValue」為「Joe Johnson」，且「依篩選型別REST API呼叫取得多個銷售機會」。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-09_

## 刪除Marketo追蹤Cookie

只有在需要的效果是完全移除Cookie時，才應該使用此方法。

此程式碼範例可用於在提交Marketo表單後，從使用者的瀏覽器中刪除Marketo追蹤Cookie。 它可在使用者提交表單後呼叫`deleteMarketoCookie`方法來運作。 此方法會將到期日設定為過去的日期，讓Cookie到期。 瀏覽器的預設行為是接著刪除此Cookie，因為它已過期。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-09_

## 在表單填寫上限制免費電子郵件網域

假設您想要限制網站訪客，使其無法透過免費電子郵件網域（例如Gmail或Yahoo）提交表單。 若要完成此操作，請在頁面原始碼中加入Marketo表單的以下指令碼。 它會檢查使用者是否輸入了非商業電子郵件（Gmail、Hotmail等），並在使用者輸入時防止Marketo表單提交。 您可以修改第9行以包含您想要限制的網域，以擴充封鎖的電子郵件網域清單。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-09_

## 對無法透過API存取的欄位進行偵錯

如果您要前往此欄位<https://nation.marketo.com/>，當我們嘗試使用SOAP API更新AnnualRevenue欄位時，回應指出記錄已更新，但AnnualRevenue欄位不會保留變更。 如果我們嘗試使用Lead資料庫手動更新欄位，對此欄位的變更也不會持續存在。 檢查該欄位是否在Admin中遭到封鎖。 有時候，另一個可能發生的情況是，這可能是從SFDC同步的「帳戶欄位」。 我們預設會封鎖這些欄位的變更，因為SFDC在許多情況下是這些欄位的記錄系統。 1)建立時間2) Marketo SFDC ID 3) Marketo唯一代碼4) SFDC Type 5)更新時間6)緊急程度7)優先順序8)建立銷售日期

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_1970-01-01_

## 新增自訂程式碼至Marketo登陸頁面

假設您想要將協力廠商追蹤指令碼(例如Google Analytics)新增至Marketo登陸頁面。 這可透過Marketo UI進行。 一般而言，您可以依照下列指示，將任何自訂程式碼(HTML、CSS、JavaScript)新增至Marketo登陸頁面。

1. 選取您的登入頁面，然後按一下編輯草稿。
1. 在HTML元素中拖曳。
1. 輸入您的自訂程式碼，然後按一下「儲存」。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-10_

## 伺服器端表單貼文

`https://community.marketo.com/MarketoResource?id=kA650000000GsXXCA0`

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-11_

## 從Forms 2.0提交中清除Marketo追蹤Cookie

### 概觀

Forms 1.0包含Munchkin追蹤Cookie的值，做為DOM中的欄位。 此資訊已連同所有其他輸入一起提交。 [Forms 2.0](/help/javascript-api/forms-api-reference.md)會省略此欄位，並在提交時動態填入值，而非載入表單時。 雖然這通常是可接受的作法，但確實會建立使用案例的類別，需要清除追蹤Cookie以避免非預期的追蹤和預先填滿。 例如，這可能會發生在商展中，一位Marketo客戶在同一部裝置上使用相同表單，並從多個人那裡取得聯絡資訊。 以下程式碼片段可讓您在提交表單時清除Cookie值，而不必從使用者的瀏覽器中刪除Cookie本身。

### 程式碼範例

此程式碼片段預計頁面上會載入單一表單。 如果有多個表單，您應該使用[loadForm或getForm方法](/help/javascript-api/forms-api-reference.md)來實作回呼。

```javascript
<script>
//add a callback to the first ready form on the page
MktoForms2.whenReady( function(form){
 //add the tracking field to be submitted
        form.addHiddenFields({"_mkt_trk":""});
        //clear the value during the onSubmit event to prevent tracking association
 form.onSubmit( function(form){
  form.vals({"_mkt_trk":""});
 })
})
</script>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2014-09-11_

## 2014年9月版本更新

### REST API的更新

已在[取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) API中新增選用欄位值，這會傳回與銷售機會記錄相關聯的Munchkin Cookie值。 只需新增「？fields=cookie」至請求即可。    

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-16_

## 將位置資料從RTP API新增至Marketo表單填寫

**您需要使用中的RTP和MLM訂閱才能實作這篇部落格中描述的使用案例。**
使用[RTP JavaScript API](/help/javascript-api/web-personalization.md)和[Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md)，您可以從RTP提取推斷的位置資料，並透過表單填寫將其推送到Marketo。 這可讓您檢視使用者在最近表單活動期間的推斷位置（根據IP位址）。 若要開始使用，您需要在Marketo中建立三個自訂字串欄位。 如果您的CRM與Marketo有原生整合，您可以透過您的CRM執行此作業，或是從Marketo管理區段的「欄位管理」功能表執行。 我建議將這些欄位命名為「最新的國家/地區」、「最新的州」和「最近的城市」。 我們使用這個命名慣例來繼續這個部落格。 這些欄位的API名稱為「mostRecentCountry」、「mostRecentState」和「mostRecentCity」。 為了擷取位置資料，我們使用[RTP方法取得訪客的位置資料](/help/javascript-api/web-personalization.md)，然後使用Marketo Forms 2.0中的[addHiddenFields和vals方法](/help/javascript-api/forms-api-reference.md)將其傳遞至表單。在您的頁面上，新增您的RTP JS標籤和Marketo表單。 接著，在下方加入指令碼。 如果您使用與上述命名慣例不同的命名慣例，則需要變更範常式式碼中目標表單欄位的名稱。

```javascript
<script>
//modify the form and grab the user
MktoForms2.whenReady( function(form) {
        //add the hidden fields to the form
 form.addHiddenFields({
  "mostRecentCountry":"",
  "mostRecentState":"",
  "mostRecentCity":""});
 //Grab the visitor data, a JS object with it is passed in the callback function of the third argument
 rtp('get','visitor',function(visitor){
  //add the desired info from the visitor object to the form fields
  form.vals({
   "mostRecentCountry":visitor.results.location.country,
   "mostRecentCity":visitor.results.location.city,
   "mostRecentState":visitor.results.location.state});
  }
  );
 });
</script>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2014-09-17_

## 封鎖Marketo登陸頁面的編目和搜尋索引

假設您想要封鎖Marketo登陸頁面，使其無法由Google等搜尋引擎進行編目及顯示在結果中。 以下是其操作方式：

1. 登入Marketo，並選取您的登陸頁面。 然後按一下「編輯草稿」。
1. 按一下「登陸頁面動作」 。 然後按一下「編輯頁面中繼標籤」
1. 將Robots欄位變更為No Index， No Follow。 然後按一下「儲存」。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-19_

## Marketo REST與SOAP API常見問題集

**更新日期： 2016年3月**&#x200B;以下是有關Marketo [REST](/help/rest-api/rest-api.md)和[SOAP](/help/soap-api/soap-api.md) API最常見問題的解答。 **問：Marketo REST與SOAP API之間有何主要差異？** A：雖然透過REST和SOAP API推送/提取特定資料的功能大多重疊，但某些功能僅存在於REST或SOAP API中。 就效能而言，REST API的[輸送量](https://en.wikipedia.org/wiki/Throughput)優於SOAP API。 就驗證模型而言，REST API的驗證模型會使用即將到期的代號。 我們的REST API也可讓您存取Marketo [資產](https://developer.adobe.com/marketo-apis/api/asset/)。   **問：REST API中有哪些功能在SOAP API中無法使用？** A： [清單API清單](/help/rest-api/list-of-standard-fields.md)、[從清單API移除銷售機會](/help/rest-api/lead-database.md)、[使用情況API](/help/rest-api/rest-api.md)以及[錯誤API](/help/rest-api/rest-api.md)僅適用於REST API。 **問：有增加SOAP API可用API數量的計畫嗎？** A：否。 **問：有增加REST API可用API數量的計畫嗎？** A：是。 REST目前是Marketo API開發的主要焦點。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-20_

## 伺服器端表單貼文

**注意：這是非公開且不受支援的API，不受支援，而且其行為隨時可能變更**&#x200B;如果您在網站上使用自己的表格，您仍可使用伺服器端貼文將此資料傳送到Marketo。 此方法的優點在於，您可以保留現有的表單和應用程式邏輯，但仍可在Marketo中使用實際的表單貼文。 這可為Marketo使用者提供「填寫表單」事件，其可用於觸發自動化流程或進行細分。 **注意：單一IP位址的速率限製為每分鐘30個伺服器端表單貼文。**&#x200B;在每個指令碼或程式語言中，都有不同的選項來執行伺服器端表單提交，而且它們可能有不同的物件/方法可用來進行Post呼叫。 例如，在PHP中，許多人使用cURL庫。 在所有情況下，您都會將名稱 — 值配對張貼至指定的URL。 名稱必須與Marketo欄位的API名稱相同。 此外，還需要包含一些系統欄位，才能正確擷取表單提交。

1. 建立表單。 第一步是在Marketo中建立表單，或使用您要提交的現有表單。 表單名稱必須是描述性的，但實際上不需要任何表單欄位。 如果您建立新表單，只需輸入名稱，然後取消勾選「開啟表單編輯器」方塊，您就完成了。
1. 尋找表單ID。 在Marketo UI中，選取表單並檢視URL：其格式應為`https://app-x.marketo.com/#FO8B2ZN12`。 在#符號後面，檢視「FO」後面緊鄰的數字，以尋找表單ID。 在此案例中，表單ID為8。 在某些情況下，您的第一個表單可能會以1001編號，並且從那裡開始計數。 表單ID為變數，因此您可以觸發不同表單的提交。
1. 取得您的Marketo帳戶ID。 前往「管理員> Munchkin」並複製Munchkin帳戶ID，其格式為000-AAA-000。您需要此項，才能將表單提交至正確的Marketo例項。
1. 決定POST URL。 在Marketo使用者介面中，請注意位置列中的網域，格式通常為`<http://app-x.marketo.com/>`。 捨棄斜線後的任何專案，然後附加「index.php/leadCapture/save」以取得完整格式的POST URL。 注意1：區分大小寫。 注意2： Marketo沙箱可能與您的生產Marketo系統具有不同的網域。 範例URL為： `http://app-x.marketo.com/index.php/leadCapture/save`您也可以使用HTTPS而非HTTP （請勿使用CNAME，因為它會產生安全性例外狀況）。
1. 尋找表單欄位名稱**前往「管理員>欄位管理」，然後按一下「匯出欄位名稱」按鈕，下載包含API欄位名稱的試算表。 使用API名稱作為名稱 — 值組中的名稱。
1. 決定要張貼的欄位。 您可以在表單提交中包含任何Marketo銷售機會欄位。 請注意，欄位名稱會區分大小寫。 除了您想要提交的欄位之外，還有兩個必填欄位和兩個建議欄位：表單上的必填欄位： (1) `munchkinId` — 此欄位用於您的Munchkin帳戶ID (2) `formid` — 此欄位會指出Marketo中已提交的表單表單表單上的建議欄位： (1)電子郵件 — 此欄位用作重複資料刪除的主要索引鍵。 如果Marketo在Marketo資料庫中找到相符的電子郵件地址，它會更新現有記錄，否則會建立新記錄。 如果有多個相符專案，它會更新最近更新的記錄(2) `_mkt_trk` — 此欄位會包含Cookie資訊，因此您可以追蹤個人的網頁瀏覽次數。 如果您的表單頁面上有Munchkin，Munchkin會自動在此隱藏的表單欄位中輸入值。 如果沒有，請從Cookie中以相同的名稱讀取，並在此欄位中傳遞給Marketo。 注意：Marketo表單的POST內文必須經過URL編碼。
1. 請參閱回應**對表單貼文的回應將是一個HTTP 302重新導向程式碼。 在某些系統中，這會顯示為錯誤。 但是，在此情況下，這表示已成功建立或更新Lead。 如果出現錯誤，您會收到4xx或5xx錯誤代碼。

以下是有關將此技巧用於取消訂閱案例[的](https://nation.marketo.com:443/t5/product-blogs/how-to-build-an-external-subscription-center/ba-p/242185)文章[，作者：Justin Cooperman，資深產品經理]

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-07_

## 尋找特定日期範圍內更新的銷售機會

假設您想要尋找透過[Marketo API](/help/soap-api/soap-api.md)在特定日期更新的潛在客戶。 使用[getMultipleLeads SOAP API](/help/soap-api/getmultipleleads.md)即可實現此目的。 此方法會針對您請求的日期範圍，傳回Marketo中發生資料值變更或新活動的任何銷售機會。 對於`leadSelector`，您可以指定`LastUpdateAtSelector`。 然後，您會定義具有`oldestUpdatedAt`和`latestUpdatedAt`時間範圍的日期範圍。 請參閱以下的請求XML範例，此範例說明如何尋找在2014年6月6日凌晨12點(PST)至2011年6月7日凌晨12點(PST)之間更新的銷售機會。 注意：日期範圍不得超過30天。

**範例要求XML以尋找於日期**&#x200B;更新的潛在客戶

```xml
<soapenv:Envelope xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
<soapenv:Header>
<mkt:AuthenticationHeader>
<mktowsUserId>REPLACE</mktowsUserId>
<requestSignature>REPLACE</requestSignature>
<requestTimestamp>2014-10-23T12:19:51-07:00</requestTimestamp>
</mkt:AuthenticationHeader>
</soapenv:Header>
<soapenv:Body>
<mkt:paramsGetMultipleLeads>
<leadSelector xsi:type="mkt:LastUpdateAtSelector">
<oldestUpdatedAt>2014-06-06T00:00:00-08:00</oldestUpdatedAt>
<latestUpdatedAt>2014-06-07T00:00:00-08:00</latestUpdatedAt>
</leadSelector>
</mkt:paramsGetMultipleLeads>
</soapenv:Body>
</soapenv:Envelope>
```

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-24_

## 新增動態內容至電子郵件

假設您每天傳送電子郵件，而且您想要在電子郵件範本中自動包含當天的日期。 若要這麼做，您可以在Marketo中使用代號和電子郵件指令碼。

1. 建立權杖。**導覽至您要使用代號的程式。 按一下「我的Token」 。
1. 連按兩下「電子郵件指令碼」。 然後命名此Token。 然後按一下「編輯」。
1. 在此視窗中貼上下方的電子郵件指令碼。 然後按一下「儲存」。

## 存取Velocity的行事曆物件

`set($x = $date.calendar)`

## 格式化日期

`set($current_date = $date.format('dd-MM-yyyy', $x.getTime()))`

## 傳回今天的日期

`$current_date`

1. 參考電子郵件範本中的權杖。**記下Token的名稱。 導覽至您的電子郵件草稿。 包含權杖。  傳送電子郵件時，會填入權杖的值。 如需詳細資訊，請參閱[電子郵件指令碼開發人員檔案](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/email-scripting)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-22_

## Bash安全性建議

Marketo已對Bash漏洞(也稱為[Shellshock (CVE-2014-6271)](https://nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-6271))進行徹底的調查，並得出結論，我們不易遭受這些攻擊。 此外，我們已採取預防動作，將軟體更新至最新版本，以確保符合[CERT建議](https://www.cisa.gov/news-events/alerts/2014/09/25/gnu-bourne-again-shell-bash-shellshock-vulnerability-cve-2014-6271)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-26_

## 如何更新SOAP API認證

最佳實務是定期更新您的[SOAP API](/help/soap-api/soap-api.md)認證。 目前無法透過Marketo API以程式設計方式執行此工作。 下列指示會示範如何透過Marketo UI更新SOAP API認證。

1. 移至「管理員」區段，然後按一下「網站服務」。
1. 設定至少10個字元的加密金鑰，按一下「儲存變更」。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-09-29_

## 如何清除Marketo資料庫

**注意：這是訪客部落格。 Josh Hill是行銷自動化機構Perkuto的Marketo業務負責人。 Josh在行銷、銷售與技術的交匯處工作，提供創收系統。 他在[MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/)撰寫有關行銷自動化和需求產生的相關文章。**&#x200B;保持您的Marketo資料庫乾淨，是管理這個強大系統的重要部分。 如果您的Marketo資料不新鮮，或您的CRM資料不新鮮，則您擔任行銷經理的難度會大幅增加，因為您可以解釋促銷活動傳送給錯誤人員或您的報告為「方向性」報告的原因。 [2011年Gartner的研究指出](https://www.data.com/export/sites/data/common/assets/pdf/DS_Gartner.pdf)不良的資料品質使勞動生產力降低20%。 這相當於因為您必須修正資料而浪費的20%時間（每週8小時或每週1天）。 但與因為您的目標定位錯誤而損失的收入相比，這種損失相形見絀。 在保持資料整潔方面投資的前五個理由如下： — 可以更妥善地劃分潛在客戶和客戶，讓您在適當的時間將訊息聚焦於適當的人。 這20%的優惠券應該只提供給新的潛在客戶，而不是您最好的客戶，對嗎？  — 避免重複傳送電子郵件。 儘管採取了各種預防措施，仍可能向同一潛在客戶傳送多封電子郵件，這通常是因為不會合併重複記錄。  — 向老闆提供準確的報告。 由於CMO在收入表格中佔有一席之地，因此您必須擁有準確、一致且可重複的報告……否則您將無法再成為收入行銷人員。  — 如果您在銷售洽談期間將錯誤的行銷資料傳送給主要潛在客戶，會損害未決交易。

如果您的資料庫未在生命週期階段提供這些詳細資訊，可能會造成一兩筆交易流產。  — 移除不良或舊的銷售機會，讓銷售機會保持在定價臨界值以下。 關於不良資料的成本，已經撰寫了大量文章。 您最直接的問題是，太多的不良、重複或舊的潛在客戶正在將您推向您的Marketo或Salesforce定價層級，這可能會導致每年產生數千項費用。 那麼，如何保持資料庫整潔？ 您可以遵循這些資料清潔原則，但您如何在Marketo中獲得這些原則？ 我對您的系統做了一些假設： — 您有一個標準的Marketo系統。  — 您有一般的銷售機會 — 聯絡人 — 客戶Salesforce設定。  — 您正在系統之間同步所有記錄。  — 您沒有使用刻意的重複專案。

尋找要修正的正確銷售機會**讓我們先尋找潛在問題的銷售機會。 對於每個使用者端，我都會瀏覽一系列智慧清單，以判斷其資料庫的健全狀態。 我建議您每月都這樣做。 智慧列示開始運作後，每個月最多只需15分鐘即可完成。 這是展示您工作和Marketo投資報酬率的絕佳方式。 建立表格以瞭解對資料庫的總計影響。 以下範例包含我要尋找的條件，因此請務必加入對貴公司重要的其他欄位。 僅依Marketo、SFDC銷售機會和SFDC聯絡人劃分每個群組。

使用自動化來更正資料值：使用自動化規則來更正常見的拼字錯誤或遺漏資料可追溯到幾十年前。 在1960年代，傳送直接郵件時資料品質不佳，可能會造成數千美元的浪費。 今天，資料品質錯誤毀壞聲譽的速度比錯誤導向的郵件更快。 電子郵件信譽、語言選擇和客戶體驗很重要，因為錯誤可能會在幾分鐘內公開傳播，所以它們更重要。 透過自動化資料清理來儲存貴公司的聲譽。 這些資料管理流程是我在Marketo中設定的第一批專案之一。 大多數公司都設定了類似的流程： — 國家/地區修正者（雖然您應該遵循原則1才不需要此項） — 國家/地區修正者或對應工具 — 如果您有國家/地區和推斷國家/地區，通常會有幫助。  — 員工人數至員工範圍。  — 不良銷售機會Source到良好銷售機會Source — 電子郵件無效到電子郵件如果電子郵件變更即為良好。  — 將舊欄位值轉換為新欄位（完整到空白）。 例如，此流程會根據員工編號來調整「員工範圍」。

資料附加工具填寫空白欄位對於更妥善地分段資料庫至關重要。 潛在客戶不一定能在表單上填寫正確的產業或功能或標題。 有時候，您會擁有舊系統的舊資料。 資料匱乏意味著，您必須將電子郵件傳送給較少的人，或可能是錯誤的人。 若要解決此問題，您需要資料附加工具。

選項1：自行填滿。 您可以插入資料以回填空白欄位。 也許您有SIC代碼，而不是產業名稱或年度收入vs.年度收入範圍。 Marketo可輕鬆自動化這些修正。

選項2：透過LaunchPoint尋找資料附加/擴充廠商在Launchpoint[中有](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)個廠商，例如NetProspex和ReachForce，可以協助您擴充潛在客戶資料。 有些客戶會要求您提供資料表，然後他們清理並傳回。 更好的選項是Marketo或Salesforce中的自動化工具，它會檢查您想要的欄位，然後推回正確的資料。 大多數廠商都使用[Marketo API或Webhook](/help/home.md)來達成此目的。

選項3：使用Marketo API更新銷售機會您可以使用Marketo API識別需要清除的銷售機會，然後透過API更新他們。 [依篩選型別REST API取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)是從Marketo中提取符合特定條件的資料的良好起點。 若要更新銷售機會，請檢視[建立/更新銷售機會REST API](/help/rest-api/leads.md)。

或者，您可以設定[Marketo Webhook](/help/webhooks/webhooks.md)，通知外部系統已發生特定事件，例如表單填寫。 然後，您可以使用值來回應，以更新銷售機會。

您應該自動化什麼？ 我在步驟1中提供了一些建議。 但是，如果我們想要清理更多資料庫，除了修正資料值之外，還需要做更多工作。  — 競爭者黑名單：請確定您正在自動收集競爭者並加入黑名單。 如果您有競爭者合作夥伴，最好將其正確標示，並放入清單中以便可能隱藏。   — 多個硬跳出：絕對會自動化此流程。 如果潛在客戶在30天內硬退信超過兩次，則將其設定為「已暫停」或「無效」。 然後每個月一次檢視問題是否為錯字或其他問題。  - 30天內多次軟退信：將它們設定為30天內的Marketing Suspended=TRUE 。   — 垃圾郵件陷阱暫停/刪除：如果您的產品意味著人們使用可能的垃圾郵件陷阱地址，請小心。 檢視垃圾郵件陷阱清單。 智慧清單。

**不要自動執行的動作**&#x200B;永遠不要自動刪除銷售機會，因為意外大量刪除錯誤記錄的風險太大。 請改為每月審視一次標示為「垃圾桶」的銷售機會。 但如果您想要刪除已清除的銷售機會，請執行類似的流程，並等待30天。

警告：使用自動化是節省時間並保持資料庫整潔的絕佳方法。 自動化是一把雙刃劍，因為如果您設定錯誤，會在幾分鐘內造成破壞。 在進行這些程式時，請務必小心，並隨時通知每個人。 一般而言，我建議不要刪除任何SFDC聯絡人，因為這些聯絡人更可能是作用中的機會、客戶或前客戶。 「財務」或「法律」可能會要求您保留某些記錄，而連絡人會附加至這些記錄。 相反地，將重點放在硬跳出、無效或不再存在的連絡人上。 現在，您知道一些保持您的Marketo資料庫整潔的技術。 如果您想出其他方法可使用API或Webhook自動化這些流程，請通知我們！

由&#x200B;_Josh_&#x200B;張貼於&#x200B;_2014-10-08_

## 2014年10月版本更新

### 外部頁面預填

在Marketo登陸頁面外部載入時，Marketo表單不提供原生預填功能。 不過，我們仍可使用[Marketo API](/help/rest-api/rest-api.md)和[Forms 2.0 JavaScript API](/help/javascript-api/forms-api-reference.md/)實作此專案。 第一步是透過伺服器的REST呼叫，從Marketo擷取銷售機會資料。 假設我們無法從伺服器立即交叉參考銷售機會ID或其他唯一識別碼，我們需要使用Munchkin Cookie &#39;_mkto_trk&#39;，透過[依篩選型別取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，從Marketo伺服器擷取資料。
若要進行此呼叫，我們需要您執行個體的驗證和REST端點。 當您使用您的Marketo執行個體進行驗證後，我們需要在`https://<host>/rest/v1/leads.json`對銷售機會API進行呼叫。 然後，我們需要建立查詢字串以篩選Marketo Cookie，如下所示`?filterType=cookie&filterValues=`。 您必須從使用者端傳送給伺服器的&#39;_mkto_trk&#39;金鑰擷取特定值。 注意： _mkto_trk Cookie值包含&amp;符號，且必須將URL編碼為`%26`，才能讓Marketo端點正確接受。 根據預設，銷售機會API傳回四個欄位： `id`、`email`、`firstName`和`updatedAt`。 若要設定特定的欄位集，您必須包含`fields`查詢引數，其欄位名稱以逗號分隔，如下所示： `&fields=email,firstName,lastName,company`。 我們的最終通話將如下所示：

`https://<host>/rest/v1/leads.json?filterType=cookie&filterValues=<cookie>&fields=email,firstName,lastName,company&access_token=<token>`

進行此呼叫時，它會傳回JSON物件，如下所示：

```json
{
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
 "lastName":"Elkington",
        "email":"<mkto@example.com>",
 "company":"Marketo Inc."
        }]
};
```

現在我們有了銷售機會詳細資料，接下來可以使用JavaScript中的whenReady和vals方法，將這些對應到Marketo表單中。 首先，我們需要在頁面上將Lead結果設定為變數：

```javascript
<script>
//print your JSON object dynamically as the mktoLead variable
var mktoLead = {
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
  "lastName":"Elkington",
        "email":"mkto@example.com",
  "company":"Marketo Inc."
        }]
}
</script>
```

頁面現在已顯示結果，可以將結果對應至表單欄位：

```javascript
<script>
MktoForms2.whenReady( function(form) {
 //set the first result as local variable
 var mktoLeadFields = mktoLead.result[0];
    //map your results from REST call to the corresponding field name on the form
 var prefillFields = {
   "Email" : mktoLeadFields.email,
   "FirstName" : mktoLeadFields.firstName,
   "LastName" : mktoLeadFields.lastName,
   "Company" : mktoLeadFields.company
   };
 //pass our prefillFields objects into the form.vals method to fill our fields
 form.vals(prefillFields);
 }
 );
</script>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2014-10-24_

## 取代電子郵件HTML

本文說明如何移除Marketo為電子郵件產生的HTML。 然後，您可以使用自己的HTML，而無需Marketo重新格式化。

1. 導覽至電子郵件，然後按一下「編輯草稿」。
1. 按一下「電子郵件動作」，按一下「HTML工具」，然後按一下「取代HTML」。
1. 以您的HTML取代此方塊中的程式碼。 然後，按一下「儲存」。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-10-28_

## 取得訪客的Cookie ID，然後查詢相關的潛在客戶資料

使用[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) REST端點取得多個銷售機會，您可以根據使用者的Cookie ID查詢銷售機會資料。 例如，您可以使用此方法在非Marketo登陸頁面上預先填寫表單。 這篇文章說明如何在網頁瀏覽期間擷取使用者的Cookie值，使用該Cookie ID查詢[取得多個銷售機會REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，然後傳回使用者的銷售機會資料。 首先，我們需要使用者的Munchkin Cookie &#39;_mkto_trk&#39;值。 以下是可用來取得Cookie值的範例JavaScript函式。 如需此方法的詳細資訊，請參閱[此StackOverflow頁面](https://stackoverflow.com/questions/10730362/get-cookie-by-name)。 我建議在頁面載入事件後設定500毫秒的延遲，然後再呼叫此函式。 這可讓Munchkin有時間載入，並為使用者提供Cookie。

```javascript
//Function to read value of a cookie
function readCookie(name) {
    var cookiename = name + "=";
    var ca = document.cookie.split(';');
    for(var i=0;i < ca.length;i++) {
        var c = ca[i];
        while (c.charAt(0)==' ') c = c.substring(1,c.length);
        if (c.indexOf(cookiename) == 0) return c.substring(cookiename.length,c.length);
    }
    return null;
}

//Call readCookie function to get value of user's Marketo cookie
var value = readCookie('_mkto_trk');
```

接下來，將&#39;_mkto_trk&#39; Cookie的值傳遞至您的伺服器。 若要擷取銷售機會資料，請從您的伺服器使用此Cookie值呼叫[取得多個銷售機會REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。 您需要執行個體的驗證和REST端點。 您的呼叫結構應如下所示：

注意： `_mkto_trk` Cookie值包含&amp;符號，且必須將URL編碼為`%26`，才能讓Marketo端點正確接受。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "cde42eff-aca0-48cf-a1ac-576ffec65a84:ab"
# Replace with filter type and values
filter_type_and_values = "&filterType=cookies&filterValues=id:AAA-BBB-CCC%26token:_mch-marketo.com-1418418733122-51548&fields=cookies,email"
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

上述範例將傳回電子郵件以及與使用者相關聯的所有Cookie。 然後，您可以使用此資料將使用者造訪的後續頁面個人化。

`{"requestId":"aa00#14a405aa786","result":[{"id":583,"email":"<testaccount@gmail.com>","cookies":"_mch-marketo.com-1418418733122-51548"}],"success":true}`

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-10-30_

## 您何時需要開發人員協助行銷自動化

注意：這是訪客部落格。 Josh Hill是行銷自動化機構Perkuto的Marketo業務負責人。 Josh在行銷、銷售與技術的交匯處工作，提供創收系統。 他在[MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/)撰寫有關行銷自動化和需求產生的相關文章。

行銷自動化平台功能強大，開箱即用，並交由經驗豐富的操作員掌控。 根據定義，平台可允許使用擴充功能應用程式，讓系統為團隊執行更令人驚豔的工作。 您可能會認為Marketo的邏輯引擎有這麼大的能力（事實也的確如此），但是這是有限制的。 Marketo無法為您完成任何工作，也不應該如此。

還有其他工具可執行的功能比Marketo建置的更好。 Marketo的平台非常開放，可讓應用程式的[LaunchPoint生態系統](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)存在。 您也可以利用這種開放性來擴展您的網站和Marketo的功能，以符合您的業務需求。 Marketo等平台的好處在於，它讓一般行銷人員能夠建立頁面、電子郵件和路由邏輯，而不需要成為完整的程式設計人員。
這些天行銷人員確實需要瞭解邏輯，但實際程式設計最好留給專家去做。 您如何知道何時需要致電開發人員？ 我有幾個基本規則或啟發法可決定程式設計師何時應參與： — 當Marketo沒有滿足需求的明顯篩選器、觸發器或功能時，通常可透過某些JavaScript或jQuery完成。  — 這對Marketo本身而言是否太複雜？ - Marketo甚至可以這麼做嗎？  — 這是不容易支援的網站自訂嗎？ - Marketo需要與網站或其他資料庫通訊嗎？  — 這聽起來像是電腦可以做的事情，但Marketo沒有它的功能嗎？ 請記住，雖然Marketo可能不會提供立即可用的功能，但可連線至許多協力廠商整合和自訂連線。

在[LaunchPoint市集](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)中檢視這些類別中的幾個類別： - [分析工具](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [資料附加](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [內容管理系統](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)某些協力廠商應用程式會在平台內提供直覺式控制面板和設定工具(GoToWebinar)。 這些是「原生」整合，您最需要做的就是設定登入，然後在Marketo中使用它。 不過，其他擴充功能需要使用較複雜的API，而且必須更直接地加以程式設計。

**Marketo的整合選項** - LaunchPoint整合 — 通常是登入或簡易設定。 - API整合 — 需要設定API和程式設計： (1) [REST API](/help/rest-api/rest-api.md) (2) [SOAP API](/help/soap-api/soap-api.md) (3) [Webhook整合](/help/webhooks/webhooks.md) — 需要設定特殊程式碼，但相當容易。 (4) [電子郵件指令碼](./email-scripting.md) (Velocity) - JavaScript和jQuery： (1) [Forms 2.0](/help/javascript-api/forms-api-reference.md) (2) [潛在客戶追蹤(Munchkin)](/help/javascript-api/lead-tracking.md) (3) [Social JS](/help/javascript-api/social.md) (4) [RTP JS](/help/javascript-api/web-personalization.md)以下是一些使用案例，說明如何使用開發人員來擴充Marketo平台的功能。 您有這些使用案例嗎？ 若是如此，開發人員的溝通時間或許已成熟。 [瀏覽LaunchPoint](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)上的服務合作夥伴區段。

由&#x200B;_Josh_&#x200B;張貼於&#x200B;_2014-11-06_

## 將Slack與Marketo整合

[Slack是企業共同作業平台](https://slack.com/)。 如果您的團隊使用Slack，您可以輕鬆將Marketo通知帶入您的工作流程。 這篇文章說明當Marketo中發生特定潛在客戶活動時，如何將通知新增到您的聊天記錄中。 潛在的使用案例包括通知您整個團隊有關表單填寫、造訪定價頁面或30天內未聯絡的潛在客戶等事宜。 底下熒幕擷圖顯示依照本說明文章中的步驟操作後，Marketo通知在Slack中的外觀。

1. 登入Slack。 按一下Slack中的整合
1. 按一下傳入Webhook的新增按鈕
1. 選擇Marketo通知的頻道。 然後按一下「新增傳入的Webhook整合」。
1. 複製並貼上Webhook URL （步驟8所需）
1. 選擇通知的名稱
1. 登入Marketo。 前往「管理員」。 按一下「Webhooks」。
1. 按一下新增Webhook
1. 輸入Webhook的名稱。 從步驟四輸入Webhook URL。 輸入Post作為請求型別。 輸入裝載範本。

以下是熒幕擷圖的裝載範本。 它會使用潛在客戶層級的名字、公司和電子郵件地址權杖。

`payload={"text": "DEVELOPER SITE ALERT: {{lead.First Name:default=edit me}} {{lead.Company=edit me}}, {{lead.Email Address:default=no email address}}" }`

1. 在Marketo中設定觸發促銷活動。 流程步驟是呼叫Webhook至Slack。 智慧清單是網頁造訪。
1. 確認其運作正常。

如需Marketo中Webhook的詳細資訊，請參閱[開發人員檔案](./webhooks/webhooks.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-10_

## 將Litmus與Marketo整合

[Litmus是一項服務](https://www.litmus.com/)，可跨瀏覽器和電子郵件使用者端測試電子郵件。 Litmus也提供電子郵件分析，包括點按、開啟和刪除。 本文會說明如何將Marketo與Litmus整合。

1. 在Marketo中設定電子郵件程式時，請按一下程式控制面板上的「我的代號」
1. 將「電子郵件指令碼」代號拖曳至中面板以將其新增。
1. 為Token命名，然後按一下「按一下以編輯」
1. 在右側的「標準物件」下方，展開「銷售機會」類別。 找到「電子郵件地址」欄位並核取方塊。 在同一頁面左側的空白指令碼空間中，貼入Litmus提供的追蹤程式碼。 在Litmus程式碼中，將`{{lead.Email Address}}`的每個執行個體取代為`${lead.Email}`。 按一下「儲存」以關閉燈箱視窗，然後在代號頁面上再次按一下「儲存」。
1. 記下Token `{{my.LitmusToken}}`的名稱。 開啟您要追蹤的電子郵件。 將新的指令碼Token放在電子郵件的最底部。 您也可以新增預設資訊以符合Litmus版本`{{my.LitmusToken:default=editme}}`。

傳送電子郵件時，Marketo會將指令碼放入電子郵件中。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-18_

## 在Facebook上共用Marketo登陸頁面時指定影像

假設您想要在Facebook上共用Marketo登陸頁面時自動顯示影像。 您可能也希望此影像不位於Marketo登陸頁面本身，而僅位於Facebook共用上。 若要這麼做，您可以新增開放圖表中繼標籤至您的Marketo登陸頁面。 執行此操作的具體步驟如下。

1. 選取您的登陸頁面。 然後按一下「編輯草稿」。
1. 按一下「編輯頁面中繼標籤」 。
1. 在Facebook的OG標籤區段中新增開放圖表中繼資料。 然後按一下「儲存」。 格式如下： `<meta property="og:image" content="http://example.com/example.jpg"/>`

[如需詳細資訊，請參閱Facebook的開發人員檔案](https://developers.facebook.com/docs/sharing/best-practices)，瞭解開啟圖表中繼標籤。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-17_

## 根據反向連結重新導向頁面

假設您想要防止直接流量進入Marketo登陸頁面。 假設此頁面有類似PDF的可下載內容，您希望使用者在收到內容前先填寫表格。 您可以透過檢查使用者是否來自特定頁面來解決此問題。 在這種情況下，這將是使用者必須填寫表單的頁面。 如果使用者不是來自該頁面，您可以然後將使用者重新導向至表單填寫頁面。 若要完成此作業，您必須檢查含有內容之登入頁面的反向連結頁面是否為表單填寫頁面。
將以下程式碼片段中`http://example.com/PageWithForm`的兩個執行個體取代為您希望使用者來自的頁面的連結。 這可以是表單填寫頁面。**

```javascript
<script>
window.onload = function() {
  if (document.referrer !== "http://example.com/PageWithForm") {
    document.location.href = "http://example.com/PageWithForm";
  };
 };
</script>
```

在含有內容的Marketo登陸頁面上，將自訂的JavaScript程式碼片段加入至結尾的標籤前。**如果未將使用者傳送至包含表單填寫頁面內容的登陸頁面，則使用者現在會被重新導向至表單填寫頁面。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-18_

## 將Trello與Marketo整合

Trello是[熱門的網頁式專案管理應用程式](https://trello.com/)。 如果您的團隊使用Trello，您可以輕鬆地將Marketo通知帶入您的工作流程中。 本文說明如何新增包含Marketo通知的卡片至您的Trello展示板。 在Marketo中發生特定潛在客戶活動時，會新增此卡片。 潛在的使用案例包括通知您整個團隊有關表單填寫、造訪定價頁面或30天內未聯絡的潛在客戶等事宜。

1. 登入Trello。 導覽至將新增Marketo通知的Trello展示板。 按一下「新增清單」，然後將其命名。
1. 按一下「顯示側欄」。 按一下「電子郵件至展示板設定」。 記下「此展示板的電子郵件地址」方塊中的電子郵件。 此電子郵件將用於步驟六。 選擇要新增Marketo通知的清單。
1. 登入Marketo。 按一下新的Smart Campaign。 輸入名稱，然後按一下[儲存]。
1. 導覽至智慧清單。 選擇此智慧行銷活動的觸發因子。 在此範例中，我們使用「表單填寫」觸發器。 將「填寫表單」觸發器拖曳至中面板。 選取您要觸發此通知的表單。
1. 建立電子郵件。 按一下「新增」。 按一下本機資產。 按一下「新增電子郵件」。 為電子郵件命名。 然後按一下「建立」。
1. 針對您在步驟5建立的電子郵件，按一下「編輯草稿」。 拖曳您要顯示在Trello卡片中的相關Token。 Marketo電子郵件的主旨列會出現在Trello卡片的標題中，而Marketo電子郵件的內文會出現在Trello卡片的說明中。 例如，如果您想要潛在客戶的名字和姓氏出現在Trello卡片標題中，可以使用「潛在客戶警報：`{{lead.First Name:default=edit me}}` `{{lead.Last Name:default=edit me}}`」。 然後核准電子郵件。
1. 導覽至Smart Campaign。 按一下「流量」。 將「傳送警報」拖曳至中間面板。 選取剛建立的電子郵件。 選取「傳送至」作為無。 選取「至其他電子郵件」作為步驟2中的格子電子郵件。
1. 按一下頂端功能表中的「排程」 。 按一下「啟動」。 按一下「確認」。
1. 測試整合。 標題中包含潛在客戶名字和姓氏的卡片將顯示在Trello展示板中。 如需詳細資訊，請參閱[Trello的檔案](https://support.atlassian.com/trello/)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-18_

## 依自訂欄位值尋找銷售機會

假設您想從Marketo API取得符合特定活動或不活動條件的銷售機會。 例如，您可能會想要尋找在過去30天內分數未變更的潛在客戶。 依照本文章中的步驟操作，您便可獲得此銷售機會清單。 為此，我們在Marketo中建立智慧型行銷活動，以識別過去30天內分數未變更的潛在客戶，然後在這些潛在客戶上儲存值以識別他們。 然後我們將使用此值查詢API。

1. 建立名為customLeadStatus的新自訂欄位**登入Marketo，然後前往「管理員」面板。 按一下「欄位管理」。 按一下「新增自訂欄位」。  為欄位命名。 然後按一下「建立」。
1. 使用智慧清單建立智慧行銷活動，尋找在30天內未更新的潛在客戶。**按一下「新增Smart Campaign」。 為新的智慧行銷活動命名。 「拖曳未計分」已從右側面板變更為中間面板。
1. 從步驟3將流程步驟新增至智慧行銷活動，以使用新值更新customLeadStatus欄位。**一下「將變更資料值」從右面板拖曳至中面板。
1. 更新Smart Campaign以允許銷售機會執行多次。**一下「排程」。 然後按一下「編輯」。  每次都選取「 」。 然後按一下「儲存」。 行銷活動現在將開始執行。
1. 透過篩選型別REST API[查詢](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)取得多個銷售機會。 提供引數filterType=customLeadStatus &amp; filterValue=needsEnrichment.**

這是傳回此資料的範例要求。

`<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?access_token=><yourAccessToken>&filterType=customLeadStatus&filterValues=needsEnrichment`

成功的API呼叫傳回JSON資料，其中潛在客戶的customLeadStatus欄位符合needsEnrichment的值。 如需詳細資訊，請檢閱[依篩選型別REST API取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-11-22_

## 透過SOAP API進行機會同步

本文說明如何透過SOAP API將商機插入Marketo，並將它們與公司和潛在客戶建立關聯。 首先說明此程式如何運作，然後提供每種情況的程式碼範例。

**資料表結構圖**&#x200B;首先，下列圖表說明資料表結構。 ID會在建立記錄時自動產生（序號）。 需要粗體欄位：只有「機會人員角色」有必填欄位。 括弧內的欄位為選用欄位，虛線的連線亦為選用欄位。

**基本機會插入**&#x200B;您先插入機會，然後插入機會人員角色，將機會連結至銷售機會。 在此範例中，請善用Lead Id和Opportunity Id來指定Opportunity Person Role。 建立機會時，您會在SOAP回應中取得機會ID。 Lead Id會顯示在Marketo Lead資料庫中的每個Lead上。

**公司連結**&#x200B;在大多數情況下，除了個人之外，您還要將商機連結到公司。 在Marketo中，您無法透過SOAP API存取公司記錄，只能存取潛在客戶記錄（潛在客戶記錄包含公司欄位）。 您仍然可以透過新增唯一的公司識別碼到每個銷售機會，並在您的機會中使用該ID，將機會連結到公司。 步驟1是在潛在客戶記錄上建立「公司ID」欄位，並填入唯一識別碼，通常來自後端系統。 步驟2是在機會上建立「公司ID」欄位：您必須要求Marketo支援或諮詢為您建立這樣的欄位。 然後在建立商機時填入此欄位，以便將商機連結到公司。 如果您使用Marketo Revenue Cycle Analytics並且想要使用取決於公司資訊的Opportunity Influence Analyzer，這會特別重要。

**使用外部識別碼**&#x200B;在許多情況下，當您與後端系統整合時，可能會有自己的唯一識別碼。 您可以透過外部索引鍵在Marketo中使用這些唯一識別碼。 對於銷售機會，您通常會使用外部系統人員ID (FSPID)，這會取代將Marketo ID或電子郵件地址當作唯一識別碼來使用。 FSPID是隱藏的系統欄位，不會顯示在Marketo內。 如果您尚未這樣做，Opportunity sync必須也將FSPID儲存在自訂欄位中，例如「外部ID」（您可以為欄位命名任何內容）。 您可以以Marketo管理員的身分自行建立此欄位。 若為Opportunities，您讓Marketo支援在Opportunity上建立另一個自訂欄位，例如「Foreign Id」（您可以將其命名為anything）。 然後在插入機會時填入此欄位。 最後，當您建立商機人員角色時，會使用兩個外部索引鍵來指定潛在客戶與商機之間的連結，而不使用Marketo ID。 您也可以使用外部索引鍵來更新商機的銷售機會。 目前無法新增外部索引鍵至機會個人角色記錄，因此您必須使用自動產生的Marketo ID才能執行此操作(建立機會個人角色後，您會在SOAP回應中取得此ID)。

**程式碼範例**&#x200B;步驟： 1. 使用外部索引鍵和公司ID 1插入/更新銷售機會。 使用外部索引鍵1插入商機。 使用外部索引鍵插入機會個人角色1。 SOAP請求 — 使用外部金鑰和公司ID更新現有銷售機會這會使用外部金鑰「12346」和帳戶/公司ID「C123」更新現有銷售機會(Marketo Id「6」)。 我們也在自訂欄位中儲存外部索引鍵，因為我們的機會個人角色需要它。 使用外部索引鍵為選用：您也可以使用Marketo ID將此Lead連結至Opportunity。 使用公司ID也是選擇性的，但如果您想在RCA中使用Opportunity Influence Analyzer，則需要使用。 要求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncLead>
         <leadRecord>
            <Id>6</Id>
            <ForeignSysPersonId>12346</ForeignSysPersonId>
            <leadAttributeList>
               <attribute>
                  <attrName>FSPID</attrName>
                  <attrValue>12346</attrValue>
               </attribute>
               <attribute>
                  <attrName>cAccountFSID</attrName>
                  <attrValue>C123</attrValue>
               </attribute>
            </leadAttributeList>
         </leadRecord>
         <returnLead>false</returnLead>
      </mkt:paramsSyncLead>
   </soapenv:Body>
</soapenv:Envelope>
```

回應：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncLead>
         <result>
            <leadId>6</leadId>
            <syncStatus>
               <leadId>6</leadId>
               <status>UPDATED</status>
               <error xsi:nil="true"/>
            </syncStatus>
            <leadRecord xsi:nil="true"/>
         </result>
      </ns1:successSyncLead>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP要求 — 機會建立在此案例中，已在Opportunity資料表上建立2個自訂欄位： - `opportunityId`，→包含機會唯一識別碼 — `cAccountFSID`，→包含公司參考，而非指定您自己的機會ID。 您也可以使用Marketo產生的機會ID。 在這種情況下，您會遺漏「外部索引鍵」節點。 公司關聯也是選擇性的，但如果您想要在RCA中使用Opportunity Influence Analyzer，則為必要。 要求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:03:28-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_4</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 4 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>501.00</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>501</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

回應：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>40</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP請求 — 機會個人角色此請求會將銷售機會連結至機會。 您可以在單一SOAP請求中指定多個連結（此範例僅將Opportunity連結至1個銷售機會）。 這會使用外部索引鍵來指定連結，但在註解中，也會說明如何使用實際ID （在此案例中：6代表潛在客戶ID，40代表機會ID）。 此「IsPrimary」和「Role」欄位為選用。 要求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <!--id>40</id-->
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_4</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

回應：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>11</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**替代方法（在一次呼叫中執行步驟2和步驟3）**&#x200B;雖然您可以先插入商機，然後再插入商機人員角色，但也可以在一次SOAP呼叫中執行此操作。 不過，您必須使用商機的外部索引鍵（您無法在商機人員角色中使用自動產生的商機ID，因為商機尚未產生）。 當然，您也可以將多個Lead連結至同一個API呼叫中的此Opportunity （此範例僅將Opportunity連結至1個Lead）。 要求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:44:08-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_5</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 5 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
             <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_5</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

回應：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>41</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
               <mObjStatus>
                  <id>12</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

由&#x200B;_Jep_&#x200B;張貼於&#x200B;_2014-11-26_

## 多執行緒REST API請求

如果您想要改善呼叫Marketo API時的效能，您可以同時提出請求。 此方法可讓您在較短的時間內取得更多資料。 發出API請求時，使用者端與伺服器之間的往返時間的一部分就是有線上的傳輸時間。 因此，如果我們可以減少彙總請求的線上傳輸時間，就能改善效能。 下列程式碼範例說明如何在Ruby中執行此動作。 它使用EventMachine，這是[事件處理程式庫，用來發出多執行緒要求](https://github.com/igrigorik/em-http-request/wiki/Parallel-Requests)。 下列範例呼叫[潛在客戶活動API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)，並發出兩個同時請求。 此方法可免除第二個要求從使用者端傳輸至伺服器的時間。 其做法是與第一個要求同時包含第二個要求。 API回應會寫入文字檔案中。

```java
require 'em-http-request'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = ["&nextPageToken=A5YMOYZQBOGD2OSYYBYDAQGEMGLBDGDANAABQGRAQWAAKKID", "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"]
# Specify activities needed
activity_type_ids = "&activityTypeIds=1&activityTypeIds=12"
requesturl_a = marketo_instance + endpoint + auth_token + since_date_time.at(0) + activity_type_ids
requesturl_b = marketo_instance + endpoint + auth_token + since_date_time.at(1) + activity_type_ids

# Make request
EventMachine.run do
  http1 = EventMachine::HttpRequest.new(requesturl_a).get
  http2 = EventMachine::HttpRequest.new(requesturl_b).get

# When API response is received, write response to a text file
  http1.callback {
  File.open('response1.txt', 'w') do |t|
  t.puts http1.response
  end }

  http2.callback {
  File.open('response2.txt', 'w') do |t|
  t.puts http2.response
  end }
end
```

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-12-03_

## 效能調整API要求

本文會討論改善向Marketo API要求資料時效能的策略。 不過，您必須將這些策略的好處與Marketo API的每日限制的作業限制進行權衡。
**策略1 — 在每個API呼叫中要求較少的資料**&#x200B;一般而言，當您在API呼叫中要求更多資料時，Marketo伺服器查詢資料庫中的資料所花的時間會增加。 如果您使用日期範圍進行API呼叫，例如[getMultipleLeads SOAP API](/help/soap-api/getmultipleleads.md)，請縮短每次呼叫的時間範圍，並使用更多呼叫進行補償。 例如，與其請求6月1日至7月1日的資料，不如一次請求一天，例如6月1日至2日的一個呼叫，然後是6月2日至1日的另一個呼叫。 如果您要進行API呼叫以從Marketo銷售機會欄位傳回資料，請僅請求這些必要的欄位。 每個額外的潛在客戶欄位都會逐步增加API呼叫所需的時間。 另一種方法是減少批次大小，或減少每次呼叫請求的潛在客戶數。
**策略2 — 提出並行要求**以提升效能並一次提取更多資料。 您可以同時向API提出請求。 此方法可減少線API請求總計花費的時間。 例如，假設您向「依篩選型別取得多個銷售機會」提出請求。 您可以針對查詢銷售機會1到300的一個請求發出並行請求，針對查詢銷售機會301到600的另一個請求發出並行請求。
**策略3 — 快取資料** Marketo中某些資料（例如潛在客戶欄位清單）的變更頻率低於其他資料（例如潛在客戶活動資料）。 如果您快取較不經常更新的資料，則可以減少必須進行的API呼叫數。 一般來說，在本機查詢資料的速度會比從遠端Web服務存取資料的速度更快，因此您也能獲得更優異的效能。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-12-05_

## 將Marketo表單資料傳送至Google Analytics

在Google Analytics中，您可以傳送自訂資料事件，然後運用資料來劃分及分析您的網站績效。 下列JavaScript程式碼片段可讓您在訪客提交網路表單後，自動將Marketo 2.0表單資料推送至Google Analytics。 以下說明設定方法。

**步驟一**&#x200B;將JavaScript標籤插入任何在程式碼底部包含Marketo Forms的頁面上（在標籤前）。 JavaScript只會傳送非隱藏欄位(sendHiddenFields ： false)。 您可以將sendHiddenFields從false變更為true來調整此設定。 您也可以在「fieldsToExclude」陣列中新增其他欄位ID，以選取要排除的欄位。

```javascript
function pushFormDataToGa(a){
setTimeout(function () {
document.getElementsByTagName('form')[0].getElementsByClassName(a.submitButton)[0].addEventListener('click', function() {
  allFields = document.getElementsByTagName('form')[0].getElementsByTagName('input');
  for(i=0;i<allFields.length;i++){
   if( (allFields[i].type !="hidden" && allFields[i].type !="submit" && allFields[i].value !="" && a.fieldsToExclude.indexOf(allFields[i].id) === -1  ) || (allFields[i].type === "hidden" && a.sendHiddenFields) ){
    console.log( allFields[i].name + ": "  + allFields[i].value);
    if(typeof(_gaq) != "undefined"){
    //Classic
    _trackEvent("Marketo Form Submission", allFields[i].value , allFields[i].name
{'nonInteraction': 1});
    }else if(typeof(ga) !="undefined"){
    //Universal
    ga('send', 'event',"Marketo Form Submission", allFields[i].value , allFields[i].name, {'nonInteraction': 1});
}}}}, false);
}, 3000);}
pushFormDataToGa({
 submitButton: "mktoButton",
 fieldsToExclude: ["Email","LastName", "FirstName"],
 sendHiddenFields : false
});
```

**步驟二** GA中的資料會顯示在「報表」區段中。 前往行為>事件>熱門事件。 **指令碼限制：** — 此程式碼範例僅與[Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md)相容。  — 由於Google的隱私權政策，您不得傳送任何個人資訊（電子郵件或姓名）。 除了潛在的隱私權顧慮外，這是個人識別資訊，因此違反[Google Analytics服務條款](https://marketingplatform.google.com/about/analytics/terms/us/)：

「您不會（也不會允許任何第三方）使用本服務來追蹤、收集或上傳任何可識別個人身分的資料（例如姓名、電子郵件地址或帳單資訊），或Google可合理連結至此類資訊的其他資料。」

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2014-12-16_

## 將全名欄位新增至Marketo表單

我們知道較短的網路表單可提高轉換率。 下列JavaScript程式碼範例可讓您將名字和姓氏欄位合併為一個全名欄位，讓您的表單變得更短。 當訪客輸入全名時，指令碼會自動將文字分成名字和姓氏欄位。 對於已知訪客，指令碼會加入名字和姓氏，然後將它們複製到新欄位，這樣他們就不需要再次填寫欄位。 以下說明設定方法。

**步驟一**在Marketo中建立稱為「全名」的新自訂欄位。 不需要在您的CRM平台中建立它，因為指令碼只會使用此欄位來顯示全名。
**步驟二**將此欄位新增至您的所有網路表單。 將您的名字和姓氏欄位設定為隱藏。 在JavaScript中，變更「splitFullName」設定以包含3個欄位名稱。 注意：請確定這些名稱未出現在頁面上的其他任何位置。
**步驟三**&#x200B;將JavaScript插入程式碼底部的所有登陸頁面中，在標籤之前。

```javascript
<script>
MktoForms2.whenReady(function (form){
        function splitFullName(a,b,c){
                String.prototype.capitalize = function(){
                        return this.replace( /(^|s)([a-z])/g , function(m,p1,p2){ return p1+p2.toUpperCase(); } );
                };
                document.getElementsByName[c](0).oninput=function(){
                        var fullName = document.getElementsByName[c](0).value;
                        if((fullName.match(/ /g) || []).length ===0 || fullName.substring(fullName.indexOf(" ")+1,fullName.length) === ""){
                                var first = fullName.capitalize();;
                                var last = "null";
                        }else if(fullName.substring(0,fullName.indexOf(" ")).indexOf(".")>-1){
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize() + " " + fullName.substring(fullName.indexOf(" ")+1,fullName.length).substring(0,fullName.substring(fullName.indexOf(" ")+1,fullName.length).indexOf(" ")).capitalize();
                                var last = fullName.substring(first.length +1,fullName.length).capitalize();
                        }else{
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize();
                                var last = fullName.substring(fullName.indexOf(" ")+1,fullName.length).capitalize();
                        }
                        document.getElementsByName[a](0).value = first;
                        document.getElementsByName[b](0).value = last;
                };
                //Initial Values
                if(document.getElementsByName[c](0).value.length < 2 && document.getElementsByName[b](0).value.length.length >2 && document.getElementsByName[a](0).value.length.length >2 ){
                        var first = document.getElementsByName[a](0).value.capitalize();
                        var last = document.getElementsByName[b](0).value.capitalize();
                        var fullName =  first + " " + last ;
                        console.log(fullName);
                        document.getElementsByName[c](0).value = fullName;
                }
        }
        splitFullName("FirstName","LastName","leadFullName");
});
</script>
```

注意：此程式碼僅適用於Marketo Forms 2.0。

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2014-12-16_

## 使用cURL透過REST API匯入銷售機會

您是否要透過REST API從CSV檔案匯入銷售機會，但請注意，使用Postman Chrome擴充功能會很困難。 在本文章中，我們將逐步說明如何使用cURL執行此操作。

1. [下載並安裝cURL](https://curl.se/download.html)，我們用來將資料推送到Marketo的REST API的命令列工具。
1. 開啟命令列，然後導覽至CSV檔案所在的位置。 CSV檔案中的欄標題必須符合API欄位名稱，而不是Marketo欄位名稱。
1. 您需要存取權杖。 登入Marketo，前往「管理員」，然後前往「LaunchPoint」。 尋找您的REST API使用者，然後按一下「檢視詳細資料」。 按一下「取得Token」按鈕。
1. 您也將需要Marketo執行個體專屬的REST端點。 登入Marketo，然後前往「管理員」，再前往「網站服務」。 在標示為「REST API」的區段中，您可以找到端點URL。
1. 在命令列上，針對cURL呼叫遵循此格式。 從步驟三以您的存取權杖取代`<accesstoken>`，從步驟四以您的REST API端點URL取代`<REST API Endpoint URL>`。 [更多資訊可在此取得](https://developer.adobe.com/marketo-apis/api/mapi/#operation/importLeadUsingPOST)。 這裡的「/bulk」將會取代端點URL結尾的「/rest」。 如果您已設定/rest/bulk的端點，則會傳回錯誤。

`curl -i -F format=csv -F file=@leaddata.csv -F access_token=<accesstoken> <REST API Endpoint URL>/bulk/v1/leads.json`

由&#x200B;_Jordan_&#x200B;張貼於&#x200B;_2014-12-16_

## 新增確認警報至Marketo，用於

假設當使用者按一下Marketo表單上的「傳送」按鈕時，您想要顯示通知並詢問使用者「是否真的可以傳送？」 實作幾行JavaScript便可達成此目的，當有人按一下「傳送」按鈕時，就會顯示確認方塊。 以下是如何執行此動作的範例。 將onSubmit函式新增至您的Marketo表單，如下所示。 如需Marketo Forms API的詳細資訊，請[檢視開發人員檔案](/help/javascript-api/forms-api-reference.md)。

```javascript
<script src="//app-e.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_19"></form>
<script>
MktoForms2.loadForm("//app-e.marketo.com", "212-RBI-463", 19,function(form){

//Add this function to your Marketo form script
form.onSubmit(function(){
alert("Do you really want to submit the form?");
});
});
</script>
```

由&#x200B;_David_&#x200B;張貼於&#x200B;_2014-12-17_

## 顯示感謝訊息，但不提供後續登陸頁面

一般而言，使用Marketo表單時，會建立兩個登陸頁面 — 一個用於將表單放置在其上，另一個用於在表單完成後重新導向至。 但是，在某些情況下，您可能不想要維護兩個獨立但非常類似的登入頁面。 您實際上可以使用Forms 2.0 JavaScript API針對表單和感謝訊息使用相同的登陸頁面。 若要這麼做，請先建立您的註冊登入頁面和表單，然後如常將表單放在登入頁面上。 然後，將HTML元素新增至頁面。 在此元素中，我們新增了一些程式碼，會在提交表單時啟用。 然後它會隱藏表單並顯示隱藏的專案 <div> 其中包含感謝訊息。 您的JavaScript應如下所示：

```javascript
//Edit host with your Marketo instance info
<script src="//<host>/js/forms2/js/forms2.js"></script>
<script>
MktoForms2.whenReady(function (form){
 //Add an onSuccess handler
  form.onSuccess(function(values, followUpUrl){
   //get the form's jQuery element and hide it
   form.getFormElem().hide();
   document.getElementById('confirmform').style.visibility = 'visible';
   //return false to prevent the submission handler from taking the lead to the follow up url.
   return false;
 });
});
</script>
```

編輯感謝訊息文字。

`<div id="confirmform" style="visibility:hidden;"><p><strong>Thank you. Check your email for details on your request.</strong></p></div>`

您將會想要編輯程式碼範例中的主機名稱及感謝訊息。 第一個應參考您的Marketo執行個體(例如&quot;//app-sj06.marketo.com/js/forms2/js/forms2.js&quot;)，第二個應包含您要在表單完成後顯示的感謝文字。 此文字會顯示在您放置HTML元素的確切位置上，因此請務必在屬性表中編輯該文字。 您也應確定HTML元素的圖層小於表單的圖層。 根據預設，兩者都會放在第15層，因此如果您將HTML元素設為第11層，這是安全的。 如果不這樣做，您將無法輸入與感謝訊息重疊的任何表單欄位方塊。 不需要變更表單或登入頁面上的後續追蹤型別，因為JavaScript會覆寫這些設定。 如需Marketo Forms API的詳細資訊，請檢視[開發人員檔案](/help/javascript-api/forms-api-reference.md)。

由&#x200B;_Kristin_&#x200B;張貼於&#x200B;_2014-12-19_

## 反白顯示以Source Platform建置的開啟Marketo專案

這是開發人員社群圍繞Marketo平台建立的開放原始碼專案，並持續發佈的第一篇文章。 我們會維護Marketo GitHub帳戶[上的](https://github.com/Marketo/Community-Supported-Client-Libraries)清單，用於追蹤Marketo開發人員社群所建立的使用者端資料庫和專案。 以下是圍繞Marketo REST和SOAP API開發的三個專案。 Daniel Chesterton在PHP中為Marketo REST API建立了[使用者端程式庫](https://github.com/dchesterton/marketo-rest-api)。 使用者端程式庫目前涵蓋12個REST API端點。Elixiter的** Kyle Halstvedt建立了一個專案，將Marketo靜態清單中的潛在客戶[提取到Google試算表中](https://github.com/Elixiter/mkto_google-spreadsheet)。 Kyle的專案使用Marketo REST API。  David Santoso已為Marketo SOAP API建立[Ruby gem。](https://github.com/davidsantoso/markety)此專案可協助您更快速地整合Marketo SOAP API與Ruby on Rails應用程式。  我們很高興能在Marketo平台上看到更多由開發人員社群建立的專案。 如果您正在處理Marketo平台的開放原始碼專案，請[透過提取請求將其提交至此GitHub存放庫](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-02_

## 根據使用者的位置動態變更頁面內容

假設您想要根據使用者所在的位置動態變更登陸頁面上的電話號碼。 例如，如果人員住在加州，您想在登陸頁面上顯示加州辦事處的電話號碼，但如果人員住在日本，您想向他們顯示日本辦事處的電話號碼。  一種實施方法是使用JavaScript和[HTML5地理位置API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)。 此方法的好處是，我們可以建立一個登陸頁面，並根據使用者的位置動態變更，而不是多個靜態登陸頁面。 我們將逐步說明下列技術實作詳細資訊。 我們會為辦公室位置建立具有經緯度座標的物件，再為辦公室電話號碼建立第二個物件。 在生產環境中，將這兩個物件合併為單一物件會比較好。

```json
//Coordinates for Marketo offices
var officeLocations = {
    "San Mateo": {latitude: 37.5596465, longitude: -122.2870142},
    "Atlanta": {latitude: 33.8547013, longitude: -84.35552349999999},
    "Tokyo": {latitude: 35.6895, longitude: 139.6917},
    "Dublin": {latitude: 53.3478, longitude: -6.2603097},
    "Sydney": {latitude: -33.873651, longitude: 151.2068896},
    "Portland": {latitude: 45.512089, longitude: -122.6763367},
    "Tel Aviv": {latitude: 32.0852999, longitude: 34.78176759999999}
}

//Phone numbers for Marketo offices
var officePhoneNumbers = {
    "San Mateo": "+1-650-376-2300",
    "Atlanta": "+1-877-260-6586",
    "Tokyo": "+81-03-6759-8280",
    "Dublin": "+353-1-242-3000",
    "Sydney": "+61-2-9045-2711",
    "Portland": "+1-877-260-6586",
    "Tel Aviv": "+1-877-260-6586"
}
```

我們會建立方法來要求使用者的位置。 為了處理錯誤，如果無法存取使用者的位置，我們會預設為Marketo總部及其電話號碼。

```javascript
//Method to get user's current location. Returns a position object with user's geo coordinates
function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(findNearestOffice);
    } else {
        x.innerHTML = "Marketo Location: San Mateo
Marketo Phone Number: +1-877-260-6586";
    }
}
```

最後，我們會建立方法，尋找離使用者所在地最近的辦公室，然後傳回頁面上最近辦公室的電話號碼。 此方法使用[Geolib的findNearest方法，這是提供地理空間作業](https://github.com/manuelbieh/Geolib)的JavaScript程式庫。

```javascript
//Find nearest Marketo office to user's location
function findNearestOffice(position) {
        var nearestOffice = geolib.findNearest({latitude: position.coords.latitude, longitude: position.coords.longitude}, officeLocations);
        x.innerHTML = "Marketo Location: " + nearestOffice.key + "
Marketo Phone Number: " +  officePhoneNumbers[nearestOffice.key];
}
```

以下是完整實作。 當使用者按一下頁面上的按鈕時，就會觸發getLocation方法。 此[GitHub存放庫](https://github.com/MurtzaM/Find-Nearest-Marketo-Office)具有設定此示範所需的檔案。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-12-20_

## 潛在客戶追蹤與多個網域

Marketo的Munchkin追蹤程式碼可協助您追蹤網站的造訪次數。 您可能會想要使用Munchkin追蹤程式碼，對網站上大部分或所有頁面的Cookie匿名銷售機會。 讓我們來逐步瞭解Munchkin的運作方式。 系統會記錄現有潛在客戶的頁面瀏覽次數，而非Cookie訪客的頁面瀏覽次數，將導致系統建立並儲存新的Cookie，並在Marketo資料庫中建立新的匿名潛在客戶。 如果訪客沒有目前網域的現有Cookie，Munchkin追蹤器將會自動Cookie。 在Marketo中，它會在潛在客戶的活動記錄中記錄事件（按一下連結、造訪網頁或新潛在客戶）。 對於指定的訪客而言，儲存在Cookie中的值是不重複的。 該值是不重複Munchkin帳戶追蹤ID、網域名稱、時間戳記和隨機整數的組合。
**如果我有多個網域會發生什麼事？**&#x200B;假設您有兩個要追蹤的網站： `<www.apples.com>`和`<www.bananas.com>`。 您可以將追蹤程式碼放在兩個網站上，但您需要考量下列事項。 Marketo Cookie是「第一方Cookie」，因此是網域所專屬的。 這表示網站1的訪客將會建立為Marketo中的匿名銷售機會，如果該相同銷售機會進入網站2，則會在Marketo中建立第二個單獨的匿名銷售機會。 如果潛在客戶填寫了網站1上的表單，則此記錄變為已知，網站2的匿名記錄將保留，並繼續累計對該網站的後續造訪。 如果潛在客戶接著使用與網站1完全相同的電子郵件地址填寫網站2上的表單，則兩個已知潛在客戶將自動合併，並且所有過去和未來的行為都將在Marketo中的單一記錄上進行追蹤。 兩個Cookie ID都會繫結至相同的銷售機會，而所有網路活動（來自任一網域）都會在該銷售機會上。
**多個子網域呢？**&#x200B;子網域不是問題。 以Marketo.com為例。 它有多個適用於不同語言的子網域，例如fr.marketo.com和de.marketo.com。 有了子網域，所有活動都將根據相同的潛在客戶記錄/Cookie進行記錄。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-01-13_

## 變更Marketo表單上的提示文字色彩

假設您想在Forms 2.0中變更提示文字顏色（也稱為預留位置文字）。這可透過自訂CSS進行。 例如，在下方的熒幕擷圖中，我將此Marketo表單的提示文字設為藍色。 根據您使用Marketo Forms的方式，有三個選項可指定執行此動作。

**選項1：如果您內嵌Marketo表單，請直接將下方的CSS新增至您的主要CSS檔案。**

```css
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
```

**選項2：當您內嵌Marketo表單時，可以在`<style></style>`區段的`<head>`標籤之間直接在頁面上新增CSS。**

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

**選項3：如果您在Marketo登陸頁面上使用Marketo表單，可以透過Marketo UI新增此自訂CSS。**&#x200B;在Marketo導覽樹狀結構中尋找登入頁面。 然後按一下「編輯草稿」。 按一下「編輯頁面中繼標籤」 。 將下方的CSS新增至「自訂HEAD HTML」區段。 `<style></style>`標籤應包含在內。

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

按一下「核准草稿」。 現在當您瀏覽Marketo登陸頁面時，提示文字是您在CSS中定義的顏色。 如需Marketo Forms的詳細資訊，[請瀏覽檔案](/help/javascript-api/forms-api-reference.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-14_

## 透過REST API取得活動資料

假設您想要取得本月新增至清單中的所有銷售機會。 使用[取得潛在客戶活動REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)，您可以取得此資料。 在呼叫Get Lead Activities API之前，必須從Authentication API取得存取Token，並且從[取得分頁權杖API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getActivitiesPagingTokenUsingGET)取得開始日期Token。 以下是Ruby中的範常式式碼，會逐步引導您瞭解必須呼叫的個別API端點，以傳回本月新增至清單的所有銷售機會。 1.取得存取Token**

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com/identity/oauth/token?grant_type=client_credentials>"
# Relace with your client id
client_id = "99985d09-22a9-3jl2-84av-f5baae7c3a45"
# Replace with your your  client secret
client_secret = "tZPVrKiEmUDezE18yZfeaPlTJ2vKn2fw"
request_url = marketo_instance + "&client_id=" + client_id + "&client_secret=" + client_secret

# Make request
response = RestClient.get request_url

# Parse reponse and return only access token
results = JSON.parse(response.body)
access_token = results["access_token"]
puts access_token
```

1. 取得分頁Token

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities/pagingtoken.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify date
since_date_time = "&sinceDatetime=2015-01-01T00:00:00-08:00"
request_url = marketo_instance + endpoint + auth_token + since_date_time

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. 取得活動資料**若要判斷此呼叫所需的活動型別識別碼，請查詢[Getting Activity Types API](/help/rest-api/activities.md)。 取得活動型別API會傳回包含所有活動型別和關聯ID的結構描述。 例如，它會針對建立的新潛在客戶傳回id 12，針對網頁瀏覽傳回id 1。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
# Specify activities needed
activity_type_ids = "&activityTypeIds=24"
request_url = marketo_instance + endpoint + auth_token + since_date_time + activity_type_ids

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. Get Lead Activities API會傳回分頁Token，其中包含您可用來分頁結果集的每個回應。**如需詳細資訊，請參閱[REST API檔案](/help/rest-api/rest-api.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-20_

## 強調以Source平台為基礎之開啟的Marketo專案：第二部分

這是開發社群圍繞Marketo平台建置的開放原始碼專案，並持續發佈的第二篇文章。 我們會維護Marketo GitHub帳戶[上的](https://github.com/Marketo/Community-Supported-Client-Libraries)清單，用於追蹤Marketo開發人員社群所建立的使用者端資料庫和專案。 以下是圍繞Marketo SOAP和Munchkin API開發的三個專案。 [PunchTab](https://www.punchtab.com/)已在Python中為Marketo SOAP API建立[使用者端資料庫](https://github.com/PunchTab/suds-marketo)。 [Flickerbox](https://www.flickerbox.com/)已在PHP中為Marketo SOAP API[建立](https://github.com/flickerbox/marketo)使用者端程式庫。* [Richard Morrison](https://x.com/mozz100)已建立[PHP指令碼，以從Marketo SOAP API取得潛在客戶資料，然後使用JavaScript將此資料傳遞給使用者端。](https://github.com/mozz100/marketo-whodat)此專案可幫助您在Marketo中根據使用者的資料修改頁面。  我們很高興能在Marketo平台上看到更多由開發人員社群建立的專案。 如果您正在處理Marketo平台的開放原始碼專案，請[透過提取請求將其提交至此GitHub存放庫](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-20_

## 將RTP建議引擎點按傳送至Google Analytics

以下為Marketo Real-Time Personalization (RTP)使用者的解決方案，可檢視Google Analytics中內容推薦引擎的點按次數。 訪客按一下「內容建議」列後，系統就會在事件類別「RTP-Recommendations」底下將事件傳送至Google Analytics。 在Analytics中，建議文字（如顯示在長條圖中的）會附加至「事件標籤」 ，而建議資產的URL會附加至「事件動作」。 此指令碼適用於Classic Google Analytics和Google Universal Analytics。 此標籤應貼至HTML頁面程式碼的結尾，因此是`</body>`標籤前的最後一個標籤。

```javascript
$( document ).ready(function() {
if(document.getElementsByClassName("insightera-bar-content").length
 >0){
document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].addEventListener("click",
 function(){
assetName
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].innerText;
assetURL
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].href;
assetURL=
 assetURL.substring(assetURL.lastIndexOf("/"),assetURL.indexOf("?iesrc"));
console.log(assetName

 * " | " + assetURL);
if(typeof(_gaq)
 != "undefined"){
//Classic
_trackEvent("RTP-Recommendations",
 assetName , assetURL , {'nonInteraction': 1});
}else
 if(typeof(ga) !="undefined"){
//Universal
ga('send',
 'event',"RTP-Recommendations", assetName , assetURL, {'nonInteraction': 1});
}
});
}
});
```

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2015-01-22_

## 搭配Boomi使用Marketo REST API：取得和儲存REST驗證Toke

設定自動匯出符合特定條件的銷售機會是Marketo的一個非常常見的使用案例。 雖然目前無法在Marketo介面中執行此作業，但使用協力廠商工具(例如Dell Boomi、包含一些資料管理行銷活動的靜態清單，以及Marketo REST API)可輕鬆完成。 REST API？ 我以為Boomi沒有Marketo REST API聯結器！ 雖然目前還無法做到，但使用HTTP聯結器及手動定義jSON回應形狀也是可能的。 第一步是設定您的Marketo執行個體來使用REST API，如[REST API Marketo開發人員頁面](/help/rest-api/rest-api.md)中所述。 我也會假設您擁有Dell Boomi帳戶的存取權，並具備Boomi的技能來建立這些型別的整合程式。 最終程式如下所示，且將包含對以下Marketo REST API作業的呼叫，每個作業都有相關聯的jSON回應圖形，可在開發人員網站上找到。 為了節省時間，我在[驗證](/help/rest-api/authentication.md)的JSON範例下列出它們

```json
{
  "access_token": "",
  "token_type": "",
  "expires_in": 0,
  "scope": ""
}
```

[依清單ID取得多個潛在客戶的JSON範例](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

[從清單移除銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)的JSON範例

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

定義屬性：在開始呼叫REST之前，請務必外部化並封裝您使用的變數。 我已定義下列專案，如下所示。

* ClientID：從您的REST Launchpoint服務取得此專案
* 使用者端密碼：從您的REST啟動點服務取得此密碼
* AccessToken：我們從REST呼叫取得此專案
* 靜態ListID：我們要操作的靜態清單的LIST ID。 從Marketo中的URL取得此專案
* 欄位：Rest服務從Marketo取得每個潛在客戶的逗號分隔欄位清單。 我的是&quot;id， email，firstName，lastName&quot; * IDStringToDelete：最終將包含靜態清單中所有潛在客戶的ID，以用於將其從清單中移除
* ActivityTypes：將用於此部落格的第2部分，我將在此展開說明！
* SinceDateTime：將用於此部落格的第2部分，我在此展開相關內容！
* PagingToken：將用於此部落格的第2部分，我在此展開說明！
* 資料夾 — 傳出： SFTP伺服器上傳出資料夾的路徑。 在此範例中，我使用「/data/outgoing」。 這可讓我們將SFTP操作引數化，以使其成為通用操作。

驗證Token：如我所說，在建立具有「無資料」開始形狀的程式後，我們將在畫布上放置聯結器（這只是個人的選擇，我喜歡我的所有聯結器看起來像英國插頭）。
聯結器的設定如下： — 聯結器是HTTP GET使用者端 — 連線使用URL： `https://123-ABC-456.mktorest.com` (請注意，結尾沒有/rest，因此我們可以將此用於REST呼叫，並使用它來取得身分存取權杖。 並將123-ABC-456變更為您的Marketo執行個體的正確名稱) — 作業為「取得oAuth權杖」（新！） — 要求設定檔=無 — 回應設定檔= JSON — 稱為「驗證權杖回應」的新設定檔 — 內容型別：純 — HTTP方法： GET — 資源路徑（新增4個不含引號）： &quot;identity/oAuth/token？grant_type=client_credentials&amp;client_id=&quot;； &quot;ClientID （取代變數）&quot;； &quot;&amp;client_secret=&quot;； &quot;clientSecret （替換變數）」 — 「配置 — >引數 — >(+)」下的設定引數：設定ClientID =流程屬性客戶端ID；設定ClientSecret =流程屬性客戶端密碼之後，將成功令牌儲存在流程屬性「AccessToken」變數中，如圖所示，從jSON響應中提取該令牌。
此步驟的模式將在後續步驟中重複，但使用具有不同jSON的新操作傳回設定檔。 事實上，許多REST呼叫的處理方式將會相同，只進行微幅變更！ 在下一期中，我們將展開此內容，並使用REST從靜態清單中取得潛在客戶清單！ 目前，請執行此程式，但在「設定屬性」之後放置停止圖形，然後在偵錯中執行，以確保您看到與Marketo中相同的代號。 他們應該完全相符！

由&#x200B;_John_&#x200B;張貼於&#x200B;_2015-01-26_

## 使用Google字型API新增自訂字型至Marketo登陸頁面

**注意：這是[Murtza Manzur](https://www.linkedin.com/in/murtzam)的部落格。 Murtza是舊金山灣區的Marketo開發人員宣傳專員。**
假設您正在Marketo中建立登陸頁面，且想要使用自訂字型。 這是使用Google字型API時的可能方式。  參照Google Fonts將匯入方法新增至CSS檔案：

`@import url(http://fonts.googleapis.com/css?family=Open+Sans:400,300,600);`

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-26_

## 使用電子郵件指令碼將潛在客戶的名字大寫

假設潛在客戶以小寫輸入其名稱，例如「John doe」。 但當您傳送電子郵件促銷活動時，您想要將電子郵件中的潛在客戶名稱轉換為大寫，例如John Doe。 您可以使用電子郵件指令碼將潛在客戶的名稱轉換為大寫。 以下是其操作方式。

1. 在您的電子郵件程式中，按一下「我的Token」索引標籤。
1. 從右側面板將「電子郵件指令碼」拖曳至中間面板，以建立新的電子郵件指令碼Token。 為Token命名。
1. 在「編輯指令碼Token」文字方塊中，貼上以下程式碼。 在右側面板中的Lead物件下，選取First Name核取方塊。 然後按一下「儲存」。

```javascript
# set($name = ${lead.FirstName})
# set($formattedFirstName = $name.substring(0).toUpperCase())
$formattedFirstName
```

1. 在您的電子郵件資產中參考權杖。 它會輸出具有首字母大寫之潛在客戶的名字。 如需電子郵件指令碼的詳細資訊，請瀏覽[電子郵件指令碼檔案](/help/email-scripting.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-26_

## 從Marketo REST API取得所有銷售機會

StackOverflow發生[問題，詢問如何透過REST API](https://stackoverflow.com/questions/28184900/how-do-i-get-the-list-of-all-the-leads-in-marketo)從Marketo取得所有潛在客戶清單。 您可以使用[依篩選型別REST API端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)取得多個銷售機會，以查詢此資料。 Marketo中的潛在客戶會依序指派潛在客戶ID，從1開始。 使用[依篩選型別取得多個銷售機會REST API端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，您可以透過每次呼叫依銷售機會ID查詢300個銷售機會。 透過對此端點的每個呼叫，您將必須將id指定為filterType，並將銷售機會id指定為filterValues。 若要取得所有銷售機會，您可以一次反複檢視銷售機會總數300。 Y
您可以透過Marketo UI取得Marketo例項中的銷售機會總數。 在Marketo UI中，前往「銷售機會資料庫」標籤，按一下「系統智慧列示」，按一下「所有銷售機會智慧列示」，最後按一下「銷售機會」標籤。 然後按一下ID欄並降序排序。 對銷售機會進行排序後，當您查詢所有銷售機會時，第一個銷售機會的ID將是銷售機會ID的上限。 如果您無法存取Marketo UI來取得銷售機會總數，有[替代方法可使用Get Lead Activities REST API取得此值](https://stackoverflow.com/questions/28419967/get-all-leads-programmatically-in-marketo-v1)。

1. 第一個API呼叫：將……取代為以下範圍內的所有值：

`/rest/v1/leads.json?filterType=Id&filterValues=1,2,3,...,298,299,300`

以下是Ruby中第一個呼叫的範常式式碼。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Replace with filter type and values
ids_needed = (1..300).to_a.join(",")
filter_type_and_values = "&filterType=Id&filterValues=" + ids_needed
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. 第二個API呼叫和每個後續API呼叫將遵循相同的模式，直到達到潛在客戶總數為止：

```
//replace ... with all the values in between
/rest/v1/leads.json?filterType=Id&filterValues=301,302,303,...,598,599,600
```

如需詳細資訊，請參閱[REST API檔案](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-01-28_

## 從Iframe到上層頁面執行表單提交動作

我們已經看到一些使用者使用iframe表單的案例，他們想要將填寫表單的訪客導向感謝頁面或PDF、影片等。 問題是由於表單內嵌於不同於父表單的登陸頁面上，因此動作只會發生在表單所在的內部頁面上。 為了解決這個問題，我們建立了2個JavaScript標籤。 將作為HTML元素插入您的iframe頁面，或直接插入您用於iframe的登入頁面範本。 將它放在最後`</body>`個標籤之前。 第一個標籤會在上層頁面上執行動作，而第二個標籤會在新標籤中將其開啟。

上層頁面上的&#x200B;**表單動作**

```javascript
<script>
MktoForms2.whenReady(function (form){
form.onSuccess(function (values, url){
window.parent.location.assign(url);
return false;
           });
});
</script>
```

在新索引標籤中的&#x200B;**表單動作**

```javascript
<script>
MktoForms2.whenReady(function (form){
var newWin;
form.onSubmit(function (){
newWin = window.open('about:blank', 'myWindow');
});
form.onSuccess(function (values, url){
newWin.location.replace(url);
return
 false;
});
});
</script>
```

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2015-02-02_

## 從YouTube影片將檢視資料傳送至市場

假設您想要根據Marketo中的潛在客戶是否已啟動或完成特定視訊來劃分潛在客戶。 您可以在Marketo中使用Munchkin、YouTube的Iframe API和智慧列示完成這項作業。 本文章中的範常式式碼可讓您透過Munchkin將視訊開始和視訊結束事件傳送到Marketo。 為了讓此功能發揮作用，您也必須先在頁面上載入Munchkin，然後才能開始將視訊檢視事件傳送到Marketo。 已啟動和已完成的影片將顯示在潛在客戶的活動記錄中。 資料傳入Marketo後，您就可以建立智慧清單，並區隔已開始或完成視訊的潛在客戶。

1. 取得您要內嵌之YouTube影片的ID。**從您要使用的YouTube視訊URL中，記下ID，它是`v=`之後的隨機字元系列。
1. 將步驟1中的YouTube視訊ID置於此程式碼範例的第八行。 然後將程式碼放置在頁面HTML中的`</body>`之前。

```javascript
<div id="player"></div>
<script>
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";
document.getElementsByTagName('head')[0].appendChild(tag);

//Change 'iiqxcjxJ5Us' to video needed
var player, videoId = 'iiqxcjxJ5Us';
function onYouTubeIframeAPIReady() {
player = new YT.Player('player', {
height: '390',
width: '640',
videoId: videoId,
events: {
'onStateChange': onPlayerStateChange
}
});
}

function onPlayerStateChange(event) {
switch( event.data ) {
//Send video started event to Marketo
case YT.PlayerState.PLAYING: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=started'
}
);
break;
//Send video finished event to Marketo
case YT.PlayerState.ENDED: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=finished'
}
);
break;
}

}
</script>
```

1. 在Marketo中建立智慧清單，包含您要尋找作為「Querystring contains」值的視訊和檢視事件的URL。 如需YouTube Iframe API的詳細資訊，[請瀏覽YouTube的API檔案](https://developers.google.com/youtube/iframe_api_reference)。 如需Munchkin的詳細資訊，[請檢閱Marketo開發人員檔案](/help/javascript-api/lead-tracking.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-02_

## Marketo SOAP API提示與秘訣

注意：這是訪客部落格。 [Ed Blachman是](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D2777965)TIBCO Software （知名企業軟體廠商[）的資深架構師](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)。 Ed正在研發的產品可讓Gartner所謂的「公民開發人員」整合他們使用的雲端服務，而不需自行進行任何程式設計。 [Marketo的SOAP API](/help/soap-api/soap-api.md)是功能強大的工具，開發人員可藉此運用Marketo的強大功能，並將其與我們自己的應用程式整合。 在[正式檔案](./getting-started.md)和[社群資源](https://nation.marketo.com/)之間，有許多有關如何使用的資訊。 剛開始使用時，我高度依賴這些資訊，並發現其價值難以估量。 不過，在此過程中，我累積了一些在上述任何地方都未見過的秘訣和技巧。 以下是我想到的部分內容。

**開發人員的沙箱**沙箱當然是API開發人員的絕佳資源：您可以在此安全的地方試驗Marketo功能，新增和移除物件，而不會干擾貴組織的實際Marketo使用者進行的真實行銷活動。 不過，沙箱並非萬能藥。
例如，我需要和其他開發群組共用我們的沙箱，這需要花一些時間，因為他們已經習慣了擁有沙箱的觀念。 最終，我們提出了幾個分享的最佳實務： — 請勿撰寫依賴完全瞭解沙箱內容的測試。 作為共用資源，結構描述可能會隨時變更，恕不另行通知，潛在客戶資料庫或方案或其他實體中的完整專案也可能隨時變更。 如果您的測試假設您完全瞭解沙箱，則開發週期會為您共用該沙箱的群組建立中斷期間。 由於它們的開發週期通常不會與您的週期一致，因此這相當於佔用了資源，而不是酷。 如果您仔細想一想，也沒必要。  — 請使用慣例來標示您所有的東西 — 您的銷售機會、您的銷售機會結構描述欄位、您的計畫，等等。 如果您每個人都可以識別自己的物件，而且如果您同意共同租使用者的意見，即您每個人都將保留其他人的物件，那麼您應該有穩固的共用基礎。 對於銷售機會，您可以建立一個自訂欄位，並使用此自訂欄位建立一個慣例，以識別這些銷售機會作為您的測試銷售機會。 對於清單或程式，您可能會以某些字串來開始物件的名稱，這些字串會將這些物件識別為您所屬。  — 請考慮撰寫可自我清理的測試，先建立您感興趣的物件，然後存取或更新或選擇性地刪除物件，最後移除物件。 (請注意，這在SOAP API中無法一律實現，因為並非沙箱或就此而言的實際執行個體中的所有專案都能透過SOAP API管理。 即使如此，您還是有必要儘可能多地做這件事。)

**實際執行個體**&#x200B;沙箱的問題在於它並未在生產環境中使用，因此很難瞭解Marketo執行個體中的實際使用情形。 現在，如果您幸運地擁有團隊中的Marketo進階使用者，或者您正在執行針對內部Marketo使用者的客製化開發，則這並不是問題。 但就我的團隊而言，這確實是一筆大買賣。 我們誰也不是Marketo專家，而且因為被要求瞭解大量雲端服務，我們只是沒有人數成為任何方面的專家。 以下是我們從存取實際執行個體中收集的一些見解： — 大型潛在客戶結構描述。 我們存取的生產執行個體中的潛在客戶結構描述有超過200個欄位。 這清楚地向我們的UI設計人員說明他們設計的UI必須容納該大小（或更大）的結構描述。  — 大量使用。 我們看到最高使用時間和低使用時間之間兩個數量級的差異（就建立或更新潛在客戶數量而言）。 這會影響我們從API呼叫傳回的資料量（顯而易見），以及API呼叫回應所需的時間（可能不太明顯）。

**API呼叫回應時間**&#x200B;根據每日時間、API呼叫的詳細資料和執行個體的內容，您可能會發現SOAP API的回應時間比平均時間長。 有時，我們的API呼叫需要一分鐘半的時間才能回應。 您必須注意處理問題的可能性： — 測試。 也許這對您的使用情況沒有影響。 但請勿僅假設上述情況，執行一些測試。  — 調整您的使用方式。 在此案例中，最大的問題是我們將呼叫[getMultipleLeads](/help/soap-api/getmultipleleads.md)的頁面大小設定為API允許的最大值。 在我們的情境中，這樣的設定有一定道理，因為我們的目標是儘可能以最高效率處理客戶的API配額。 但在您的情境下，您或許不需要特別擔心使用者的API呼叫配額，若是如此，您要求較小型的資料頁面絕對可獲得較佳的回應時間。

**潛在客戶分割** Marketo提供強大的工具 — 分割和工作區，允許多個行銷群組共用單一Marketo執行個體。 不過，這些工具不會直接反映在SOAP API中。 例如，當您使用getMultipleLeads來取得自某個日期時間以來已更新或建立的所有潛在客戶時，您會收回執行個體中符合這些條件的所有潛在客戶，而不考慮（且不會指示）哪個分割區或工作區包含任何指定潛在客戶。 潛在客戶建立和新增潛在客戶至清單是其他情境，在這些情境中，潛在客戶分割可能會影響您API呼叫的實際作業。 請注意，這表示分割區和工作區可能不是上述沙箱共用問題所需的解決方案。 那麼，您如何判斷這是否對您而言是個問題？ 我發現所有這些都很有幫助：開發人員宣傳人員致力於幫助我們成功使用API，並且在有問題的地方，他們非常善於尋找答案。 - [API檔案](./getting-started.md)。 宣傳人員已將此問題帶入部分檔案中，並且作為他們承諾要成功的一部分，他們對更新檔案真的很好。  — 您自己的測試案例。 雖然使用分割區和工作區來共用沙箱可能不是個好主意，但沙箱是您與分割區和工作區一起玩耍的好地方，以便瞭解它們是否對您的預期用途構成挑戰。 （這也是縮小向福音傳道者提問的範圍的好方法，這始終是好主意。）

**TIMTOWTDI和測試** 「有多種方法可以做到」 — Perl程式設計格言在某些內容中會實際套用至Marketo SOAP API。 例如，我想將更新一組銷售機會與將這些銷售機會新增到一些清單結合使用。 SOAP API提供您兩種執行此動作的方法： 1. [importToList](/help/soap-api/importtolist.md) + [getImportToListStatus](/help/soap-api/getimporttoliststatus.md)。 閱讀本檔案後，這顯然是執行此動作的「正常」方式。 不過，您必須輪詢匯入作業的狀態這個事實為我帶來一個黃色旗標。 這真的是我想實作匯入的方式嗎？ 1. [syncMultipleLeads](/help/soap-api/syncmultipleleads.md) + [listOperation](/help/soap-api/listoperation.md)。 這看起來比單一importToList呼叫來得輕鬆許多，但不仰賴輪詢。 這是一個可行的選項嗎？ 這類案例很難讓福音傳教士處理，因為它們真的取決於您正在處理的例項的性質，以及您正在嘗試執行的確切動作。 幸運的是，如果您建立了強大的單元測試環境，您應該能夠用它來探索類似這樣的問題。 在此特定案例中，我發現選項2比選項1更適合我的使用案例 — 不是因為輪詢，而是因為在importToList上我遇到欄位導向的限制，而且因為我嘗試編寫可用於我沒有控制權的內容和執行個體的程式碼。 但您的使用案例可能不同，而測試是您瞭解的唯一方法。

**結論**&#x200B;我認為這些都不是大秘密。 另一方面，如果我開始之前就知道這些的話，我就會領先於遊戲。 我希望您覺得它很有用。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-02-05_

## 搭配Boomi使用Marketo REST API：從靜態清單取得和刪除銷售機會

在此系列的第1部分中，我討論了如何透過Boomi開始將REST API與Boomi HTTP聯結器搭配使用，尤其是取得存取REST API所需的驗證權杖，並將其儲存在流程變數中。 接下來，我們將開始撥打Marketo，而在此分期付款中，我將向您展示如何透過清單ID [取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)以及[從清單中移除銷售機會](/help/rest-api/lead-database.md)。 請特別留意將潛在客戶從清單中移除，因為在那裡，Boomi有非常「輕描淡寫」和微妙的層面，當我們到達那裡時，我會進一步加以說明。

在下一次分期付款中，我們擴展了這項功能，以開始做一些有趣的事情，例如取得銷售機會活動，但那是一天的部落格。 在本期中，我們將檢視第二和第三個醒目提示區域。 回顧一下，我已在下方納入我們所需的JSON回應。 請記得，若要在Boomi中建立JSON設定檔，您只需要建立JSON型別的設定檔元件，然後按一下「匯入」並選取檔案即可。 Boomi會完成其餘工作，推斷是否應允許多個ID。 [依清單ID取得多個潛在客戶的JSON範例](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

[從清單要求移除銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)的JSON範例

```json
{
   "input":[
      {
         "id": ""
      },
      {
         "id": ""
      }
   ]
}
```

[從清單回應中移除銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)的JSON範例

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

透過清單ID取得多個銷售機會**使用與上一篇文章中定義的相同連線，將另一個聯結器(Get)拖放到您的流程中。 建立名為「依清單ID取得多個銷售機會」（我是一致性的嚴格標準）的新作業。其屬性如下 — 請求設定檔：無（此專案使用請求URL） — 回應設定檔型別：jSON — 回應設定檔：根據上述「依清單ID取得多個銷售機會」回應來建立新設定檔。 請注意，您可以加以變更，使其傳回您想要的欄位，而不只是列出的欄位。 請務必記住，JSON回應設定檔應確實符合您從REST API要求的欄位清單，且您應僅要求您需要的欄位。 在Process Properties物件中，我們定義了一個名為「fields」的屬性，它是您希望REST傳回的欄位清單（以逗號分隔）。 這是必須符合設定檔的清單。 內容型別：text/plain （這只是URL要求） HTTP方法： GET （只要在REST API檔案中查詢，一律會列出）資源路徑（新增5） rest/v1/list/ listID （取代變數） /leads.json？access_token= access_token （取代變數） &amp;fields=欄位（取代變數）。 然後，在聯結器的「引數」標籤中，您可以輸入變數值，這些值先前都已填入流程屬性中。 在下一節中，我將討論如何避免手動填入這些內容。 Im將略過我將「依清單ID取得多個銷售機會」的回應對應至一般檔案設定檔中的程式部分，並將其貼到FTP伺服器上，因為這是簡單明瞭的Boomi功能。

從清單中刪除銷售機會這個很有趣，我的同事之一[丹羽健](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D7429494)教給我這個下一個技巧，它非常酷，而且是根據Boomi題為「如何為RESTful應用程式建立POST要求」的文章，如下所示。 ...但首先要做的事情。 在程式中，從「依清單ID取得多個銷售機會」中，我們有「依清單ID取得多個銷售機會」回應圖形，我們需要將其對應到「從清單請求移除銷售機會」中，因為對應相當簡單，只需將我們從原始清單中的銷售機會取得的ID對應到我們傳至刪除jSON的ID清單中。 接下來，拖放另一個具有「傳送」動作的聯結器，使用相同的連線建立名為「從清單請求中移除銷售機會」的新操作。 其屬性為要求設定檔： jSON內容型別： application/json要求設定檔： [JSON設定檔]從清單要求中移除銷售機會（從上述檔案建立）回應設定檔型別： jSON回應設定檔： [JSON設定檔]從清單中移除銷售機會回應（從上述檔案建立）內容型別： application/json HTTP方法： DELETE資源路徑（新增4） rest/v1/lists/ listID （取代變數） /leads.json？access_token= access_token （取代變數）此處這個聯結器的有趣之處。 我們不會在聯結器標籤中明確新增引數。 相反，如文章所述，我們會建立與取代變數具有相同名稱的動態檔案屬性。 在此案例中，這些變數分別為listID和access_token。 執行此動作時，jSON形狀會流入REST呼叫，而引數會顯示在URL上其正確位置。 我們無法對上一個呼叫執行此操作，因為這是GET而非POST。 因此，此時您已看到GET和POST REST API呼叫，而您可以開始看到發出這些REST呼叫的模式。 在下一期中，我們將開始檢視透過REST API匯出潛在客戶活動，這會涉及更多層面。

由&#x200B;_John_&#x200B;張貼於&#x200B;_2015-02-06_

## 在YouTube登陸頁面上內嵌含有銷售機會追蹤的Marketo影片

在上一篇部落格中，我說明如何根據潛在客戶是否已開始或完成特定Marketo影片，在YouTube中劃分潛在客戶。 在這篇部落格中，我們將逐步解說如何從該文章中取得實作，並將其用於Marketo登陸頁面。

1. 導覽至Marketo中您要建立新登陸頁面的計畫。 按一下「新增本機資產」 ，然後按一下「登陸頁面」 。
1. 為登入頁面命名。 指派頁面URL。 選取範本。 然後按一下「建立」。
1. 建立登入頁面後，按一下「編輯草稿」。
1. 從右側面板，將「HTML」按鈕拖曳至左側的主畫布。
1. 隨即顯示的自訂HTML編輯器方塊中。 然後按一下「儲存」。
1. 拖曳方塊外框，調整HTML元素的大小。 然後按一下「核准並關閉」。
1. 按一下「檢視核准的頁面」以測試登入頁面的即時版本。 具有YouTube的登入頁面會在新視窗中開啟。 已啟動和已完成的影片將顯示在潛在客戶的活動記錄中，如下面的第一個和第二個熒幕擷圖所示。 將資料放入Marketo後，您可以建立智慧清單，並將已開始或完成視訊的潛在客戶細分，如下面的熒幕擷取所示。 如需YouTube Iframe API的詳細資訊，[請瀏覽YouTube的API檔案](https://developers.google.com/youtube/iframe_api_reference)。 如需Munchkin的詳細資訊，[請檢閱Marketo開發人員檔案](/help/javascript-api/lead-tracking.md)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-09_

## 使用Munchkin進行單頁應用程式網頁追蹤

單頁應用程式是一個網站，可在第一個頁面載入時載入導覽網站所需的所有資源。 當使用者按一下連結時，內容會從第一個頁面載入資料載入。 對使用者而言，網站會如預期般運作，因為位址列中的URL就像傳統的頁面導覽。 Munchkin適用於傳統網站，因為Munchkin會在每次使用者載入新頁面時執行。 不過，若是使用單頁應用程式，當您不載入新頁面時，Munchkin將只會執行一次。 我在這篇文章中介紹的方法是追蹤使用者何時點按連結，然後將此資訊傳送到Munchkin。 我們使用`clickLink` Munchkin函式實作此專案。 以下是jQuery中的實作範例，此實作會將點按事件繫結至`clickLink` Munchkin方法。 呼叫`clickLink` Munchkin方法時，會傳遞所點按URL的引數。

```javascript
<script>
$("a").on('click', function(event) {
    var urlThatWasClicked = $(this).attr('href');
    Munchkin.munchkinFunction('clickLink', { href: urlThatWasClicked});
});
</script>
```

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-11_

## 透過REST API變更銷售機會分數

假設您想使用API變更Marketo中的潛在客戶分數。 這可以透過建立/更新潛在客戶端點與REST API一起使用。 以下是Ruby中的程式碼範例，說明如何進行此呼叫。

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "https://AAA-BBB-CCC.mktorest.com"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

在請求的JSON內文中，我們將`updateOnly`指定為動作。 這表示請求只有在潛在客戶存在時才有效，否則會失敗。 如果要在銷售機會不存在時建立銷售機會，則指定`createOrUpdate`作為動作。 我們使用潛在客戶的電子郵件作為主要識別碼，以在Marketo中尋找潛在客戶記錄。 最後，我們使用索引鍵`leadScore`來指定潛在客戶評分的值。 使用此方法一次可以更新300個銷售機會。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-19_

## 強調以Source平台為基礎之開啟的Marketo專案：第三部分

這是開發社群圍繞Marketo平台建置的開放原始碼專案，並持續發佈第三篇文章。 我們會維護Marketo GitHub帳戶[上的](https://github.com/Marketo/Community-Supported-Client-Libraries)清單，用於追蹤Marketo開發人員社群所建立的使用者端資料庫和專案。 以下是圍繞Marketo REST API開發的三個專案。 **[Usermind](http://www.usermind.com/)已為Marketo REST API[建立](https://github.com/MadKudu/node-marketo)Node.js使用者端資料庫。** **[Arunim Samat](https://github.com/asamat)已在Python中為Marketo REST API建立[使用者端資料庫](https://github.com/asamat/python_marketo)。** **[來自Marketo](https://www.linkedin.com/in/jalemieux)的Jacques Lemieux已為Marketo REST API在Ruby中建立[使用者端資料庫。](https://github.com/jalemieux/mkto_rest)**&#x200B;我們很高興在Marketo平台上看到開發人員社群建立的更多專案。 如果您正在處理Marketo平台的開放原始碼專案，請[透過提取請求將其提交至此GitHub存放庫](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-02-20_

## 將Marketo表單插入RTP行銷活動

許多行銷人員都有興趣將Marketo表單放入Marketo即時Personalization (RTP)行銷活動中。 無論是Dialog、In Zone或Widget RTP促銷活動型別，您都可以複製表單HTML程式碼並將其貼到RTP的促銷活動編輯器中。 我見過以下使用方式的範例： — 讓訪客在第二次或第三次點選您的網站後註冊您的電子報 — 快速、有效的網路研討會登錄檔單 — 下載封閉式個案研究 — 提供過去取消訂閱的銷售機會重新訂閱行銷活動中的表單並收到感謝或要求的內容，減少一次點選即可達成您的目標。 以下說明將說明如何執行此作業，並將Marketo Form 2.0內嵌至Marketo RTP行銷活動。 檢視以下來自eMarketer的絕佳範例，RTP使用者更進一步，而不是將訪客導向感謝頁面 — 決定在RTP行銷活動中顯示「感謝您」訊息。 此選項的程式碼也如下。 很高興聽到您使用這項技術的經驗！

1. 以滑鼠右鍵按一下已核准的表單。 選取&#x200B;**內嵌程式碼。**
1. 複製&#x200B;**代碼。**
1. 在Marketo RTP中，前往&#x200B;**行銷活動**。
1. 按一下&#x200B;**建立新行銷活動**。
1. 在RTF編輯器中，按一下&#x200B;**HTML圖示**。
1. 將表單內嵌程式碼貼到HTML Source編輯器中。 按一下&#x200B;**更新**。
1. 該表單不會顯示在編輯器檢視中，但您可以預覽該表單，以檢視其在行銷活動中的呈現方式。
1. 按一下&#x200B;**啟動**&#x200B;以啟動行銷活動。

### 備註

對表單的任何變更都必須在編輯表單草稿的Marketo行銷活動中完成。

### 相關文章

* [Forms 2.0](/help/javascript-api/forms-api-reference.md)

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2015-12-20_

## 將重設按鈕新增至Marketo表單

```javascript
<script src="//app-sj01.marketo.com/js/forms2/js/forms2.min.js"></script>
<form id="mktoForm_116"></form>
<script>MktoForms2.loadForm("//app-sj01.marketo.com", "410-XOR-673", 116,
function(form) { form.getFormElem()[0].querySelector('button[type="submit"]').insertAdjacentHTML('afterend','<button type="reset" class="mktoButton">Reset</button>') });
</script>
```

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-03-18_

## 2015年3月版本更新

[Marketo REST Asset API已在2015年3月版本](https://developer.adobe.com/marketo-apis/api/asset/)中發行。 此API可讓您存取Marketo的檔案、資料夾、代號、電子郵件和電子郵件範本物件。 請注意，已新增兩個角色許可權，可提供對資產API端點的存取權：唯讀Assets、讀寫Assets。 如果您的API使用者角色早於Asset API發行，則您需要建立具有這些許可權的新API使用者角色以啟用存取權。 否則，您會收到603「拒絕存取」錯誤回應。 除了REST Asset API以外，還更新了現有的REST API端點。 [合併銷售機會REST API端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST)已更新，以允許合併多個銷售機會。 [排程行銷活動REST API端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)已更新，以允許排程行銷活動時複製行銷活動。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2015-03-23_

## 延遲觸發RTP行銷活動

此自訂JavaScript可讓RTP使用者在網頁載入幾秒後顯示行銷活動。 建議將此用於對話方塊和Widget行銷活動。 當訪客檢視頁面上的一般內容後，這可用於在延遲後顯示促銷活動。 建議僅在應顯示行銷活動的特定頁面上實作此程式碼。 不建議在所有頁面中使用此程式碼，因為它可能會影響效能。 **設定指示**&#x200B;自訂程式碼會傳送RTP自訂資料事件（t=timeOnPage，即：t=60），然後載入符合此事件的行銷活動。 預設會在60秒後觸發。 您可以將sendCustomRTPEvent引數變更為任何其他數字，以自訂它。 將程式碼放置在標準RTP程式碼的後面：

```javascript
<script>
function sendCustomRTPEvent(a){
 var eventValue="t="+a;
 setTimeout(function(){
  rtp('send', 'event', {value: eventValue});
  rtp('get', 'campaign',true);
 }, 1000 \* a);
}
sendCustomRTPEvent(60); //Seconds
</script>
```

若要設定RTP促銷活動，以便在延遲時間後做出反應：1. 登入您的RTP帳戶1。 建立新的區段1。 在「區段事件」區段中新增： `t=60`。 訪客在每個工作階段中只能比對每個RTP區段一次，因此每個行銷活動只能看到一次（除非將其設定為sticky）。

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2015-03-24_

## 2015年4月版本更新

### Marketo Mobile Engagement SDK v0.3.2

Marketo現在包含行動應用程式的行銷自動化和使用者參與。 將[Marketo Mobile SDK](/help/mobile/mobile.md)安裝至您的iOS或Android應用程式，可讓行銷人員接聽應用程式內事件並傳送相關推播通知。

### REST API增強功能

* 自訂物件

已引入新的[自訂物件端點](/help/rest-api/custom-objects.md)，可讓您以程式設計方式列出、描述及CRUD存放Marketo自訂物件的資料。

請注意，已新增角色許可權，以提供對自訂物件API端點的存取權：唯讀自訂物件、讀寫自訂物件。 如果您的API使用者角色早於自訂物件API的發行，則您需要建立具有這些許可權的新API使用者角色以啟用存取。 否則，您會收到603「拒絕存取」錯誤回應。

* 排程行銷活動 — 原地複製方案

[排程行銷活動API](/help/rest-api/data-ingestion.md)中引入了新的選用引數「cloneToProgramName」。 出現此引數時，將會複製行銷活動的父方案，並排程新建立的行銷活動。 引數會為產生的程式指定所需的名稱。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2015-04-28_

## 跨執行處理同步電子郵件取消訂閱

您管理多個Marketo例項嗎？ 讓不同執行個體之間的潛在客戶資訊保持同步化是一項困難的挑戰。 以下是使用呼叫外部Web服務的webhook，同步處理不同執行個體間的電子郵件取消訂閱的方法。 外部Web服務會在每個執行個體中循環，尋找觸發取消訂閱事件的已知銷售機會。 找到相符的銷售機會時，會更新對應銷售機會記錄中的「已取消訂閱」欄位。 以下是說明此想法的圖表。  實作Web服務由您決定，但下列程式碼應能協助您快速啟動程式！

### 外部Web服務

外部Web服務會針對每個需要同步的Marketo執行個體執行下列步驟：

1. 撰寫執行個體特定的REST API [端點URL](/help/rest-api/endpoint-reference.md)
1. 使用[身分](/help/rest-api/authentication.md)取得存取權杖
1. 使用[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)取得多個銷售機會，以取得符合電子郵件地址的銷售機會記錄清單
1. 使用建立/更新銷售機會更新每個銷售機會記錄的「已取消訂閱」欄位

以下是另一個圖表，詳細說明外部Web服務呼叫和Marketo REST API呼叫。  下列程式碼範例並非立即可用的Web服務。 相反的，它是一個主控台模式程式，您可以透過命令列將引數傳遞到。 這裡的目的是說明如何呼叫適當的Marketo API，以更新跨執行個體的潛在客戶記錄。 實作Web服務是讀者的一項練習。
**範常式式碼**&#x200B;若要讓範常式式碼啟動並執行，您必須在您最喜愛的IDE中建立Java專案。 之後，您需要進行下列變更： 1. 範常式式碼使用[json-simple](https://code.google.com/archive/p/json-simple)來剖析JSON字串。 將json-simple jar新增到Java專案。 1.範常式式碼的結構包含每個Marketo例項的中繼資料。 將例項中的實際值放入結構中，如下所示：

```java
public static String instanceInfo[][] = {
{ "AccountId1", "ClientId1","ClientSecret1" },    // Instance 1 metadata
{ "AccountId2", "ClientId2","ClientSecret2" },    // Instance 2 metadata
{ "AccountId3", "ClientId3","ClientSecret3" }     // Instance 3 metadata
};
```

您可以在Marketo管理面板中找到執行個體的中繼資料：

* 帳戶Id管理員>整合> Munchkin > Munchkin帳戶
* 使用者端Id和使用者端密碼管理員>整合> LaunchPoint >電子郵件取消訂閱同步>檢視詳細資訊

範常式式碼採用兩個命令列引數，這些引數模擬上述外部Web服務的「id」和「email」查詢引數。

1. 引數[0] =帳戶識別碼
1. 引數[1] =電子郵件地址

將實際值從您的執行個體傳遞為引數至程式。 以下是來自Intellij IDEA的專案設定熒幕擷圖。

**SyncEmailUnsubscribe.java**

```java
package com.marketo;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Iterator;
import java.util.NoSuchElementException;
import java.util.Scanner;

public class SyncEmailUnsubscribe {
    // Define Marketo instance meta data here.
    // Each row contains three elements: Account Id, Client Id, Client Secret.
    // For example:
    //  public static String instanceData[][] = {
    //    {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    //  };
    //
    public static String instanceData[][] = {
            // ADD YOUR INSTANCE META DATA HERE
    };

    public static void main(String[] args) {
        String accountId = args[0];     // Account id that processed the unsubscribe
        String emailAddress = args[1];  // Email address of lead that unsubscribed

        SyncEmailUnsubscribe seu = new SyncEmailUnsubscribe();

        // Loop through each Marketo instance
        for (int i = 0; i < instanceData.length; i++) {

            // Make sure we skip instance that triggered the webhook
            if (!accountId.equals(instanceData[i][0])) {
                String endpointUrl = String.format("https://%s.mktorest.com", instanceData[i][0]);

                // Generate access token
                String identityUrl = String.format("%s/identity/oauth/token?grant_type=client_credentials&client_id=%s&client_secret=%s", endpointUrl, instanceData[i][1], instanceData[i][2]);
                String token = seu.getToken(identityUrl);

                // Get lead records for given email address (may be duplicates)
                String getLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=email&filterValues=%s", endpointUrl, token, emailAddress);
                String leads = seu.getLeads(getLeadsUrl);

                // Update unsubscribed field in lead record
                String updateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", endpointUrl, token);
                seu.updateLeads(updateLeadsUrl, leads, accountId);
            }
        }

        System.exit(0);
    }

    // Call Identity Service to generate access token
    public String getToken(String url) {
        // Call Identity Service
        String tokenData = getData(url);

        // Convert response into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(tokenData);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }

        // Retrieve access_token
        JSONObject jsonObject = (JSONObject)obj;
        return jsonObject.get("access_token").toString();
    }

    // Call Get Multiple Leads by Filter Type Service to get lead records
    public String getLeads(String url) {
        return getData(url);
    }

    // Call Create/Update Lead Service to update "unsubscribed" flag in lead record
    public void updateLeads(String url, String leads, String account) {
        JSONObject body = composeBody(leads, account);
        if (body != null) {
            postData(url, body);
        }
    }

    // Compose JSON body for Create/Update Leads Service
    private JSONObject composeBody(String leads, String account) {
        JSONObject body = new JSONObject();

        // Convert leads into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(leads);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }
        JSONObject leadsObj = (JSONObject)obj;

        Object success = leadsObj.get("success");
        if (success.equals(true)) {
            body.put("action", "updateOnly");
            body.put("lookupField", "id");
            body.put("asyncProcessing", "true");

            // Build array of lead objects
            JSONArray input = new JSONArray();
            JSONArray result = (JSONArray) leadsObj.get("result");
            Iterator<JSONObject> iterator = result.iterator();
            while (iterator.hasNext()) {
                JSONObject leadIn = (JSONObject)iterator.next();
                JSONObject lead = new JSONObject();
                lead.put("id", leadIn.get("id"));
                lead.put("unsubscribed", "true");
                lead.put("unsubscribedReason", "Cross instance synch triggered by webhook from: " + account);
                input.add(lead);
            }

            body.put("input", input);
        }
        return body;
    }

    // HTTP POST request
    private String postData(String endpoint, JSONObject body) {
        String data = "";
        try {
            // Make request
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("POST");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "application/json");
            urlConn.connect();
            OutputStream os = urlConn.getOutputStream();
            os.write(body.toJSONString().getBytes());
            os.close();
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    // HTTP GET request
    private String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

### Marketo設定

針對您要同步的每個Marketo執行個體執行以下步驟。

1. 建立具有角色許可權的自訂服務：讀寫銷售機會。 如果您不熟悉建立自訂服務，請按一下[這裡](/help/rest-api/custom-services.md)。
1. 建立可呼叫外部Web服務的Webhook。 如果您不熟悉建立Webhook，請按一下[這裡](/help/webhooks/webhooks.md)。
1. 新增Webhook作為Smart Campaign中的流程步驟。

以下熒幕擷圖顯示如何使用權杖自動填入查詢引數，建立webhook以叫用上述指定的服務。 現在我們已建立webhook，可以將其新增至Smart Campaign作為流量動作。  智慧清單應包含「從電子郵件取消訂閱」觸發器。

### 驗證

若要全面測試，請在數個Marketo執行個體中以相同的電子郵件地址建立銷售機會。 請確定您擁有電子郵件地址！ 在觸發傳送電子郵件流程動作的一個執行個體中，開啟產生的電子郵件，然後按一下取消訂閱。 若要驗證結果，請登入其他每個執行個體，並檢查與電子郵件地址相關的潛在客戶記錄。 應核取「已取消訂閱」核取方塊，「已取消訂閱原因」欄位應包含具啟動同步之來源帳戶ID的附註。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-05-11_

## 使用REST API同步化銷售機會資料變更

該貼文提供程式碼範例，可循環執行以輪詢Marketo以取得更新。 我們的想法是使用Marketo API來識別潛在客戶資料的變更，並擷取已變更的潛在客戶資料。 然後，可將此資料推送至外部系統以進行同步。 顯示的程式碼範例使用我們的SOAP API。 我們有[新的行進方式](https://www.youtube.com/watch?v=G-7ZJjLy5D8&feature=youtu.be)，該方式是使用[Marketo REST API](/help/rest-api/rest-api.md)。 這篇文章說明如何使用兩個REST端點來達成相同的目標： [取得銷售機會變更](/help/rest-api/rest-api.md)，[依ID取得銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)。 此程式包含2個主要步驟：

1. 呼叫Get Lead Changes ，產生所有Lead ID的清單，這些清單可能包含已變更的特定銷售機會欄位，或在指定時段內新增的銷售機會ID。
1. 呼叫清單中每個銷售機會ID的取得銷售機會ID ，從銷售機會記錄中擷取欄位資料。

我們將採用在步驟2中擷取的資料，並將其格式化以供外部系統使用。  **程式輸入**&#x200B;依預設，程式會從目前日期起回頭一天，以尋找變更。 舉例來說，您可以每天在同一時間執行此程式。 若要更進一步回到過去，您可以將天數指定為命令列引數，有效增加時間範圍。 此程式包含數個您可以修改的變數： CUSTOM_SERVICE_DATA — 這包含您的Marketo [自訂服務](/help/rest-api/custom-services.md)資料（帳戶ID、使用者端ID、使用者端密碼）。 LEAD_CHANGE_FIELD_FILTER — 這包含我們將檢查變更之潛在客戶欄位的逗號分隔清單。 READ_BATCH_SIZE — 這是一次要擷取的記錄數。 使用此選項調整回應到內文大小。 **程式輸出**&#x200B;程式會收集所有變更的潛在客戶記錄，並將它們格式化為JSON中的潛在客戶物件陣列，如下所示：

```json
{
    "result": [
        {
            "leadId": "318592",
            "updatedAt": "2015-07-22T19:19:07Z",
            "firstName": "David",
            "lastName": "Everly",
            "email": "<deverly@marketo.com>"
        },

        ...more lead objects here...
    ]
}
```

這表示您可以接著傳遞此JSON作為要求裝載給外部Web服務，以同步資料。 **程式邏輯**&#x200B;我們先建立時間範圍、撰寫REST端點URL，並取得驗證存取權杖。 接下來，我們啟動「取得分頁權杖/取得銷售機會變更」回圈，在此回圈中執行，直到我們耗盡銷售機會變更的供應為止。 此回圈的目的是累積不重複銷售機會ID的清單，以便我們稍後可以在程式中將其傳遞至依ID取得銷售機會。 在此範例中，我們指示Get Lead Changes尋找以下欄位的變更：firstName、lastName、email。 您可以隨意選取任何欄位組合，以符合您的目的。 Get Lead Changes會傳回包含活動型別ID的「result」物件，我們可將其用於篩選結果。 注意：您可以呼叫[取得活動型別](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getAllActivityTypesUsingGET) REST端點來取得活動型別清單。 我們對傳回的2種活動型別感興趣：1. 新銷售機會(12)

```json
{
    "id": 12024682,
    "leadId": 318581,
    "activityDate": "2015-03-17T00:18:41Z",
    "activityTypeId": 12,
    "primaryAttributeValueId": 318581,
    "primaryAttributeValue": "David Everly",
    "attributes": [
        {
            "name": "Created Date",
            "value": "2015-03-16"
        },
        {
            "name": "Source Type",
            "value": "New lead"
        }
    ]
}
```

1. 變更資料值(13)我們可藉由檢查變更資料值回應中的「name」屬性，得知哪個欄位已變更。

```json
{
    "id": 12024689,
    "leadId": 318581,
    "activityDate": "2015-03-17T22:58:18Z",
    "activityTypeId": 13,
    "fields": [
        {
            "id": 31,
            "name": "lastName",
            "newValue": "Evely",
            "oldValue": "Everly"
        }
    ],
    "attributes": [
        {
            "name": "Source",
            "value": "Web form fillout"
        }
    ]
}
```

當傳回這兩種活動中的任一種時，我們會將相關聯的Lead ID儲存在清單中。 取得清單後，我們可以逐一檢視清單，對每個專案呼叫Get Lead by ID。 這會擷取清單中每個潛在客戶的最新潛在客戶資料。 在此範例中，我們擷取下列潛在客戶欄位： `leadId`、`updatedAt`、`firstName`、`lastName`和`email`。 您可以隨意選取任何欄位組合，以符合您的目的。 若要這麼做，請指定Get Lead by Id的欄位引數。 最後，我們將結果命名為Lead物件的陣列，如上所述。

**程式碼**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadChanges {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            {INSERT YOUR CUSTOM SERVICE DATA HERE};

    // Lead fields that we are interested in
    private static final String LEAD_CHANGE_FIELD_FILTER = "firstName,lastName,email";

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Activity type ids that we are interested in
    private static final int ACTIVITY_TYPE_ID_NEW_LEAD = 12;
    private static final int ACTIVITY_TYPE_ID_CHANGE_DATA_VALE = 13;

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String leadChangesUrl = String.format("%s/rest/v1/activities/leadchanges.json?access_token=%s&fields=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_CHANGE_FIELD_FILTER, READ_BATCH_SIZE);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        HashSet leadIdList = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {

            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;

            do {
                moreResult = false;

                // Call Get Lead Changes API
                JsonObject leadChangesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadChangesUrl, nextPageToken)));
                if (leadChangesObj.get("success").asBoolean()) {
                    moreResult = leadChangesObj.get("moreResult").asBoolean();
                    nextPageToken = leadChangesObj.get("nextPageToken").asString();

                    if (leadChangesObj.get("result") != null) {
                        JsonArray resultAry = leadChangesObj.get("result").asArray();
                        for (JsonValue resultObj : resultAry) {
                            int activityTypeId = resultObj.asObject().get("activityTypeId").asInt();


                            // Store lead ids for later use
                            boolean storeThisId = false;
                            if (activityTypeId == ACTIVITY_TYPE_ID_NEW_LEAD) {
                                storeThisId = true;
                            } else if (activityTypeId == ACTIVITY_TYPE_ID_CHANGE_DATA_VALE) {
                                // See if any of the changed fields are of interest to us
                                JsonArray fieldsAry = resultObj.asObject().get("fields").asArray();
                                for (JsonValue fieldsObj : fieldsAry) {
                                    String name = fieldsObj.asObject().get("name").asString();
                                    if (LEAD_CHANGE_FIELD_FILTER.contains(name)) {
                                        storeThisId = true;
                                    }
                                }

                            }

                            if (storeThisId) {
                                leadIdList.add(resultObj.asObject().get("leadId").toString());
                            }
                        }
                    }
                }

            } while (moreResult);
        }

        JsonObject result = new JsonObject();
        JsonArray leads = new JsonArray();

        for (Object o : leadIdList) {
            String leadId = o.toString();

            // Compose Get Lead by Id URL
            String getLeadUrl = String.format("%s/rest/v1/lead/%s.json?access_token=%s",
                    baseUrl, leadId, accessToken);

            // Call Get Lead by Id API
            JsonObject leadObj = JsonObject.readFrom(getData(getLeadUrl));
            if (leadObj.get("success").asBoolean()) {
                if (leadObj.get("result") != null) {
                    JsonArray resultAry = leadObj.get("result").asArray();
                    for (JsonValue resultObj : resultAry) {

                        // Create lead object
                        JsonObject lead = new JsonObject();
                        lead.add("leadId", leadId);
                        lead.add("updatedAt", resultObj.asObject().get("updatedAt").asString());
                        lead.add("firstName", resultObj.asObject().get("firstName").asString());
                        lead.add("lastName", resultObj.asObject().get("lastName").asString());
                        lead.add("email", resultObj.asObject().get("email").asString());

                        // Add lead object to leads array
                        leads.add(lead);
                    }
                }
            }
        }

        // Add leads array to result object
        result.add("result", leads);

        // Print out result object
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

您現在已擁有，[生活是一首快樂的歌](https://www.youtube.com/watch?v=zFaBwZDywLk)。 祝您使用愉快！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-07-31_

## 2015年5月版本更新

### REST API

* 機會API。 已引入新的[機會端點](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)，可讓您以程式設計方式列出、描述及CRUD Marketo機會物件中的資料。

附註：已新增角色許可權，以提供對Opportunity端點的存取權：唯讀Opportunity、讀寫機會。 如果您的API使用者角色早於機會API的發行，則您需要建立具有這些許可權的新API使用者角色以啟用存取權。 否則，您會收到603「拒絕存取」錯誤回應。

* 資產API — 代碼片段。 已引入程式碼片段的新[資產端點](https://developer.adobe.com/marketo-apis/api/asset/#snippet_endpoints)，以允許您以程式設計方式處理程式碼片段物件。 程式碼片段可做為電子郵件和登入頁面中的動態內容區塊。
* 銷售機會API — 更新銷售機會分割。 已新增分割區[的新](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updatePartitionsUsingPOST)潛在客戶端點，以允許您更新一或多個潛在客戶的分割區。
* 修正銷售機會相關API在「createdAt」和「updatedAt」屬性中缺少時區位移的問題。
* 修正排程行銷活動在超過每日最大呼叫數時未傳回正確錯誤碼的問題。
* 修正「依ID取得資料夾」有時會為「父」和「說明」屬性傳回null的問題。
* 修正「依ID核准電子郵件」在某些情況下會引發系統錯誤的問題。
* 修正「依資料夾ID建立Token」會造成特定情況下無法使用Token的問題。

### 即時Personalization (RTP)

* Rich Media Recommendation API. 新的[多媒體建議](/help/javascript-api/web-personalization.md)功能已新增至RTP JavaScript API。 「多媒體內容建議」可讓您的網站訪客參與機器學習和預測性分析所支援的最相關內容。 使用文字說明和影像增強您的內容資產，並在您的網站上內嵌多個內容建議。

### 行動參與SDK

iOS v0.3.4/Android v0.3.3

* 自訂動作。 新增透過傳送自訂動作追蹤使用者互動的功能。 如需詳細資訊，請參閱[這裡](/help/mobile/mobile.md)的[傳送自訂動作]。
* 已棄用trackPushNotification方法。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-05-26_

## 2015年6月版本更新

### REST API

* 公司API

已引入新的[公司端點](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)，可讓您以程式設計方式列出、描述及CRUD Marketo公司物件中的資料。

注意：已新增角色許可權，以提供對「方案」端點的存取權：唯讀公司、讀寫公司。 如果您的API使用者角色早於公司API的發行日期，則您需要透過這些許可權更新您的API使用者角色以啟用存取權。 否則，您會收到603「拒絕存取」錯誤回應

* 資產API — 程式

更新Asset API以支援Application資料夾和Program資料夾。 數個資產API已接受整數形式的請求資料夾ID。 資料夾ID可以作為URI的一部分或請求本文的一部分傳遞，具體取決於API。

現在，您必須連同資料夾ID一起指定資料夾型別。 資料夾型別是「Program」或「Folder」。 「資料夾」指定應用程式資料夾，而「程式」指定程式資料夾。 視您呼叫的API而定，資料夾型別的指定方式有兩種：

1. 使用「FolderIdType」資料結構。 FolderIdType是一個簡單的JSON區塊，其中包含id和型別配對，如下所示：

`{ "id" : **id**, "type" : "**type**" }`

其中&#x200B;**id**&#x200B;是資料夾識別碼，&quot;**type**&quot;是資料夾型別。 「**型別**」的允許值為「資料夾」或「程式」。

範例 — 建立資料夾

`POST /asset/v1/folders.json`

`parent={"id":416,"type":"Folder"}&name=Test Folder&description=This is a test folder`

1. 使用現有的id引數，並使用「type」查詢引數指定其型別。

範例 — 依Id取得資料夾

`GET /rest/asset/v1/folder/1016.json?type=Program`

在Result物件中包含資料夾ID的所有API回應，現在也將包含其值為FolderIdType的folderId屬性。 這可用來決定指定資料夾ID的資料夾型別。

範例 — 依名稱取得資料夾

`GET /rest/asset/v1/folder/byName.json?name=Social Media`

```json
"result": [
{
...

"folderId": {"id":341, "type": "Program"},
...

"id":"341"
}
]
```

若要判斷指定識別碼的資料夾型別，您可以使用[瀏覽資料夾](https://developer.adobe.com/marketo-apis/api/asset/browse-folders/) API。

API回應中的「type」屬性已重新命名為「folderType」。 這是為了避免與FolderIdType中包含的「type」元素混淆。

例如，從這個：

&quot;**type**&quot;：&quot;Marketing Folder&quot;

這個：

&quot;**folderType**&quot;： &quot;Marketing Folder&quot;


### 行動參與SDK

iOS 0.3.5

* 修正設定測試裝置對話方塊在主執行緒上執行的問題。 [MOB-638]
* 新增無法註冊測試裝置時的錯誤處理。 [MOB-639]

Android 0.3.3

* 已將android:configChanges屬性新增至AndroidManifest.xml `<activity>`元素，以便在您新增測試裝置並變更方向時，不會關閉進度對話方塊。 [MOB-687]

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-06-30_

## 使用RTP API的應用程式內網頁Personalization (Beta)

我們的數位客戶為其使用者提供網頁應用程式解決方案，而如果他們在安全的網頁應用程式環境中使用Marketo即時Personalization (RTP)，我們便會收到相關請求。 答案是肯定的！ 我們已發佈應用程式內傳訊API，因此您可以個人化內容並推廣行銷活動，例如網路研討會、新功能發佈、向上銷售，並根據您的網頁應用程式資料與使用者互動。 例如，個人化以下專案的應用程式內內容：

* 根據使用者活動的試用優惠
* 不同的訂閱型別（向上銷售、交叉銷售或網路研討會培訓）
* 與使用者活動相關的新功能

**使用案例範例** Marketo的客戶成功團隊使用應用程式內Web Personalization與特定訂閱型別（Spark、Standard、Select或Enterprise）進行通訊並提供個人化內容，確保他們看到漸進式行銷活動，並根據參與度培養應用程式內使用者。 讓我們看看如何為具有企業訂閱型別的使用者完成這項作業。 **先決條件**&#x200B;瞭解[RTP使用者內容API](/help/javascript-api/web-personalization.md)。 **啟用Marketo支援的使用者內容API**&#x200B;要求，為您的RTP帳戶啟用使用者內容API。 **設定自訂變數** RTP中有5個可用的自訂變數位置可傳送資料。 在此範例中，我們將傳送使用者訂閱型別Enterprise至自訂變數1。

`rtp('set', 'customVar1', 'Enterprise');`

**建立新的RTP區段**&#x200B;移至&#x200B;**區段**，按一下&#x200B;**新建**。

1. 將&#x200B;**使用者內容API**&#x200B;篩選器拖曳至區段產生器。
1. 選取&#x200B;**值**&#x200B;為&#x200B;**Enterprise**&#x200B;的&#x200B;**自訂變數1** （訂閱型別）。

**根據先前的造訪記錄顯示行銷活動**&#x200B;如果訪客在上次造訪中點按了行銷活動，則向訪客顯示其他行銷活動。

1. 在&#x200B;**使用者內容API**&#x200B;中，按一下&#x200B;**(+)**&#x200B;以新增另一個使用者內容API屬性
1. 新增&#x200B;**運運算元（AND或OR）。**
1. 選取&#x200B;**促銷活動 — 已點按。**&#x200B;將&#x200B;**行銷活動ID**&#x200B;設定為行銷活動的ID。 （請參閱下方的附註，瞭解如何尋找Campaign ID。）
1. 按一下&#x200B;**儲存並定義行銷活動**&#x200B;以建立行銷活動創意。

整體而言，如果訪客與等於Enterprise的自訂變數（訂閱型別）相關聯，且在上次造訪中點選了促銷活動（識別碼：5390），則此區段相符。 下一步是定義此區段的個人化行銷活動。 底下熒幕擷圖顯示「我的Marketo」頁面上出現的RTP對話方塊促銷活動（左下方），此促銷活動可為Enterprise使用者推廣網路研討會。  **注意：** **找到行銷活動ID**&#x200B;移至&#x200B;**行銷活動**，將游標停留在&#x200B;**行銷活動名稱**上以找到行銷活動ID。
由_David_&#x200B;張貼於&#x200B;_2015-06-17_

## 使用Marketo REST API傳送交易式電子郵件：第1部分

Marketo有一些設定需求，需要透過Marketo REST API執行必要的呼叫。

* 收件者必須在Marketo中擁有記錄
* 您的Marketo執行個體中需要建立和核准交易式電子郵件。
* 必須有已要求Campaign (Source：網站服務API)的有效觸發促銷活動，設定為傳送電子郵件

首先[建立並核准您的電子郵件](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。 如果電子郵件確實是交易式的，您可能需要將其設定為可操作，但請確保其符合操作性法律資格。 這可透過「電子郵件動作>電子郵件設定」下的「編輯」畫面來設定。 核准此專案，我們就準備好建立我們的行銷活動了。 如果您是建立行銷活動的新手，請檢視docs.marketo.com上的[建立新的Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/creating-a-smart-campaign/create-a-new-smart-campaign)文章。 在您建立行銷活動後，我們需要完成這些步驟。 使用「已請求促銷活動」觸發程式設定您的智慧清單：現在我們需要設定流程，以將「傳送電子郵件」步驟指向我們的電子郵件。 啟用之前，您必須在「排程」索引標籤中決定某些設定。 如果此特定電子郵件只應傳送至指定記錄一次，則保留資格設定。 不過，如果要求他們多次收到電子郵件，您可將之調整為每次或其中一個可用步調。 現在我們已準備好啟動。

### 傳送API呼叫

**注意：**&#x200B;在下方的Java範例中，我們使用minimal-json套件處理程式碼中的JSON表示法。 您可以在此閱讀此專案的詳細資訊： [https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json)透過API傳送交易式電子郵件的第一部分是，確定您的Marketo執行個體中存在具有對應電子郵件地址的記錄，並且我們有權存取其銷售機會ID。 出於本文目的，我們將假設電子郵件地址已在Marketo中，並且我們只需要擷取記錄ID。 為此，我們使用[依篩選器型別取得多個銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)呼叫，並且正在重複使用上篇文章中的部分Java程式碼。 讓我們來看看要求行銷活動的主要方法：

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("<requestCampaign.test@marketo.com>");

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

若要從leadsRequest的JsonObject回應取得這些結果，我們需要編寫一些程式碼。 若要擷取Array中的第一個結果，我們需要從JsonObject擷取Array，並取得在0索引的物件：

`JsonArray leadsResult = leadsRequest.getData().get("result").asArray();`
`int leadId = leadsResult.get(0).asObject().get("id").asInt();`

從現在開始，我們只需要進行「請求行銷活動」呼叫。 為此，所需的引數為請求URL中的ID，以及包含一個成員「id」的JSON物件陣列。 讓我們來看看相關的程式碼：

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
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   System.out.println("Executing RequestCampaign calln" + "Endpoint: " + s + "nRequest Body:n"  + requestBody);
   URL url = new URL(s);
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
   System.out.println("Result:n" + result);
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

此類別有一個接受驗證的建構函式，以及促銷活動的ID。 透過傳遞包含記錄的ID的ArrayList `<Integer>`給setLeads，或透過使用addLead （取一個整數並將其附加到leads屬性中的現有ArrayList）將潛在客戶新增到物件。 若要觸發API呼叫以將潛在客戶記錄傳遞至促銷活動，需要呼叫postData，其會傳回包含來自請求的回應資料的JsonObject。 呼叫要求促銷活動時，Marketo中的目標觸發促銷活動會處理每個傳遞至呼叫的潛在客戶，系統會傳送先前建立的電子郵件給他們。 恭喜，您已透過Marketo REST API觸發電子郵件。 請留意第2部分，我們將透過「請求行銷活動」以動態方式自訂電子郵件內容。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-07-17_

## 使用REST API從Marketo中驗證及擷取潛在客戶資料

**注意：**&#x200B;在下方的Java範例中，我們使用minimal-json套件處理程式碼中的JSON表示法。 您可以在這裡閱讀更多有關本專案的資訊： [https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json)與Marketo整合時最常見的要求之一是擷取潛在客戶資料。 大部分（如果不是全部整合的話）都需要從Marketo訂閱擷取或提交潛在客戶資料，所以今天我們將透過使用訂閱[驗證](/help/rest-api/authentication.md)，然後從中擷取潛在客戶資料，來瞭解基本潛在客戶資訊擷取任務。 若要擷取銷售機會，首先我們需要使用OAuth 2.0向Target Marketo執行個體進行驗證。我們需要向Marketo驗證的三項資訊：使用者端ID、使用者端密碼和Marketo執行個體主機。 以下是我們用來驗證的類別：

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Auth {
 protected String marketoInstance; //Instance Host obtained from Admin -> Web Service
 private String clientId; //clientId obtained from Admin -> Launchpoint -> View Details for selected service
 private String clientSecret; //clientSecret obtained from Admin -> Launchpoint -> View Details for selected service
 private String idEndpoint; //idEndpoint constructed to authenticate with service when constructor is used
 private String token; //token is stored for reuse until expiration
 private Long expiry; //used to store time of expiration

 //Creates an instance of Auth which is used to Authenticate with a particular service on a particular instance
 public Auth(String id, String secret, String instanceUrl) {
  this.clientId = id;
  this.clientSecret = secret;
  this.marketoInstance = instanceUrl;
  this.idEndpoint = marketoInstance + "/identity/oauth/token?grant_type=client_credentials&client_id=" + clientId + "&client_secret=" + clientSecret;
 }
 //The only public method of Auth, used to check if the current value of Token is valid, and then to retrieve a new one if it is not
 public String getToken(){
  long now  = System.currentTimeMillis();
  if (expiry == null || expiry < now){
   System.out.println("Token is empty or expired. Trying new authentication");
   JsonObject jo = getData();
   System.out.println("Got Authentication Response: " + jo);
   this.token = jo.get("access_token").asString();
                        //expires_in is reported as seconds, set expiry to system time in ms + expires \* 1000
   this.expiry = System.currentTimeMillis() + (jo.get("expires_in").asLong() \* 1000);
  }
  return this.token;
 }
 //Executes the authentication request
 private JsonObject getData(){
  JsonObject jsonObject = null;
  try {
   URL url = new URL(idEndpoint);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
   urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "application/json");
            System.out.println("Trying to authenticate with " + idEndpoint);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                Reader reader = new InputStreamReader(inStream);
                jsonObject = JsonObject.readFrom(reader);
            }else {
                throw new IOException("Status: " + responseCode);
            }
  } catch (MalformedURLException e) {
   e.printStackTrace();
  }catch (IOException e) {
            e.printStackTrace();
        }
  return jsonObject;
 }
}
```

此程式碼可讓您從「管理員 — >啟動點（ID和密碼）」和「管理員 — >網站服務（主機）」，使用我們的使用者端ID、使用者端密碼和主機（做為marketoInstance）建立Auth執行個體。 它會公開getToken方法，該方法會測試目前儲存的權杖是否為Null或過期，然後傳回現有權杖，或提出新的驗證請求，然後從JSON回應的「access_token」成員傳回新權杖。 現在您可以使用Marketo執行個體進行驗證，下一步是擷取我們的銷售機會。 我們正在使用此類別：

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Leads {
 private StringBuilder endpoint;
 private Auth auth;
 public String filterType;
 public ArrayList filterValues = new ArrayList();
 public Integer batchSize;
 public String nextPageToken;
 public ArrayList fields = new ArrayList();

 public Leads(Auth a) {
  this.auth = a;
  this.endpoint = new StringBuilder(this.auth.marketoInstance + "/rest/v1/leads.json");
 }
 public Leads setFilterType(String filterType) {
  this.filterType = filterType;
  return this;
 }
 public Leads setFilterValues(ArrayList filterValues) {
  this.filterValues = filterValues;
  return this;
 }
 public Leads addFilterValue(String filterVal) {
  this.filterValues.add(filterVal);
  return this;
 }
 public Leads setBatchSize(Integer batchSize) {
  this.batchSize = batchSize;
  return this;
 }
 public Leads setNextPageToken(String nextPageToken) {
  this.nextPageToken = nextPageToken;
  return this;
 }
 public Leads setFields(ArrayList fields) {
  this.fields = fields;
  return this;
 }

 public JsonObject getData() {
        JsonObject result = null;
        try {
         endpoint.append("?access_token=" + auth.getToken() + "&filterType=" + filterType + "&filterValues=" + csvString(filterValues));
         if (batchSize != null && batchSize > 0 && batchSize <= 300){
             endpoint.append("&batchSize=" + batchSize);
            }
            if (nextPageToken != null){
             endpoint.append("&nextPageToken=" + nextPageToken);
            }
            if (fields != null){
             endpoint.append("&fields=" + csvString(fields));
            }
            URL url = new URL(endpoint.toString());
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "text/json");
            InputStream inStream = urlConn.getInputStream();
            Reader reader = new InputStreamReader(inStream);
            result = JsonObject.readFrom(reader);
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return result;
    }
 private String csvString(ArrayList fields) {
  StringBuilder fieldCsv = new StringBuilder();
     for (int i = 0; i < fields.size(); i++){
      fieldCsv.append(fields.get(i));
      if (i + 1 != fields.size()){
       fieldCsv.append(",");
      }
     }
  return fieldCsv.toString();
 }
}
```

此類別有一個建構函式，它接受Auth物件，然後會公開選用的和必要引數的數個設定器。 在此情況下，我們只需要擔心設定filterType和filterValues，即可依電子郵件地址取得銷售機會。 所以我們將setFilterType用於「電子郵件」，並將addFilterValue用於需要擷取ID的電子郵件地址。 當您設定好引數後，可以使用getData方法從潛在客戶端點擷取JsonObject，其中包含結果陣列，該陣列具有擷取之潛在客戶記錄的JSON表示法。

### 整合在一起

我們已完成範常式式碼，現在來看看簡單範例，以擷取符合測試電子郵件地址<testlead@marketo.com>的潛在客戶。 為此，我們需要將setFilterType用於「電子郵件」，並將addFilterValue用於需要擷取資訊的電子郵件地址。 當您設定好引數後，可以使用getData方法從潛在客戶端點擷取JsonObject，其中包含結果陣列，該陣列具有擷取之潛在客戶記錄的JSON表示法。

```java
package dev.marketo.blog_leads;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        //Change the credentials here to your own
        Auth auth = new Auth("CHANGE ME", "CHANGE ME", "CHANGE ME");
        //Create and parameterize an instance of Leads
        Leads getLeads = new Leads(auth)
              .setFilterType("email")
              .addFilterValue("<testlead@marketo.com>");
        //get the inner results array of the response
        JsonObject leadsResult = getLeads.getData();
        System.out.println(leadsResult);
    }
}
```

在此主要方法範例中，我們會建立Auth的例項，然後將其傳遞至新的Leads建構函式。 我們使用setFilterType和addFilterValue來設定銷售機會執行個體，擷取和電子郵件地址&quot;<testlead@marketo.com>&quot;相符的銷售機會。 此範例會將它列印到主控台：

Token是空的或過期的。 正在嘗試新的驗證
正在嘗試使用<https://299-BYM-827.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id=b417d98f-9289-47d1-a61f-db141bf0267f&client_secret=0DipOvz4h2wP1ANeVjlfwMvECJpo0ZYc>進行驗證
取得驗證回應： {&quot;access_token&quot;：&quot;ec0f02c0-28ac-4d6c-b7d7-00e47ae85ff1:st&quot;，&quot;token_type&quot;：&quot;bearer&quot;，&quot;expires_in&quot;:538，&quot;scope&quot;：&quot;<apiuser@mktosupport.com>&quot;}
{&quot;requestId&quot;：&quot;14fb6#14e6a7a9ad6&quot;，&quot;result&quot;：[{&quot;id&quot;:1026322，&quot;updatedAt&quot;：&quot;2015-07-07T21:43:25Z&quot;，&quot;lastName&quot;：&quot;Lead&quot;，&quot;email&quot;：&quot;<testlead@marketo.com>&quot;，&quot;createdAt&quot;：&quot;2015-07-07T21:43:25Z&quot;，&quot;firstName&quot;：&quot;Name&quot; test&quot;}，{&quot;id&quot;:1026323，&quot;updatedAt&quot;：&quot;2015-07-07T21:43:43Z&quot;，&quot;lastName&quot;：&quot;Lead2&quot;，&quot;email&quot;：&quot;<testlead@marketo.com>&quot;，&quot;createdAt&quot;：&quot;2015-07-07T21:43:43Z&quot;，&quot;firstName&quot;：&quot;Test&quot;}]，&quot;succcess&quot;:true}

現在我們有銷售機會資料，可以用任何我們想要的方式處理。 感謝您閱讀，請在評論中留下任何回饋意見。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-07-10_

## 2015年7月版本更新

REST API

* 銷售人員API

已引入新的[銷售人員端點](/help/rest-api/sales-persons.md)，可讓您以程式設計方式列出、描述及CRUD Marketo銷售人員物件中的資料。 此外，銷售人員可指派給銷售機會、商機或公司。 這是透過在呼叫銷售機會、商機或公司的Create/Update/Upsert端點時指定「externalSalesPersonId」屬性來完成的。

備註：已新增角色許可權，以提供對「方案」端點的存取權：唯讀銷售人員、讀寫銷售人員。 如果您的API使用者角色早於銷售人員API的發行日期，則您需要更新具有這些許可權的API使用者角色以啟用存取權。 否則，您會收到603「拒絕存取」錯誤回應。

* 資產API — 登陸頁面範本

已引入新的[登入頁面範本端點](https://developer.adobe.com/marketo-apis/api/asset/#landing_page_templates_endpoints)，可讓您以程式設計方式列出、建立及更新與登入頁面範本相關聯的資料。

* 資產API — 區段

已引入兩個區段相關端點：

[取得區段](https://developer.adobe.com/marketo-apis/api/asset/get-segments)

[依Id取得分段](https://developer.adobe.com/marketo-apis/api/asset/get-segmentation-by-id)

* 修正「依名稱取得資料夾」未接受「workSpace」引數的問題。 [LM-61059]
* 對自訂物件API進行幾項效能改善。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-07-17_

## 使用Marketo REST API建立並關聯銷售機會、公司和機會

為了充分利用Marketo分析，在您的銷售機會、公司及機會記錄之間建立正確且強大的關聯性至關重要。 當您未運用原生CRM同步時，建立這些關係可能會遇到一些困難，因此我們今天會逐步建置這些關係。

### 物件關係

在Marketo中，有幾個重要關係可以完全建立機會報告：

* 潛在客戶與商機與OpportunityRole物件具有多對多關係。
* Opportunerrole同時具有leadId和externalopportunityid欄位，可建立從潛在客戶到商機的關係。
* 為了符合Has Opportunity智慧清單篩選器的資格，潛在客戶必須具有與機會相關的OpportunityRole。
* 機會透過externalCompanyId欄位與公司物件有多對一的關係。
* 潛在客戶透過externalCompanyId欄位與公司具有一對多關係。
* 機會是根據潛在客戶的贏取方案或其在方案中的成員資格和成功來歸因於方案（請參閱[瞭解歸因](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/reporting/revenue-cycle-analytics/revenue-tools/attribution/understanding-attribution)）。

在潛在客戶資料庫中建立這些關係，可讓您充分利用Marketo Analytics，並瞭解您的方案對機會建立和成功率的影響。

### 公司

建立這些關係最簡單的方式是從公司建立開始。 這可確保我們可以在建立期間將externalCompanyId傳遞給商機，而不必執行其他API呼叫來更新建立商機。 根據現有設定，這不一定是必要的步驟，但關聯公司的淨新潛在客戶和聯絡人需要將這些記錄新增到您的Marketo執行個體中，才能建立關係，因此讓我們看看建立公司記錄的一些程式碼。

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertCompanies {
 public List<JsonObject> input; //a list of Companies to use for input.  Each must have a member "externalCompanyId".
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters(externalCompanyId), idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertCompanies(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/companies.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertCompanies(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 //adds input to existing list, creates arraylist if it was built without a list
 public UpsertCompanies addCompanies(JsonObject... companies){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : companies) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
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
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our company records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertCompanies instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 //sets or replaces existing input with list
 public UpsertCompanies setInput(List input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertCompanies setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertCompanies setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

公司API提供兩個重複資料刪除選項，由請求中的dedupeBy引數設定：「dedupeFields」和「idField」。 可透過呼叫Describe Companies來明確擷取這些值。 如果未設定dedupeBy，其預設值為dedupeFields。 若是公司記錄，dedupeFields一律會對應至「externalCompanyId」（由外部來源設定的任意字串）以及「marketoId」欄位(由Marketo建立後產生並傳回的整數)的idField。 根據dedupeBy的選取專案，公司記錄的任何更新插入呼叫中必須包含其中一個externalCompanyId或marketoId。 這些相同的要求適用於機會和機會角色物件API。 我們的程式碼會公開兩個建構函式：一個接受Auth物件的單一引數，另一個接受Auth和[JsonObject](https://github.com/ralfstx/minimal-json)公司記錄的清單。 如果未使用輸入List建構，則必須透過addCompanies方法新增公司記錄，如果輸入為Null，此方法會檢查建立新的ArrayList，然後將所有JsonObject引數新增到輸入List。 使用範例如下：

```java
//Create a new company to associate to
JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
JsonObject companiesResult = upsertCompanies.postData();
System.out.println(companiesResult);
```

我們正在建立單一公司JsonObject，其中只有一個欄位`externalCompanyId`，然後建構UpsertCompanies的執行個體，並將我們的公司新增到具有`addCompanies`的輸入清單中。

### 機會

與公司物件類似，機會API有`dedupeBy`引數，接受&quot;dedupeFields&quot;或&quot;idField&quot;，分別對應至&quot;externalopportunityid&quot;和&quot;marketoGUID&quot;。 以下是我們的程式碼，看起來與UpsertCompanies類別相當類似：

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunities {
 public List<JsonObject> input; //a list of Opportunities to use for input.  Each must have a member "externalopportunityid".  Each can optionally include "externalCompanyId" for company association
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertOpportunities(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunities(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunities addOpportunities(JsonObject... opp){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : opp) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
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
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our Opportunity records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunities setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunities setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunities setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

提供的建構函式選項相同，會使用Auth或`Auth+List<JsonObject>`以及`addOpportunities`方法來輸入JsonObject機會。 使用範例如下：

```java
//Create some JsonObjects for Opportunity Data
JsonObject opp1 = new JsonObject().add("name", "opportunity1")
    .add("externalopportunityid", "Opportunity1Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");
JsonObject opp2 = new JsonObject().add("name", "opportunity2")
    .add("externalopportunityid", "Opportunity2Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");

//Create an Instance of UpsertOpportunities and POST it
UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                        .setAction("createOnly")
                        .addOpportunities(opp1, opp2);
JsonObject oppsResult = upsertOpps.postData();
System.out.println(oppsResult);
```

在此，我們要建立兩個範例機會，然後提供名稱、externalopportunityid、externalCompanyId和externalCreatedDate欄位的值。 我們尚未討論externalCreatedDate，但請務必使用，因為在RCE中，機會建立時會被視為主要欄位，因此對正確的歸因很重要。 您可以使用組織的商業邏輯，根據您正在回填現有商機資料還是即時建立新資料，來決定您在此欄位中輸入的內容。 我們會建立UpsertOpportunities例項，然後透過addOpportunities新增JsonObjects。 現在執行個體已設定，您可以將它透過postData推送到Marketo並列印結果

### 角色

角色與前兩個物件相當類似，不同之處在於將dedupeBy設定為dedupeFields時，角色的需求稍有不同。 透過此方法建立或更新記錄時，角色需要包含三個欄位：「leadId」、「role」和「externalopportunityid」。 &quot;role&quot;可以是任何字串值，但其他兩個值必須分別參考潛在客戶的有效ID和機會的有效ID。

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunityRoles {
 public List<JsonObject> input; //Array of Opportunity Roles as JsonObjects, must have "leadId", "role" and "externalopprtunityid"
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate, defaults to createOrUpdate if unset
 public String dedupeBy;//select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object

 //Constructs an UpsertOpportunityRoles with Auth, but with no input set
 public UpsertOpportunityRoles(Auth auth) {
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities/roles.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunityRoles(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunityRoles addRoles(JsonObject... role){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : role) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }
 //executes the request to Marketo, body will be empty if input is not set
 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");//"application/json" content-type is required.
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream();  //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 public JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject();
  JsonArray in = new JsonArray();
  for (JsonObject jo : input) {
   in.add(jo);
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action);
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy);
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunityRoles setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunityRoles setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunityRoles setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

建構函式和addRoles方法的模式與上一個範例類似。 範例如下。

```java
//Create Some opp roles now
JsonObject opp1Role = new JsonObject()
                        .add("role", "Captain")
                        .add("externalopportunityid", opp1.get("externalopportunityid").asString())
                        .add("leadId", 318794);
JsonObject opp2Role = new JsonObject()
                        .add("role", "Commander")
                        .add("externalopportunityid", opp2.get("externalopportunityid").asString())
                        .add("leadId", 318795);

//Create an Instance of UpsertOpportunityRoles and POST it
UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
                        .setAction("createOnly")
                        .addRoles(opp1Role, opp2Role);
JsonObject rolesResult = upsertRoles.postData();
System.out.println(rolesResult);
```


我們在此為兩個範例角色建立新的JsonObjects，並新增其必要的dedupeFields，從我們已建立的機會中提取externalopportunityid，然後將其向下推送至Marketo。

### 整合所有內容

以下是主要方法的完整範例：

```java
package dev.marketo.opportunities;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //create an Instance of Auth
        Auth auth = new Auth("CLIENT_ID_CHANGE_ME", "CLIENT_SECRET_CHANGE_ME", "MARKETO_HOST_CHANGE_ME");

        //Create a new company to associate to
        JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
        UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
        JsonObject companiesResult = upsertCompanies.postData();
        System.out.println(companiesResult);

        //Create some JsonObjects for Opportunity Data
        JsonObject opp1 = new JsonObject().add("name", "opportunity1")
           .add("externalopportunityid", "Opportunity1Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");
        JsonObject opp2 = new JsonObject().add("name", "opportunity2")
           .add("externalopportunityid", "Opportunity2Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");

        //Create an Instance of UpsertOpportunities and POST it
        UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                                .setAction("createOnly")
                                .addOpportunities(opp1, opp2);
        JsonObject oppsResult = upsertOpps.postData();
        System.out.println(oppsResult);

        //Create Some opp roles now
        JsonObject opp1Role = new JsonObject()
           .add("role", "Captain")
           .add("externalopportunityid", opp1.get("externalopportunityid").asString())
           .add("leadId", 318794);
        JsonObject opp2Role = new JsonObject()
           .add("role", "Commander")
           .add("externalopportunityid", opp2.get("externalopportunityid").asString())
           .add("leadId", 318795);

        //Create an Instance of UpsertOpportunityRoles and POST it
        UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
           .setAction("createOnly")
           .addRoles(opp1Role, opp2Role);
        JsonObject rolesResult = upsertRoles.postData();
        System.out.println(rolesResult);

    }
}
```

您可以檢視建立公司、商機和角色的順序。 現在，您已準備好將您的公司和機會資料同步至Marketo。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-08-07_

## 使用Marketo REST API傳送交易式電子郵件：第2部分，自訂內容

本週我們將探討如何透過Request Campaign API呼叫，將動態內容傳遞給電子郵件。 Request Campaign不僅允許從外部觸發電子郵件，而且您也可以取代電子郵件中[My Token](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program)的內容。 我的Token是可重複使用的內容，可在方案或行銷資料夾層級自訂。 這些也可作為預留位置存在，以透過您的請求行銷活動呼叫取代。

### 建立電子郵件

為了自訂我們的內容，首先我們需要在Marketo中設定[程式](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/create-a-program)和[電子郵件](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。 若要產生自訂內容，我們必須在方案內建立權杖，然後將它們放入要傳送的電子郵件中。 為了簡單起見，在此範例中，我們僅使用一個權杖，但您可以取代電子郵件、寄件者電子郵件、寄件者名稱、回覆或電子郵件中任何內容的任何數量權杖。 所以讓我們建立一個Token Rich Text作為取代，並將其稱為「bodyReplacement」。 RTF可讓我們使用想要輸入的任意HTML來取代權杖中的任何內容。 Token在空白時無法儲存，因此請在此插入一些預留位置文字。 現在，我們需要將代號插入電子郵件中：現在，可以透過請求行銷活動呼叫存取此代號，以進行取代。 此代號可以很簡單，只需一行文字，但需要根據每封電子郵件進行取代，也可以包含電子郵件的幾乎整個版面。

### 代碼

我們正在擴充上週的程式碼，以將自訂代號傳遞至我們的請求促銷活動呼叫。

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
        String bodyReplacement = "<div class="replacedContent"><p>This content has been replaced</p></div>";
        rc.addToken("{{my.bodyReplacement}}", bodyReplacement);
        rc.postData();
    }
}
```

這次我們要在bodyReplacement變數中建立權杖的內容，然後使用addToken方法將其新增至請求。 addToken會取得索引鍵和值，然後建立JsonObject表示法並將其新增至內部Token陣列。 然後在postData方法期間將其序列化，並建立如下所示的主體：

`{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}`

結合之後，我們的主控台輸出如下所示：

```bash
Token is empty or expired. Trying new authentication
Trying to authenticate with&nbsp;...
Got Authentication Response: {"access_token":"19d51b9a-ff60-4222-bbd5-be8b206f1d40:st","token_type":"bearer","expires_in":3565,"scope":"<apiuser@mktosupport.com>"}
Executing RequestCampaign call
Endpoint: .../rest/v1/campaigns/1578/trigger.json?access_token=19d51b9a-ff60-4222-bbd5-be8b206f1d40:st
Request Body:
{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}
Result:
{"requestId":"1e8d#14eadc5143d","result":[{"id":1578}],"success":true}
```

### 正在結束

此方法可透過多種方式擴充，可變更個別版面區段內或外部電子郵件中的內容，讓自訂值可傳遞至任務或有趣的時刻。 在程式中可以使用代號的任何地方，都可以使用此方法自訂。 [排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)呼叫也有類似的功能，可讓您處理整個批次行銷活動中的權杖。 這些無法根據潛在客戶進行自訂，但對於在廣泛的潛在客戶群中自訂內容非常有用。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-07-24_

## 使用API更新Marketo中的銷售機會資料

一年前，我們在Marketo中發佈了使用API更新客戶和潛在客戶資訊。 該貼文提供程式碼範例，可循環執行，以輪詢外部系統是否有更新。 其構想是擷取外部資料，並將其推送到Marketo以更新潛在客戶資訊。 顯示的程式碼範例使用我們的SOAP API。 REST端點以完成相同的目標。 **程式輸入**&#x200B;您可能需要將外部系統的資料轉換為Marketo REST API可使用的格式。 由於我們使用「建立/更新銷售機會API」，因此資料必須格式化為JSON，才會在要求內文中傳送。 以下範例將對此提供最詳盡的解釋。 在以下Java程式碼範例中，我們已將模擬銷售機會資料放置在字串陣列中。 每個潛在客戶的資料後面每個字串都有：名字、姓氏、電子郵件地址、職稱。

```java
private static String externalLeadData[] = {
        "Henry, Adams, <henry@superstar.com>, Director of Demand Generation",
        "Suzie, Smith, <ssmith@gmail.com>, VP Marketing"
};
```

範常式式碼會將陣列轉換為下方的JSON區塊。

```json
{
"input":
    [
        {"firstName":"Henry", "lastName":"Adams", "email":"<henry@superstar.com>", "title":"Director of Demand Generation"},
        {"firstName":"Suzie", "lastName":"Smith", "email":"<ssmith@gmail.com>", "title":"VP Marketing"}
    ]
}
```

每個「輸入」陣列專案都對應至Marketo中的個別銷售機會。 陣列專案是包含一或多個Marketo潛在客戶欄位名稱及其個別值的JSON物件。 您決定指定的欄位名稱（在此例中為firstName、lastName、email和title）必須符合為Marketo訂閱定義的REST API名稱。 您可以匯出欄位名稱，在Marketo管理面板的欄位管理區段中找到REST API名稱。 欄位名稱將會匯出到Excel檔案中，如下所示。 或者，您可以呼叫[Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2) API，以程式設計方式尋找欄位名稱。 例如，以下是Describe回應片段，其中包含「名字」欄位的REST API名稱。

```json
{
    "id": 29,
    "displayName": "First Name",
    "dataType": "string",
    "length": 255,
    "soap": {
        "name": "FirstName",
        "readOnly": false
    },
    "rest": {
        "name": "firstName",
        "readOnly": false
    }
},
```

**程式邏輯**&#x200B;一旦裝載為適當格式，我們就可以呼叫建立/更新銷售機會。 請注意，在此範例中，我們並未指定任何選用引數。 預設行為是建立或更新潛在客戶記錄（更新插入）、使用電子郵件進行重複資料刪除，以及使用同步處理。 如果建立/更新銷售機會的呼叫成功，則回應內文會包含「result」陣列，其中包含Marketo銷售機會ID和建立/更新操作的狀態。 根據您在請求中傳遞的「action」引數，狀態可以是「已更新」、「已建立」或「已略過」。 繼續我們的範例，假設第一個銷售機會不存在(Henry Adams)，而第二個銷售機會確實存在(Suzie Smith)。 類似下列的回應會指出已建立第一個銷售機會，且已更新第二個銷售機會。

```json
{
    "success":true,
    "requestId":"118f8#14f1a0b82fc",
    "result":[
        {
            "id":318798,
            "status":"created"
        },
        {
            "id":318797,
            "status":"updated"
        }
    ]
}
```

就目前而言，答案是肯定的。 快樂的程式碼！ **程式碼**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStreamWriter;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

public class CreateUpdateLeads {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            { INSERT YOUR CUSTOM SERVICE DATA HERE };

    //
    // Mock up data that could be read from an external data source.
    // An array of CSV strings the form:
    //   "firstName, lastName, email, title"
    private static String externalLeadData[] = {
        "Henry, Adams, henry@superstar.com, Director of Demand Generation",
        "Suzie, Smith, ssmith@gmail.com, VP Marketing"
    };

    public static void main(String[] args) {
        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URL for Create/Update Leads
        String createUpdateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", baseUrl, accessToken);

        // Convert String array into JsonArray to pass as part of request body
        JsonArray input = convertExternalLeadDataToJson(externalLeadData);

        // Build request body JSON
        JsonObject requestObj = new JsonObject();
        requestObj.add("input", input);

        // Call Create/Update Lead API
        JsonArray result = new JsonArray();
        JsonObject leadObj = JsonObject.readFrom(httpRequest("POST", createUpdateLeadsUrl, requestObj.toString()));
        if (leadObj.get("success").asBoolean()) {
            if (leadObj.get("result") != null) {
                result = leadObj.get("result").asArray();
            }
        }

        // Print out results object
        System.out.println(result);
        System.exit(0);
    }

    // Convert array of CSV formatted Strings into an array of JSON objects
    private static JsonArray convertExternalLeadDataToJson(String input[]) {
        JsonArray output = new JsonArray();

        // Loop through each CSV row in array
        for (int i = 0; i < input.length; i++) {
            // Split CSV row into separate fields
            List items = Arrays.asList(input[i].split(","));

            // Add fields to JSON object
            JsonObject lead = new JsonObject();
            lead.add("firstName", items.get(0));
            lead.add("lastName", items.get(1));
            lead.add("email", items.get(2));
            lead.add("title", items.get(3));
            output.add(lead);
        }
        return output;
    }

    // Perform HTTP request
    private static String httpRequest(String method, String endpoint, String body) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setDoOutput(true);
            urlConn.setRequestMethod(method);
            switch (method) {
                case "GET":
                    break;
                case "POST":
                    urlConn.setRequestProperty("Content-type", "application/json");
                    urlConn.setRequestProperty("accept", "text/json");
                    OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                    wr.write(body);
                    wr.flush();
                    break;
                default:
                    System.out.println("Error: Invalid method.");
                    return data;
            }
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-08-14_

## 將SalesPerson資料新增至Marketo


有了新的SalesPerson API，您就可以在沒有原生CRM整合的情況下，自由地將Marketo銷售機會與SalesPerson記錄相關聯。 這可讓您使用Marketo中的{{lead.Lead Owner Email Address}}及相關欄位和權杖。

### 建立銷售人員記錄

若要將銷售機會與SalesPerson記錄建立關聯，我們首先需要將SalesPerson記錄輸入到Marketo中。 以下是PHP中的範例類別：

```php
<?php

class SalesPerson{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $dedupeBy;//dedupeFields or idField
 private $input;//array of salesperson objects for input

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->dedupeBy)){
   $body->dedupeBy = $this->dedupeBy;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/salespersons.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction($action){
  return $this->action;
 }
 public function setDedupeBy($dedupeBy){
  $this->dedupeBy = $dedupeBy;
 }
 public function getDedupeBy($dedupeBy){
  return $this->dedupeBy;
 }
 public function addSalesPersons($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else{
   $this->input = $input;
  }
 }
 public function getInput(){
  return $this->input;
 }

}
```

在上述類別中，$input變數是stdClass物件的陣列，可能具有成員：

* externalSalesPersonId（僅在建立時有效）
* id（僅作為索引鍵updateOnly模式）
* 電子郵件
* 傳真
* 名字
* 姓氏
* 行動電話
* 電話
* 標題

透過Describe SalesPerson呼叫可擷取有關型別和欄位長度的更多詳細資訊。

### 同步銷售機會

以下是同步我們所需潛在客戶的快速範例類別：

```php
<?php

class Leads{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $lookupField;//dedupeFields or idField
 private $input;//array of salesperson objects for input
 private $asyncProcessing;//specify whether input should be processed asynchronously
 private $partitionName;//partition name for update if requires

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->lookupField)){
   $body->lookupField = $this->lookupField;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/leads.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction(){
  return $this->action;
 }
 public function setDedupeBy($lookupField){
  $this->dedupeBy = $lookupField;
 }
 public function getDedupeBy(){
  return $this->dedupeBy;
 }
 public function addLeads($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else if (is_array($input)){
   $this->input = $input;
  }else{
   $this->input = [$input];
  }
 }
 public function getInput(){
  return $this->input;
 }
 public function setAsyncProcessing($asyncProcessing){
  $this->asyncProcessing = $asyncProcessing;
 }
 public function getAsyncProcessing() {
  return $this->asyncProcessing;
 }
 public function setPartitionName($partitionName){
  $this->partitionName = $partitionName;
 }
 public function getPartitionName() {
  return $this->partitionName;
 }

}
```

### 程式

以下是建立兩個業務代表記錄，並將它們與兩個潛在客戶記錄產生關聯的範例：

```php
<?php

require 'Auth.php';
require 'SalesPerson.php';
require 'Leads.php';

//Create an Auth object for authentication
$auth = new Auth("https://284-RPR-133.mktorest.com", "7f99bd49-0e0e-4715-a72a-0c9319f75552", "O5lZHhrQDfDKRhulY89IU42PfdhRSe6m");

//Compose new SalesPerson Records
$sales1 = new stdClass();
$sales1->externalSalesPersonId = "SalesPerson 1";
$sales1->email = "sales1@example.com";
$sales1->firstName = "Jane";
$sales1->lastName = "Doe";

$sales2 = new stdClass();
$sales2->externalSalesPersonId = "SalesPerson 2";
$sales2->email = "sales2@example.com";
$sales2->firstName = "John";
$sales2->lastName = "Doe";

//Compose lead records
$lead1 = new stdClass();
$lead1->email = "marketoDev@example.com";
//Add the external id of the desired salesperson
$lead1->externalSalesPersonId = $sales1->externalSalesPersonId;

$lead2 = new stdClass();
$lead2->email = "devBlog@example.com";
$lead2->externalSalesPersonId = $sales2->externalSalesPersonId;

//Create a new instance of SalesPerson to submit records
$salesPerson = new SalesPerson($auth, [$sales1, $sales2]);
$spResponse = $salesPerson->postData();
print_r($spResponse . "rn");
$json = json_decode($spResponse);

//Check for success on synching SalesPersons
if ($json->success){
 $leads = new Leads($auth, [$lead1, $lead2]);
 $leadsResponse = $leads->postData();
 print_r($leadsResponse . "rn");
}else{
 print_r("SalesPerson request failed with errors:rn");
 foreach ($json->errors as $error){
  print_r("code: " . $error->code . ", message: " . $error->message . "rn");
 }
}
```

對於範例類別，我們只是建立stdClass物件來代表需要同步的SalesPerson和Lead記錄，並將每個想要的欄位新增為成員。 執行此程式碼後，潛在客戶<marketoDev@example.com>和<devBlog@example.com>都會填入潛在客戶擁有者電子郵件、潛在客戶擁有者名字，以及潛在客戶擁有者姓氏欄位，讓他們能夠針對這些欄位使用相關權杖，並透過相關智慧清單篩選器進行篩選。

### 驗證類別

```php
<?php

class Auth{
 private $host;//host of the target Marketo instance
 private $clientId;//client Id
 private $clientSecret;//client secret
 private $token;//access_token
 private $expiry;

 function _construct($host, $clientId, $clientSecret){
  $this->host = $host;
  $this->clientId = $clientId;
  $this->clientSecret = $clientSecret;
 }
 public function getToken(){
  if (!isset($this->token) || $this->expiry > time()){
   $data = $this->getData();
   $this->expiry = time() + $data->expires_in;
   $this->token = $data->access_token;
   return $this->token;
  }else{
   return $this->token;
  }
 }
 private function getData(){
  $url = $this->host . "/identity/oauth/token?grant_type=client_credentials&client_id=" . $this->clientId . "&client_secret="
     . $this->clientSecret;
  $ch = curl_init($url);
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  $response = curl_exec($ch);
  $json = json_decode($response);
  curl_close($ch);
  return $json;
 }
 public function getHost(){
  return $this->host;
 }
}
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-08-21_

## API使用者和自訂服務的最佳作法

Marketo的REST API使用自訂服務進行驗證，且其中每個服務都由僅限API的Marketo使用者擁有。 每個自訂服務的功能取決於指派給該使用者的每個角色的許可權。 將個別使用者和自訂服務配置給整合可讓您獲得多項優點：

* 您可以透過指定給使用者的角色，微調指定給每個個別服務的[許可權](/help/rest-api/custom-services.md)。
* 您可以刪除對應的自訂服務，而不停用其他服務，藉此停用個別Web服務以停止呼叫您的執行個體。
* API呼叫使用量報告將依使用者細分，讓您識別高使用量和異常使用量
* 比較容易判斷各個Web服務有權存取哪些資料
* 啟用Workspace的執行個體可以限制存取特定業務單位，方法是僅將角色授予可存取的工作區

### API 使用情況

每個API使用者會在API使用報告中個別報告，因此依使用者分割網站服務可讓您輕鬆說明每個整合的使用情況。 如果對您執行個體的API呼叫數量超過限制，並導致後續呼叫失敗，使用此作法可讓您計算每個服務的數量，並讓您評估如何解決問題。 前往「管理員 — >網站服務」，按一下過去7天的通話次數，即可檢視您的使用情形。

### 停用服務

如果整合產生不良效果，如果您尚未將每個整合指派給個別自訂服務，就可能會很枯燥且難以停用。 逐一區分這些區段，就像在您的「管理員 — >啟動點」中刪除違規服務一樣容易。

### Workspace管理

對於Marketo Enterprise訂閱，通常服務只需要存取單一工作區，這可透過對API使用者的角色指派[強制執行](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/allow-user-access-to-a-workspace)。 每個使用者角色皆可全域指派，或按工作區指派，因此工作區中的存取權可加以限制，儘可能提供最低的許可權集。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-08-28_

## 如何使用REST API指定銷售機會分割

**銷售機會分割** Marketo銷售機會分割提供隔離銷售機會的便利方式。 分割區可讓組織內的不同行銷群組共用單一Marketo執行個體。 如需詳細資訊，請參閱[瞭解Workspaces和Lead Partitions](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/understanding-workspaces-and-person-partitions)。 假設您正使用銷售機會分割，並使用Marketo REST API以程式設計方式建立銷售機會。 您如何確定您建立的潛在客戶最後會位於正確的資料分割中？ 這篇文章會向您展示如何做到！ 在此範例中，我們將使用工作區與分割區，根據地理位置來隔離銷售機會。

首先，我們將定義名為「國家/地區」的工作區。 接著，我們在該工作區中建立兩個分割區，稱為「墨西哥」和「加拿大」。  **在資料分割中建立銷售機會**&#x200B;假設我們想要在「墨西哥」資料分割中建立兩個銷售機會。 若要建立銷售機會，我們會呼叫。 若要指定分割區，我們必須在要求內文中包含「partitionName」屬性。 我們如何知道要用於partitionName值的內容？ 我們可以呼叫[Get Lead Partitions](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeProgramMemberUsingGET) API，擷取執行個體的有效資料分割名稱值清單，如下所示：

`GET /rest/v1/leads/partitions.json`

```json
{
    "requestId": "20ae#14f9a1a5a30",
    "result": [
        {
            "id": 1,
            "name": "Default",
            "description": "Initial system lead partition"
        },
        {
            "id": 2,
            "name": "Mexico",
            "description": "Leads that live in Mexico"
        },
        {
            "id": 3,
            "name": "Canada",
            "description": "Leads that live in Canada"
        }
    ],
    "success": true
}
```

在此案例中，正確的值為「Mexico」，因此我們會將其傳遞至「建立/更新銷售機會」，如下所示：

`POST /rest/v1/leads.json`

```json
{
    "input": [
        {"email":"enrique.pena-nieto@gob.mx"},
        {"email":"angelica.rivera@gob.mx"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

以下是我們在Marketo中新建立的銷售機會。  **更新資料分割中的銷售機會**&#x200B;若要更新資料分割中現有的銷售機會，我們只要呼叫[建立/更新銷售機會]，並指定partitionName，就像之前一樣。 針對此更新，我們將為上述建立的銷售機會指派名字、姓氏和標題。

`POST /rest/v1/leads.json`

```json
{
"input": [
        {"email":"enrique.pena-nieto@gob.mx", "firstName":"Enrique", "lastName":"Pena Neito", "title": "El Presidente"},
        {"email":"angelica.rivera@gob.mx", "firstName":"Angelica", "lastName": "Rivera", "title": "Primera Dama"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

以下是我們在Marketo中最新開發的銷售機會。
**識別潛在客戶的磁碟分割**&#x200B;我們如何知道潛在客戶所在的磁碟分割？ 為此，我們使用[Get Lead by Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) API，並在「欄位」查詢引數中指定「leadPartitionId」。 在此案例中，我們將擷取上述所建立之銷售機會ID318816案的資訊。

`GET /rest/v1/lead/318816.json?fields=leadPartitionId,email,firstName,lastName,title`

```json
{
    "requestId": "5c57#14f9a495b1f",
    "result": [
        {
            "id": 318816,
            "lastName": "Pena Neito",
            "title": "El Presidente",
            "email": "enrique.pena-nieto@gob.mx",
            "firstName": "Enrique",
            "leadPartitionId": 2
        }
    ],
    "success": true
}
```

請注意，我們傳回leadPartitionId而非partitionName。 若要取得partitionName，我們需要從上方的Get Lead Partitions API重新造訪輸出。

```json
{
    "id": 2,
    "name": "Mexico",
    "description": "Leads that live in Mexico"
},
```

在此案例中，2的leadPartitionId值對應至「Mexico」的partitionName。 僅此而已。 快樂的資料分割！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-09-04_

## 比較Marketo電子郵件指令碼中的分數欄位

**注意：**&#x200B;這是Cathal Moran的客座貼文。 Cathal是Marketo在愛爾蘭都柏林的EMEA辦事處工作的解決方案顧問。

### 比較分數欄位

許多Marketo客戶（尤其是專注於交叉銷售的客戶）擁有多個分數欄位，這通常用於測量潛在客戶對特定產品/區域的興趣。 假設我銷售蘋果和香蕉，如果潛在客戶在蘋果和香蕉上的分數分別為50和10，那麼就能清楚知道偏好在哪裡，如果我的內容反映了這種偏好，那不是件好事。 電子郵件指令碼可用於比較分數，並根據接收電子郵件的特定潛在客戶的最高分數（或最低分數）來個人化電子郵件中的內容。

### 由於我想比較2個數字，因此必須將欄位值轉換為整數

```
#set ($score1 = $math.toInteger(${lead.Apple_Score}))
#set ($score2 = $math.toInteger(${lead.Banana_Score}))
##check if the lead score is greater than feature score
#if($score1 >= $score2)
                ##if Apple score is greater
                #set($Interest = "Special offer on Apples")
##check is the feature score is higher
#elseif($score2 >= $score1)
                ##if Feature score is greater
                #set($Interest = "Special offer on Bananas")
#else
                ##otherwise
                #set($Interest = "Special offer on Fruit")
#end
##display the Interest as content
${Interest}
```

在上述範例中，我只是將文字個人化，但我可以同樣輕鬆地根據較高的分數顯示不同的影像。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-09-14_

## 在背景提交Marketo表單

當您的組織擁有許多不同的平台來託管Web內容和客戶資料時，通常需要從表單中並行提交資料，以便在不同的平台上收集產生的資料。 有多種策略可以達成此目的，但最佳策略通常最簡單：使用Forms 2 API提交隱藏的Marketo表單。 這適用於任何新的Marketo表單，但理想情況下，您應該為此建立空白表單，該表單沒有欄位。 這將確保表單不會載入任何超出必要的資料，因為我們不需要呈現任何內容。 現在，只要從您的表單抓取[內嵌程式碼](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)，並將其新增到所需頁面的內文，即可進行小幅修改。 您的內嵌程式碼包含如下表單元素：

`<form id="mktoForm_1068"></form>`

您會想要將&#39;style=&quot;display:none&quot;新增至元素，使其不可見，如下所示：

`<form id="mktoForm_1068" style="display:none"></form>`

一旦表單嵌入並隱藏，提交表單的程式碼就會非常簡單：

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"test@example.com",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

如果潛在客戶已填寫並提交可見表單，Forms以此方式提交的行為將完全相同。 由於每個實施都有不同的動作可提示，因此觸發提交作業將因實施而異，但您基本上可讓提交作業在任何動作中發生。 重要部分是正確設定欄位和值。 請務必使用您可在[匯出欄位名稱](/help/rest-api/list-of-standard-fields.md)找到的欄位SOAP API名稱，以確保正確提交值。

從Munchkin關聯銷售機會移轉

背景表單提交是Munchkin關聯銷售機會的建議取代方法之一。 以下範例HTML頁面透過重複使用提交給「關聯銷售機會」的相同值，示範高層次的移轉。

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
            }
          }
          var s = document.createElement('script');
          s.type = 'text/javascript';
          s.async = true;
          s.src = '//munchkin.marketo.net/munchkin-beta.js';
          s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
              initMunchkin();
            }
          };
          s.onload = initMunchkin;
          document.getElementsByTagName('head')[0].appendChild(s);
        })();
        </script>
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //submit the form
            form.submit();


        })
    </script>
</body>
</html>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-09-25_

## Marketo REST API例外狀況與錯誤處理：第1部分

Marketo REST API可能會傳回例外狀況或錯誤，為方便起見，我們將從此處開始呼叫錯誤。 錯誤可能發生在三個不同的層級：

* HTTP層級，這些錯誤以4xx程式碼表示
* 回應層級，這些錯誤包含在JSON回應的「錯誤」陣列中
* 記錄層級，這些錯誤包含在JSON回應的「結果」陣列中，並以「狀態」欄位和「原因」陣列依個別記錄指示

### HTTP錯誤

在正常作業情況下，Marketo應該只傳回兩個HTTP狀態錯誤：413請求實體太大，以及414請求URI太長。 擷取錯誤、修改請求和重試都可以復原這些設定，但使用智慧編碼實務作法，您絕不應該在萬不得已的情況下遇到這些設定。 如果要求裝載超過1MB，Marketo會傳回413，或是[匯入銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads)會傳回10MB。 在大多數情況下，可能不會達到這些限制，但新增檢查至要求的大小，以及移動任何導致限制超過新要求的記錄，應可防止任何端點傳回導致此錯誤的任何情況。 當GET請求的URI超過8KiB時，將會傳回414。 若要避免出現這種情況，請檢查查詢字串的長度，看看是否超過此限制。 如果它確實將您的請求變更為POST方法，則使用其他引數「_method=GET」將您的查詢字串輸入為請求內文。 這放棄了URI的限制。 在大多數情況下很少達到此限制，但在擷取具有長的個別篩選值（例如GUID）的大量記錄時，這比較常見。

### 回應層級錯誤

當回應的「success」引數設為false時，就會出現回應層級錯誤。 「錯誤」中的每個專案都有兩個成員，「編碼」一個數字（6xx或7xx）和一個「訊息」，提供錯誤的純文字原因。 6xx程式碼一律會指出要求完全失敗且未執行。 這方面的範例為601，「存取權杖無效」，這可以透過重新驗證並使用請求傳遞新存取權杖來復原。 7xx錯誤表示請求失敗，可能是因為未傳回任何資料，或是請求引數化不正確，例如包含無效的日期，或遺漏必要的引數。

### 記錄層級錯誤

記錄層級錯誤表示無法完成個別記錄的作業，但要求本身有效。 這些會在回應的「結果」陣列中的個別記錄中發生。 這些記錄的「狀態」欄位將「略過」，並會出現「原因」陣列。 每個原因都包含「代碼」成員和「訊息」成員。 代碼將一律為1xxx，而訊息將指出略過記錄的原因。 例如，「建立/更新銷售機會」請求的「action」設為「createOnly」，但已提交記錄中的其中一個索引鍵已存在銷售機會。 此案例會傳回代碼1005，而訊息為「潛在客戶已存在」。 在接下來的幾篇文章中，我們將瞭解可復原的錯誤，以及程式碼中如何處理這些錯誤的一些範例。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-10-09_

## 來賓貼文 — 將SQL聯結器直接連線至Marketo資料庫

**這是來自Sumit Sarkar of Progress， Inc.**&#x200B;的訪客貼文

**連線至Marketo資料庫的SQL聯結器** Marketo已完整記錄存取資料的API。 不過，有時組織需要直接SQL存取。 在Marketo商店，行銷與IT之間的界限變得模糊，導致對標準型SQL連線的需求增加。 可透過[Progress DataDirect Cloud](https://www.progress.com/connectors/cloud-data-integration)使用直接到Marketo的SQL聯結器。 DataDirect Cloud是一項資料連線服務，可透過開放的產業標準公開Marketo資料，以便透過ODBC （「開放式資料庫連線」）或JDBC （「Java資料庫連線」）進行SQL存取，以及透過OData （「開放式資料通訊協定」）進行REST。 以下是使用DataDirect Cloud立即連線Marketo資料的一些常見使用案例：

* 資料探索和視覺效果(Qlik、Tableau、SAP Lumira)
* 企業報告(SAP Business Objects、Microstrategy、Cognos)
* 資料整合(SQL Server Integration Services - SSIS、Oracle Data Integrator - ODI、Informatica)
* 資料同盟(SQL Server Linked Server、SAP Hana SDA、Oracle資料庫閘道)
* Ad Hoc Query (Microsoft Office、DB Visualizer、Aqua Data Studio)
* 資料準備(Alteryx、Trifecta、Paxata)

**SQL如何存取Marketo？**

* DataDirect Cloud會針對Marketo整合API公開的資料建立邏輯結構描述。
* DataDirect Cloud會針對從Marketo API擷取的資料，使用彈性即時查詢引擎來處理來自輕量型ODBC或JDBC使用者端的SQL請求。
* DataDirect Cloud連線可直接與即時連線，且資料完全不重複。
* DataDirect Cloud Service會將Marketo API抽象化，讓使用者不必擔心API版本變更，或使用SOAP與REST。

自2006年起，DataDirect就開始建立這種連線至SaaS資料來源的樣式，從第一個建置在其Web服務API之上的Salesforce ODBC驅動程式開始。 **開始連線到Marketo：**

1. [註冊DataDirect雲端登入](https://pacific.progress.com/console/register?productName=d2c&ignoreCookie=true)
1. 按一下「資料來源」，然後按「+新增資料Source」按鈕
1. 選取「Marketo」並輸入連線資訊。 您可以向您的Marketo管理員確認或登入以尋找SOAP整合的[連線資訊](/help/soap-api/soap-api.md)。
1. 按一下「測試連線」按鈕。 請注意，有一個OData索引標籤可以從Marketo產生OData，我們將在未來的部落格中討論。
1. 如果您想要檢查Marketo架構是否公開，或想要從UI內發出基本SQL查詢，請按一下「SQL測試」。
1. 按一下左側的[下載]，然後選取要安裝之應用程式和平台的DataDirect Cloud ODBC或JDBC驅動程式。
1. 安裝DataDirect Cloud ODBC或JDBC驅動程式後，您就可以將任何標準型應用程式連線至Marketo。

以下是使用DataDirect Cloud ODBC使用者端[連線](https://www.youtube.com/watch?v=H6PHra56Iig)的影片範例。 以下是其他適用於Marketo的DataDirect Cloud教學課程：

* [SAP資料分析](http://scn.sap.com/community/lumira/blog/2015/08/05/connect-sap-lumira-to-eloqua-marketo-google-analytics)
* [微策略企業報告](https://community.microstrategy.com/t5/Tech-Corner/What-MSTR-developers-should-know-about-Cloud-Data-Sources/ba-p/253083)
* [Oracle資料整合](https://www.ateam-oracle.com/post/a-universal-cloud-applications-adapter-for-odi/)

**跨雲端來源(例如Marketo**)建置SQL連線的研發挑戰

並非所有SaaS API都會公開標準查詢語言。 在這些情況下，工程團隊會分別審視每個物件。 每個物件可能會以不同的API公開，且具有唯一的叫用、搜尋篩選等規則。 要提供在整個資料模型中進行查詢的標準體驗，需要花費大量時間。

處理完整加入功能。 如果SaaS API不支援具有JOIN功能的查詢語言，工程團隊必須執行該操作。 這需要SQL的轉譯，才能有效地呼叫Marketo API，以便在執行聯結前傳回最少的資料量。 加入兩個非常大的物件時，資料存取層可能會佔用應用程式伺服器或桌上型電腦上相當多的資源。 因此，將資料存取層部署至DataDirect Cloud等彈性雲端服務較為合理，原因有二：

1. 提升使用者端應用程式伺服器或桌上型電腦的效能，並減少記憶體/CPU資源的使用量
1. 善用DataDirect Cloud與Marketo之間優異的頻寬，交換預先加入的資料集。

如何處理資料模型？ 是靜態還是動態？ 如何偵測變更並與使用者端溝通？ 每個SaaS資料來源不同，在Marketo中，透過檢視查詢特定物件較佳，而透過表格查詢其他物件較佳。 跨所有SaaS來源處理此資料模型與物件的矩陣當然是一項挑戰。

**開發人員的Marketo和DataDirect雲端參考：**

* Marketo連線屬性（[連結至檔案](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）
* 支援的SQL和具有Marketo的擴充功能（[連結至檔案](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html,-hubspot,-and-marketo.html)）
* 公開的Marketo表格和檢視（[檔案連結](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）
* 從Marketo傳回的常見錯誤訊息（[連結至檔案](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）

**履歷：** Sumit Sarkar是Progress的首席資料宣導者，在資料連線領域有超過10年的工作經驗。 Sumit是開放資料標準與雲端資料連線方面的全球領先顧問，其興趣包括資料存取層的效能調整（他已針對該層開發一項正在申請專利的技術進行分析）、SaaS平台的商業智慧和資料倉儲，以及PaaS環境的資料連線，並專注於ODBC、JDBC、ADO.NET和ODATA等標準。 彼為IBM Cognos Business Intelligence的IBM認證顧問及TDWI會員。 他曾在各種會議上展示資料連線研討會，包括Dreamforce、Oracle OpenWorld、Strata Hadoop、MongoDB World和SAP Analytics and Business Objects Conference等等。 Twitter： @SAsInSumit LinkedIn： [www.linkedin.com/in/meetsumit](http://www.linkedin.com/in/meetsumit)

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-12-07_

## 建立API使用情況和錯誤計數的儀表板

身為Marketo API消費者，此資訊相當實用，您應密切留意。 如果您可以取得歷史使用資料，以協助偵測一段時間內的趨勢，該怎麼做？ 如果您可以取得API錯誤碼的摘要，以協助測量整合的健全狀態，該怎麼做？ 身為Marketo技術合作夥伴，如果您可以在一個儀表板中取得所有客戶帳戶的使用情況和錯誤資料，該怎麼辦？ 本貼文將提供回答上述問題的方法。 繫好安全帶，開始吧！
**已排程統計資料擷取的工作**&#x200B;讓我們使用[取得每日使用量](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLast7DaysErrorsUsingGET)和[取得每日錯誤](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyErrorsUsingGET)端點來建立應用程式，以擷取使用量和錯誤資料。 此應用程式設計為排程每天執行一次。 每次應用程式執行時，都會將一天使用情況資料附加至一個檔案，並將一天錯誤資料附加至另一個檔案。 每個月初會建立一對新檔案。 這些檔案將當作我們隨時可存取的歷史記錄。 以下是應用程式邏輯……

* 從外部來源讀取Marketo帳戶資訊(Munchkin id和使用者端憑證)。 注意：此來源必須安全，以防止其他人存取帳戶資料。
* 逐一檢視每個帳戶，然後……
   * 呼叫「取得每日使用量」以擷取一天的使用量資料
   * 將每日使用量資料附加至每月「使用量」檔案
   * 呼叫Get Daily Errors以擷取一天的錯誤資料
   * 將每日錯誤資料附加至每月「錯誤」檔案

輸出檔案格式輸出檔案的格式為JSON，符合從個別API呼叫（使用情況和錯誤）傳回的「結果」陣列。 「result」陣列的每個元素都是包含一天資料的JSON物件。 輸出檔案命名輸出檔案的命名如下：

`<type>_<yyyy>_<mm>_<account>.json`

其中，

`<type>` — 資料型別（「使用情況」或「錯誤」） `<yyyy>` — 年份（4位數） `<mm>` — 月份（2位數） `<account>` — 帳戶ID (Munchkin ID)

輸出檔案範例usage_2015_10_111-AAA-222.json

```json
[
    { "date": "2015-10-15", "total": 0, "users" : [] },
    { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
    { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] },
]
```

`errors_2015_10_111-AAA-222.json:`

```json
[
    { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
    { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
    { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
]
```

此應用程式的程式碼會顯示在本文結尾處(Stats.java)。 **Stats Web服務**&#x200B;所以現在我們需要一種方式將此資料放入我們的瀏覽器。 建議建立網站服務以傳遞資料。 Web服務將會使用應用程式的輸出檔案，然後將資料以可方便呈現的表單傳回瀏覽器。 為了簡單起見，並繞過相同來源原則限制，我們將利用[JSONP](https://en.wikipedia.org/wiki/JSONP)模式。 以下是外部Web服務的建議REST端點規格： **URI**： /stats **方法**： GET

**參數**

**說明**

**範例**

月

擷取本月資料。 要包含的月份清單（以逗號分隔） （2位數表示）。 預設為所有月份。

10,11

年

擷取本年度的資料。 要包含的年份清單（4位數表示），以逗號分隔。 預設為全部年份。

2015

account

擷取此帳戶的資料(Munchkin id)。

111-AAA-222

callback

用以包裝JSON內容的函式名稱。

processStats

範例要求

`GET //localhost:8080/stats?month=10&year=2015&account=111-AAA-222&callback=processStats`

Web服務會讀取「使用情況」和「錯誤」檔案，並將它們結合在一起，然後以此格式傳回：


```html
**<Name of Callback here>**

<Contents of Usage file here>,

<Contents of Error file here>
```

這是具有2個引數的JSONP回呼。 第一個引數是用法「result」陣列。 第二個引數是錯誤「result」陣列。 範例回應

```json
processStats(
    [
        { "date": "2015-10-15", "total": 0, "users" : [] },
        { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
        { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] }
    ],
    [
        { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
        { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
        { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
    ]
);
```

如您所見，Web服務已直接包裝應用程式中兩個輸出檔案的內容。 我們已使用[Mocky](https://designer.mocky.io)建立模擬Web服務回應。 此模型的Web服務範例為[此處。](http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats)建立此Web服務是讀取器的練習： **儀表板網頁**&#x200B;所以現在我們只需要一個網頁來呼叫我們的Web服務並格式化資料。 若要使用JSONP模式，我們只需要新增叫用Web服務的`<script>`標籤：

`<script src="http: //<hostname>/stats?month=10&year=2015&account=284-RPR-133&callback=processStats"></script>`

這會將Web服務回應本文直接插入HTML頁面。 然後新增JSONP回呼函式：

```javascript
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
;
```


此函式會在Web服務呼叫後自動呼叫。 在此範例中，我們將每個陣列上名為[prettyPrint.js](https://github.com/padolsey-archive/prettyprint.js/tree/master)的簡單JavaScript「變數傾印」稱為。 prettyPrint函式只會使用陣列的內容產生HTML表格。 以下是HTML表格的熒幕擷圖。 薇拉，那是我們的儀表板！ 當然，這並不十分優雅，但它應該可以讓您瞭解什麼是可能的。 沒有什麼能阻止您以任何想要的方式轉換資料，以打造自己的搶眼視覺效果。 HTML頁面如下(Index.html)您可以使用以下步驟在瀏覽器中即時檢視上述表格：

1. 儲存Index.html的本機復本
1. 儲存prettyPrint.js的本機復本
1. 在瀏覽器中開啟Index.html

就是這樣。 希望這篇文章能為您提供一些有關監控Marketo API統計資料的概念。 快樂的程式碼！ **Stats.java**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import java.io.\*;
import java.lang.reflect.Array;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

import java.nio.file.Files;
import java.nio.file.Paths;

import javax.net.ssl.HttpsURLConnection;

public class Stats {
    //
    // Define Marketo instance meta data here. Each row contains three elements: Account Id, Client Id, Client Secret.
    //
    // Note: that this information would typically be stored read from an external source (i.e. database)
    //
    // For example:
    //
    // private static String final CUSTOM_SERVICE_DATA[][] = {
    // {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    // };
    //
    private static final String CUSTOM_SERVICE_DATA[][] = {
    };

    // Output directory for stats files
    private static final String OUTPUT_DIR = "C:stats";

public static void main(String[] args) {

    // Loop through each Marketo instance
    for (int i = 0; i < CUSTOM_SERVICE_DATA.length; i++) {

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA[i][0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
            baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[i][1], CUSTOM_SERVICE_DATA[i][2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose Get Last 7 Days Usage URL
        String usageUrl = String.format("%s/rest/v1/stats/usage/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Compose Get Last 7 Days Errors URL
        String errorsUrl = String.format("%s/rest/v1/stats/errors/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Process usage data
        JsonObject usageObj = JsonObject.readFrom(httpRequest("GET", usageUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (usageObj.get("result") != null) {
                updateFile(usageObj, "usage", CUSTOM_SERVICE_DATA[i][0]);
            }
        }

        // Process errors data
        JsonObject errorsObj = JsonObject.readFrom(httpRequest("GET", errorsUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (errorsObj.get("result") != null) {
                updateFile(errorsObj, "errors", CUSTOM_SERVICE_DATA[i][0]);
            }
        }
    }
    System.exit(0);
}

// Write yesterday's data to output file
private static void updateFile(JsonObject usageObj, String statsType, String account){

    JsonArray usageResultAry = usageObj.get("result").asArray();
    JsonObject yesterdayUsageResultObj = usageResultAry.get(1).asObject();

    String yesterdayDate = yesterdayUsageResultObj.get("date").asString();
    String[] yesterdayDateAry = yesterdayDate.split("[-]+");

    // "C:statsstats_yyyy_mm_account.json"
    String statsFile = String.format("%s%s_%s_%s_%s.json",
        OUTPUT_DIR, statsType, yesterdayDateAry[0], yesterdayDateAry[1], account);

    // Create file
    File file = new File(statsFile);
    try {
        if (file.createNewFile()) {
            // created new file, seed with empty array
            FileWriter fw = new FileWriter(file.getAbsoluteFile());
            BufferedWriter bw = new BufferedWriter(fw);
            bw.write("[n]");
            bw.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Read file
    String content = null;
    try {
        content = new String(Files.readAllBytes(Paths.get(statsFile)));
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Remove trailing "]", append new record, append trailing "]"
    content = content.substring(0, content.length() - 1);
    content += yesterdayUsageResultObj.toString();
    content += "n]";

    // Write file
    FileWriter fw = null;
    try {
        fw = new FileWriter(file.getAbsoluteFile());
        BufferedWriter bw = new BufferedWriter(fw);
        bw.write(content);
        bw.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}

// Perform HTTP request
private static String httpRequest(String method, String endpoint, String body) {
    String data = "";
    try {
        URL url = new URL(endpoint);
        HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
        urlConn.setDoOutput(true);
        urlConn.setRequestMethod(method);
        switch (method) {
            case "GET":
                break;
            case "POST":
                urlConn.setRequestProperty("Content-type", "application/json");
                urlConn.setRequestProperty("accept", "text/json");
                OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                wr.write(body);
                wr.flush();
                break;
            default:
                System.out.println("Error: Invalid method.");
                return data;
        }
        int responseCode = urlConn.getResponseCode();
        if (responseCode == 200) {
            InputStream inStream = urlConn.getInputStream();
            data = convertStreamToString(inStream);
        } else {
            System.out.println(responseCode);
            data = "Status:" + responseCode;
        }
    } catch (MalformedURLException e) {
        System.out.println("URL not valid.");
    } catch (IOException e) {
        System.out.println("IOException: " + e.getMessage());
        e.printStackTrace();
    }
    return data;
}

private static String convertStreamToString(InputStream inputStream) {
    try {
        return new Scanner(inputStream).useDelimiter("A").next();
    } catch (NoSuchElementException e) {
        return "";
    }
}
}
```

**Index.html**

```html
<html>
<head>
<title>Marketo API Stats</title>
<!-- Browser JavaScript variable dumper                  -->
<!-- https://github.com/padolsey-archive/prettyprint.js  -->
<script src="prettyPrint.js"></script>
</head>
<body>

<h1>Marketo API Stats</h1>

<script>
// JSONP callback that uses prettyPrint to format API stats
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
};
</script>

<!-- Web service for you to implement as an exercise                                                        -->
<!-- <script src="http://localhost:8080/stats?month=10&account=111-AAA-222&callback=processStats"></script> -->

<!-- Mock web service that returns sample payload -->
<!-- http://www.mocky.io/                         -->
<script src="http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats"></script>"
</body>
</html>
```

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-10-22_

## Marketo REST API例外狀況與錯誤處理：第3部分

在大部分情況下，從Marketo REST API傳回的錯誤將不會自動復原。 不過，在少數情況下，您可以自動復原，或確保不會看到特定型別的錯誤。

### 請求大小錯誤

如我們在此系列的最後一篇文章中所見，若您的URI長度超過8KiB，Marketo將會產生HTTP狀態代碼414；若您的請求內文超過1MB，則會產生413；若為Import Lead，則會產生10MB。 雖然414將很少見，但如果您使用「依篩選條件取得銷售機會」型別，根據300個不同的GUID或類似條件來要求記錄，您可能會看到這些值。 假設您有以下請求： `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?filterType=customGUID&fields=email`， company...firstName， lastName>當您提交請求時，Marketo會傳回414狀態，因為URI超過8KiB。

若要處理此問題，我們需要變更此請求的模式，並提交POST而不是GET，將&#39;_method=GET&#39;附加至URI，並將請求內文中的查詢字串改為以x-www-form-urlencoded請求傳遞： URI： `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?_method=GET>`請求內文： filterType=customGUID&amp;fields=email，company...firstName， lastName我們不從HTTP回應中擷取此例外狀況，而是在執行階段檢查請求的總長度，並在URI超過8k時部署此替代模式。 或者，您可以在所有案例中使用POST方法，以批次擷取記錄。 對於413s，我們可以遵循類似的模式，在序列化步驟期間新增記錄時檢查請求內文的長度，如果超過此限制，則將請求分割成多個部分。

### 驗證錯誤

我們下一類可復原的錯誤與驗證有關。 當在expires_in期間後使用先前有效的存取權杖時，第一次使用將傳回602的錯誤代碼「存取權杖已過期」。 之後，使用相同Token會傳回601，「存取Token無效」。 字串若不是目標訂閱的有效存取Token，任何其他使用方式都會導致601錯誤。 在這兩種情況下，可以透過重新驗證並傳遞新的存取權杖以重試失敗的請求來從復原此錯誤。

### 逾時

在極少數的情況下，呼叫可能會在30秒逾時期間後傳回604「要求逾時」。 對於批次請求，例如建立/更新銷售機會，請求可以分割成較小的批次並重試，直到傳回成功（如果批次分割到少於100筆記錄且請求仍然逾時，您可能應該提出支援案例）。 最常見的其他案例是資產核准呼叫，其他使用者或服務可能會將鎖定保留在目前核准的記錄上，例如[電子郵件](https://developer.adobe.com/marketo-apis/api/asset/approve-email-by-id/)或[電子郵件範本](https://developer.adobe.com/marketo-apis/api/asset/approve-email-template-by-id/)的情況。 在這些情況下，應使用[指數回退](https://en.wikipedia.org/wiki/Exponential_backoff)進行重試，以允許解決任何現有的鎖定。 在接下來的幾週內再來檢查系列的最後部分，我們將更深入瞭解一些特定的、無法復原的錯誤。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-10-30_

## Marketo安全性增強功能

Marketo嚴肅看待安全性。 作為&#x200B;**[全產業計畫](https://security.googleblog.com/2014/09/gradually-sunsetting-sha-1.html)**&#x200B;的一部分，Marketo正在升級Web驗證和加密，以增強我們的安全性保護。 推出已排程在2011年12月12日進行。 **誰會受到影響？**&#x200B;只有少數使用者會受到影響，僅限那些從已使用10年以上且最近未更新的系統整合至Marketo的使用者。 請參閱&#x200B;**[此清單](https://www.digicert.com/faq/sha2/sha-2-compatibility)**，以取得支援哪些系統和版本的詳細資訊。 **下列使用者將不會受到影響：**

* 使用者透過新式瀏覽器存取Marketo.com （[請參閱清單](https://www.digicert.com/faq/sha2/sha-2-compatibility)）
* 使用Informatica、Dell Boomi和Scribe等整合合作夥伴的客戶。
* 使用Launchpoint合作夥伴的客戶。

其他所有Marketo網域都已使用SHA2憑證。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-11-18_

## 使用REST API輪詢活動

活動是Marketo平台中的核心物件。 活動是儲存的關於每次網頁瀏覽、電子郵件開啟、網路研討會出席或貿易展覽出席的行為資料。 一個常見的使用案例是將活動資料與來自組織其他部分的資料相結合。 此範常式式包含6個步驟：

1. 致電[取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)，以產生在指定日期/時間建立的所有活動記錄清單。 我們使用篩選器來限制傳回的活動記錄型別。
1. 從每個活動記錄中擷取感興趣的欄位。
1. 呼叫[依篩選型別](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)取得多個銷售機會，以產生與步驟1中的活動相對應的銷售機會記錄清單。 我們會使用步驟2中從活動記錄擷取的leadId欄位做為篩選器，以指定傳回哪些銷售機會。
1. 從每個潛在客戶記錄中擷取感興趣的欄位。
1. 使用步驟4的潛在客戶資料加入步驟2的活動資料。
1. 將步驟5中的資料轉換為外部系統可使用的格式。

下圖顯示，在此範例中，我們已選擇擷取電子郵件相關活動。

**程式輸入**&#x200B;依預設，程式會從目前日期起的一天回到過去，以尋找變更。 舉例來說，您可以每天在同一時間執行此程式。 若要更進一步回到過去，您可以將天數指定為命令列引數，有效增加時間視窗。 此程式包含數個您可以修改的變數：CUSTOM_SERVICE_DATA — 這包含您的Marketo自訂服務資料（帳戶ID、使用者端ID、使用者端密碼）。 READ_BATCH_SIZE — 這是一次要擷取的記錄數。 使用此選項調整回應到內文大小。 LEAD_FIELDS — 這包含我們要收集的銷售機會欄位清單。 ACTIVITY_TYPES — 這包含我們要收集的活動型別清單。

**程式邏輯**&#x200B;我們先建立時間範圍、撰寫REST端點URL，並取得驗證存取權杖。 接著，我們啟動「取得分頁權杖/取得潛在客戶活動」回圈，直到我們用盡活動供給為止。 此回圈的目的是擷取活動記錄，並從這些記錄中擷取感興趣的欄位。 我們指示「取得銷售機會活動」僅尋找下列活動型別：

* 電子郵件已傳遞
* 取消訂閱電子郵件
* 開啟電子郵件
* 按一下「電子郵件」。

我們從每個活動記錄中擷取下列感興趣的欄位：

* leadId
* activitytype
* activityDate
* primaryAttributeValue

您可以隨意選取活動型別和活動欄位的任意組合，以符合您的目的。 接著，我們啟動「依篩選器型別取得多個銷售機會」回圈，此回圈會一直執行，直到我們耗盡銷售機會的供給為止。 請注意，我們使用「filterType=id」引數搭配一系列「filterValues」引數，將擷取的潛在客戶記錄限製為僅與我們先前擷取的活動相關聯的潛在客戶。 我們從每個潛在客戶記錄中擷取下列感興趣的欄位：

* 名字
* 姓氏
* 電子郵件

同樣地，您可以選取任何您喜歡的潛在客戶欄位。 接下來，我們使用銷售機會ID將銷售機會欄位與活動欄位連結在一起，使它們聯結。 最後，我們會循環處理所有資料，將其轉換為JSON格式，然後列印至主控台。 **程式輸出**&#x200B;以下是範常式式的輸出範例。 這會顯示活動欄位和潛在客戶欄位合併為JSON「結果」物件。 這裡的構想是，您可以將此JSON作為請求裝載傳遞至外部Web服務。

```json
{
    "result": [
        {
            "leadId": 318581,
            "activityType": "Email Delivered",
            "activityDate": "2015-03-17T20:00:06Z",
            "primaryAttributeValue": "My Email Program",
            "firstName": "David",
            "lastName": "Everly",
            "email": "everlyd@marketo.com"
        },
        {
            "leadId":318581,
            "activityType":"Open Email",
            "activityDate":"2015-03-17T23:23:12Z",
            "primaryAttributeValue":"My Email Program - Auto Response",
            "firstName":"David",
            "lastName":"Everly",
            "email":"everlyd@marketo.com"
       },
        ... more result objects here...
    ]
}
```

**程式碼**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadActivities {
//
// Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    private static Map<String, String> CUSTOM_SERVICE_DATA;
    static {
        CUSTOM_SERVICE_DATA = new HashMap<String, String>();
//        CUSTOM_SERVICE_DATA.put("accountId", "111-AAA-222");
//        CUSTOM_SERVICE_DATA.put("clientId", "2f4a4435-f6fa-4bd9-3248-098754982345");
//        CUSTOM_SERVICE_DATA.put("clientSecret", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W");
    }

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Lookup lead records using lead id as primary key
    private static final String LEAD_FILTER_TYPE = "id";

    // Lead record lookup returns these fields
    private static final String LEAD_FIELDS = "firstName,lastName,email";

    // Lookup activity records for these activity types
    private static Map<Integer, String> ACTIVITY_TYPES;
    static {
        ACTIVITY_TYPES = new HashMap<Integer, String>();
        ACTIVITY_TYPES.put(7, "Email Delivered");
        ACTIVITY_TYPES.put(9, "Unsubscribe Email");
        ACTIVITY_TYPES.put(10, "Open Email");
        ACTIVITY_TYPES.put(11, "Click Email");
    }

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA.get("accountId"));

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA.get("clientId"), CUSTOM_SERVICE_DATA.get("clientSecret"));

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String activityTypesUrl = String.format("%s/rest/v1/activities/types.json?access_token=%s",
                baseUrl, accessToken);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        // Use activity ids to create filter parameter
        String activityTypeIds = "";
        for (Integer id : ACTIVITY_TYPES.keySet()) {
            activityTypeIds += "&activityTypeIds=" + id.toString();
        }

        // Compose URL for Get Lead Activities
        // Only retrieve activities that match our defined activity types
        String leadActivitiesUrl = String.format("%s/rest/v1/activities.json?access_token=%s%s&batchSize=%s",
                baseUrl, accessToken, activityTypeIds, READ_BATCH_SIZE);

        Map<Integer, List> activitiesMap = new HashMap<Integer, List>();
        Set leadsSet = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {
            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;
            do {
                moreResult = false;

                // Call Get Lead Activities API to retrieve activity data
                JsonObject leadActivitiesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadActivitiesUrl, nextPageToken)));
                if (leadActivitiesObj.get("success").asBoolean()) {
                    moreResult = leadActivitiesObj.get("moreResult").asBoolean();
                    nextPageToken = leadActivitiesObj.get("nextPageToken").asString();

                    if (leadActivitiesObj.get("result") != null) {
                        JsonArray activitiesResultAry = leadActivitiesObj.get("result").asArray();
                        for (JsonValue activitiesResultObj : activitiesResultAry) {

                            // Extract activity fields of interest (leadID, activityType, activityDate, primaryAttributeValue)
                            JsonObject leadObj = new JsonObject();
                            int leadId = activitiesResultObj.asObject().get("leadId").asInt();
                            leadObj.add("activityType", ACTIVITY_TYPES.get(activitiesResultObj.asObject().get("activityTypeId").asInt()));
                            leadObj.add("activityDate", activitiesResultObj.asObject().get("activityDate").asString());
                            leadObj.add("primaryAttributeValue", activitiesResultObj.asObject().get("primaryAttributeValue").asString());

                            // Store JSON containing activity fields in a map using lead id as key
                            List leadLst = activitiesMap.get(leadId);
                            if (leadLst == null) {
                                activitiesMap.put(leadId, new ArrayList());
                                leadLst = activitiesMap.get(leadId);
                            }
                            leadLst.add(leadObj);

                            // Store unique lead ids for use as lead filter value below
                            leadsSet.add(leadId);
                        }
                    }
                }
            } while (moreResult);
        }

        // Use unique lead id values to create filter parameter
        String filterValues = "";
        for (Object object : leadsSet) {
            if (filterValues.length() > 0) {
                filterValues += ",";
            }
            filterValues += String.format("%s", object);
        }

        // Compose Get Multiple Leads by Filter Type URL
        // Only retrieve leads that match the list of lead ids that was accumulated during activity query
        String getMultipleLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=%s&fields=%s&filterValues=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_FILTER_TYPE, LEAD_FIELDS, filterValues, READ_BATCH_SIZE);
        String nextPageToken = "";

        do {
            String gmlUrl = getMultipleLeadsUrl;

            // Append paging token to Get Multiple Leads by Filter Type URL
            if (nextPageToken.length() > 0) {
                gmlUrl = String.format("%s&nextPageToken=%s", getMultipleLeadsUrl, nextPageToken);
            }

            // Call Get Multiple Leads by Filter Type API to retrieve lead data
            JsonObject multipleLeadsObj = JsonObject.readFrom(getData(gmlUrl));
            if (multipleLeadsObj.get("success").asBoolean()) {
                if (multipleLeadsObj.get("result") != null) {
                    JsonArray multipleLeadsResultAry = multipleLeadsObj.get("result").asArray();

                    // Iterate through lead data
                    for (JsonValue leadResultObj : multipleLeadsResultAry) {
                        int leadId = leadResultObj.asObject().get("id").asInt();

                        // Join activity data with lead fields of interest (firstName, lastName, email)
                        List leadLst = activitiesMap.get(leadId);
                        for (JsonObject leadObj : leadLst) {
                            leadObj.add("firstName", leadResultObj.asObject().get("firstName").asString());
                            leadObj.add("lastName", leadResultObj.asObject().get("lastName").asString());
                            leadObj.add("email", leadResultObj.asObject().get("email").asString());
                        }
                    }
                }
            }

            nextPageToken = "";
            if (multipleLeadsObj.asObject().get("nextPageToken") != null) {
                nextPageToken = multipleLeadsObj.get("nextPageToken").asString();
            }
        } while (nextPageToken.length() > 0);

        // Now place activity data into an array of JSON objects
        JsonArray activitiesAry = new JsonArray();
        for (Map.Entry<Integer, List> activity : activitiesMap.entrySet()) {
            int leadId = activity.getKey();
            for (JsonObject leadObj : activity.getValue()) {
                // do something with leadId and each leadObj
                leadObj.add("leadId", leadId);
                activitiesAry.add(leadObj);
            }
        }

        // Print out result objects
        JsonObject result = new JsonObject();
        result.add("result", activitiesAry);
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

就是這樣。 快樂的程式碼！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2015-11-20_

## 將SoundCloud播放器與Munchkin API整合

SoundCloud提供令人驚豔的音訊託管平台，具備豐富的分析和功能，適用於從追求獨立搖滾藝人，到音樂產業頂尖的EDM藝人，再到講故事的播客，應有盡有。 除了平台令人驚豔的原生功能以外，還有世界級的API程式，可移動您的資料並追蹤聆聽行為。 這對於播客特別有用，並且可以讓您將特定的聆聽動作（例如播放、暫停和分享）與指令碼和音訊中的特定內容建立關聯。 今天，我們將瞭解如何運用[SoundCloud的Widget API](https://developers.soundcloud.com/docs/api/html5-widget)，在Marketo中傳送及追蹤這些活動。 首先，讓我們來看看產生Munchkin活動，此活動將記錄到潛在客戶登入的活動Marketo。 在最基本的層面上，我們會呼叫Munchkin.munchkinFunction，並將「visitWebPage」傳入作為第一個引數。 這會使用Marketo記錄「造訪網頁」活動，並記錄任何我們傳至方法的任意URL和查詢字串資料。 第二個引數接受含有我們資料的JavaScript物件，該資料有兩個成員：「url」和「params」，均為字串。 url成員對應至Marketo中的活動網頁，而params對應至Querystring。 基於我們的目的，我們會使用url做為SoundCloud相關動作「soundCloudInteraction」的識別碼，而引數將包含有關特定活動的其他資料。 以下是我們用來追蹤每個動作的函式：

```javascript
var trackActivity = function(action){

    //set action param to be the string passed to the function
    var qs = "action=" + action;

    //use getCurrentSound callback to get the name of the current track
    soundCloudMunchkin.widget.getCurrentSound(function(currentSound){

        //add it to our querystring
        qs = qs + "&sound=" + currentSound.title;

        //use the getPosition callback to get the position of the track in ms
        soundCloudMunchkin.widget.getPosition(function(position){

            //add it to the querystring
            qs = qs + "&position=" + position;

            //assemble our data object for the munchkin activity
            var dataObject = {
                "url": "soundCloudInteraction",
                "params": qs
            }

            //call the munchkinFunction to submit the activity
            Munchkin.munchkinFunction("visitWebPage", dataObject);
        });
    });
}
```

由於標準SoundCloud Widget內嵌於iframe中，因此Widget會使用post訊息來通訊，而需要使用回呼來取得資料，如您透過currentSound和getPosition方法所見。 SoundCloud Widget API提供了一組JavaScript回呼，我們可用來回應播放器中的個別事件，並將這些事件提交至Marketo。 我們感興趣的事件是使用者所收聽的內容、使用者所收聽的時間長短，以及使用者與播放器進行的互動，因此我們檢視的是下列事件：

* 播放
* 暫停
* 完成
* 搜尋
* 按一下_下載
* CLICK_BUY
* OPEN_SHARE_PANEL

我們還需要使用來自Widget的bind()方法，為上述每個事件新增回呼。 讓我們來看一個範例：

```javascript
widget.bind(SC.Widget.Events.PLAY, function(){
    soundCloudMunchkin.trackPlay();
});
```

這樣一來，每當播放追蹤時，我們都會引發trackPlay方法，將事件傳送至Marketo，其中包含目前追蹤的相關資料。 您可以在[這裡](https://gist.github.com/kelkingtron/6750bb07c1397d93d9c7#file-soundcloudmunchkin-js)找到完整的指令碼。 soundCloudMunchkin物件具有init方法，該方法接受SoundCloud Widget物件作為其唯一引數，該引數會將追蹤方法繫結到相關回呼，並將設定您的Widget以追蹤活動至Marketo。 您的頁面需要載入您的[Munchkin程式碼](/help/javascript-api/lead-tracking.md)，以及[SoundCloud API程式庫](https://w.soundcloud.com/player/api.js)。 除了內嵌您實際的SoundCloud Widget之外，您還需要初始化所有專案：

```javascript
window.onload=function(){
var iframe = document.getElementById(iframeId);
    if(iframe) {
        widget = SC.Widget(iframe);
        soundCloudMunchkin.init(widget);
    };
};
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2015-12-21_

## RTP和歐盟電子隱私權法令

本文說明如何使用RTP來通知網站訪客，他們正被追蹤，或自動停用歐洲訪客的追蹤。 自2012年起，歐洲訪客的任何網站都必須遵守歐盟電子隱私權指令。 2011年生效的新法律禁止在使用者不知情且未經其同意的情況下，將識別資訊儲存在使用者的電腦上。 如果您使用Cookie或任何其他技術進行非必要的追蹤，則必須：

1. 告知使用者已使用追蹤技術。
1. 說明使用這些技術的理由。
1. 使用該技術前，請先取得使用者的同意，並隨時允許他們撤回許可權。

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_1970-01-01_

## 使用AP更新Marketo中的客戶和潛在客戶資訊

在某些情況下，會使用專屬系統來更新客戶和潛在客戶資訊。 行銷團隊希望這些更新能反映在Marketo中，以便擁有最準確的記錄系統，用於行銷活動。 您可以透過下列方法，設定定期上傳至Marketo，以使用專有系統中修改的資料來更新Marketo聯絡資訊。 下圖顯示了在設定定期計時器上執行的API呼叫。 當定期計時器觸發時，使用者端邏輯會先從專有系統擷取更新的連絡人。 此操作方式因系統而異，系統使用API，或從專有系統匯出資料。 擷取更新的連絡人資訊後，我們將詳細說明執行的Marketo API。 SOAP要求syncMultipleLeads：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadRecordList>
    <leadRecord>
      <Email>henry@superstar.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Henry</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Adams</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>Director of Demand Generation</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
    <leadRecord>
      <Email>ssmith@gmail.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Suzie</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Smith</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>VP Marketing</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
  </leadRecordList>
  <dedupEnabled>true</dedupEnabled>
</ns2:paramsSyncMultipleLeads>
```

來自syncMultipleLeads的SOAP回應：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
    <syncStatusList>
      <syncStatus>
        <leadId>1094593</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
      <syncStatus>
        <leadId>1094594</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
    </syncStatusList>
  </result>
</ns2:successSyncMultipleLeads>
```

`syncMultipleLeads`執行UPSERT作業。 如果根據提交的電子郵件地址而Marketo內的連絡人已存在，則會更新屬性。 如果連絡人不存在，則會建立連絡人。 來自`syncMultipleLeads`的回應會傳回每個已提交連絡人的狀態。 `<attrName/>`內的`<leadAttributeList/>`值必須符合為該Marketo訂閱定義的SOAP API名稱。 您可以匯出欄位名稱，探索SOAP API名稱，其位於Marketo管理面板的欄位管理區段內。

請參閱以下執行上述情境的範例Java程式：

```java
 import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import java.util.\*;

public class SyncMultipleLeadsExample {

  public static void main(String[] args) {

    try {
      URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
      String marketoUserId = "CHANGE ME";
      String marketoSecretKey = "CHANGE ME";

      QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
      MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
      MktowsPort port = service.getMktowsApiSoapPort();

      // Create Signature
      DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
      String text = df.format(new Date());
      String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
      String encryptString = requestTimestamp + marketoUserId ;

      SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
      Mac mac = Mac.getInstance("HmacSHA1");
      mac.init(secretKey);
      byte[] rawHmac = mac.doFinal(encryptString.getBytes());
      char[] hexChars = Hex.encodeHex(rawHmac);
      String signature = new String(hexChars);

      // Set Authentication Header
      AuthenticationHeader header = new AuthenticationHeader();
      header.setMktowsUserId(marketoUserId);
      header.setRequestTimestamp(requestTimestamp);
      header.setRequestSignature(signature);

      // Create Request
      ParamsSyncMultipleLeads request = new ParamsSyncMultipleLeads();

      ObjectFactory objectFactory = new ObjectFactory();

      JAXBElement dedup = objectFactory.createParamsSyncMultipleLeadsDedupEnabled(true);
      request.setDedupEnabled(dedup);

      // The list of contacts defined here would be retrieved from the proprietary system
      Contact contact = new Contact("Henry","Adams","henry@superstar.com", "Director of Demand Generation");
      Contact contact2 = new Contact("Suzie","Smith","ssmith@gmail.com", "VP Marketing");

      ArrayList updatedContacts = new ArrayList();
      updatedContacts.add(contact);
      updatedContacts.add(contact2);

      ArrayOfLeadRecord arrayOfLeadRecords = new ArrayOfLeadRecord();

      Iterator it = updatedContacts.iterator();
      while(it.hasNext())
      {
        Contact c = it.next();

        LeadRecord leadRec = new LeadRecord();

        JAXBElement email = objectFactory.createLeadRecordEmail(c.email);
        leadRec.setEmail(email);

        Attribute attr1 = new Attribute();
        attr1.setAttrName("FirstName");
        attr1.setAttrValue(c.fname);

        Attribute attr2 = new Attribute();
        attr2.setAttrName("LastName");
        attr2.setAttrValue(c.lname);

        Attribute attr3 = new Attribute();
        attr3.setAttrName("Title");
        attr3.setAttrValue(c.title);

        ArrayOfAttribute aoa = new ArrayOfAttribute();
        aoa.getAttributes().add(attr1);
        aoa.getAttributes().add(attr2);
        aoa.getAttributes().add(attr3);

        QName qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
        JAXBElement attrList = new JAXBElement(qname, ArrayOfAttribute.class, aoa);

        leadRec.setLeadAttributeList(attrList);
        arrayOfLeadRecords.getLeadRecords().add(leadRec);

      }

      request.setLeadRecordList(arrayOfLeadRecords);

      JAXBContext context = JAXBContext.newInstance(SuccessSyncMultipleLeads.class);
      Marshaller m = context.createMarshaller();
      m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
      m.marshal(request, System.out);

      SuccessSyncMultipleLeads result = port.syncMultipleLeads(request, header);

      m.marshal(result, System.out);
    }

    catch(Exception e) {
      e.printStackTrace();
    }
  }

  public static class Contact {
    public String fname, lname, email, title;

      public Contact(String fname, String lname, String email, String title) {
          this.fname = fname;
          this.lname = lname;
          this.email = email;
          this.title = title;
      }
  }
}
```
 
本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Travis Kaufman_&#x200B;張貼於&#x200B;_2014-03-24_

## 使用API從Marketo傳送交易式電子郵件

需要使用Marketo UI建立現有的Smart Campaign。 電子郵件收件者也必須存在於Marketo中。 因此，在呼叫requestCampaign API之前，請使用[getLead API]&#x200B;(/help/soap-api/getlead.md)來驗證電子郵件是否存在於Marketo中。 在透過requestCampaign API進行呼叫後，您可檢查以檢視Smart Campaign是否已在Marketo中執行以進行確認。 我們會先說明如何建立Smart Campaign、如何設定觸發程式以透過API傳送行銷活動、如何定義電子郵件作為流程動作的一部分，以及用來執行此行銷活動的程式碼範例。
**如何在Marketo中建立新的Smart Campaign** Marketo中的Smart Campaign執行您的所有行銷活動。 您可以設定一系列自動化動作，以針對智慧型連絡人清單執行。 在傳送異動電子郵件的情況下，您可在行銷活動中設定觸發條件（如下所示），以使用API傳送電子郵件。 首先設定Smart Campaign。 1.在「行銷活動」中選擇方案，然後在「新增」下拉式清單中，按一下「新增本機資產」。

1. 按一下Smart Campaign
1. 輸入智慧行銷活動名稱，然後按一下建立

**將觸發器新增至Smart Campaign**&#x200B;將觸發器新增至Smart Campaign可讓您根據即時事件，一次對一個人執行Smart Campaign，在此情況下是透過[requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST)提出的請求。 1.搜尋「已要求行銷活動」觸發程式，然後將其拖放至畫布。

1. 在觸發器中，選取「是」和「網站服務API」。

**如何在行銷活動上建立電子郵件流程動作**&#x200B;電子郵件與Smart Campaign的關聯可讓行銷人員管理想要電子郵件的外觀，並允許協力廠商應用程式決定接收者與接收時間。 將電子郵件建立為新的本機資產後，您可以在行銷活動中將其設定為流量動作。  尋找並選取您要傳送的電子郵件。

**呼叫requestCampaign API的程式碼範例**&#x200B;在Marketo介面中設定行銷活動和觸發器後，我們會示範如何使用API傳送電子郵件。 第一個範例是XML要求，第二個範例是XML回應，最後一個範例是可用來產生XML要求的Java程式碼範例。 我們也會說明如何尋找呼叫`requestCampaign` API時使用的促銷活動ID。
此API呼叫也要求您預先知道Marketo促銷活動的ID。 您可以使用下列其中一種方法來判斷促銷活動ID： 1. 使用[getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1。 在瀏覽器中開啟Marketo促銷活動，並檢視URL位址列。 行銷活動ID （以4位數的整數表示）可在「SC」後面立即找到。 例如 `<https://app-stage.marketo.com/#SC**1025**A1>`。粗體部分是促銷活動ID - &quot;1025&quot;。 `requestCampaign`的SOAP請求

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

requestCampaign的SOAP回應

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

請參閱底下執行上述情境的範例Java程式。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-03-27_

## 使用AP從Marketo傳送包含動態內容的電子郵件 — 

想像您想要自動處理客服中心後續追蹤的電子郵件。 您的支援代表與客戶交談後，您想要自動傳送一封電子郵件，感謝他們連絡您的公司。 讓我們更進一步瞭解一下，並假設您想加入與您在CRM中追蹤的客戶討論的特定交談主題。 您可以使用requestCampaign SOAP API從Marketo執行此作業，以傳送包含動態內容的電子郵件。 requestCampaign API可讓您傳入銷售機會或銷售機會。 它也可讓您傳入可與現有行銷活動搭配使用的方案代號，以傳送動態內容。 requestCampaign SOAP API需要電子郵件收件者存在於Marketo中。 因此，在呼叫requestCampaign API之前，請使用[getLead API](/help/soap-api/getlead.md)來驗證電子郵件是否存在於Marketo中。 我們會先說明如何建立Smart Campaign、如何設定觸發程式以透過API傳送行銷活動、如何建立透過方案權杖接受動態內容的電子郵件、如何定義電子郵件作為流程動作的一部分，以及第五個用於執行此行銷活動的程式碼範例。 **如何在Marketo中建立新的Smart Campaign** Marketo中的Smart Campaign執行您的所有行銷活動。 您可以設定一系列自動化動作，以針對智慧型連絡人清單執行。 在傳送異動電子郵件的情況下，您可在行銷活動中設定觸發條件（如下所示），以使用API傳送電子郵件。 首先設定Smart Campaign。 1.在「行銷活動」中選擇方案，然後在「新增」下拉式清單中，按一下「新增本機資產」

1. 按一下Smart Campaign
1. 輸入智慧行銷活動名稱，然後按一下「建立&#x200B;**將觸發器新增至Smart Campaign」**&#x200B;將觸發器新增至Smart Campaign可讓您根據即時事件（在此例中為透過[requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST)提出的請求），一次對一個人執行Smart Campaign。
1. 搜尋「已要求行銷活動」觸發程式，然後將其拖放至畫布。
1. 在觸發器中，選取「是」和「網站服務API」。

**如何使用Marketo中的API傳遞動態內容**，我的Token是您可在程式中使用的變數。 我的Token可讓您在一個位置輸入與方案相關的資訊、以您指定的值取代該資訊，以及在應用程式的其他部分（例如電子郵件範本）中擷取此資訊。 使用requestCampaign SOAP API，您可以傳遞一系列方案代號，以覆寫現有代號。 行銷活動執行後，會捨棄代號。 您可以在Campaign資料夾層級或方案層級建立My Token。 Campaign資料夾層級的Token會繼承至Campaign資料夾內包含的所有方案。 如果您在Campaign資料夾層級建立My Token，則可以在方案層級覆寫繼承的值。 例如，如果您在Campaign檔案夾層級定義「方案日期」和「方案說明」的代號，則可以在個別「方案」層級覆寫這些值。

以下是其操作方式。 1.從「行銷活動」樹狀結構中，選取您要建立代號的「促銷活動」資料夾或方案。 從頂端功能表列中，選取「我的Token」 。 接著會顯示「我的Token」畫布。 從右側樹狀結構拖曳權杖型別至畫布，此例中為「文字」。 在Token Name欄位中，反白顯示My Token並輸入唯一的Token Name，在此例中為「my.conversationtopic」。 在「值」欄位中，輸入Token的相關值，在此案例中為「感謝您今天與我們通話」。 請注意，我們將透過API覆寫預設的「我的Token」值。 按一下「儲存」以儲存自訂Token。  1.按一下[新增]以建立新電子郵件。 然後按一下「新增本機Assets」並選取「電子郵件」。 接著，填寫相關欄位，為您的電子郵件命名。 起草電子郵件時，請按一下「代號」圖示，將代號加入電子郵件中。 現在您已使用Token建立範本電子郵件，我們將在後續步驟中新增該電子郵件，作為Campaign的流程動作。 因此，當您透過API呼叫行銷活動時，將會傳送電子郵件。
**如何在行銷活動上建立電子郵件流程動作**電子郵件與Smart Campaign的關聯可讓行銷人員管理想要電子郵件的外觀，並允許協力廠商應用程式決定接收者與接收時間。 將電子郵件建立為新的本機資產後，您可以在行銷活動中將其設定為流量動作。 尋找並選取您要傳送的電子郵件。
**呼叫requestCampaign API的程式碼範例**&#x200B;在Marketo介面中設定行銷活動和觸發器後，我們會示範如何使用API傳送電子郵件。 第一個範例是XML要求，第二個範例是XML回應，最後一個範例是可用來產生XML要求的Java程式碼範例。 我們也會說明如何尋找在呼叫requestCampaign API時使用的促銷活動ID。 此API呼叫也要求您預先知道Marketo促銷活動的ID。 您可以使用下列其中一種方法來判斷促銷活動ID： 1. 使用[getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1。 在瀏覽器中開啟Marketo促銷活動，並檢視URL位址列。 行銷活動ID （以4位數的整數表示）可在「SC」後面立即找到。 例如 `<https://app-stage.marketo.com/#SC**1025**A1>`。粗體部分是促銷活動ID - &quot;1025&quot;。 SOAP的requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
      </leadList>
      <programTokenList>
        <attrib>
          <name>{{my.conversationtopic}}</name>
          <value>Thank you for calling about adding a line of service to your current plan.</value>
        </attrib>
      </programTokenList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

requestCampaign的SOAP回應

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

請參閱底下執行上述情境的範例Java程式。

```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            leadKeyList.getLeadKeies().add(key);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            ArrayOfAttrib aoa = new ArrayOfAttrib();

            Attrib attrib = new Attrib();
            attrib.setName("{{my.conversationtopic}}");
            attrib.setValue("Thank you for calling about adding a line of service to your current plan.");

            aoa.getAttribs().add(attrib);

            JAXBElement<ArrayOfAttrib> arrayOfAttrib = objectFactory.createParamsRequestCampaignProgramTokenList(aoa);
            request.setProgramTokenList(arrayOfAttrib);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

在透過requestCampaign API進行呼叫後，您可檢查以檢視Smart Campaign是否已在Marketo中執行以進行確認。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-04-03_

## 根據商業邏輯擷取匿名訪客活動

想像您想要追蹤造訪公司部落格上特定文章的使用者。 假設在造訪貼文的使用者總數中，您只想追蹤至少花費5秒並捲動頁面以表示其感興趣的使用者。 對於匿名使用者，您想要透過此事件在Marketo中建立新的銷售機會；對於已知使用者，您想要透過此事件更新其銷售機會活動。 您可以在網站上使用[Munchkin追蹤代碼](/help/javascript-api/lead-tracking.md)來達到此目的。 當非Cookie使用者前往具有Munchkin追蹤代碼的頁面時，會在使用者的瀏覽器上建立新的Cookie，並在Marketo中建立新的匿名銷售機會。 如果使用者已經完成Cookie，且使用者是Marketo中的現有銷售機會，則對頁面的瀏覽將記錄在Marketo中使用者的活動記錄中。 我們會先說明如何在Marketo中產生Munchkin追蹤程式碼，接著說明如何修改Munchkin範常式式碼以只在符合某些條件時觸發，再說明如何確認在Marketo中記錄了匿名使用者的頁面造訪。

**如何產生Munchkin追蹤代碼** Munchkin追蹤代碼可讓您追蹤網站的造訪次數。 底下說明有三種型別的Munchkin程式碼，但在此範例中，我們使用非同步Munchkin追蹤程式碼。 A)簡單：程式碼行數最少，但無法最佳化網頁載入時間。 此程式碼會在每次載入網頁時載入jQuery程式庫。 B)非同步：減少網頁載入時間。 此程式碼會檢查jQuery程式庫是否已存在，如果缺少程式庫，則會將其載入，並在載入網頁其餘部分後用來執行追蹤程式碼。 C)非同步jQuery：減少網頁載入時間並改善系統效能。 此程式碼假設您已擁有jQuery，且未檢查以載入它。 1.按一下應用程式右上角的「管理員」 。  1.按一下左側樹狀結構中的Munchkin 。  1.選取「非同步」以追蹤程式碼型別。 1.按一下並複製JavaScript追蹤程式碼，以放在您的網站上。
**Cookie使用者和追蹤事件的程式碼範例**&#x200B;請將追蹤程式碼放置在網頁上的`</body>`標籤之前。 在Marketo中建立的登入頁面會自動包含追蹤程式碼，因此您不需要將此程式碼放在這些頁面上。 此程式碼範例會在指令碼載入後呼叫Munchkin API：

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```

此程式碼範例會在使用者在頁面上停留5秒鐘，且已向下捲動500畫素後向呼叫Munchkin API：

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
      }
     }

     var s = document.createElement('script');
     s.type = 'text/javascript';
     s.async = true;
    s.src = '//munchkin.marketo.net/munchkin.js';
     s.onreadystatechange = function() {
      if (this.readyState == 'complete' || this.readyState == 'loaded') {
          initMunchkin();
      }
     };
     s.onload = initMunchkin;
     document.getElementsByTagName('head')[0].appendChild(s);
   })();
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**如何確認匿名使用者的頁面造訪記錄於Marketo**

1. 按一下頂端功能表中的Analytics ，然後按一下「新增報表」 。 選擇「網頁活動」作為報表型別，然後為您的報表命名。
1. 建立報表後，按一下「智慧列示」。 然後從右側的方塊中選取「造訪的網頁」篩選器。 輸入放置Munchkin追蹤代碼的網頁。
1. 按一下「設定」。 選取「來自ISP的匿名訪客」，並將選項變更為「已顯示」。
1. 按一下「報表」 。 您現在會看到在您選取的網頁上追蹤的活動。
1. 按兩下潛在客戶記錄，然後將顯示活動記錄，您可以在其中檢視匿名使用者造訪的特定頁面。

本文包含用於實作自訂整合的程式碼。 由於其自訂性質，Marketo技術支援團隊無法疑難排解自訂工作。 如果沒有適當的技術經驗或經驗豐富的開發人員的存取權，請勿嘗試實作下列程式碼範例。

由&#x200B;_Murta_&#x200B;張貼於&#x200B;_2014-04-17_

## 使用RTP動態變更本機電話號碼

Personalization無所不包 — 我們很久以前就知道了。 話雖如此，我仍感到驚訝的是，每次需要立即協助時，很難在網站上找到相關的當地電話號碼。 好在我們[上安裝了](https://business.adobe.com/products/marketo/content-personalization.html)Marketo Real-Time Personalization<https://business.adobe.com/products/marketo/adobe-marketo.html> (RTP)。 我們可以運用[RTP訪客API](/help/javascript-api/web-personalization.md)，動態變更網站訪客在網站不同區段看到的電話號碼。 哇！ 您相信這點嗎？ 這道奇蹟如何運作？ 首先，您的網站上必須安裝RTP，如[此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)所述。 接下來，請依照下列指示，在您的網站上實作JavaScript程式碼：

1. 在&#x200B;**defaultPhone**&#x200B;設定中插入您的國際電話號碼
1. 在&#x200B;**divIds**&#x200B;設定中插入HTML元素ID
1. 如果您想要將電話號碼設為行動瀏覽器的點按通話連結，請將&#x200B;**mobileLink**&#x200B;設定設為&#x200B;**true**。
1. 在&#x200B;**cityPhone**、**statePhone**&#x200B;及&#x200B;**countryPhone**&#x200B;設定中將不同位置與其電話號碼對應

例如，以下是組態設定的範例值：

```json
  defaultPhone:"+1.503.608.4679", // Optional
  divIds:["phoneId1","phoneId2"],
  mobileLink: true,
  cityPhone: {
    "<a href="#">yanir</a>": ["San Mateo", "San Francisco"],
    "+353.1.242.3000": ["tel-aviv"]
  },
  statePhone: {
    "+1.650.376.2300": ["CA"],
    "+1.650.376.2302": ["OR"]
  },
  countryPhone: {
    "+1.650.376.2300": ["United States"],
    "+353.1.242.3000": ["Israel"]
  }
```

最後，插入包含與&#x200B;**divIds**&#x200B;中其中一個ID相符的HTML錨點標籤（來自上述步驟2）。 例如，如果您在&#x200B;**divIds**&#x200B;中指定「phoneId1」，則HTML錨點標籤會如下所示：

`  <a href="tel:+1800229933" id="phoneId1">+1800229933</a>`

指令碼會檢查此順序中是否有相符專案： cityPhone > statePhone > countryPhone > defaultPhone您也可以將電話號碼取代為文字（範例：「加入我們的San Francisco使用者群組！」） 或HTML程式碼，並根據地理位置動態變更內容。 祝您使用愉快！

```html
<a href="tel:+1800229933" id="phoneId1">+1800229933</a>
<script>
(function(a){
    rtp('get','visitor',function(yc){
        var location = yc.results.location;
        var loop = true;
        var phoneChanged = false;
        console.log(yc.results);
        function checkObj(obj){
            return Object.getOwnPropertyNames(obj).length >0;
        }
        function changePhone(p){
            d=a.divIds;
            for(i=0;i<d.length;i++){
                if(document.getElementById(d[i]) !== null){
                    document.getElementById(d[i]).innerHTML = p;
                    if(a.mobileLink){
                        document.getElementById(d[i]).href= "tel:" + p;
                    }
                    console.log(p);
                }
            }
            loop = false;
            phoneChanged = true;
        }
        function matchLocation(loc,objc){
            for (var key in objc) {
                for(i=0;i<objc[key].length && loop;i++){
                    if (!loop) { return true;};
                    val = objc[key][i];
                    //console.log(loc + location[loc] + " ? " + val);
                    if(location[loc].toLowerCase() === val.toLowerCase()){
                        changePhone(key);
                    }
                }
            }
        }
        if(checkObj(a.cityPhone)){
            matchLocation("city",a.cityPhone);
        }else if(checkObj(a.statePhone)){
            matchLocation("state",a.statePhone);
        }else if(checkObj(a.countryPhone)){
            matchLocation("country", a.countryPhone);
        }else if(!phoneChanged && a.defaultPhone.length > 0 ){
            changePhone(a.divId,a.defaultPhone);
        }
    });
})({
    defaultPhone:"",  //  [Optional] the number to show if visitor does not match the mapping below
    divIds:["phoneId1","Floater"],  //the phone HTML element ID, can be <div>, <a>, <span>, <p> etc.
    mobileLink: true,  //if you use click to call link (with href="tel:") you can also change its number

    cityPhone: {
        "<a href='#'>yanir</a>": ["San Mateo", "San Francisco"],
        "+353.1.242.3000": ["tel-aviv"]
    },
    statePhone: {
        "+1.650.376.2300": ["CA"],
        "+1.650.376.2302": ["OR"]
    },
    countryPhone: {
        "+1.650.376.2300": ["United States"],
        "+353.1.242.3000": ["Israel"]
    }
});
</script>
```

由&#x200B;_Yanir_&#x200B;張貼於&#x200B;_2016-02-02_

## 2016年冬季更新

### 自訂物件

* [自訂物件N:N關係現在已支援](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)
   * 潛在客戶或帳戶記錄現在可能透過自訂物件（透過中間物件的定義）具有多對多關係。 建立獨立自訂物件型別之後，可以透過連結欄位建立中繼物件型別，以連結至獨立物件以及潛在客戶或帳戶。
   * 此功能沒有新的API呼叫，但物件定義必須正確設定，才能透過API利用這些關係。
* `getLeadActivities`和`getLeadChanges`將不再傳回匿名潛在客戶的活動。 如需詳細資訊，請參閱[新一代Munchkin追蹤常見問題集](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2016-02-05_

## 使用REST API擷取單一銷售機會的活動

以下是我們的開發人員社群一再詢問的問題：

「如何取得個別潛在客戶過去活動的清單？」

直到最近，使用REST API達成此目標還沒有簡單的方法。 但現在有了！ 2016年冬季版本的REST API包含幾項不錯的增強功能。 [取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)現在接受可用來指定潛在客戶ID的&#x200B;**leadIds**&#x200B;引數。 指定&#x200B;**leadIds**&#x200B;引數時，只會傳回該銷售機會ID的活動。 您可以將這視為銷售機會ID篩選器。 請注意，**leadIds**&#x200B;引數可以包含以逗號分隔的銷售機會ID清單，以備您想要篩選多個銷售機會（最多30個）的結果時使用。 例如，將活動限製為特定公司的潛在客戶時，這可能很實用。 **範例**&#x200B;以下是對包含[leadIds](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)引數的&#x200B;**取得潛在客戶活動**&#x200B;的範例要求。 我已為&#x200B;**leadIds**&#x200B;引數指定值「50」，該值對應至我Marketo執行個體中的任意銷售機會。 我已為activityTypeIds引數指定值「129」，該值對應至我Marketo執行個體上的「行動應用程式工作階段」活動。

`<https://123-abc-456.mktorest.com/rest/v1/activities.json?leadIds=50&activityTypeIds=129&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3J4SMAZRQO4RKIXCEMLFCM2APRSQ====>`

以下是上述要求之回應的摘錄。 如您所見，其中只包含結果物件，具有「leadId」：50和「activityTypeId」：129。

```json
{
    "id": 846,
    "leadId": 50,
    "activityDate": "2015-04-06T21:58:59Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 7
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 879,
    "leadId": 50,
    "activityDate": "2015-04-07T00:45:11Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 5
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1114,
    "leadId": 50,
    "activityDate": "2015-04-08T00:02:41Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 241
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1551,
    "leadId": 50,
    "activityDate": "2015-04-09T23:31:56Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1716,
    "leadId": 50,
    "activityDate": "2015-04-15T22:44:19Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
```

## 使用Zapier與Marketo及500多個應用程式緊密整合

這是Marketo首席解決方案顧問Philippe Delle Case的文章。

### 目標

本文詳細說明如何在Zapier的協助下，將Marketo與可能超過500種雲端應用程式整合。 為此，我們將從頭開始建立適用於Marketo的Zapier聯結器，並實作兩個實用的整合使用案例：使用案例1：單向銷售機會從FullContact卡Reader整合至Marketo

* 使用FullContact行動卡片Reader應用程式掃描任何連絡人的名片，並在Marketo中自動建立銷售機會。
* 在Marketo中將現有銷售機會新增至靜態清單，並尋找自動新增至Google工作表的銷售機會。
* 修改您Google工作表中的任何銷售機會，並尋找回呼至Marketo的變更。

### 先決條件

**使用Zapier**&#x200B;註冊免費帳戶[Zapier](https://zapier.com/)是一項網頁應用程式自動化服務，可讓您輕鬆自動化其他線上應用程式之間的工作，而不需要程式設計師或任何IT資源。 在[這裡](https://zapier.com/api/v4/helpdocs/category/redirect/what-is-zapier)可找到簡介。 現在Zapier支援超過500種應用程式，遍佈行銷、CRM、CMS、客戶支援、電子簽章、Forms等眾多領域。 一個應用程式與另一個應用程式之間的單一整合稱為Zap。 如需支援的網頁應用程式完整清單，請檢視Zapier的[zapbook](https://zapier.com/apps)。  在[這裡](https://zapier.com/sign-up/)註冊免費帳戶。 您可以存取最多100個任務/月、5個zap和每15分鐘執行的zap。 您當然可以訂閱Zapier的付費方案（基本、商業、商務加號等），以獲得更多報酬

**以管理員身分或以提供的API使用者帳戶存取Marketo執行個體**&#x200B;我們的Zapier聯結器將使用Marketo REST API將Lead資料推送至Marketo。 若要使用此API，您需要API使用者和自訂服務(如果您是Marketo執行個體的管理員，則可自行建立)。 如果沒有，則管理員需要提供這些給您。 您也可以建立Webhook，只有Marketo管理員可以存取。 您可以在[這裡](http://rest-api/custom-services/)找到如何建立Marketo API User和自訂服務的逐步說明。 完成後，您應具備下列憑證以叫用Marketo REST API：使用者端ID、使用者端密碼、Munchkin帳戶ID、Munchkin帳戶ID

您可以從Munchkin或網站服務管理員畫面取得Munchkin帳戶ID。 其模式看起來像這樣： `000-XXX-000`。  不需要取得存取Token，因為它僅在一小時內有效。 聯結器會自動為您產生權杖。
**使用Google Docs、工作表和投影片免費註冊帳戶，這些是生產力應用程式，可讓您建立各種線上檔案、與其他人即時處理這些檔案，以及將它們儲存在您的Google線上磁碟中。 我們的使用案例需要Google工作表。 您可以在[此處](https://workspace.google.com/products/docs/)找到Google Docs的其他功能以及使用Google建立帳戶。
**使用FullContact註冊免費帳戶** FullContact可讓您透過提取所有連絡人並持續將他們與社交設定檔、像片、電子郵件簽章、公司資訊等的變更同步化，與最重要的人員保持完全的連線。 這款產品提供行動名片讀卡機，可將名片掃描到250多種網頁應用程式中，包括Zapier。 您可以在[這裡](https://app.fullcontact.com/login)註冊免費帳戶。 您也可以訂閱額外功能和容量的付費付費付費帳戶。 行動應用程式可從[Apple AppStore](https://apps.apple.com/us/app/fullcontact-business-card/id576780807)或[Google Play](https://play.google.com/store/apps/details?id=com.fullcontact.cardreader)下載。 FullContact Zap記錄在[此處](https://zapier.com/apps/contacts-plus/integrations)。

### 實作適用於Zapier的Marketo Connector

**從Zapier網頁介面建立Marketo App**，前往開發人員入口網站。 按一下&#x200B;**新增應用程式**，並至少填寫標題(例如&#39;Marketo&#39;)和說明。 標誌是選購的，但很好用。\ **驗證**&#x200B;在本節中，我們將宣告Marketo REST API驗證與驗證設定所使用的不同欄位。 先建立下列欄位：

編輯「驗證設定」：

* 驗證型別：工作階段驗證
* 驗證對應：

  `{"access_token":"{{access_token}}"}`

* 存取Token位置**：Querystring中的** Token

建立Marketo自訂服務後，使用者端ID和使用者端密碼即可使用。 我們使用使用者端識別碼和使用者端密碼，透過REST API [驗證](/help/rest-api/authentication.md)端點產生存取權杖。 我們隨後可以使用此存取權杖向REST API提出後續請求。 代號會在一小時後到期，必須重新產生才能繼續呼叫REST API。 我們選擇驗證型別=「工作階段驗證」，因為它可讓我們在工作階段權杖過期時執行自訂驗證指令碼。 我們將在「指令碼API」一節中瞭解如何實作此機制，其只能用於此型別的驗證。
**觸發器** Zapier觸發器可將資料帶入Zapier。 我們的使用案例不需要URL，因為我們將改用Marketo Webhook。 不過，我們仍需編寫虛擬觸發程式，作為Marketo聯結器的強制測試。 我們將建立呼叫Marketo REST API [取得每日使用量](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyUsageUsingGET)端點的測試觸發器。 按一下&#x200B;**新增觸發器**&#x200B;以啟動精靈並填入下列欄位（未提及的欄位可保留空白）：名稱和說明

* 名稱：測試觸發器
* 索引碼： test_trigger
* 說明： Marketo應用程式的測試觸發程式
* 重要事項？ 未勾選
* 隱藏？ 已核取

觸發欄位

* None

資料來源

* 資料Source：輪詢
* 輪詢URL： `https://{{munchkin_account_id}}.mktorest.com/rest/v1/stats/usage.json`

範例結果

* 留空

按一下&#x200B;**管理觸發程式設定**，並將測試觸發程式設定為我們將用來驗證使用者認證的觸發程式。 **動作** Zapier動作可從Zapier傳送資料。 我們將實作Create_Update銷售機會動作，以呼叫Marketo REST API。 此動作可讓我們在Marketo中建立新的銷售機會，如果銷售機會已存在，則會使用提交的值加以更新。 我們會使用「電子郵件」欄位來進行去重複化。 按一下&#x200B;**新增動作**&#x200B;以啟動精靈並填入下列欄位（未提及的欄位可保留空白）：名稱和說明

* 名稱：Create_Update銷售機會
* 名詞：銷售機會
* 索引碼：create-update-lead
* 說明：在Marketo中建立新的銷售機會，或如果該銷售機會已存在，則使用提交的值更新銷售機會
* 重要事項？ 已核取
* 隱藏？ 未勾選

動作欄位動作欄位是使用者將對應資料的欄位。 請根據您的需求仔細選擇，因為它們會呈現您可以在Marketo中更新的所有資料。 Zapier提供使用者可在Marketo中使用的所有欄位，但會導致更多程式碼和複雜性，而非一次性聯結器的必要專案。 例如，我們選取了下列欄位。

在我們的情況下，分割區名稱是強制性的，因為我們的Marketo執行個體有服務中的潛在客戶分割區。 否則可省略。 我們將其與「input」群組分開，以便終端使用者瞭解這不是要同步的欄位。 「附註」欄位來自Marketo和Salesforce之間的同步。如果您的Marketo執行個體中沒有此欄位，請勿使用。 「呼叫」欄位已建立於Marketo執行個體中，如果您的Marketo執行個體中沒有該欄位，請勿使用。 當然，目標是讓您從Marketo中挑選所需的欄位。 建議從小處開始，稍後新增額外欄位。 資料傳送位置

* 動作端點URL： `https://{{munchkin_account_id}}.mktorest.com/rest/v1/leads.json`

範例結果

* 留空

### Zapier指令碼API

Zapier的指令碼功能可讓您操控應用程式API與Zapier之間交換的要求和回應。 您可以在傳送HTTP請求之前修改請求，以及在Zapier執行任何動作之前先剖析回應。 我們需要它來完成自訂「工作階段驗證」驗證，以便與Marketo搭配使用。 詳細資訊為[這裡](https://zapier.com/developer/documentation/scripting/)。 複製下列程式碼，我們稍後將進行一些說明：

```javascript
var Zap = {

    get_session_info: function(bundle) {

       console.log('Entering get_session_info method ...');

         var access_token,
            access_token_request_payload,
            access_token_response;


        // Assemble the meta data for our Access Token swap request
         console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
        access_token_request_payload = {
            method: 'POST',
            url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
            params: {
                'grant_type' : 'client_credentials',
                'client_id' : bundle.auth_fields.client_id,
                'client_secret' : bundle.auth_fields.client_secret
            },
            headers: {
                'Content-Type': 'application/json',  // Could be anything.
                Accept: 'application/json'
            }
        };

        // Fire off the Access Token request.
        access_token_response = z.request(access_token_request_payload);

        // Extract the Access Token from returned JSON.
        access_token = JSON.parse(access_token_response.content).access_token;
        console.log('New Access_Token=' + access_token);

        // This will be mixed into bundle.auth_fields in future calls.
        //bundle.auth_fields.access_token=access_token;
        return {'access_token': access_token};
    },


    test_trigger_pre_poll: function(bundle) {

         console.log('Entering test_trigger_pre_poll method ...');

         bundle.request.params = {
         'access_token':bundle.auth_fields.access_token
         };

         return bundle.request;

    },


    test_trigger_post_poll: function(bundle) {

        console.log('Entering test_trigger_post_poll method ...');

        var data = JSON.parse(bundle.response.content);
        if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

           throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }

        return JSON.parse(bundle.response.content);
    },

    create_update_lead_pre_write: function(bundle) {

       bundle.request.params = {'access_token':bundle.auth_fields.access_token};
       return bundle.request;
    },

    create_update_lead_post_write: function(bundle) {

         var data = JSON.parse(bundle.response.content);
         if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
            throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }
        return JSON.parse(bundle.response.content);
    }
};
```

**方法** **get_session_info**

* 此方法負責產生或重新產生呼叫Marketo REST API [驗證](/help/rest-api/authentication.md)端點的存取權杖。
* 每當任何「post_poll」方法發生「存取權杖已過期」錯誤時，就會呼叫此方法。 存取Token已排程為每1小時過期，因此預期會如此。
* 動作端點URL： https://{{munchkin_account_id}}.mktorest.com/identity/oauth/token

**pre_poll， pre_write**

* 我們必須在已建立的任何觸發程式上建立「預先輪詢」方法，在傳送HTTP請求之前加以修改，這樣我們就能在其引數中新增Marketo存取權杖。
* 基於同樣的原因，我們必須在已建立的任何動作上建立「預先寫入」方法。

**post_poll， post_write**

* 我們必須在所有已建立的觸發程式上建立「post-poll」方法，以便在Zapier對回應執行任何操作前進行剖析，最終擷取「存取權杖已過期」錯誤。
* 出於同樣的原因，我們必須在已建立的任何動作上建立「寫入後」方法。
* 如果發生這類錯誤，我們會擲回InvalidSessionException，告訴Zapier重新執行驗證並再次執行get_session_info方法。

請注意，您可以從畫面右上角的「快速連結」功能表，從指令碼API存取套件組合記錄檔。 這對於偵錯指令碼非常有用。

使用案例1：整合Marketo與FullContact卡片Reader

針對這項整合，我們從FullContact建立單一Zap至Marketo。 有了此Zap，您就能使用FullContact行動卡Reader掃描名片，並將銷售機會推送至Marketo。   Marketo **Zap FullContact ->從Zapier儀表板按一下[建立新的Zap]按鈕。**

在Zapier中觸發&#x200B;****

* 挑選應用程式FullContact
* 選擇FullContact觸發器[新名片]
* 連線到您的FullContact帳戶
* 測試FullContact應用程式

Zapier中的&#x200B;**動作**

* 挑選我們先前建立的應用程式Marketo，應用程式應會顯示在Beta中
* 選擇Marketo動作「Create_Update銷售機會」
* 連線至您的Marketo帳戶，填寫驗證引數(Munchkin帳戶ID、使用者端ID、使用者端密碼)
* 將欄位從FullContact對應至Marketo
* 最終填入您新潛在客戶應該前往的資料分割名稱(只有在您的Marketo執行個體中存在資料分割時)
* 測試Marketo應用程式
* 啟用您的Zap

注意：請務必從FullContact下載名片Reader，並直接從行動裝置啟用Zapier整合。

使用案例2：整合Marketo與Google工作表 — 

針對這項整合，我們會建立兩個Zap。 一個從Marketo到Google工作表，另一個從Google工作表到Marketo。 有了此Zap，您就可以在Marketo和Google工作表之間同步一些潛在客戶或聯絡人。   **Zap Marketo Webhook -> Google工作表**
我們第一次使用Zap時不會仰賴Marketo的自訂聯結器，但會利用Marketo的Webhooks和「Zapier的Webhooks」觸發器。 在Zapier控制面板中，按一下「製作新的Zap」按鈕。 **在Zapier中觸發第1部分**

* 挑選「Webhooks by Zapier」觸發應用程式
* 勾選「Catch Hook」，即可等待POST或GET進入Zapier URL
* 不需要挑選子索引鍵
* Zapier已產生自訂&#x200B;**webhook URL**，供您傳送請求並複製到剪貼簿

**Marketo中的Webhook （管理員要完成的步驟）**

* 前往「管理員 — > Webhooks」
* 建立名為「將銷售機會推送至Zapier」的新Webhook，並編輯Webhook表單。

在範本的欄位中，宣告您要轉移至Zapier的所有銷售機會欄位，並運用Marketo的代號。 在我們的使用案例中，我們採用為自訂Zapier聯結器定義的相同欄位，該聯結器會將Leads推送至Marketo：

```json
{
"firstName":"{{lead.First Name}}",
"lastName":"{{lead.Last Name}}",
"email":"{{lead.Email Address}}",
"phone":"{{lead.Phone Number}}",
"leadOwner":"{{lead.Lead Owner First Name}} {{lead.Lead Owner Last Name}}",
"leadOwnerEmail":"{{lead.Lead Owner Email Address}}",
"leadNotes":"{{lead.Lead Notes:default=edit me}}",
"called":"{{lead.Called}}"
}
```

* 儲存表單
* 不需要回應對應，因此您已完成Webhook

**在Marketo中測試行銷活動（行銷人員或管理員應完成的步驟）**

* 從行銷活動，建立新的Smart Campaign

為了測試之目的，我們將建立一個行銷活動，每當潛在客戶狀態變更為MQL時，就會觸發我們的Webhook。 當然，您可以將Webhook用於任何其他業務目的。

* 編輯智慧清單
* 在流量中呼叫Webhook
* 排程行銷活動
* 確保每個潛在客戶每次都能執行流程
* 啟動Smart Campaign

**在Zapier中觸發第2部分**

* 為了完成「Webhooks by Zapier」觸發應用程式，我們需要啟動一次Marketo智慧行銷活動，並在Zapier中接住Webhook
* 在我們的測試案例中，我們只需要移至Marketo銷售機會資料庫、開啟銷售機會並將其狀態變更為「MQL」

**在Google Sheets中建立試算表**

* 建立新的試算表
* 建立工作表或使用預設工作表
* 為您要從Marketo同步的每個欄位(在Marketo webhook中宣告的欄位)新增一欄

  Zapier中的&#x200B;**動作**

* 挑選應用程式Google工作表
* 勾選「建立試算表列」選項
* 連線至您的Google工作表帳戶
* 選取您的Google工作表試算表
* 選取工作表
* 對&#39;Webhooks by Zapier&#39;觸發應用程式與Google工作表之間的所有欄位。

* 測試Google工作表應用程式
* 啟用您的Zap

**Zap Google工作表 — > Marketo**

在Zapier控制面板中，按一下「製作新的Zap」按鈕。

在Zapier中觸發&#x200B;****

* 挑選「Google工作表」觸發應用程式
* 勾選在試算表中新增或修改新列時觸發的「更新試算表列」
* 連線至您的Google帳戶
* 選取您要觸發的「試算表」（應與上一個Zap使用的試算表相同）和「工作表」
* 將觸發欄設為&#39;any_column&#39;
* 測試Google工作表應用程式

Zapier中的&#x200B;**動作**

* 挑選我們先前建立的應用程式Marketo，應用程式應會顯示在Beta中
* 選擇Marketo動作「Create_Update銷售機會」
* 連線至您的Marketo帳戶，填寫驗證引數(Munchkin帳戶ID、使用者端ID、使用者端密碼)
* 將欄位從Google工作表對應至Marketo
* 最終填入您新潛在客戶應該前往的資料分割名稱(只有在您的Marketo執行個體中存在資料分割時)
* 測試Marketo應用程式
* 啟用您的Zap

### 結論

以下是改善Zapier適用的Marketo聯結器的一些建議：

* 新增與不同Marketo物件（清單、自訂物件等）相關的其他觸發器和動作
* 您可以從Marketo動態提取欄位，而不是從Marketo硬式編碼欄位，但這需要在Marketo和Zapier之間有一些技術翻譯工作。
* 與開發團隊共用聯結器，最終使其可供普遍使用。

Zapier可能會部署Premium Marketo配接卡，使得實作我們的使用案例更加容易。 無論如何，本文總是可用來整合Marketo與Zapier以及免費的Zapier計畫，也能建置進階配接卡可能不支援的自訂使用案例。 我們希望您喜歡這篇文章，並協助您更成功地使用Marketo和Zapier。 感謝您！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2016-04-17_

## 2016年春季更新

**REST API**

* Asset API — 網頁
   * **登入頁面**&#x200B;現在透過15個新端點公開，這些端點將允許建立、更新、刪除、複製及登入頁面的草稿管理。 登入頁面範本現在也公開草稿管理端點
      * 取得登陸頁面
      * 依ID取得登陸頁面
      * 依名稱取得登陸頁面
      * 建立登陸頁面
      * 更新登陸頁面中繼資料
      * 取得登陸頁面內容
      * 新增登陸頁面內容區段
      * 更新登陸頁面內容區段
      * 刪除登陸頁面內容區段
      * 取得動態內容區段
      * 更新動態內容區段
      * 捨棄登陸頁面草稿
      * 核准登陸頁面
      * 取消驗證登陸頁面草稿
      * 刪除登陸頁面
   * **登陸頁面範本**
      * 捨棄登入頁面範本草稿
      * 核准登陸頁面範本
      * 取消命名登陸頁面範本
      * 刪除登入頁面範本
   * **Forms**&#x200B;已發行21個新端點，可透過API提供完整的建立、編輯和管理功能。 這些API不支援變更Forms 1.0表單。
      * 取得Forms
      * 依ID取得表單
      * 依名稱取得表單
      * 取得表單欄位清單
      * 更新表單欄位清單
      * 建立表單
      * 取得表單感謝頁面
      * 更新表單感謝頁面
      * 更新表單
      * 捨棄表單草稿
      * 核准表單
      * 取消核准表單
      * 複製表單
      * 刪除表單
      * 更新表單欄位
      * 移除表單欄位
      * 更新表單欄位可見性規則
      * 新增RTF表單欄位
      * 新增欄位集
      * 從欄位集中移除欄位
      * 取得可用的表單欄位
      * 變更表單欄位位置
      * 更新提交按鈕
   * 使用&#x200B;**取得或瀏覽方案**&#x200B;時，將會傳回連結至SFDC促銷活動之方案的SFDC促銷活動ID

**自訂物件**&#x200B;自訂物件現在支援文字區域資料型別，允許在此型別的自訂物件欄位中儲存最多2000個字元的字串欄位。 **IP位址白名單**&#x200B;管理員使用者現在可以管理IP位址白名單，以防止透過API進行未經授權的存取。 [您可以在這裡閱讀更多關於此功能的資訊](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。 **自訂活動UI**&#x200B;管理員使用者現在可以在他們的管理員功能表中定義自訂活動型別，並透過[新增自訂活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST) API將記錄新增到銷售機會。 [您可以在這裡閱讀有關定義自訂活動型別的資訊](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2016-06-01_

## 2016年夏季更新

2016年9月23日夏季發行版本中，將發行三種開發人員導向的功能。

### REST API中的電子郵件2.0支援

所有[僅與v1.0電子郵件和範本相容的預先存在的資產API](/help/rest-api/assets.md)現在都可與v2.0電子郵件資產一起使用。

### 將銷售機會推送至Marketo

[推播銷售機會](/help/rest-api/leads.md)是替代的銷售機會同步方法，專為在Smart Campaigns中更輕鬆觸發而設計。 您可以建立單一活動記錄專案、關聯銷售機會，以及更新一次呼叫中的銷售機會記錄。 其作用類似於由潛在客戶填寫的單一表格，可更輕鬆地用作Proxy方式提交表格，而不使用現有的Sync Leads方法。

### HTTP壓縮

REST API現在可以使用HTTP 1.1規格定義的標準來壓縮回應。 這有助於減少會提高傳輸速度並將頻寬使用量降到最低的回應大小。  

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2016-09-23_

## 搭配Marketo使用swagger-codegen

[Swagger-codegen](https://github.com/swagger-api/swagger-codegen)是功能強大的Java程式庫，可從swagger定義產生伺服器stub和API使用者端。 這可以大幅簡化針對任何特定語言產生使用者端的難度和成本。 若要開始使用並產生您的第一個使用者端，您首先需要取得Marketo其中一個Swagger定義的執行個體特定副本。 從您要測試的執行個體輸入Munchkin ID。 從身分定義開始。 現在您有了執行個體專屬的定義，因此您必須下載並安裝swagger-codegen。 此程式是您的作業系統所特有的，您可以在[這裡](https://github.com/swagger-api/swagger-codegen#prerequisites)取得指示。使用預設設定，codegen將會輸出一個使用者端，涵蓋所有提供的端點與模型。 這些通常是透過稱為DefaultApi的類別來管理，包含呼叫可用端點的方法以及「docs」資料夾中提供的範例（並非所有語言預設都包含這方面的範本）。 現在，讓我們建置第一個使用者端。 建立您要讓程式碼存留的資料夾，然後在您的終端機工作階段中前往該資料夾，並使用generate命令以您想要的語言建置使用者端（我們假設您已使用homebrew安裝方法）：

swagger-codegen產生 — i $definitionLocation -l $yourLanguage -o $yourLocation

這會將使用者端代碼輸出到您想要的位置。 現在來看看是否使用此項來呼叫身分端點並取得存取權杖：

```java
 public static void main(String[] args){
  String client_id = args[0];
  String client_secret = args[1];
  ApiClient client = new ApiClient();
  DefaultApi id = new DefaultApi();
  try {
   String token = id.identityOauthTokenGet(client_id, client_secret, "client_credentials").getAccessToken();
   System.out.println(token);
  } catch (ApiException e) {
   e.printStackTrace();
  }
 }
```

```php
<?php
require_once(_DIR_ . '/vendor/autoload.php');

$api_instance = new Swagger\\Client\\Api\\DefaultApi();
$client_id = "client_id_example";
$client_secret = "client_secret_example";
$grant_type = "grant_type_example";

try {
    $result = $api_instance->identityOauthTokenGet($client_id, $client_secret, $grant_type);
    print_r($result->getAccessToken);
} catch (Exception $e) {
    echo 'Exception when calling DefaultApi->identityOauthTokenGet: ', $e->getMessage(), "\\n";
}
?>
```


```c#
  public static void Main(string[] args)
  {
      string clientId = "CHANGE ME";
      string clientSecret = "CHANGE ME";

      IdentityApi instance = new IdentityApi();
      ResponseOfIdentity response = instance.IdentityUsingGET(clientId, clientSecret, "client_credentials");

      string message = string.Format("Access Token: {0}, Expires In: {1}, Scope: {2}, Token Type: {3}",
          response.AccessToken, response.ExpiresIn, response.Scope, response.TokenType);
      Console.WriteLine(message);
  }
```

現在我們知道如何取得授權，我們將在未來幾週探討自動產生使用者端的更進階使用案例。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2016-10-10_

## Excel整合第1部分：使用Power Query擷取並塑造Marketo資料

這是說明如何運用Microsoft Excel內建的Power BI技術，透過Marketo建立真正的自助業務分析體驗的一系列文章中的第一篇。 運用這些文章所涵蓋的概念，您能夠：

* 從Marketo將資料匯入Excel
* 從其他來源（SaaS應用程式、資料庫、一般檔案等）匯入及合併資料
* 針對業務需求和分析用途塑造資料
* 隨選重新整理來自Excel的資料
* 使用公式建立計算欄和測量
* 在異質資料之間建立關係
* 使用樞紐分析表和樞紐分析圖分析資料並建立進階報表
* 製作令人驚豔的資料視覺效果

### Excel的Power Query

本文先介紹使用Power Query技術的資料匯入與整形程式。 Power Query是一種資料連線技術，可讓您探索、連線、合併及調整資料來源，以符合您的分析需求。 Excel和Power BI Desktop都提供Power Query的功能。 Power Query可連線至許多資料來源，例如資料庫、Facebook、Salesforce、MS Dynamics CRM等。 現成不支援Marketo，但幸運的是，我們可以使用Marketo REST API來遠端執行系統的許多功能，而Power Query隨附一套豐富的公式（非正式名稱為「M」），允許您編寫自訂資料來源的指令碼。

### 自訂聯結器

使用Power Query編寫單一REST API呼叫的指令碼並不容易，但處理以下需求會變得更加困難：

* 存取權杖管理，包括驗證機制和定期的權杖重新整理
* 大型資料集的分頁機制
* 錯誤處理

本文說明如何建置強大的自訂聯結器，以便耗用Marketo的REST API來提取各種資料（銷售機會、活動、自訂物件、程式等）。 您唯一的限制是低於您的Marketo API每日請求限制。 此處所說明的概念著重於Marketo，但也可用來整合其他提供REST API的SaaS解決方案。

### 先決條件

#### Power查詢

在Excel 2016發行之前，適用於Excel的Microsoft Power Query是作為Excel增益集來下載和安裝在Excel 2010或Excel 2011上。 自Excel 2016起，這項技術即是原生功能，已整合至「取得與轉換」區段下的「資料」功能區中。 針對本文製作的所有指令碼均已在Excel 2016 for Windows上進行測試。 這些概念應該與Excel 2013或Excel 2010相同，但可能需要一些調整。  Power Query目前僅適用於Microsoft Windows版本的Excel；很遺憾，不支援Mac版本。

#### Marketo

Power Query將使用Marketo REST API從Marketo存取資料。 若要使用這些API，您需要API使用者和自訂服務(如果您是Marketo執行個體的管理員，則可自行建立)。 如果沒有，則管理員需要提供這些給您。 您可以在[這裡](/help/rest-api/custom-services.md)找到如何建立Marketo API User和自訂服務的逐步說明。 完成後，您應該要有下列認證以叫用Marketo REST API： **使用者端識別碼**&#x200B;和&#x200B;**使用者端密碼**。 在Marketo的Web服務管理員的REST API區段中可以找到&#x200B;**REST API端點**，它應該具有下列模式：

`<https://XXX-XXX-XXX.mktorest.com/rest>`

Marketo的API有每日請求限制，此限制可在網站服務管理員以及耗用量報告中找到。 **在設計查詢時，請務必不要超過每日限制，因為報表中可能會遺漏某些資料**。

### Power Query活頁簿建立

讓我們從新的Excel活頁簿開始。 我們會建立特定的設定工作表，用於宣告所有Marketo REST API設定。 在此工作表中，我們會建立三個表格：

資料表&#39;**REST_API_Authentication**&#39;具有資料行： **URL**：您的Marketo REST API端點。 **使用者端識別碼**：來自您的Marketo REST API OAuth2.0認證。 **使用者端密碼**：來自您的Marketo REST API OAuth2.0認證。
資料表&#39;**範圍**&#39;具有下列資料行： **分頁權杖SinceDatetime**：遵循ISO 8601標準日期標籤法的日期（例如「2016-10-06T13:22:17-08:00」、「2016-10-06」為有效日期/時間），藉由初始的「日期型」分頁權杖，用來擷取自指定期間以來的Marketo活動。 此日期主要用於限制要匯入活頁簿的資料量。 **清單ID**： Marketo中參考所有銷售機會/聯絡人的靜態清單ID。 此靜態清單可在Marketo中自由管理（例如，智慧型行銷活動可定期提供或與潛在客戶及聯絡人即時提供）。
若要取得靜態清單的ID，請在Marketo中開啟該清單，並從URL取得其數值ID，例如`<https://myorg.marketo.com/#ST3517A1LA1>`，清單ID=3511。 **最大記錄頁數**：這用於我們的虛擬遞回演演算法，它會使用「以位置為基礎」的分頁權杖逐一檢視Marketo輸出資料，每頁最多可存放300筆記錄。 因為我們希望每頁能有儘可能多的記錄，所以會保留到300筆。 因此，通常將最大記錄頁面數設為33.333，表示容量為33.333 X 300 = 99.999億筆記錄；但也表示Marketo API每日請求限製為33.333 K。 只要從查詢取得所有資料，演演算法仍會立即停止，因此此引數只是回圈的安全限制。

資料欄為`Leads`潛在客戶欄位&#x200B;**的資料表**：查詢潛在客戶與聯絡人時，要從Marketo收集以逗號分隔的潛在客戶欄位。 在Excel中宣告表格非常簡單。 在試算表中輸入兩列，列有欄名稱和值，用滑鼠反白表格的周邊，並在「插入」選單中選取「表格」圖示，然後為其命名。 為表格及其欄指定的名稱非常重要，因為我們的指令碼會直接呼叫它們。

## 驗證和存取Token

### 關於Marketo REST API驗證

Marketo的REST API已透過雙腿OAuth 2.0驗證。使用者端ID和使用者端密碼由您定義的自訂服務提供。 每個自訂服務都由僅限API的使用者擁有，該使用者擁有一組可授權服務執行特定動作的角色和許可權。 存取權杖與單一自訂服務相關聯。
完整的驗證機制[此處](/help/rest-api/authentication.md)記錄在Marketo開發人員網站上。 存取權杖最初建立時，其有效期限為3600秒或1小時。 相同自訂服務的每個連續驗證呼叫都會傳回目前存取權杖及其剩餘壽命。 權杖過期後，驗證會傳回全新的存取權杖。 管理存取權杖到期很重要，可確保您的整合順暢運作，並防止在正常作業期間發生未預期的驗證錯誤。

#### 建立查詢

從「資料」功能表的「Get&amp;Transform」區段按一下「新增查詢」圖示，以建立新查詢。 選取要以開頭的空白查詢，並賦予其名稱，例如&#39;MktoAccessToken&#39;。 從「查詢編輯器」啟動「進階編輯器」，以便手動編寫一些Power Query公式的指令碼。 在進階編輯器中輸入下列程式碼：

```
let
    // Get url and credentials from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
    clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

    // Calling Marketo API Get Access Token
    getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
    TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

    // Parsing access token
    accessTokenStr = TokenJson [access_token]

in
    accessTokenStr
```

內嵌在原始程式碼中的註釋（前面有「//」）會讓程式碼具有自我說明的功能。 如果您需要任何函式參考資料，請參閱本文參考章節中提供的連結。 按一下「完成」按鈕。 檢查存取權杖是否已成功顯示在最終套用步驟「accessTokenStr」的輸出中。  關於Excel安全性的快速註解；有時可能會要求您啟用黃色橫幅的外部資料連線。 這是讓查詢正常運作所必需的。

#### 將查詢轉換為函式

返回進階編輯器，並使用以下函式宣告來包裝程式碼：

```
let
    FnMktoGetAccessToken =()=>

        let
            // Get url and credentials from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
            clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
            clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

            // Calling Marketo API Get Access Token
           getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
           TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

            // Parsing access token from Json
           accessTokenStr = TokenJson [access_token]

        in
            accessTokenStr

in FnMktoGetAccessToken
```

此函式不會接受輸入中的任何引數，但會從設定工作表取得這些引數。 它會產生存取權杖作為輸出。 重新命名您的查詢`FnMktoGetAccessToken`並儲存。 請注意，您可以隨時按一下「資料」功能表「取得和轉換」區段中的「顯示查詢」按鈕，以在Excel中檢視所有查詢。 您的函式現在應使用函式圖示「Fx」標籤。

### 載入靜態清單的成員

#### 取得銷售機會

Marketo銷售機會API提供針對銷售機會記錄的簡單CRUD操作、修改銷售機會在靜態清單和計畫中的成員資格，以及啟動銷售機會的Smart Campaign處理。 所有這些功能都記錄在[這裡](/help/rest-api/leads.md)。 可以根據靜態清單或計畫中的成員資格來擷取大型潛在客戶記錄。 使用靜態清單的ID，您可以擷取屬於該靜態清單成員的所有潛在客戶記錄。 清單的識別碼是呼叫中的路徑引數。 如需詳細資訊，請參閱Marketo開發人員檔案中的「清單和計畫成員資格」一章。 每個API呼叫最多可取得300筆潛在客戶記錄，因此我們需要運用分頁權杖來收集每頁300筆記錄的記錄。 我們會在第一次呼叫後，在Json回應中取得分頁Token，而且我們知道當分頁Token不再位於輸出中時，我們已完成。

### 基本查詢

讓我們開始一個功能齊全的查詢，旨在從靜態清單下載所有銷售機會。 建立名為&#39;MktoLeads&#39;的新空白查詢，並在進階編輯器中輸入下列程式碼：

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the number of iterations (pages of 300 records) - Table Scoping
    iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    pagingTokenParamStr = "",

    // Function iterating though the pages
    FnProcessOnePage =
    (accessTokenParamStr, pagingTokenParamStr) as record =>
        let

            // Send REST API Request
            content = Web.Contents(getMultipleLeadsByListIdUrl & accessTokenParamStr & pagingTokenParamStr),

            // Recover Json output and watch if token is expired, in that case, regenerate access token
            newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
            getMultipleLeadsByListIdJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(getMultipleLeadsByListIdUrl & newAccessTokenParamStr & pagingTokenParamStr)),

            // Parse Json outputs: data and next page token
            data = try getMultipleLeadsByListIdJson[result] otherwise null,
            next  = try  "&nextPageToken=" & getMultipleLeadsByListIdJson[nextPageToken] otherwise null,
            res = [Data=data, Next=next, Access=newAccessTokenParamStr]
        in
            res,

    // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
    GeneratedList =
        List.Generate(
            ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
            each [i]<iterationsNum and [res][Data]<>null,
            each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
            each [res][Data])
in
    GeneratedList
```

Power Query不提供傳統的回圈功能(例如For-loop、While-loop)和不支援遞回。 一個好的因應方法是使用List.Generate實作For-loop。 [此處](http://msdn.microsoft.com/query-bi/m/list-generate)記錄此函式。 含清單。 產生可在頁面上重複的專案。 在反複運算的每個步驟中，我們都會擷取資料頁面、保留URL （包含下一頁的分頁Token），並將結果儲存在產生清單的下一個專案中。 來自[Datachant](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/)的部落格是解決此問題的絕佳資源。 我們的引數「最大記錄頁面」在此限制頁面數量，並將其限制在真實範圍內，以避免無限回圈。 另一個難題是確儲存取權杖永遠不會過期。 使用Power Query追蹤其剩餘壽命會過於複雜。 因此，所有對REST API的呼叫都已備份並附上錯誤檢查；如果發生錯誤，我們會假設權杖已過期，並先續約並重新執行呼叫。 如果第二次呼叫失敗，則會將第二次失敗通知給Excel （最壞的情況下，您不會收到任何資料）。 在儲存查詢後或隨時按一下「重新整理按鈕」以啟動查詢。  在我們的案例中，1364個銷售機會記錄被擷取以適合5個清單中5個頁面的資料。

### 塑造資料

我們需要調整資料形狀，讓所有這些記錄都位於單一單一單一單一記錄清單中。 有兩種方法可以達成此目的：

* 使用更多程式碼
* 利用Power Query UI

在輸出格線上按一下滑鼠右鍵，然後在內容功能表中選擇「至表格」，將其轉換為清單表格。  在「至表格」彈出式視窗中，將預設值保留在2個挑選清單中。  現在展開產生的清單表格。 現在，我們已將所有記錄放在一個清單中。 以Json格式編碼的記錄包含欄位及其相關值。 再次展開。 在快顯視窗中選取您要保留的所有欄位，取消勾選「使用原始欄名稱作為前置詞」核取方塊。  歡迎光臨！ 所有記錄都會妥善顯示在表格中。 如果重新開啟進階編輯器，可檢視已新增3行程式碼來設定資料的形狀：

```
# "Converted to Table" = Table.FromList(GeneratedList, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
# "Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
# "Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"}, {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"})
```

您可以使用Power Query完成更多工作，例如使用計算值建立額外的欄，我們稍後將看到更多可能性。 讓我們儲存並關閉此查詢。 現在隨時可以手動重新整理，或透過背景重新整理自動重新整理。

### 重新導向結果

現在的問題是，要將結果資料傳送到何處？ 使用滑鼠將滑鼠停留在您的查詢上，並在內容功能表中選取功能表「載入至……」。 在快顯視窗中，您可以選取：

* &#39;Table&#39;如果您想要傳送所有成型資料至工作表（新的或現有的資料），
* 如果您的目標是要在Power Pivot中做進一步分析，請選取「僅建立連線」。

核取方塊「將此專案新增至資料模型」可讓您利用Power Pivot中的資料；這是我們想要用於本文第二部分的內容。

### 管理分頁

由於我們專案的目的是要建立更多查詢，因此我們要進行一些重構，並擷取可重複使用的函式以管理分頁。 建立名稱為&#x200B;**FnMktoGetPagedData**&#x200B;的新空白查詢，並在進階編輯器中輸入下列程式碼：

```
let
    FnMktoGetPagedData =(url, accessTokenParamStr, pagingTokenParamStr)=>

    let

        // Get the number of iterations (pages of 300 records) - Table Scoping
        iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],

        // Sub-function iterating though the REST API service result pages
        FnProcessOnePage =
        (accessTokenParamStr, pagingTokenParamStr) as record =>
            let

                // Send REST API Request
                content = Web.Contents(url& accessTokenParamStr & pagingTokenParamStr),

                // Recover Json output and watch if token is expired, in that case, regenerate access token
                newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
                contentJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(url & newAccessTokenParamStr & pagingTokenParamStr)),

                // Parse Json outputs: data and next page token
                data = try contentJson[result] otherwise null,
                next  = try  "&nextPageToken=" & contentJson[nextPageToken] otherwise null,
                res = [Data=data, Next=next, Access=newAccessTokenParamStr]
            in
                res,

        // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
        GeneratedList =
            List.Generate(
                ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
                each [i]<iterationsNum and [res][Data]<>null,
                each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
                each [res][Data])
    in
        GeneratedList

in FnMktoGetPagedData
```

儲存查詢。 我們接下來會使用它。

### 簡化查詢

讓我們重新寫入查詢&#39;MktoLeads&#39;，它將呼叫&#x200B;**FnMktoGetPagedData**&#x200B;函式。

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    // No initial paging token required for this call
    pagingTokenParamStr = "",

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getMultipleLeadsByListIdUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

如您所見，我們的查詢現在真的可以輕鬆讀取和維護。 我們將針對其他查詢再次運用&#x200B;**FnMktoGetPagedData**&#x200B;函式。

### 從定義的時段載入特定活動

#### 透過分頁取得活動

Marketo允許與潛在客戶記錄相關的各種活動型別。 幾乎所有變更、動作或流程步驟都會針對潛在客戶的活動記錄進行記錄，並可透過API擷取，或在「智慧清單」和「智慧行銷活動」篩選器和觸發器中運用。 活動一律透過leadId與潛在客戶記錄建立關聯（與記錄ID相對應），且其本身也有唯一的整數ID。 您可以在[這裡](/help/rest-api/activities.md)找到完整的REST API檔案。

潛在活動型別非常多，可能因訂閱而異，每種都有獨特的定義。 雖然每個活動都有自己的唯一id、leadId和activityDate，primaryAttributeValueId和primaryAttributeValue的含義會有所不同。 我們將重點放在「有趣的時刻」上，這是一種Marketo使用ID 41追蹤的活動。 我們要解決的新挑戰是：

* 我們需要啟動「以日期為基礎」的分頁權杖來定義活動發生的時間段，
* 塑造資料會比較棘手，因為根據活動型別，活動特定屬性的清單會以Json提供，並需要剖析和平面化以方便分析。

#### 以日期為基礎的分頁Token

我們需要先建置此函式，以產生初始「日期型」分頁Token，這是設定活動查詢之時間範圍的必要條件。 您可以在[這裡](/help/rest-api/paging-tokens.md)找到分頁權杖的相關檔案。 建立名稱為&#x200B;**FnMktoGetPagingToken**&#x200B;的新空白查詢，並在進階編輯器中輸入下列程式碼：

```
let
    FnMktoGetPagingToken =(accessTokenStr)=>

        let
            // Get url from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],

            // Get Paging Token SinceDatetime from config worksheet - Table Scoping
            mktoPTSinceDatetimeStr = DateTime.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Paging Token SinceDatetime], "yyyy-MM-ddThh:mm:ss"),

            // Building URL for API Call
            getPagingTokenUrl = mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & accessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr,

            // Calling Marketo API Get Paging Token
            content = Web.Contents(getPagingTokenUrl),

            // Recover Json output and watch if access token is expired, in that case, regenerate it
            newAccessTokenStr = if Json.Document(content)[success]=true then accessTokenStr else "?access_token=" & FnMktoGetAccessToken(),
            pagingTokenJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & newAccessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr)),

            // Parsing Paging Token
            pagingTokenStr = pagingTokenJson[nextPageToken]

        in
            pagingTokenStr

in FnMktoGetPagingToken
```

儲存函式。 我們接下來會使用它。

#### 有趣的時刻活動

現在來撰寫查詢&#39;MktoInterestedMentsActivities&#39;，此查詢將會呼叫&#x200B;**FnMktoGetPagedData**&#x200B;和&#x200B;**FnMktoGetPagingToken**&#x200B;函式。

```
let

    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),

    // Build Get Activities URL
    getActivitiesUrl = mktoUrlStr & "/rest/v1/activities.json?ListId=" & listIdStr & "&activityTypeIds=46",

    // Build Marketo Access Token URL parameter
    accessTokenStr = FnMktoGetAccessToken(),
    accessTokenParamStr = "&access_token=" & accessTokenStr,

    // Obtain date-based paging token used to scope in time the activities
    pagingTokenParamStr = "&nextPageToken=" & FnMktoGetPagingToken(accessTokenStr),

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getActivitiesUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

此查詢的結果同樣是清單清單，因此它需要一些進一步的資料處理才能用於分析。

### 塑造資料

讓我們對潛在客戶進行相同的成型作業：

* 在輸出格線上按一下滑鼠右鍵，然後在內容功能表中選擇「至表格」，將其轉換為清單表格，
* 展開產生的清單表格，
* 再展開一次，在快顯視窗中選取您要保留的所有欄位（取消勾選「使用原始欄名稱作為前置詞」）。

除了仍包含與有趣時刻相關的特定屬性清單的「屬性」欄以外，您可以看到這些欄及其值。 讓我們展開這些屬性。 現在，清單已展開為記錄，我們再次展開，選取我們想要的欄位（每個屬性的名稱和值），並取消勾選「使用原始欄名稱作為前置詞」的核取方塊。  因此，所有資料都是可見的，包括屬性，但每個有趣的時刻活動都會跨越三行。 這很難用於我們的分析。  理想情況下，每個活動只需一行，所有屬性都會顯示為額外欄。 我們可以從表格中樞軸旋轉3個屬性，輕鬆達到此目的。 從活動屬性中選取2欄「名稱」和「值」，然後按一下「轉換」功能表中的「樞紐分析表」。 在快顯視窗中詢問進階選項，並選取「值欄」=值和「不要彙總」值函式。  按一下「確定」，您即須針對每個活動輸出一行程式資料。  下列「資料塑形」程式碼行應已自動附加至查詢的指令碼：

```
  #"Converted to Table" = Table.FromList(result, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
    #"Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}, {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}),
    #"Expanded attributes" = Table.ExpandListColumn(#"Expanded Column2", "attributes"),
    #"Expanded attributes1" = Table.ExpandRecordColumn(#"Expanded attributes", "attributes", {"name", "value"}, {"name", "value"}),
    #"Pivoted Column" = Table.Pivot(#"Expanded attributes1", List.Distinct(#"Expanded attributes1"[name]), "name", "value")
in
    #"Pivoted Column"
```

### 後續步驟

您現在應該能夠設計所有必要的查詢，以存取透過其REST API取得的任何特定Marketo資料。 我們希望您喜歡這篇文章，並協助您善用Excel和Marketo結合的絕佳優點。 第二篇文章中也提供具有所有查詢的範例活頁簿。

### 引用

#### Power查詢

* [Power Query — 概觀與學習](https://support.microsoft.com/en-us/article/Power-Query-Overview-and-Learning-ed614c81-4b00-4291-bd3a-55d80767f81d)
* [Power Query公式參考](http://msdn.microsoft.com/query-bi/m/power-query-m-reference)
* [Matt Masson部落格](http://www.mattmasson.com/tag/power-query/)提供一些有關Power Query的好資源
* [DataChant Blog](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/)對於分頁機制的實作非常有用 

由&#x200B;_菲利普_&#x200B;張貼於&#x200B;_2016-10-18_

## 2016年秋季更新

在2016年秋季版本中，我們新增了Email v2變數和模組的CRUD支援，以及指定帳戶的CRUD支援。 您現在可以在本機使用Marketo REST API來讀取和編輯內容，以及移動、重新排序和刪除內容。 請參閱以下的完整更新清單：

### 潛在客戶資料庫API

* [**已命名的帳戶**](/help/rest-api/named-accounts.md)
   * 讀取、更新及刪除具名帳戶的新端點
   * 已知問題：
      * 截至2016年秋季版，潛在客戶無法透過API與具名帳戶建立關聯

### 資產API

* [**電子郵件**](https://developer.adobe.com/marketo-apis/api/asset/#operation/describeUsingGET_5)
   * 操控電子郵件v2變數的新端點
   * 操控電子郵件v2模組的新端點
   * 已知問題：
      * 查詢和更新包含預測性代號的區段將會傳回錯誤
      * 含有包含預測性權杖之內容區段的電子郵件，可能無法使用API來核准

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2016-12-07_

## 我們為什麼淘汰@MarketoDev Twitter Handl3

我們已決定在Twitter上淘汰@MarketoDev控點。 該帳戶將於2011年12月9日停用。 好奇為什麼？ **後退至2014年初……**&#x200B;我們剛才已啟動Marketo開發人員網站，協助開發人員根據我們的API進行建置。 我們想要提升對Marketo平台的認知，並降低開發人員進入的障礙。 同時，我們也建立@MarketoDev與想要與Marketo建立整合式解決方案的技術合作夥伴和客戶互動。 就像任何勇敢的新事業一樣，我們不確定事情會如何發展。 我們最初發推文發佈新的部落格和新的API版本。 一切順利；開發人員網站的流量增加！ 我們也開始收到各種各樣的問題，並忠實地回答。 **快進到2016年底……**&#x200B;隨著[LaunchPoint合作夥伴](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)生態系統的成長，推文活動的數量也增加了。 回應張貼到@MarketoDev上的大量問題，已成為我們小團隊的一項重要工作。 我們收到越來越多的一般產品及銷售相關問題，並已重新導向至@Marketo或@MarketoCares。 我們也發現140個字元完全不足以回答開發相關問題。 回答這些問題通常會導致冗長且涉及對話的對話，而這種方式無法擴展。 最後，我們分析了開發人員網站訪客的流量來源，發現大多數來自有機搜尋，以及我們部落格上的「立即訂閱」功能。 基於這些原因，我們決定拔插@MarketoDev。 **從這裡開始……**&#x200B;如果您是Twitter的粉絲（而且不是），請不要擔心！ 我們的企業Twitter控點@Marketo仍在運作；我們的客戶支援亦然@MarketoCares控點。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2016-12-02_

## Excel整合第2部分：使用Power Pivot和Power View建置進階Marketo報表和資料視覺效果 — 

這是兩篇文章系列中的第二篇，說明如何運用Microsoft Excel內建的Power BI技術，透過Marketo建立真正的自助業務分析體驗。 運用這些文章所涵蓋的概念，您能夠：

* 從Marketo將資料匯入Excel
* 從其他來源（SaaS應用程式、資料庫、一般檔案等）匯入及合併資料
* 針對業務需求和分析用途塑造資料
* 在Excel中隨選重新整理資料
* 使用公式建立計算欄和測量
* 在異質資料之間建立關係
* 使用樞紐分析表和樞紐分析圖分析資料並建立進階報表
* 製作令人驚豔的資料視覺效果

### Power Pivot和Power View for Excel

在本文中，我們提供如何建置下列專案的範例：

* 使用Power Pivot利用不同Marketo資料集合之間關係的進階Marketo報表
* 使用Power View呈現靜態和動畫視覺效果

**Power Pivot**&#x200B;是Excel增益集，已包含在Excel 2016中，可用來執行強大的資料分析，並建立複雜的資料模型。 使用Power Pivot，您可以合併來自各種來源的大量資料、快速執行資訊分析，以及輕鬆分享見解。 使用Power Query從不同資料來源擷取的資料，可以傳送至資料模型、Excel試算表或兩者。 在第一篇文章中，我們從Marketo匯入並塑造資料，然後傳送給資料模型，在試算表上提供資料之前，執行更複雜的分析。 **Power View**&#x200B;是Excel視覺化圖層的替代方案。 這是一種互動式資料探索、視覺化和簡報體驗，鼓勵直覺式的臨機報告和儀表板。

本文說明的所有步驟已在Windows適用的Excel 2016上測試。 Excel 2013或Excel 2010 （不含Power View）的概念應該相同，但可能需要一些調整。 Power Pivot和Power View目前僅適用於Microsoft Windows版本的Excel 2016，而Office 365版本的Excel 2016不完全支援Power Pivot和Power View。

### Marketo Power活頁簿

#### 下載活頁簿

在第一篇文章中，我們介紹了使用Power Query技術的資料匯入與塑造程式。 我們已瞭解如何實作一些進階的效能查詢，以從Marketo擷取銷售機會和活動。 因為有些人會想直接跳到他們建立報表和視覺效果的時間點，而不用編碼。 此活頁簿包含第一篇文章中詳述的所有查詢以及更多查詢。 我們改善了錯誤處理，並在設定工作表中新增了一些額外引數。 如果您瀏覽了第一篇文章，我們仍建議您下載Marketo Power活頁簿並檢視新增的內容。

免責宣告： Marketo Power活頁簿並非官方Marketo產品，因此Marketo不支援。 您可以根據個人業務需求，自由使用與擴充，但若要這麼做，您將自行承擔相關風險。

#### 設定活頁簿

填寫Marketo設定工作表中的所有必要資訊：

* **需要Marketo REST API驗證：**
* **範圍：**&#x200B;設定分頁權杖SinceDatetime，以及包含您要分析之所有潛在客戶的Marketo靜態清單識別碼
* **銷售機會：**&#x200B;對於即將到來的報告，您必須至少指定下列銷售機會欄位： `id`、`firstName`、`lastName`、`email`、建立`edAt`、`updatedAt`、`title`、`company`、`industry`、`inferredCountry`、`inferredCity`
   * 如果其中一個自訂欄位中的城市資訊更準確，則您可以使用自己的欄位
* **活動：**&#x200B;這裡針對每個活動集指定要從Marketo資料庫擷取的活動型別，現在不需要變更此專案。
   * 請注意，我們提供了活頁簿上的公用程式查詢，如果您稍後要調整此資訊，此查詢會列在Excel活頁簿中的所有現有活動型別

請注意，您可能會看到一些安全性相關快顯視窗。 信任外部連線，並將其設定為「公用」。 如果您看到下方的快顯視窗，請留意「匿名」網頁存取內容。 Marketo的驗證由我們的自訂查詢直接管理，因此不需要啟用任何其他型別的存取權。

#### 下載Marketo資料

首先，請確定您在Marketo設定工作表的範圍區域中定義的引數，不會導致下載過多資料，超過您的Marketo API每日請求限制。 準備就緒後，從「資料」功能表按一下「全部重新整理」按鈕，然後等待所有資料都下載到活頁簿中。 如果在下載資料時顯示格式錯誤訊息（類似「column1找不到」），則表示一個或多個查詢無法取得資料，因此格式也會失敗。 請稍後再試，如果錯誤持續發生，請檢查您的Excel版本（請勿使用Office 365中的Excel 2016）。 同樣重要的是，要留意Marketo平台的延遲情形。 如果您在靜態清單或潛在客戶資料中進行任何變更，最好等到啟動Power Queries後再進行。

### Power Pivot中的資料模型

按一下Power Pivot功能表中的[管理]按鈕（位於上方功能表列）開啟Power Pivot （如果無法使用，請檢查Excel版本，Power Pivot可以在某些Excel版本中作為增益集安裝）。 從Marketo下載並傳送至資料模型的所有資料，應該都可以從Power Pivot視窗底部的不同索引標籤存取。

### 資料分析運算式(DAX)

我們需要擴充或重新格式化某些報表的資料。 讓我們使用Power Pivot資料分析運算式(DAX)來定義某些自訂計算，如計算欄和測量（也稱為計算欄位）。 請參閱「參考」區段中的「Power Pivot中的DAX」連結，瞭解更多有關DAX的資訊。 確定計算區域顯示在Power Pivot視窗中；如果沒有，請從Power Pivot首頁功能表啟用它。  選取&#x200B;**MktoLeads**&#x200B;索引標籤，並將&#x200B;**Leads Count**&#x200B;量值新增至Leads計算區域中的任意位置： **Leads Count：=****DISTINCTCOUNT****([id])**。 此測量方式會根據其ID計算清單中可用的不同潛在客戶。 它也會考慮報表內容中已有的最終篩選器。 此量度並非真正必要，因為報表可彙總銷售機會數量，但我們這麼做是為了讓銷售機會計數具有比「MktoLeads總和」更好的名稱。 這也是一個簡單的範例，可讓您輕鬆想像一些更複雜的測量，針對特定型別的資料輸入進行平均、最小值、最大值等操作（例如分數高於50的所有潛在客戶、平均分數等）。 ...)。  現在來選取&#x200B;**MktoWebActivities**&#x200B;索引標籤，並建立三個計算資料行。 捲動至表格的最右側，並按一下欄「新增欄」，插入下列計算欄。 **活動：**&#x200B;在表格MktoActivityTypes中查詢活動ID，取得使用者易記的活動標籤。 **\=****LOOKUPVALUE****（MktoActivityTypes[名稱]，MktoActivityTypes[id]，[activityTypeId]）** **年 — 月：**&#x200B;以更適合某些報表的模式「YYYYmm」重新格式化活動日期。 **\=****LEFT****([activityDate]，4)&amp;****MID****([activityDate]，6,2)** **日期：**&#x200B;活動日期只是原始查詢的字串，請將其轉換為適當的日期。 **\=****日期****(****LEFT****([activityDate]，4)，****MID****([activityDate]，6,2)，****MID****([activityDate]，9,2))**&#x200B;現在讓我們為&#x200B;**MktoEmailActivities**&#x200B;索引標籤建立三個相同的測量，以及兩個其他測量：**促銷活動：**&#x200B;通過在MktoCampaigns表中查詢行銷活動ID，獲取使用者友好的行銷活動名稱。 **\=****LOOKUPVALUE****(MktoCampaigns[name]，MktoCampaigns[id]，[campaignId])** **方案：**&#x200B;在表格MktoCampaigns中查詢促銷活動ID，取得好記的方案名稱。 MktoPrograms表格可提供有關程式的更多詳細資料，例如資料夾、工作區等。 **\=****LOOKUPVALUE****(MktoCampaigns[programName]，MktoCampaigns[id]，[campaignId])**

### Entity-Relationship

我們先前曾看到一種方式，可讓您從模型內的另一個表格中查詢資訊，以完成一些遺漏的資訊。 Power Pivot提供更強大的選項，可定義資料模型某些表格之間的關係，讓我們直接從報表運用這些關係。 讓我們定義報告的關鍵關係。 從Power Pivot視窗中選取「圖表檢視」。 在資料模型圖表中追蹤下列關係：

* **MktoInterestedMomentActivities:leadId →** **MktoLeads:id**
* **MktoScoringActivities:leadId →** **MktoLeads:id**
* **MktoRevenueStageActivities:leadId →** **MktoLeads:id**
* **MktoWebActivities:leadId →** **MktoLeads:id**
* **MktoEmailActivities:leadId →** **MktoLeads： ID**

我們不會在報告中使用所有這些關係和物件，只會使用銷售機會、網頁活動和電子郵件活動。 現在該建置一些報告了。

### 電子郵件效能樞紐分析圖

此第一個報表顯示以標準Excel樞紐分析圖為基礎的電子郵件效能KPI。 它可讓我們依產業和/或行銷活動篩選資料。 您可以從[樞紐分析表]選取器選取[樞紐分析圖]，直接從[Power樞紐分析表]功能表建立樞紐分析圖。  另一種選擇是直接從Excel試算表建立樞紐分析圖，點選「使用此活頁簿的資料模型」選項。  從&#x200B;**MktoEmailActivities**&#x200B;與&#x200B;**MktoLeads**&#x200B;表格拖放欄位，如下圖所示： **MktoEmailActivities.Activity →** **Legend** （這會使用我們在&#x200B;**MktoEmailActivities**&#x200B;先前版本）上實作的DAX計算資料行) **MktoEmailActivities.Date →** **Axis** （這會使用我們實作的計算資料行&#x200B;**MktoEmailActivities**&#x200B;更早） **MktoEmailActivities.Id →** **∑值** **MktoEmailActivities.Campaign →** **篩選器** **MktoLeads.industry →** **篩選器**

您可以對每個捨棄的欄位選取「值欄位設定」，以建立自訂名稱。 在此情況下，我們會將「電子郵件活動ID」欄位拖放至「∑值」區段，並將其自訂名稱編輯為「活動數」。 現在來設定樞紐分析圖。 直接在圖表上按一下滑鼠右鍵，然後選取內容功能表中的「變更圖表型別」選項。 這就是我們如何為所有資料序列選取不同的圖表型別。

### 具有Power View的銷售機會地圖

第二個報表會依地理位置及產業顯示您的「銷售機會」與「聯絡人」。 此報表需要Power View。 請依照下列參考連結開啟Excel中的功能表。 或者，您可以在Excel搜尋方塊中輸入&#39;power view&#39;。 選取「插入Power View報表」。  在空白的Power View報表上，選取右側面板上的&#x200B;**MktoLeads**&#x200B;表格，並拖放銷售機會位置欄位（例如&#x200B;**inferredCity**）。 現在，功能表「設計」會出現在主功能表中。

在Power View的「設計」選單中選取「地圖」，切換至地圖視覺效果。 從&#x200B;**MktoLeads**&#x200B;資料表中拖放欄位，如下圖所示： **MktoLeads.industry →** **Color** **MktoLeads.inferredCity →** **位置** **MktoLeads.Leads計數→** **∑大小** （這會使用我們先前在&#x200B;**MktoLeads**&#x200B;上實作的DAXmeasure），而且您的銷售機會對應已準備就緒！ 您只需要調整地圖的大小、自訂標題和圖例。 Power View可讓您在單一試算表上建置含有多個圖表的進階儀表板。 請檢視&#39;[建立令人驚豔的Power View報表](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7)&#39;下參考的教學課程，瞭解如何使用Power View繼續處理更多儀表板元件。

### 在3D地圖上以動畫呈現的網頁活動

這份第三份報告會依產業在3D世界地圖上顯示您的Lead網頁活動。 此報表需要3D地圖。 只要在Excel搜尋方塊中輸入「3D」，然後選取「3D Map」即可。 從快顯視窗建立新的導覽。  選取右側面板上的泡泡圖。 從&#x200B;**MktoLeads**&#x200B;和&#x200B;**MktoWebActivities**&#x200B;資料表拖放欄位，如下圖所示： **MktoLeads.industry →** **Category** **MktoLeads.inferredCity →** **位置** **MktoWebActivities.Activity →** **時間** （這會使用我們在&#x200B;**上實作的DAX計算資料行） MKTOWebAcTIVITIES**&#x200B;更早版本。 ID欄位也可用於計算活動。) **MktoWebActivities.Date →** **Time** （這會使用我們在&#x200B;**MktoWebActivities**&#x200B;先前實作的DAX計算資料行） **MktoWebActivities.Activity**&#x200B;也可用作為篩選條件，以篩選出不同型別的網頁活動。

使用「主題」按鈕來變更3D地圖的色彩配置。 開啟「場景選項」以自訂動畫。
3D World Map已經完成，現在您可以盡情製作全球的動畫，並從中製作視訊。

### 後續步驟

我們剛開始探討Excel Power BI工具的功能。 我們建議您搜尋網路上的其他優秀文章和教學課程，以擴充您的Excel技能，並設計達成業務目標所需的報告。 我們希望您喜歡這些文章，希望它們能協助您運用Excel和Marketo結合的絕佳優勢。

### 引用

#### Power Pivot

* [Power Pivot：在Excel中強大的資料分析和資料模型](https://support.microsoft.com/en-us/article/Power-Pivot-Powerful-data-analysis-and-data-modeling-in-Excel-d7b119ed-1b3b-4f23-b634-445ab141b59b)
* Power Pivot[中的](https://support.microsoft.com/en-us/article/Data-Analysis-Expressions-DAX-in-Power-Pivot-bab3fbe3-2385-485a-980b-5f64d3b0f730)資料分析運算式(DAX)

#### Power View

* [在Excel 2016中開啟Power View](https://support.microsoft.com/en-us/article/Turn-on-Power-View-in-Excel-2016-for-Windows-f8fc21a6-08fc-407a-8a91-643fa848729a)
* [教學課程：建立令人驚豔的Power View報告](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7)

由&#x200B;_菲利普_&#x200B;張貼於&#x200B;_2017-02-02_

## Marketo API中活動記錄的重要變更

**注意：此貼文將會更新，以反映由於移轉至新基礎結構而對API傳回的活動記錄所做的變更。** **上次更新： 2018年9月13日**&#x200B;隨著Marketo新一代活動服務於2017年9月開始推出，我們將無法在活動、資料值變更或Marketo API傳回的銷售機會刪除記錄中強制使用整數&quot;id&quot;欄位的唯一性或存在性。 為避免擷取活動記錄的整合服務中斷，應將id欄位視為選用。 轉換此變更將開始影響訂閱和即將發行的版本。 此變更將會影響下列端點： REST API


受影響的SOAP型別為`ActivityRecord`和`LeadChangeRecord`。

### 範例

下列範例顯示將受影響的記錄型別。 在這兩個範例中，受影響的欄位都稱為「id」。

**範例REST欄位： id**

```json
  {
      "id" : 102988,
      "leadId" : 1,
      "activityDate" : "2015-01-16T23:32:19Z",
      "activityTypeId" : 1,
      "primaryAttributeValueId" : 71,
      "primaryAttributeValue" : "localhost/munchkintest2.html",
      "attributes" : [
          {
              "name" : "Client IP Address",
              "value" : "10.0.19.252"
          },
          {
              "name" : "Query Parameters",
              "value" : ""
          },
          {
              "name" : "Referrer URL",
              "value" : ""
          },
          {
              "name" : "User Agent",
              "value" : "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
          },
          {
              "name" : "Webpage URL",
              "value" : "/munchkintest2.html"
          }
      ]
  }
```

**範例SOAP欄位： id**

```xml
  <activityRecord>
    <id>1030680</id>
    <activityDateTime>2013-07-09T16:44:28-05:00</activityDateTime>
    <activityType>Visit Webpage</activityType>
    <mktgAssetName>ClickDemo</mktgAssetName>
    <activityAttributes>
      <attribute>
        <attrName>Webpage ID</attrName>
        <attrType xsi:nil="true" />
        <attrValue>2547</attrValue>
      </attribute>
      <attribute>
        <attrName>Webpage URL</attrName>
        <attrType xsi:nil="true" />
        <attrValue>/ClickDemo.html</attrValue>
      </attribute>
    </activityAttributes>
    <campaign />
    <personName xsi:nil="true" />
    <mktPersonId>1089965</mktPersonId>
    <foreignSysId xsi:nil="true" />
    <orgName xsi:nil="true" />
    <foreignSysOrgId xsi:nil="true" />
  </activityRecord>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2017-03-01_

## 2017年冬季更新

在2017年冬季版本中，我們新增了以非同步方式大量匯入自訂物件資料，以及在引導式登陸頁面中操作變數的功能。 請參閱以下的完整更新清單。

### 潛在客戶資料庫API

#### 大量匯入自訂物件

新端點可支援大量匯入自訂物件。 您可以在[這裡](/help/rest-api/custom-objects.md)找到詳細資料。

#### 活動即將變更的通知

請注意，Marketo發行下一代活動服務時，將會發生重大變更。 此變更將會影響下列端點：

SOAP

[getLeadActivity](/help/soap-api/getleadactivity.md)，[getLeadChanges](/help/soap-api/getleadchanges.md)

由這些端點傳回的記錄中包含的整數「id」欄位將不再保證唯一。 這會影響活動、資料值變更和潛在客戶刪除記錄型別。 為避免擷取這些記錄型別的整合服務中斷，id欄位應視為選用。

#### 推播權杖移除

新增透過SDK API移除推播權杖的功能。 您可在此找到詳細資料： [iOS](/help/mobile/push-notifications.md)、[Android](/help/mobile/push-notifications.md)。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2017-03-01_

## 2017年春季更新

在2017年春季版本中，我們新增了以非同步方式大量擷取銷售線索和活動物件資料，以及操控已命名帳戶清單的功能。 請參閱以下的完整更新清單。

### 大量擷取銷售機會

新端點可支援大量提取銷售機會。 使用各種選項指定記錄選取條件。 您可以在[這裡](/help/rest-api/bulk-lead-extract.md)找到詳細資料。

### 大量擷取活動

新端點可支援大量擷取活動。 使用日期範圍和活動清單指定記錄選擇條件。 您可以在[這裡](/help/rest-api/bulk-extract.md)找到詳細資料。

### 具名帳戶清單

新端點可支援具名帳戶清單上的CRUD操作。 您可以在[這裡](/help/rest-api/named-account-lists.md)找到詳細資料。

### 其他增強功能

* [取得行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignByIdUsingGET)端點現在可讓您篩選「可觸發」行銷活動。 這是透過將「isTriggerable=true」傳遞為查詢引數來達成。
* [Clone Program](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)端點現在支援包含所有資產型別（SMS訊息除外）的程式。

### 離子型

您現在可以使用Marketo Mobile MME和[Ionic](https://ionicframework.com/)應用程式架構。 您可以在[這裡](/help/mobile/ionic.md)找到詳細資料。

### 取消註冊推播通知權杖

已在SDK中新增方法，可讓您向Marketo取消註冊推播通知權杖。 當使用者從您的應用程式登出時，這非常有用。 如需使用方式，請參閱`unregisterPushDeviceToken` (iOS)或`uninitializeMarketoPush` (Android)方法[這裡](/help/mobile/push-notifications.md)。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2017-06-16_

## 使用IFTTT和Zapier為行銷人員提供的物聯網

物聯網(IoT)是連線裝置、裝置、穿戴式裝置、車輛等的網際網路連結。 內嵌式電子產品、軟體、感應器及網路連線能力，讓這些物件能夠收集資料並與雲端資訊系統交換。 這些技術的成長與趨勢是如此之快，以至於它們會在短時間內影響我們的生活方式、工作方式以及業務開展方式。 領先的行銷參與平台Marketo已準備好因應物聯網，具備擴充能力及與任何形式的通訊頻道互動。 Marketo可追蹤已超過70種與電子郵件、網頁、行動裝置、CRM等相關的活動，並支援任何第三方系統可提供的[自訂活動](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-activities/create-a-custom-activity.html)。 Marketo [自訂物件](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects.html)可讓您追蹤與業務相關的所有協力廠商量度，並允許行銷人員直接從Marketo智慧型行銷活動篩選器和觸發器運用這些量度。 為消費者實作IoT需要集中式伺服器才能與消費者裝置互動，而此伺服器會與Marketo開放平台及REST API、自訂物件、自訂活動等功能交換資料。  — 記錄[這裡](http://eto.com/)。 不容易透過部落格進行示範。 相反地，我們打算將IFTTT服務與Marketo整合，為行銷人員實作一些酷炫的IoT使用案例，例如：

* 每次潛在客戶登記參加路演時，在辦公室閃爍彩色燈光，讓您的行銷團隊歡呼
* 每次贏得交易時，自動啟動連線至連線電源插頭的鈴鐺鈴聲，讓您的銷售團隊歡呼雀躍
* 自動在LinkedIn、Facebook、Slack等社交網路上發佈行銷成功里程碑。 ...
* 自動根據下列專案啟動行銷活動：
   * 當天氣警報（風、溫、雨等）發生時
   * 當《紐約時報》等報紙發表新文章時，符合某些特定條件
   * 美國參議院或眾議院投票時
   * 國際空間站經過特定位置時
   * 等等。 ...

您可能會發現這些案例很有趣，但卻毫無用處，但它們正是為了示範一種新的概念方式，不僅可以與人進行行銷，而且可以與我們連線世界中的事物進行行銷。 本文章介紹的另一個有趣點，是如何利用開放式Web整合平台（例如Zapier）作為第三方系統與Marketo之間的「服務入口」，以管理驗證。

### IFTTT服務

IFTTT是「IF This Then That」的縮寫。 這是免費的網頁式服務，可供使用者用來建立簡單條件陳述式鏈結（稱為applet）。 Applet是由某些合作夥伴Web服務中發生的變更所觸發，因此動作會傳送給其他合作夥伴Web服務。 IFTTT由Linden Tibbets、Jesse Tane、Scott Tong和Alexander Tibbets於2011年在舊金山推出。 乍看上去，IFTTT看起來類似於[Zapier](https://zapier.com/)之類的服務，例如，它對消費者和IoT裝置（遙控器、警示、燈光、恆溫器、汽車、印表機、行動電話等等）的關注度更高。  首先，您必須從[IFTTT網站](https://ifttt.com/explore)建立IFTTT的帳戶。 您可以隨時探索所有可用的酷炫小程式，因為您肯定會有其他情境點子！

### 製作者管道

沒有管道的網頁應用程式（代表與IFTTT合作）必須使用Maker Channel。 使用Maker Channel，您可以建立可與任何提出或接收網路請求的裝置或應用程式搭配使用的Applet。 它提供下列整合：

* 接收來自第三方系統的網路請求的傳入觸發器，以觸發動作
* 在網際網路上公開存取向第三方系統發出Web要求的輸出動作

在IFTTT中，搜尋「Maker」服務並按一下它。  第一次，您需要透過按一下「連線」按鈕來啟動它。  現在Maker Channel已啟動。 您可以按一下「Maker Settings」按鈕來取得秘密金鑰：複製提供的URL並貼到瀏覽器以取得詳細資訊。

### 從市場直接觸發IFTTT動作

首先，我們著重於從Marketo觸發各種協力廠商Web服務動作。 為此，我們將使用[Marketo Webhook](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook.html)。 我們將透過IFTTT行動應用程式，從您行動電話或平板電腦上的推送訊息開始，然後實施讓Philips色相燈閃爍的IoT案例。

### Marketo Webhook

從Marketo觸發事件（充當IFTTT的「if」）很簡單。 您只需要依照此模式URL，傳送包含事件名稱和您的秘密金鑰的POST網路要求給IFTTT：

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}>`

Maker也可以透過網路請求傳遞最多3個引數。 這可以使用查詢引數來完成，

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={value1}&value2={value2}&value3={value3}>`

或使用最多包含三個值的JSON內文：

`{ "value1" : "{value1}", "value2" : "{value2}", "value3" : "{value3}" }`

在Marketo中，從管理員介面建立新的Webhook。  為您新的Webhook提供下列資訊：

**Webhook名稱：** IFTTT程式成功

**說明：**&#x200B;從Smart Campaign在IFTTT上觸發事件，以獲得方案成功

**URL：** `<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={{program.name}}&value2={{lead.Email> Address}}&value3={{lead.Full Name}}`

event_name，例如MarketoProgramSuccess

secret_key，使用您IFTT Maker Service的秘密金鑰

針對三個可用值使用靜態文字或Marketo權杖。 您可以在方案層級定義自己的權杖，並透過這些值傳遞它們，以推送互動式更強的訊息。

**要求型別：**&#x200B;張貼

**範本：**&#x200B;留空

**要求Token編碼：**&#x200B;表單/Url

**回應型別：**&#x200B;無

不需要定義回應對應。

### IFTTT Applet

在IFTTT入口網站中，選取主功能表中的「我的Applet」。  按一下[新增Applet]按鈕，然後按一下&#x200B;**+this**&#x200B;區段。  搜尋Maker服務。  建立觸發器，在Maker服務收到通知其發生事件的網路請求時引發。 使用與Marketo Webhook的URL中指定的相同事件名稱，例如「MarketoProgramSuccess」，然後按一下「建立觸發器」按鈕。  現在可以按一下&#x200B;**+該**區段來指定動作服務。  我們將以簡單的動作服務開始，任何人都可以測試此服務，而不需要投資任何IoT裝置：Notifications Service。 搜尋並選取通知服務。
選擇「傳送通知」動作，將通知傳送至您的裝置。  您可以善用您從Marketo傳送的3個值，將其新增為組成要素，以傳送有意義的通知給使用者，如以下範例一樣……然後按一下「建立動作」按鈕。 檢閱並完成IFTTT Applet。 請確定已啟用。

### 測試IFTTT Applet

如果您想要在行動裝置上收到通知，必須先下載裝置的IFTTT應用程式。  您可以在Marketo智慧行銷活動流程中使用Webhook來觸發Marketo方案成功事件。 請記住，Marketo Webhook僅能用於觸發的「智慧行銷活動」（例如，將聯絡人填寫表單新增到清單等時觸發）。  以下是您行動電話上的IFTTT通知範例。

### 讓我們使用IFTTT取得Creative

IFTTT提供Applet Actions超過300個合作夥伴，因此您的應用程式與裝置產品組合以及您的想像力是有限的……讓我們以Philips的[色相燈](https://www.philips-hue.com/en-us)為例，您可以在電子商店或線上購買該燈。 當Marketo觸發方案成功時，下列Applet會以目前指派的顏色閃爍您的其中一個光源，這可能會增強您在辦公室的行銷團隊。 我們會依照之前的步驟建立新的Applet，其中會以webhook觸發Marketo，但這次我們會從Philips Hue服務選擇動作。

讓我們選取「閃光燈」動作。 應用程式會要求Philips Hue提供您所有可用的燈光，以便您挑選要閃爍的燈光。 您需要先使用Philips Hue、Hue橋接器，以及至少一個Hue燈泡、燈條、投影機或燈來設定帳戶。  我們剛剛新增了一個新的Applet，每次潛在客戶註冊到路演或網路研討會時，都會閃爍彩色光線。 您的行銷團隊每天都會在辦公室裡進行這項設定，以歡呼雀躍。

### 透過Zapie從IFTTT執行Marketo動作

現在，我們將從IFTTT平台觸發Marketo Smart Campaign。 為此，我們將使用[Marketo REST API](/help/rest-api/rest-api.md)。 由於此API是安全且叫用任何內容前都需要OAuth2驗證，因此我們需要透過其他平台（例如Zapier）處理該驗證，因為IFTTT不允許使用Maker Channel連結API上的兩個連續呼叫。 我們選擇了[Zapier](https://zapier.com/)網頁應用程式自動化服務，因為我們已發佈，並逐步說明如何為Zapier實作自訂Marketo聯結器。 其他平台（例如[Workato](https://www.workato.com/integrations/marketo)）也可以是解決方案。

### Marketo行銷活動

使用已排程的Smart Campaign建立您的Marketo方案。 為了測試目的，您可以建立下列智慧行銷活動作為範例： **智慧列示**&#x200B;僅使用篩選器，而不使用觸發程式。 請確定您至少符合資格。 **流量**&#x200B;傳送電子郵件或任何其他型別的通知給您。 **排程**&#x200B;請確定您每次都能執行整個流程，以處理多項測試。 您可以從URL取得Smart Campaign ID。 範例： _https://{{marketo_url}}/#SC4289A1_ — 智慧行銷活動識別碼為4289。 您可以透過Marketo REST API觸發此行銷活動。 您可以使用適用於Chrome的[Postman](https://www.postman.com/)外掛程式，並傳送下列兩個連續HTTPS呼叫： **驗證步驟：**

`https://{{Your Munchkin_Account_id}}.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id={{Your_Client_Id}}&client_secret={{Your_Client_Secret}}`

從JSON回應復原存取權杖。 **行銷活動啟動步驟：**

`https://{{Your_Munchkin_Account_id}}.mktorest.com/rest/v1/campaigns/{{Campaign_Id}}/schedule.json?access_token={{access_token}}`

### 中繼Zapier自訂聯結器以啟動Marketo行銷活動

我們需要建立自訂Zapier聯結器，能透過Marketo REST API進行驗證，並啟動Smart Campaign。

* 先決條件
* 實作適用於Zapier的Marketo Connector
* 使用其他標題，例如「Marketo Campaign」
   * 執行「驗證」步驟
   * 執行「觸發器」步驟（Zapier測試所需的步驟）
   * 請依照下列說明，執行下列負責啟動Marketo行銷活動的特定「動作」步驟：


資料動作端點URL的傳送位置：

` https://{{munchkin_account_id}}.mktorest.com/rest/v1/campaigns/{{CampaignId}}/schedule.json`

將其他選用欄位保留空白。

#### 指令碼API

Zapier的指令碼功能可讓您操控應用程式API與Zapier之間交換的要求和回應。 您可以在傳送HTTP請求之前修改請求，以及在Zapier執行任何動作之前先剖析回應。 我們需要它來完成我們的自訂「工作階段驗證」驗證。 原始文章中提供詳細資訊。 複製下列程式碼與原始程式碼非常類似，我們剛才已變更動作方法：

```javascript
var Zap = {

 get_session_info: function(bundle) {

 console.log('Entering get_session_info method ...');

 var access_token,
 access_token_request_payload,
 access_token_response;


 // Assemble the meta data for our Access Token swap request
 console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
 access_token_request_payload = {
 method: 'POST',
 url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
 params: {
 'grant_type' : 'client_credentials',
 'client_id' : bundle.auth_fields.client_id,
 'client_secret' : bundle.auth_fields.client_secret
 },
 headers: {
 'Content-Type': 'application/json', // Could be anything.
 Accept: 'application/json'
 }
 };

 // Fire off the Access Token request.
 access_token_response = z.request(access_token_request_payload);

 // Extract the Access Token from returned JSON.
 access_token = JSON.parse(access_token_response.content).access_token;
 console.log('New Access_Token=' + access_token);

 // This will be mixed into bundle.auth_fields in future calls.
 //bundle.auth_fields.access_token=access_token;
 return {'access_token': access_token};
 },

 test_trigger_pre_poll: function(bundle) {

 console.log('Entering test_trigger_pre_poll method ...');

 bundle.request.params = {
 'access_token':bundle.auth_fields.access_token
 };

 return bundle.request;

 },

 test_trigger_post_poll: function(bundle) {

 console.log('Entering test_trigger_post_poll method ...');

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }

 return JSON.parse(bundle.response.content);
 },

 launch_campaign_pre_write: function(bundle) {

 bundle.request.params = {'access_token':bundle.auth_fields.access_token};
 return bundle.request;
 },

 launch_campaign_post_write: function(bundle) {

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }
 return JSON.parse(bundle.response.content);
 }

};
```

##### 新增Zap

在Zapier控制面板中，按一下「製作新的Zap」按鈕。 **觸發**

* 選取「Zapier的Webhooks」觸發應用程式
* 勾選「鉤子」
* 不需要挑選子索引鍵
* Zapier已產生自訂webhook URL，可供您傳送請求，並保持其位於某處
* 啟動下面的「呼叫Zapier Webhook的IFTTT Applet」情境，以測試webhook URL。 這可讓Zapier瞭解webhook裝載，並讓您為動作指派行銷活動ID

**動作**

* 選取先前建立的Marketo Campaign聯結器
* 選擇唯一可用的動作： **啟動促銷活動**
* 連線至您的Marketo帳戶，填寫驗證引數(Munchkin帳戶ID、使用者端ID、使用者端密碼)
* 編輯範本，並將觸發程式的促銷活動ID與「啟動促銷活動」促銷活動ID引數建立關聯
* 測試步驟，並檢查是否啟動Marketo行銷活動

### 呼叫Zapier Webhook的IFTTT Applet

我們先從容易測試的簡單案例開始。 我們挑選「日期與時間」觸發程式，每小時啟動一次Marketo Campaign。 此動作是張貼至Zapier Webhook URL並傳遞Smart Campaign ID的網路請求。 請確定Zapier Zap和IFTTT Applet皆為作用中，並測試一切如預期般運作。

### 讓我們使用IFTTT取得Creative

IFTT提供Applet Triggers與300多個合作夥伴，因此您的應用程式與裝置產品組合以及您的想像力也是有限的……讓我們以[Weather Underground](https://www.wunderground.com/)服務為例，我們將在天氣警示時用來啟動Marketo促銷活動。 下列觸發程式將在宣佈Rain條件時啟動。 接著將觸發器與Maker Webhook動作建立關聯，就像之前填入Zapier webhook引數一樣。  當然，您現在需要好雨才能再次檢查這是否真的有效。

我們希望您能樂於運用本文提供的概念。 但最重要的是，我們認為這能協助任何人將Marketo與其他協力廠商系統整合，因為本文提供的重要概念如下：

* MARKETO REST API
* Marketo Webhooks
* 如何善用開放式Web整合平台（例如Zapier）作為第三方系統與Marketo之間的「服務平台」，以管理驗證

由&#x200B;_菲利普_&#x200B;張貼於&#x200B;_2017-06-20_

## 2017年夏季更新

在2017年夏季版本中，我們將發佈一些計畫API的次要增強功能。

### 瀏覽程式

我們將透過新增選用的earliestUpdatedAt和latestUpdatedAt引數，將依日期範圍取得程式的功能新增至[取得程式](https://developer.adobe.com/marketo-apis/api/asset/#operation/browseProgramsUsingGET)端點。 您可以使用您選擇的日期時間來設定這兩個引數中的一個，以僅傳回在兩個日期時間之間已建立或更新過的程式。 這對於擷取新的和更新的行銷宣傳資料集非常有用，尤其是對於翻譯和商業智慧使用案例。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_1970-01-01_

## 使用Marketo Cloud功能延伸Google商業邏輯 — 

本文會根據以下簡單範例，提出使用Marketo Cloud Platform (GCP)擴充具有商業邏輯功能之Google的解決方案： Marketo潛在客戶記錄上的3個自訂欄位：

* **OnLinePreference**：表示潛在客戶/客戶對線上通訊的興趣的累加分數。
* **OfflinePreference**：表示潛在客戶/客戶對離線通訊的偏好的累加分數。
* **偏好設定**：由GCP運算出的欄位，如果離線分數高於線上分數，則顯示「離線」，反之則顯示「線上」

此技術為更進階的商業邏輯開啟了道路，最終可對外部Web服務發出呼喚，並在Marketo中轉換和整合成果。  

### 關於Google Cloud平台與函式

[Google Cloud Platform](https://cloud.google.com/) (GCP)是一套雲端運算服務，執行的基礎結構與Google內部用於一般使用者產品(例如Google Search和YouTube)的基礎結構相同。 除了管理工具之外，它還提供一系列模組化雲端服務，包括運算、資料儲存、資料分析、機器學習、巨量資料等等。 我們原本可以使用許多不同的GCP服務來滿足我們的需求，例如運算引擎、應用程式引擎或Kubernetes引擎，但我們選擇使用[雲端功能](https://cloud.google.com/functions) (仍在Beta中)的主要優點如下：

* 無伺服器雲端運算是可隨選旋轉邏輯以回應HTTP呼叫等事件的位置。
* 減輕伺服器維護與部署所造成的大部分煩惱。
* 具成本效益，因為您只須為每次函式呼叫支付GCP，而不須為維持伺服器正常運作付費。
* 實作簡單快速，因為您只關注應用程式邏輯。
* 自動擴充，因應極高的工作負荷。

請檢視[GCP網站](https://cloud.google.com/)，以取得此技術及其定價的詳細資訊。 一般而言，本教學課程不應產生任何重大成本，且完全符合GCP試用版的免費信用。  

### 準備Google雲端環境

您需要Google Cloud帳戶。 您可以免費試用GCP，而且信用額度足以執行本教學課程，只要按一下[GCP網站](https://cloud.google.com/)上的[免費試用]按鈕即可。 請依照Google的[HTTP教學課程](https://cloud.google.com/functions/docs/calling)中「開始之前」一節的所有步驟操作：

1. 建立Cloud Platform專案：移至管理資源頁面
1. 啟用專案的帳單： [啟用帳單](https://cloud.google.com/billing/docs/how-to/modify-project?visit_id=638816637273392093-1926929734&rd=1)
1. 啟用雲端功能API： [啟用API](https://accounts.google.com/InteractiveLogin?continue=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&followup=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&osid=1&passive=1209600&service=cloudconsole&ifkv=ASKV5Mh81NGNsqcJqhx7hst0KFnyA0MJ-2zay8ovyluBfpvDoM820nF9Wq_SKbC1m_sjQvvRNoKSuA)
1. [安裝並初始化Cloud SDK](https://cloud.google.com/sdk/docs/)
1. 更新及安裝Gcloud元件

   **Gcloud元件更新和&amp;** **Gcloud元件安裝測試版**

1. 準備您的環境以進行Node.js開發： [移至安裝指南](https://cloud.google.com/nodejs/docs/setup)

### scoreCompare雲端函式的實作

1. 建立雲端儲存貯體，將您的雲端函式檔案暫存。 您可以使用命令列來執行：

   ` gsutil mb gs://[YOUR_STAGING_BUCKET_NAME]`

   或從Google Cloud Web介面，選取您的專案並按一下「儲存」功能表：
   * 為儲存貯體提供唯一名稱
   * 選取預設儲存類別
   * 選取最適合的區域位置

1. 在本機系統上建立應用程式程式碼的目錄。
1. 使用下列JavaScript程式碼在此目錄中建立「index.js」檔案：程式碼非常容易理解。 它會以JSON剖析HTTP要求內文中的兩個輸入引數，並以JSON進行處理及編碼HTTP回應。

```javascript
 /\*\*
     \* HTTP scoreCompare Cloud Function.
     \*
     \* @param {Object} req Cloud Function request context.
     \* @param {Object} res Cloud Function response context.
     \*/
    exports.scoreCompare = function scoreCompare (req, res) {
     var onlineScore=parseInt(req.body.onlineScore);
     var offlineScore=parseInt(req.body.offlineScore);
     console.log('/scoreCompare: got values onlineScore =' + onlineScore + ', offlineScore =' + offlineScore);
     var result;
     if (onlineScore>offlineScore) {result = 'online';} else {result = 'offline';}
     console.log('/scoreCompare: and result is ' + result);
     res.status(200).json({output: result}).end();
    };
```

部署scoreCompare函式與HTTP觸發程式。 從您的目錄執行下列命令：

**gcloud測試版函式部署[FUNCTION] —stage-bucket [YOUR_STAGING_BUCKET_NAME] —trigger-http**

其中[YOUR_STAGING_BUCKET_NAME]是您的中繼雲端儲存貯體的名稱。 在我們的範例中：

**gcloud測試版函式部署scoreCompare —stage-bucket mktostorage —trigger-http**

1. 記下主控台輸出的雲端函式URL (httpsTrigger URL)，看起來像這樣： `https://[YOUR_REGION]-[YOUR_PROJECT_ID].cloudfunctions.net/[FUNCTION]`，其中

   * [YOUR_REGION]是部署函式的區域。 當您的函式完成部署時，即可在您的終端機中看到它。
   * [YOUR_PROJECT_ID]是您的Cloud專案識別碼。 當您的函式完成部署時，即可在您的終端機中看到它。
   * [FUNCTION]是您的函式名稱。

   在我們的範例中： [**https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare**](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
1. 使用[Postman](https://www.postman.com/)之類的工具測試您的功能：
   * HTTP動詞： POST
   * URL： [https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
   * 標頭： content-type = application/json
   * 內文： {&quot;onlineScore&quot;:110，&quot;offlineScore&quot;:200}輸出應該會產生： {&quot;output&quot;： &quot;offline&quot;}。

### 從Marketo的Webhook呼叫雲端函式

下列三個自訂欄位必須在Marketo中的潛在客戶記錄上建立：

* **OnlinePreference**：整數
* **OfflinePreference**：整數
* **喜好設定**：字串

使用您的「scoreCompare」雲端函式URL和自訂欄位的Token，從Marketo管理介面建立以下webhook。 使用Marketo觸發的智慧型行銷活動測試webhook。

* **Marketo webhook只能從觸發的智慧行銷活動叫用，不能從批次智慧行銷活動叫用。**
* **如果您未使用雲端功能，請將其刪除或刪除整個專案，以避免您的Google Cloud Platform帳戶產生費用。**

我們希望本教學課程值得您花時間，並能讓您思考更進階的情況，包括複雜的處理與第三方服務。 善用Google的機器學習服務Google Cloud AI就是一個很好的範例。 例如，您可以從Marketo表單中剖析一些任意文字，並要求Google自然語言API揭露文字的結構和意義，然後在Marketo中將此分析儲存回；只需開啟閘門即可檢視想法。

由&#x200B;_菲利普_&#x200B;張貼於&#x200B;_2017-11-21_

## 2017年秋季更新

在2017年秋季版本中，我們將發佈Asset API的幾項增強功能。 請參閱以下的完整更新清單。

### 依日期範圍瀏覽程式

我們已將依日期範圍取得方案的功能新增至[取得方案](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)端點。 這是使用`earliestUpdatedAt`和`latestUpdatedAt`引數完成的。 您可以使用您選擇的日期時間來設定這兩個引數中的一個，以僅傳回在兩個日期時間之間已建立或更新過的程式。

### 預覽電子郵件

您現在會使用[取得電子郵件完整內容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailFullContentUsingGET)端點預覽電子郵件，該端點會傳回電子郵件序列化HTML版本。 所有Token、代碼片段、動態內容及內嵌元件均已完整轉譯。 可傳遞選用的&#x200B;**leadId**&#x200B;引數來模擬指定的銷售機會。

### 取代電子郵件2.0的HTML

我們已新增[更新電子郵件完整內容](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailFullContentUsingPOST)端點，好讓您取代HTML電子郵件內容的區塊。 如果您使用HTML Email 2.0編輯器編輯Marketo電子郵件的Marketo程式碼，則電子郵件與其範本之間的關係會中斷，如需有關[此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html)的詳細資訊。 使用此端點，您可以程式設計方式更新關係已中斷之電子郵件的HTML內容。 此外，我們已修改所有其他與電子郵件生命週期相關的端點，以與關係已中斷的電子郵件相容：

* 核准電子郵件草稿
* 取消核准電子郵件
* 刪除電子郵件
* 捨棄電子郵件草稿
* 複製電子郵件
* 更新電子郵件中繼資料

### 其他增強功能

* 日期範圍篩選器的最大時間範圍已增加到31天。 這與[大量潛在客戶擷取篩選器](/help/rest-api/bulk-lead-extract.md) （createdAd或updatedAt）和[大量活動擷取篩選器](/help/rest-api/bulk-activity-extract.md) (createdAt)有關。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2017-12-15_

## 測試 — 社群上的外部影片連結

[電子郵件傳遞能力電源套件教學課程影片](https://nation.marketo.com:443/t5/product-space-archive-videos/email-deliverability-power-pack-tutorial-video/m-p/283550)  

由&#x200B;_David_&#x200B;張貼於&#x200B;_1970-01-01_

## 2018年冬季更新

在2018年冬季版本中，我們將發佈一些API的增強功能。 請參閱以下的完整更新清單。

### 啟用/停用觸發程式行銷活動

我們已新增啟用和停用觸發式行銷活動的功能，這可以簡化自動化您的方案範本程式。 這是透過呼叫兩個新新增的端點來達成： [啟用Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#operation/activateSmartCampaignUsingPOST)、[停用Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#operation/deactivateSmartCampaignUsingPOST)。 如需詳細資訊，請參閱[促銷活動](/help/rest-api/assets.md)檔案中的「觸發器」一節。

### 依名稱取得計畫

新增兩個引數至[依名稱取得方案](https://developer.adobe.com/marketo-apis/api/asset/#operation/getProgramByNameUsingGET)端點，以方便擷取方案成本和方案標籤。 請參閱&#x200B;**程式**&#x200B;檔案中的&#x200B;**includeCosts**&#x200B;和[includeTags](/help/rest-api/assets.md)引數以取得詳細資料。

### 其他增強功能

大量擷取API現在提供「工作區感知」。 當您為[自訂服務](/help/rest-api/custom-services.md)建立僅限API的使用者時，您必須選取一或多個工作區具有API存取許可權的使用者角色。 先前已授與自訂服務存取所有工作區的許可權。 現在，自訂服務僅被授予在僅限API使用者建立期間所選取工作區的存取權。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2018-03-02_

## 2018年春季更新

在2018年春季版本中，我們將發佈新的REST API和網路追蹤隱私權增強功能。 請參閱以下的完整更新清單。

REST API

### 靜態清單CRUD

允許使用者遠端建立、讀取、更新及刪除靜態清單記錄。 可透過REST API管理靜態清單的整個生命週期，包括填入及維護成員資格。 您可以在[這裡](/help/rest-api/static-lists.md)找到詳細資料。

### 自訂活動中繼資料

允許使用者遠端建立自訂活動記錄。 透過REST API啟用自訂活動的整個生命週期管理，包括型別和型別屬性的操作。 您可以在[這裡](/help/rest-api/activities.md)找到詳細資料。

### 智慧清單中繼資料

允許使用者從遠端讀取、複製和刪除智慧清單記錄。 透過REST API啟用智慧列示管理。 您可以在[這裡](/help/rest-api/smart-lists.md)找到詳細資料。

### 網路追蹤隱私權

Munchkin JavaScript網路追蹤程式碼已增強，包含下列與隱私權相關的增強功能：

* 選擇退出 — 允許訪客永久選擇退出網頁追蹤的功能。 您可以在[這裡](/help/javascript-api/lead-tracking.md)找到詳細資料。
* IP位址匿名化 — 提供匿名化網頁訪客IP位址的功能，以遵守當地及國際隱私權法規。 請參閱&#x200B;**anonymizeIP**&#x200B;設定引數[這裡](/help/javascript-api/configuration.md)。

### 其他增強功能

* 現在當指定非根父項且maxDepth=1時，[Get Folders](https://developer.adobe.com/marketo-apis/api/asset/#operation/getFolderUsingGET)端點會傳回所有資料夾。
* 依ID[的](https://developer.adobe.com/marketo-apis/api/asset/#operation/getLandingPageByIdUsingGET)取得登陸頁面端點現在會在所有情況下(http://或https://)傳回使用通訊協定的URL屬性。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2018-06-29_

## 2018年夏季更新

2018年夏季版本主要是維護版本，包含次要增強功能和缺陷解決方案。 請參閱以下的完整更新清單。

### REST API

* 新增原本不必要省略的電子郵件處置欄位支援。 這些欄位現在可以視情況透過REST讀取和寫入。
   * 已加入封鎖清單
   * 行銷活動已暫停
   * 電子郵件已暫停
   * 相對急迫性
* 按篩選型別[的](https://developer.adobe.com/marketo-apis/api/lead-database-endpoint-reference/#/Leads/getLeadsByFilterUsingGET)取得銷售機會端點現在支援leadPartionId作為filterType。

### 瑕疵解決方案

**REST端點**

**說明**

[核准方案](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveProgramUsingPOST)

如果您在建立方案時將「封鎖非操作電子郵件」設為false，則對核准方案的呼叫會重設為true。

[大量擷取](/help/rest-api/bulk-extract.md)

擷取輸出檔案中的某些Unicode字元已損毀。

[復製程式](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

* 如果您複製電子郵件程式，則無論初始設定為何，SmartList篩選邏輯在產生的程式中都會重設為「全部」。
* 如果您嘗試複製包含靜態清單（已刪除）的程式，則會收到709「下列資產不受支援:List」錯誤。
* 如果您嘗試跨工作區復製程式，則會收到「無法復製程式」錯誤611。

[依Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getStaticListByIdUsingGET)取得靜態清單

如果自訂服務具有唯讀資產角色許可權，您將會收到603「存取遭拒」錯誤。

[將銷售機會推送至Marketo](https://developer.adobe.com/marketo-apis/api/mapi/#operation/pushToMarketoUsingPOST)

如果在&#x200B;**輸入**&#x200B;陣列銷售機會物件中指定了&#x200B;**Cookie**&#x200B;屬性，之前的匿名活動不會與新建立的銷售機會產生關聯。

[排程行銷活動](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)

若您指定了&#x200B;**runAt**&#x200B;未來的日期，則促銷活動不會執行，且會傳回&#x200B;**success=true**。 現在，如果&#x200B;**runAt**&#x200B;日期超過未來2年，則會傳回錯誤[1042](/help/rest-api/error-codes.md)。

[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST)

如果您指定`action=createDuplicate`和`externalCompanyId`引數（將新的銷售機會與現有的公司相關聯），則銷售機會與空的公司相關聯（而不是指定的公司）。

#### 其他

* 已從所有端點回應中移除下列資料變更值： mktoClientReqId。 這僅供內部使用。
* 新增錯誤查詢功能。 在搜尋方塊中輸入REST API錯誤碼，然後從下面的自動完成清單中選取，以跳至錯誤說明。
* 新增[端點參考](/help/rest-api/endpoint-reference.md)頁面。 這是一份包含所有REST API端點的清單，可從單一位置排序。 您也可以使用此頁面來產生應用程式所需的最低許可權集。 這在建立自訂服務時相當實用。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2018-10-12_

## 2018年秋季更新

2019年秋季版本主要是維護版本，包含次要增強功能和缺陷解決方案。 請參閱以下的完整更新清單。

### 增強功能

* 已新增[資產API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-cc)的[電子郵件CC欄位](/help/rest-api/assets.md)支援。 副本欄位設定會在核准/復製作業期間如預期般傳播（電子郵件或電子郵件範本草稿核准、電子郵件或方案複製）。 現在，所有與電子郵件相關的端點都會傳回&#x200B;**ccFields**&#x200B;屬性中的CC欄位值。 向下捲動下列回應以檢視範例。 此變更會影響下列端點： [依ID取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByIdUsingGET)、[依名稱取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByNameUsingGET)、[取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailUsingGET)、[核准電子郵件草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST)、[核准電子郵件範本草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST_1)、[複製電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)、[復製程式。](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

```json
{
    "success": true,
    "errors": [],
    "requestId": "cc96#166e836348b",
    "warnings": [],
    "result": [
        {
            "id": 2061,
            "name": "Test Email",
            "description": "This is a test",
            "createdAt": "2018-11-06T05:27:10Z+0000",
            "updatedAt": "2018-11-06T08:33:16Z+0000",
            "url": "<https://app-sjqe.marketo.com/#EM2061A1LA1>",
            "subject": {
                "type": "Text",
                "value": "This is a test with CC Fields"
            },
            "fromName": {
                "type": "Text",
                "value": "Tommy Tester"
            },
            "fromEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "replyEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "folder": {
                "type": "Program",
                "value": 7494,
                "folderName": "Initiative"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "approved",
            "template": 1218,
            "workspace": "Default",
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
                {
                    "attributeId": "855",
                    "objectName": "lead",
                    "displayName": "emailcc",
                    "apiName": "emailcc"
                },
                {
                    "attributeId": "857",
                    "objectName": "lead",
                    "displayName": "leadDetails",
                    "apiName": "leadDetails"
                },
                {
                    "attributeId": "859",
                    "objectName": "company",
                    "displayName": "headquarters",
                    "apiName": "headquarters"
                }
            ]
        }
    ]
}
```

### 瑕疵解決方案

* 已調整[資產API](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)的[多個品牌化網域](/help/rest-api/assets.md)支援。 以前，在核准電子郵件草稿、複製電子郵件或複製計畫時，不會傳播多個品牌化網域設定。 此錯誤已修正。 此變更會影響下列端點： [核准電子郵件草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST)、[複製電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)、[復製程式。](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)
* 已新增[apiOnly](/help/javascript-api/configuration.md)組態設定。 依預設，包含Munchkin標籤的網頁會在網頁載入瀏覽器時引發「造訪網頁」事件。 在某些情況下，這是不需要的。 例如，需要完全控制此事件引發時間的單頁網頁應用程式。 為了支援此使用案例，我們已新增新的&#x200B;**apiOnly**&#x200B;組態設定。 設為true時，Munchkin標籤不會在頁面載入期間產生「造訪網頁」活動。
* 已新增[domainSelectorV2](/help/javascript-api/configuration.md)組態設定。 依預設，Munchkin標籤無法正確處理在含有兩個字母[國家代碼最上層網域](https://en.wikipedia.org/wiki/Country_code_top-level_domain)的網站上託管的網頁（範例： .io、.co、.ly）。 這會導致Munchkin Cookie網域屬性設定不正確。 為了取得更好的開箱即用體驗，我們新增了新的&#x200B;**domainSelectorV2**&#x200B;組態設定。 若設為true，系統會使用改良的演演算法來自動設定Munchkin Cookie網域屬性。
* 已調整[選擇退出](/help/javascript-api/lead-tracking.md) Cookie網域。 在某些情況下，Munchkin選擇退出Cookie (mkto_opt_out)的網域屬性設定不正確。 Munchkin選擇退出Cookie現在會使用與Munchkin Cookie (_mkto_trk)相同的邏輯來判斷網域Cookie屬性，包括遵守&#x200B;**domainLevel**&#x200B;組態設定。
* Android應用程式開發人員現在可以直接將Google的[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM)與此SDK搭配使用。 您可以在[這裡](/help/mobile/installation.md)找到詳細資料。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2018-12-07_

## 2019年春季更新

2019年春季版主要是維護版本，包含次要增強功能和缺陷解決方案。 請參閱以下的完整更新清單。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2019-03-19_

## 2019年6月更新

2019年6月版本主要是維護版本，包含次要增強功能和缺陷解決方案。 請參閱以下的完整更新清單。

### REST API

1. 已對大量匯出狀態端點新增總和檢查碼。 您可以將總和檢查碼與擷取檔案的雜湊進行比較，以驗證擷取檔案的完整性。 總和檢查碼是匯出檔案的SHA-256雜湊，會在匯出作業完成時儲存在fileCheckSum屬性中。

下列端點傳回總和檢查碼： [取得匯出潛在客戶作業狀態](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)，[取得匯出潛在客戶作業](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET)，[取得匯出活動作業狀態](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)，[取得匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportActivitiesUsingGET)

#### 瑕疵解決方案

1. 修正將十進位數字匯入整數欄位時，[大量自訂物件匯入](/help/rest-api/bulk-custom-object-import.md)的問題。 在修正之前，透過指定整數部分並捨棄小數部分，將小數轉換為整數（例如，5.432轉換為5）。 現在，包含資料不符的每一列都會產生「欄位Source ID中的資料型別無效」錯誤。
1. 修正使用[Clone Program](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)端點建立的電子郵件程式在某些情況下未遵循通訊限制設定的問題。
1. 修正[核准登陸頁面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)端點會傳回611的問題。 登入頁面包含電子郵件取消訂閱表單時出現「系統錯誤」。
1. 修正[核准登陸頁面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)端點會傳回611的問題。 使用[複製登陸頁面](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneLandingPageUsingPOST)端點複製登陸頁面時出現「系統錯誤」。

#### 淘汰

1. 作為[電子郵件編輯器1.0淘汰](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)的一部分，1.0電子郵件Assets將在2019年底成為唯讀。 所有1.0電子郵件Assets都必須轉換為2.0，因為describeDiscard電子郵件草稿，位於[此處](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)。 為協助開發人員準備好因應措施，我們已在所有嘗試修改1.0電子郵件Assets的電子郵件相關端點新增警告。 以下是包含警告的範例回應：

`{` `"success": true,` `"errors": [],` `"requestId": "15c57#16b338d6e75",` `"warnings": [` `"This is a v1 email asset. API support for modifying v1 emails is being dropped, and this operation will not work on v1 emails in the future. To avoid service interruptions, upgrade this and related assets by editing them in the User Interface."` `],` `"result": [` `"{\"service\":\"sendTestEmail\",\"result\":true}"` `]` `}`

下列電子郵件相關端點會傳回警告：

* 更新電子郵件完整內容
* 更新電子郵件內容
* 傳送範例電子郵件
* 取消核准電子郵件
* 復製程式
* 複製電子郵件
* 核准電子郵件草稿
* 更新電子郵件動態內容區段
* 更新電子郵件中繼資料
* 核准計畫

電子郵件指令碼(Apache Velocity)


#### 淘汰

1. 基於安全考量，Velocity指令碼功能的子集已停用。 這包括下列方法和變數： getClass()、$class、$context、$text。 在[這裡](https://nation.marketo.com:443/t5/knowledgebase/unsupported-velocity-tools-disabled-in-june-2019-release/ta-p/251177)可找到更多資訊。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2019-06-14_

## 2019年8月更新

在2019年8月，我們將發行新的REST API、增強現有API並解決缺陷。 請參閱以下的完整更新清單。

### 增強功能

1. 增強的Smart Campaign生命週期功能。 新增端點以允許您在Smart Campaigns上執行下列操作：依名稱取得、建立、更新、複製和刪除。 您可以在[這裡](/help/rest-api/smart-campaigns.md)找到完整的資訊。
1. 增強智慧清單端點以改進查詢功能。
   1. 當您傳遞[includeRules](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByIdUsingGET)布林值引數時，**依ID取得智慧清單**&#x200B;端點現在會傳回智慧清單規則說明（觸發器和篩選器）。
   1. [取得智慧列示](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListsUsingGET)端點現在可讓您在傳遞&#x200B;**earliestUpdatedAt**&#x200B;和&#x200B;**latestUpdatedAt**&#x200B;日期時間引數時，依日期範圍篩選結果。 此外，此端點現在會傳回屬於行銷活動和電子郵件計畫成員的智慧清單。
1. 新增擷取智慧列示定義的端點。
   1. 依Smart Campaign Id[取得](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListBySmartCampaignIdUsingGET)智慧清單端點會傳回指定智慧行銷活動ID的智慧清單記錄。
   1. 依程式ID[取得](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByProgramIdUsingGET)智慧清單端點會傳回指定程式ID的智慧清單記錄。
1. 增強[更新電子郵件內容](https://developer.adobe.com/marketo-apis/api/asset/#operation/updateEmailContentUsingPOST)端點，允許更新範本中損毀之電子郵件的電子郵件標題欄位（主旨、名稱、電子郵件、回覆）。 [此處](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html)說明從範本中斷。

### 瑕疵解決方案

1. 修正在已核准的登陸頁面上呼叫[刪除登陸頁面](https://developer.adobe.com/marketo-apis/api/asset/#operation/deleteLandingPageByIdUsingPOST)會刪除登陸頁面的問題。 現在它會正確傳回「709，無法刪除已核准的登陸頁面」錯誤。 [LM-127271]
1. 修正[傳送範例電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/sendSampleEmailUsingPOST)端點會傳回611的問題。 電子郵件從範本中斷時出現「系統錯誤」。 [LM-127288]
1. 修正[刪除程式](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)端點的問題，在某些情況下，它會刪除使用中的程式，而不是發出「709，無法刪除程式」。 資產在其他地方使用或不可刪除」錯誤。 [LM-125431]

### 淘汰

1. 電子郵件編輯器1.0的API支援預計在2020年1月淘汰。 請記得在那之前將您的資產轉換為2.0。 在1月之後嘗試寫入或複製Email 1.0資產時，將會導致錯誤而非警告。 在[此處](https://nation.marketo.com:443/t5/knowledgebase/email-2-0-and-email-api-faq-s/ta-p/251423)進一步瞭解電子郵件API。
1. 為符合Adobe的世界級安全性標準，自2019年12月13日起，我們將不再支援傳輸層安全性(TLS) 1.0和1.1。 與Marketo整合的系統若不符合1.2通訊協定，可能會失去對Marketo Engage服務的存取權。 若要維持您的Marketo Engage存取權，請在2019年12月13日之前確認所有使用者端系統皆符合TLS 1.2規範。 在[這裡](https://nation.marketo.com:443/t5/knowledgebase/tls-1-0-1-1-deprecation-faq/ta-p/249085)可找到更多詳細資訊。


1. 所有Smart Campaign相關內容現在位於[Smart Campaigns](/help/rest-api/smart-campaigns.md)功能表專案(在REST API > Assets底下)。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2019-08-16_

## 2020年1月更新

2020年1月，我們將發佈新的REST API，以強化現有API並解決缺陷。 請參閱以下的完整更新清單。

* 新增以程式設計方式建立自訂物件結構描述定義的功能。 這可讓您定義自訂物件一次，並視需要將其布建至多個例項。 這可讓您讓使用者有效運用沙箱和卓越中心模型。 此外，ISV也可以簡化客戶上線流程。 您需要適當的訂閱型別才能存取自訂物件中繼資料API。
* 新增大量匯入和匯出計畫成員的功能。 這組新的端點會遵循現有的Marketo REST API模式，以建立非同步的大量處理作業。 方案成員記錄可以包含方案成員自訂欄位和/或潛在客戶欄位。
* 新增「取得可用的表單方案成員欄位」端點以支援使用方案成員自訂欄位作為表單欄位。 這會傳回可以在Marketo表單中使用的所有方案成員自訂欄位的清單。
* 新增[Get Email Template Used by an](/help/rest-api/email-templates.md)端點會傳回相依於指定電子郵件範本的電子郵件資產清單。 這可讓您快速瞭解潛在電子郵件範本變更的影響，並更輕鬆地解決這些相依性。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2020-01-17_

## 如何擷取每個自訂物件

我們經常被問到如何使用Marketo的API來取得所有[自訂物件](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home) (CO)的清單。 查詢CO需要超過其名稱：還需要有各個CO的&#x200B;_先驗知識_。 取得該知識的方法可能不明顯，因為API沒有提供直接查詢它的方法。 和Marketo Engage中的許多目標一樣，智慧清單為連結至人員（銷售機會）的CO提供答案。 智慧列示對公司的運作方式不同，您最後會看到公司連結至篩選器物件型別的所有人員清單，因此您可能會發現有必要根據您的目標刪除重複公司。 每當核准新的「自訂物件」時，就會建立關聯的篩選器。 其名稱格式為&quot;**具有CO名稱**&quot;。 在下列範例中，自訂物件名稱為&quot;**會議曲目訂閱&quot;**，其篩選名稱為&quot;**具有會議曲目訂閱**&quot;。 建立智慧清單之後，您可以使用[自訂物件端點](/help/rest-api/custom-objects.md)來擷取查詢關聯CO所需的資訊。 匯出清單，確認已包含連結的欄位（ID或電子郵件地址）。 您可以使用[smartListName](/help/rest-api/bulk-lead-extract.md)、**smartListId**&#x200B;篩選或&#x200B;**從UI**&#x200B;匯出，使用[大量潛在客戶擷取API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-lists-and-static-lists/managing-people-in-smart-lists/export-people-to-excel-from-a-list-or-smart-list)篩選進行匯出。 在下一步中，您將使用每個連結欄位值來個別查詢相關聯的自訂物件。 在此範例中，自訂物件的名稱為&#x200B;**&quot;Conference Track訂閱&quot;**，其API名稱為&#x200B;**conferenceTrackSubscription_c**。 您在UI中找到「**API名稱**」的API名稱，並透過API找到「**名稱**」的API名稱。  管理員 | Marketo自訂物件[/標題]，以下是[清單自訂物件API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/listCustomObjectsUsingGET)端點傳回的片段：

```json
{
    "name": "conferenceTrackSubscription_c",
    "displayName": "Conference Track Subscription",
    "description": "Track subscription for conference attendees",
    "createdAt": "2020-01-13T19:50:06Z",
    "updatedAt": "2020-01-13T19:50:06Z",
    "idField": "marketoGUID",
    "dedupeFields": [
        "subscriptionID"
    ],
    "searchableFields": [
        [
            "subscriptionID"
        ],
        [
            "marketoGUID"
        ],
        [
            "leadID"
        ]
    ],
    "relationships": [
        {
            "field": "leadID",
            "type": "child",
            "relatedTo": {
                "name": "Lead",
                "field": "Id"
            }
        }
    ]
}
```

若要擷取與智慧清單中的人員相關聯的自訂物件(一個(1:1)或一對多(1:N))，請提出如下要求：

`GET /rest/v1/customobjects/conferenceTrackSubscription_c.json?filterType=leadID&filterValues=1000302,1000303,1000304,1000306,1000307`

在此範例中，此自訂物件透過&#x200B;**leadID**&#x200B;欄位連結至人員，所以篩選型別是&#39;&#39;**leadID**&#39;&#39;。 filter values引數是從智慧清單匯出中取得的逗號分隔ID清單。 請求可包含您可在單一請求URI中容納的篩選器值數量：最多8K個字元。 超過此長度的請求會傳回414 HTTP層級的錯誤代碼。 回應可能會傳回一個以上的區塊。 若是如此，**moreResult**&#x200B;將為&#x200B;**true**，且將包含&#x200B;**nextPageToken**。 然後您需要[逐頁瀏覽](/help/rest-api/paging-tokens.md)結果，直到&#x200B;**moreResult**&#x200B;為&#x200B;**false**&#x200B;為止。 以下是上述API請求的部分結果：

```json
"result": [
    {
        "seq": 0,
        "marketoGUID": "d6b3ed3d-4eb8-4066-a9cd-184c8d385cfe",
        "leadID": "1000302",
        "subscriptionID": "4ad59184-6bf1-4eeb-a583-d82aeee68210",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 1,
        "marketoGUID": "e5e0aba4-f27f-494d-93ed-9cb580989bf3",
        "leadID": "1000303",
        "subscriptionID": "fc5596d5-6fa2-4848-b4a2-89d96e245c59",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 2,
        "marketoGUID": "e65007cd-86b1-4c17-8d55-057c96e1788a",
        "leadID": "1000304",
        "subscriptionID": "7e54b8a0-2170-4d81-a809-4eac349508d0",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 3,
        "marketoGUID": "39d956b2-85e2-4c24-94e7-e9fa5a09d3d0",
        "leadID": "1000306",
        "subscriptionID": "644c8e5b-fc0c-4d4a-80f8-358a65ce0a68",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 4,
        "marketoGUID": "a2151350-50c8-437f-bc71-7a054bb601f0",
        "leadID": "1000307",
        "subscriptionID": "bf14218c-ae6a-42b3-a14e-f7182903cbcd",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    }
```

您現在擁有與智慧清單中的人員直接關聯的每個自訂物件的值，除了擷取這些值之外，您還可以使用&#x200B;**marketoGUID**&#x200B;來[更新](/help/rest-api/custom-objects.md)或[刪除](/help/rest-api/custom-objects.md)這些物件。 對於在多對多關係(N:N)中與人員相關聯的自訂物件，上述技術會傳回第一層物件，即連線每個人員與多個第二層CO的中間物件。

若要擷取那些第二級CO，請透過篩選連結欄位以及從第一級中介物件擷取的值，為第二級CO型別啟動一組新的查詢。 例如，上述「**會議追蹤訂閱」**&#x200B;物件可能有另一個物件層級，代表名為&#x200B;**「工作階段」**&#x200B;的工作階段，可能會由&#x200B;**subscriptionID**&#x200B;連結。 擷取連結至上述會議追蹤訂閱之工作階段的請求會如下所示：

`GET /rest/v1/customobjects/session_c.json?filterType=subscriptionID&filterValues=4ad59184-6bf1-4eeb-a583-d82aeee68210,e5e0aba4-f27f-494d-93ed-9cb580989bf3,e65007cd-86b1-4c17-8d55-057c96e1788a,39d956b2-85e2-4c24-94e7-e9fa5a09d3d0,bf14218c-ae6a-42b3-a14e-f7182903cbcd`

_註腳_ _1)**smartListName**和&#x200B;**smartListId**篩選型別不適用於部分訂閱。 如果您的訂閱無法使用，您在呼叫Create Export Lead Job端點時收到錯誤（**&quot;1035，目標訂閱&quot;**&quot;不支援的篩選器型別）。 客戶可以聯絡Marketo支援，以便在他們的訂閱中啟用此功能。_

由&#x200B;_Tony_&#x200B;張貼於&#x200B;_2020-01-14_

## 如何擷取每個人（銷售機會）

我們會收到許多有關從Marketo Engage執行個體擷取每個人（銷售機會）所需流程的查詢。 我們提供了許多有用的答案，但沒有一個比這個更完整。 我已找出使用Marketo的大量擷取API來擷取每個銷售機會所需的一些關鍵概念。 所有其他詳細資訊都可以從我建立的示範程式碼中學習。 閱讀本文章並探索示範程式碼後，您將擁有從Marketo Engage例項擷取每個銷售機會所需瞭解的所有資訊。

### 概觀

核心技術使用[大量潛在客戶擷取API](/help/rest-api/bulk-lead-extract.md)。 您可能希望能夠建立沒有篩選器的大量潛在客戶匯出作業，但您不能：**API需要篩選器**。 可用的篩選器為&#x200B;**createdAt**、**staticListName**、**staticListId、** **updatedAt**、**smartListName**&#x200B;和&#x200B;**smartListId**。 依沒有篩選器的智慧清單進行篩選似乎也很吸引人。 嘗試一下，您會發現系統足夠聰明，可以處理沒有篩選器的智慧清單：此API也需要篩選器。 由於我們需要篩選，因此要使用的可靠且標準篩選是&#x200B;**createdAt**。 此篩選器型別允許日期時間範圍最多為31天，因此我們需要執行多個工作並合併結果。 我們先從尋找目標例項中潛在客戶最舊的建立日期開始。 從儘可能最舊的日期開始，我們會建立橫跨31天減一秒（稍後更詳細）的作業。 建立每個工作後，我們會將其排入佇列並等待其完成。 然後，我們將下載產生的檔案，並使用總和檢查碼檢查其完整性。 最後，依ID重複刪除銷售機會，然後寫入唯一的銷售機會並輸出為CSV檔案。

### 尋找您最舊的銷售機會

我正在使用一些「訣竅」，取得Target執行個體中潛在客戶的最早可能日期。 沒有專用於該任務的API端點，因此我們需要一點創造力。 我所做的是查詢具有&#x200B;**maxDepth = 1**&#x200B;的所有資料夾，這會提供執行個體中所有最上層資料夾的清單。 接著，我收集&#x200B;**createdAt**&#x200B;日期、剖析這些日期，並尋找最舊的日期。 此方法可正常運作，因為某些預設的頂層資料夾是使用例項建立的，而在此之前無法建立銷售機會。

### 選取必填欄位

您需要決定需要擷取哪些欄位。 使用[Describe Lead 2端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2)尋找目標執行個體的可用欄位。 對該請求的回應將包含一個名為「欄位」的清單。 以下是範例回應的節選：

```json
  "fields": [
      {
          "name": "AccountSource",
          "displayName": "Account Source",
          "dataType": "string",
          "length": 40,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "acquisitionProgramId",
          "displayName": "Acquisition Program",
          "dataType": "reference",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Active_c",
          "displayName": "Active",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "address",
          "displayName": "Address",
          "dataType": "text",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Address_lead",
          "displayName": "Address (L)",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "annualRevenue",
          "displayName": "Annual Revenue",
          "dataType": "currency",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "anonymousIP",
          "displayName": "Anonymous IP",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      }, ...
```

此端點會傳回完整清單，包含標準和自訂欄位。 您請求的欄位越多，匯出作業完成所需的時間就越長，產生的檔案也就越大。 您通常應該只選擇所需的欄位。 沒有任何專案會阻止您要求每個可用的欄位，所以我正在降級。 建立匯出作業時所需的欄位識別碼為&#x200B;**名稱**&#x200B;值。 我會將名稱值擷取至所有欄位名稱的清單。 然後，我會在建立每個匯出作業時，使用它來請求每個可用欄位。

### 匯出工作日期範圍：各31天

每個匯出作業最多可持續31天。 我使用的示範執行個體是在2016年8月建立，因此今天需要建立40多個工作。 這是自第一個建立日期以來的天數除以31向上舍入。 API允許同時處理兩個匯出作業，以便您可以使用兩個並行執行的作業進行擷取。 大量擷取工作是與所有其他整合專案共用的資源，因此我會做得很好。 我保留其他工作可用於其他整合，並將逐一示範執行單一工作。 用於&#x200B;**createdAt**&#x200B;篩選器的日期使用[ISO 8601規格](https://www.w3.org/TR/NOTE-datetime)格式化。 時區一律以GMT (Z+0000)表示，因此時區只會以「Z」或「+00:00」表示。 2016年8月1日為&#x200B;**2016-08-01T00:00:00+00:00**，31天後為2016年9月1日，即&#x200B;**2016-09-01T00:00:00+00:00。**&#x200B;開始和結束時間皆不含，因此我要從該結束時間減去1秒： **2016-09-01T00:00:00+00:00**&#x200B;變成&#x200B;**2016-08-31T23:59:59+00:00**。 減去秒數可避免重疊時間。 由於GMT是預設值，因此您也可以關閉&#x200B;**Z**&#x200B;或&#x200B;**+00:00**。

### 重複資料刪除

雖然我已努力避免重疊時間，但我也實作重複資料刪除。 我之所以這麼做，是因為在某些邊緣案例中，時間變更（[日光節約時間](https://en.wikipedia.org/wiki/Daylight_saving_time)）會導致模稜兩可的值，因此Marketo的大量擷取API可能會傳回原本意外的重複銷售機會。 很少發生這種情形，但需要使用日期時間篩選範圍在任何整合中說明。 我移除了一秒鐘，以清楚表示時間是包含在內的。 我不希望您認為分別以&#x200B;**2016-08-01T00** 00Z **和** 2016-09-01T00 **00Z:00:的** createdAt **和:00:endAt**&#x200B;次建立工作，將不會包含在&#x200B;**2016-09-01T00:00:00Z**&#x200B;上建立的潛在客戶；威爾。

### 建立工作

第一個步驟是使用[建立匯出潛在客戶作業端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createExportLeadsUsingPOST)來建立作業。 在本示範中，建立第一個匯出工作的要求如下：

`POST /bulk/v1/leads/export/create.json`

```json
{ "filter": {"createdAt": {"startAt": "2016-08-01T00:00:00",
                           "endAt": "2016-09-01T00:00:00"}},
"fields":["AccountSource","acquisitionProgramId","Active_c","address","Address_lead","annualRevenue","anonymousIP","BillingAddress","billingCity","billingCountry","BillingGeocodeAccuracy","BillingLatitude","BillingLongitude","billingPostalCode","billingState","billingStreet","blackListed","blackListedCause","city","CleanStatus","CleanStatus_account","company","CompanyDunsNumber","contactCompany","cookies","country","createdAt","customfield","DandbCompanyId","DandbCompanyId_account","dateOfBirth","dddS","department","doNotCall","doNotCallReason","dS","dueDate","DunsNumber","email","EmailBouncedDate","EmailBouncedReason","emailInvalid","emailInvalidCause","emailSuspended","emailSuspendedAt","emailSuspendedCause","externalCompanyId","externalSalesPersonId","facebookDisplayName","facebookId","facebookPhotoURL","facebookProfileURL","facebookReach","facebookReferredEnrollments","facebookReferredVisits","fax","firstName","gender","GeocodeAccuracy","holll","id","industry","inferredCity","inferredCompany","inferredCountry","inferredMetropolitanArea","inferredPhoneAreaCode","inferredPostalCode","inferredStateRegion","interested","interestedIn","isAnonymous","IsEmailBounced","isLead","iSTRUE","Jigsaw","JigsawCompanyId_account","JigsawContactId_lead","Jigsaw_account","Languages_c","lastName","LastReferencedDate","LastReferencedDate_account","lastReferredEnrollment","lastReferredVisit","LastViewedDate","LastViewedDate_account","Latitude","leadPartitionId","leadPerson","leadRevenueCycleModelId","leadRevenueStageId","leadRole","leadScore","leadSource","leadStatus","linkedInDisplayName","linkedInId","linkedInPhotoURL","linkedInProfileURL","linkedInReach","linkedInReferredEnrollments","linkedInReferredVisits","links","Longitude","MailingAddress","MailingGeocodeAccuracy","MailingLatitude","MailingLongitude","mainPhone","marketingSuspended","marketingSuspendedCause","middleName","mktoAcquisitionDate","mktocomment1","mktocomments2","mktoCompanyNotes","mktoDoNotCallCause","mktoIsCustomer","mktoIsPartner","mktoName","mktoPersonNotes","mktosync","mktotest1","mobile","mobilePhone","NaicsCode","NaicsDesc","newcustom","numberOfEmployees","originalReferrer","originalSearchEngine","originalSearchPhrase","originalSourceInfo","originalSourceType","OtherAddress","OtherGeocodeAccuracy","OtherLatitude","OtherLongitude","personPrimaryLeadInterest","personTimeZone","personType","phone","PhotoUrl","PhotoUrl_account","postalCode","priority","ProductInterest_c","rating","referal","registrationSourceInfo","registrationSourceType","relativeScore","relativeUrgency","requiredNumberofCylinder","salutation","sfdcAccountId","sfdcContactId","sfdcId","sfdcLeadId","sfdcLeadOwnerId","sfdcType","ShippingAddress","ShippingGeocodeAccuracy","ShippingLatitude","ShippingLongitude","sicCode","SicDesc","site","state","surveyAnswers","syndicationId","testBooleanField","testscore","title","totalReferredEnrollments","totalReferredVisits","Tradestyle","twitterDisplayName","twitterId","twitterPhotoURL","twitterProfileURL","twitterReach","twitterReferredEnrollments","twitterReferredVisits","uNSUBSCIBE","unsubscribed","unsubscribedReason","updatedAt","urgency","url","website","YearStarted"]}
```

回應如下所示：

```json
{
  "requestId": "6902#16fb52118bf",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Created",
          "createdAt": "2020-01-17T20:10:43Z"
      }
  ],
  "success": true
}
```

### 將工作排入佇列

工作現在已建立，但只是坐在那裡什麼也不做。 若要執行工作，我們需要使用[exportId](https://developer.adobe.com/marketo-apis/api/mapi/#operation/enqueueExportLeadsUsingPOST)值呼叫&#x200B;**排入佇列端點**，以建置要求的URI。 看起來像這樣：

`POST /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/enqueue.json`

此POST沒有內文，我們在這裡只是使用POST HTTP動詞。 該請求將產生如下的回應：

```json
{
  "requestId": "1836a#16fb5238a48",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Queued",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z"
      }
  ],
  "success": true
}
```

如我先前所述，一次可以執行的工作數量有限。 同時排入佇列的作業數也有限制： 10。 我們需要40個以上的工作崗位，才能避免一次建立所有的工作崗位。 其他整合功能也可以執行工作，因此我們需要考慮所有位置已滿的可能性。 嘗試將新工作排入佇列時，若已有10個已排入佇列的工作，將會產生[1029](/help/rest-api/error-codes.md)錯誤。 當您收到&#x200B;**1029**&#x200B;時，請使用指數輪詢，直到工作可以排入佇列為止。 我等待1分鐘，並在每次收到請求之間最多4分鐘的&#x200B;**1029**&#x200B;錯誤碼時加倍該值，但不會超過該時間。 此技巧稱為[截斷的二進位指數倒推](https://devopedia.org/binary-exponential-backoff)，是修復錯誤和狀態檢查的最佳實務。

### 等待工作完成

每個工作都需要一些時間才能執行，因此我們將呼叫[狀態端點](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)以監視其進度。 我們再次將&#x200B;**exportId**&#x200B;加入要求URI中，如下所示：

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/status.json`

在工作完成之前，您會收到如下的回應：

```json
{
    "requestId": "153cb#16fb525435d",
    "result": [
        {
            "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-17T20:10:43Z",
            "queuedAt": "2020-01-17T20:13:23Z",
            "startedAt": "2020-01-17T20:13:49Z"
        }
    ],
    "success": true
}
```

當工作狀態為「已排入佇列」時，我執行相同的指數回退（1分鐘到4分鐘）。 狀態並非即時；每分鐘會更新一次，而且對於更快速的輪詢幾乎沒有好處。 當工作狀態變更為「處理中」時，我重設輪詢。 我們正在等待「已完成」狀態，如下所示：

```json
{
  "requestId": "10ad9#16fb5268f9b",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Completed",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z",
          "startedAt": "2020-01-17T20:13:49Z",
          "finishedAt": "2020-01-17T20:15:28Z",
          "numberOfRecords": 59,
          "fileSize": 74436,
          "fileChecksum": "sha256:de553362e7ffad6556ae9ea749655c35010c7f0e944fc5a85782183130dca18d"
      }
  ],
  "success": true
}
```

當要求未傳回潛在客戶時，**numberOfRecords**&#x200B;值為零。 我檢查此值，並在必要時略過後續步驟。 當傳回潛在客戶時，我解壓縮&#x200B;**fileChecksum**&#x200B;值。 我們使用它來檢查檔案下載時的完整性。

### 取得您的銷售機會

如果&#x200B;**numberOfRecords**&#x200B;大於零，請使用[取得匯出潛在客戶檔案](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET)下載匯出的檔案，要求如下：

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/file.json`

檢查是否傳輸了未發生錯誤的檔案：計算檔案的總和檢查碼，並與先前儲存的&#x200B;**fileChecksum**&#x200B;進行比較。 使用[SHA-2](https://en.wikipedia.org/wiki/SHA-2)並特別使用SHA256雜湊函式來計算總和檢查碼。 如果計算的檢查值不符，檔案傳輸中會發生錯誤，您可以再次嘗試傳輸，或中止並手動復原。

### 彙總資料

從第一個銷售線索到今天，每隔31天循環一次，您就擁有一套完整的產品。 每個範圍也會有一個檔案。 要建立包含每個潛在客戶的單一彙總檔案，最簡單的方法是在移除第一個檔案以外的其他所有檔案標題列後，串連這些檔案。 如果您這麼做，別忘了期待並規劃資料處理管道稍後可能出現的重複作業。 在示範中，我會在下載檔案時處理這些檔案。 在將每一列資料新增到輸出檔案之前，我會檢查是否已寫入列的潛在客戶ID，以刪除重複資料。

我已開發一些主控於[這裡](https://github.com/Marketo/REST-Sample-Code/tree/master/python/LeadDatabase/Leads)的展示程式碼，希望填入此程式的詳細資料，並可作為您自己的開發範本。 示範程式碼旨在作為學習工具，因此生產系統需要改善穩健性。 **此程式碼是以MIT授權依原樣**&#x200B;提供，但可能適合在人力監督下一次性使用。 現在沒有任何事情能阻止您！ 當您依照此程式進行時，將會使用Marketo的大量擷取API來擷取每個銷售機會，並可能使用目標Marketo Engage例項的每個欄位。 若要進一步擴充資料，請使用技巧取得每個潛在客戶的活動。

由&#x200B;_Tony_&#x200B;張貼於&#x200B;_2020-03-05_

## 2020年2月更新

2020年2月，我們將發佈新的REST API。 請參閱以下的完整更新清單。

### 公告

* 2020年9月後，[Asset API](/help/rest-api/assets.md)端點將不再接受&#x200B;**_method**&#x200B;查詢引數。 這可用來在POST主體中傳遞查詢引數，以略過URI長度限制。 為因應需要此引數的請求，資產API的URI限制將從6KiB增加到65KiB。
* 關於我們在ITP上的立場，請參閱此Marketo社群貼文： [瀏覽器Cookie更新： Marketo/Munchkin如何受影響](https://nation.marketo.com:443/t5/knowledgebase/browser-cookie-updates-how-marketo-munchkin-is-affected/ta-p/251524)
* 「進度變更狀態」活動已變更。 已新增「方案成員ID」屬性，以支援即將推出的「方案成員自訂欄位」功能。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2020-02-26_

## 淘汰Munchkin關聯銷售機會方法

下次發行Munchkin JavaScript Client 159版後，我們將開始淘汰Munchkin [Associate Lead方法](/help/javascript-api/api-reference.md)。 從此版本開始，在叫用方法時，將在瀏覽器主控台中發出警告，指示將在未來版本中移除方法。 移除方法後，嘗試使用方法會導致失敗。 我們最近確定使用此方法的Marketo客戶將會個別收到使用通知。 如需有關Munchkin 159版本的詳細資訊，[請參閱此行銷國家貼文](https://nation.marketo.com:443/t5/product-documents/munchkin-javascript-version-159-amp-associate-lead-deprecation/ta-p/299687)。

### 常見問題集

我如何知道自己是否受到影響？

Adobe會通知我們觀察到其訂閱使用此方法的客戶，並會在淘汰期間多次這麼做。

何時會移除方法？

此方法將在Munchkin JS v161中移除，並排程在2021年10月Marketo版本正式發行。

為何要移除此方法？

自此方法推出以來，已實施並發行更有效率的方式，讓使用者符合相同使用案例。 為了改善服務的效能和健康狀態，有時候您必須移除無法執行可接受標準的功能。

我應該使用什麼來取代此方法？

Munchkin關聯銷售機會有兩個主要使用案例：提交個人資料，以及將瀏覽器網路追蹤Cookie關聯至Marketo中的相對應個人記錄。 自從推出Munchkin Associate Lead以來，已實施更強大的方式來執行資料提交和Cookie關聯。

#### 伺服器端提交

如果您不需要瀏覽器端提交，REST API會提供[個人資料提交](/help/rest-api/leads.md)的多種方法，以及將Cookie與個人記錄建立關聯的[專門建置方法](/help/rest-api/leads.md)。

Munchkin 159版何時推出？

159版自2020年10月Marketo發行以來已全面推出。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2020-05-06_

## 在背景提交Marketo表單

當您的組織擁有許多不同的平台來託管Web內容和客戶資料時，通常需要從表單中並行提交資料，以便在不同的平台上收集產生的資料。 有多種策略可以達成此目的，但最佳策略通常最簡單：使用Forms 2 API提交隱藏的Marketo表單。 這適用於任何新的Marketo表單，但理想情況下，您應該為此建立空白表單，該表單沒有欄位。 這將確保表單不會載入任何超出必要的資料，因為我們不需要呈現任何內容。 現在，只要從您的表單抓取[內嵌程式碼](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)，並將其新增到所需頁面的內文，即可進行小幅修改。 您的內嵌程式碼包含如下表單元素：

`<form id="mktoForm_1068"></form>`

您會想要將&#39;style=&quot;display:none&quot;新增至元素，使其不可見，如下所示：

`<form id="mktoForm_1068" style="display:none"></form>`

一旦表單嵌入並隱藏，提交表單的程式碼就會非常簡單：

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"<test@example.com>",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

如果潛在客戶已填寫並提交可見表單，Forms以此方式提交的行為將完全相同。 由於每個實作都有不同的動作可提示，因此觸發提交作業的方式會因實作而異，但基本上任何動作都可以觸發。 重要部分是正確設定欄位和值。 請務必使用您可在匯出欄位名稱中找到的欄位SOAP API名稱，以確保正確提交值。

### 從Munchkin關聯銷售機會移轉

背景表單提交是Munchkin關聯銷售機會的建議取代方法之一。 以下範例HTML頁面透過重複使用提交給「關聯銷售機會」的相同值，示範高層次的移轉。

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
            }
          }
          var s = document.createElement('script');
          s.type = 'text/javascript';
          s.async = true;
          s.src = '//munchkin.marketo.net/munchkin-beta.js';
          s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
              initMunchkin();
            }
          };
          s.onload = initMunchkin;
          document.getElementsByTagName['head'](0).appendChild(s);
        })();
        </script>
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //pass the same set of values to associateLead
            //hashString: secret + email
            Munchkin.munchkinFunction('associateLead', values, "CHANGE ME");

            //submit the form
            form.submit();


        })
    </script>
</body>

</html>
```
   
由_Kenny_&#x200B;張貼於&#x200B;_2020-05-26_

## 2020年7月更新

2020年7月，我們將發行新的REST API、增強現有API並解決缺陷。 請參閱以下的完整更新清單。

* 新增兩個端點，可讓您查詢和刪除尚未接受邀請的使用者（即「擱置」使用者）。 [依ID](/help/rest-api/user-management.md)取得受邀的使用者端點可讓您查詢擱置的使用者。 [刪除受邀使用者](/help/rest-api/user-management.md)端點可讓您刪除擱置中的使用者。
* [邀請使用者](/help/rest-api/user-management.md)端點已更新，以接受&#x200B;**expiresAt**&#x200B;引數符合ISO 8601的日期時間字串。
* 已更新[依ID](/help/rest-api/user-management.md)取得使用者和[更新使用者屬性](/help/rest-api/user-management.md)端點，以傳回&#x200B;**lastLoginAt**&#x200B;屬性中的上次使用者登入時間。
* 修正當您嘗試建立已存在的靜態清單時，[建立靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#operation/createStaticListUsingPOST)端點會傳回錯誤「611，系統錯誤」的問題。 已變更為傳回錯誤「709，相同名稱的靜態清單已存在」。 [LM-135934]
* 修正當您嘗試建立已存在的電子郵件時，[建立電子郵件](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailUsingPOST)端點會傳回錯誤「611，系統錯誤」的問題。 已變更為傳回錯誤「709，相同名稱的電子郵件已存在」。 [LM-138648]
* 修正登陸頁面查詢端點傳回不正確&#x200B;**createdAt**&#x200B;值的問題。 端點傳回上次核准登入頁面的時間。 這類報表現在會傳回至登入頁面的建立時間。 [LM-138648]
* 修正[合併潛在客戶](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST)端點會因無效的合併操作傳回錯誤「611，系統錯誤」的問題。 合併導致重複的銷售機會，且&#x200B;**mergeinCRM**&#x200B;設定為true時發生。 已變更為傳回錯誤「712，您正在建立重複記錄。 我們建議您改用現有記錄」。 [LM-137463]

由&#x200B;_David_&#x200B;張貼於&#x200B;_2020-08-01_

## 訪客貼文 — 深入探討：自訂物件與自訂活動vs自訂欄位

**這是來自Amit Jain、Marketo Champion 2020、MarTech IT Specialist**&#x200B;的訪客貼文。作為企業級行銷自動化平台，Marketo會管理您從數個來源取得的使用者/客戶/潛在客戶資訊，並在Marketo中維持這些資訊，以提升個人化、細分和報表效能。 這些來源涵蓋從您的網站表單到清單建置，再到您的CRM資料與您的電子商務資料。 Marketo提供標準物件，例如銷售機會、公司、商機和活動等。 這些標準物件有時不足以滿足Marketo中可用擴充資料集的新興行銷需求。

例如，新增到購物車的專案、申請不同課程的學生、特定人員擁有的產品，以及同意不同訂閱等。 這些資訊對於行銷人員至關重要，可跨平台提供更個人化的內容及統一的使用者體驗，讓客戶/潛在客戶持續參與。 為了滿足這些業務需求，Marketo提供自訂方式，將此型別的資料儲存在自訂欄位、自訂活動和自訂物件中。 我觀察到人們在瞭解何時使用哪個選項時發生問題。 在這篇部落格中，**我們將詳細瞭解這三件事的實質，以及何時搭配某些範例使用哪一個。**

**自訂欄位**

Marketo中的「Lead」物件是主物件，其他所有物件都直接或間接與此物件連結。 Marketo可讓您在「銷售機會」物件、「公司」物件上建立自訂欄位，最近他們宣佈支援「方案成員」自訂欄位。 這些自訂欄位滿足您儲存特定資料型別的需求。 例如，您可能需要儲存工作函式或使用者的不同同意。 Marketo中有兩種型別的自訂欄位：

1. **從CRM欄位同步：**&#x200B;在Marketo↔︎SFDC自動同步處理中，MarketoMarketo會同步處理潛在客戶、連絡人、帳戶和機會物件上對SFDC使用者可見的所有自訂欄位。
1. **僅限Marketo的欄位：**&#x200B;您可以在Marketo中直接建立欄位。 這些欄位的資料將不會與SFDC同步。

**快速提示**

* 您有僅限Marketo的欄位且現在想要在SFDC中同步資料嗎？ 請檢視[此部落格](https://themarketingautomationblog.com/2019/12/06/field-management-merging-remapping-hiding-marketo-fields/)以瞭解如何執行此操作。
* 在[此處](https://themarketingautomationblog.com/2019/11/15/knowing-your-marketo-fields/)進一步瞭解自訂欄位。
* Marketo API目前不支援更新/建立自訂欄位。

**自訂物件**

除了標準物件之外，Marketo可讓您建立自己的自訂物件。 _您可以建立自訂物件，並與Company或Lead物件或其他自訂物件建立關聯。_&#x200B;自訂物件是補充標準Lead和Company記錄的一組自訂記錄。 自訂物件可讓您以可擴充的方式儲存其他資料，並將該資料連結至潛在客戶或公司記錄。 您可以使用任何標準（連結欄位）和自訂欄位的組合來建立自訂物件，填入這些欄位以建立自訂物件&#x200B;_記錄_，然後將這些記錄連結到潛在客戶或公司記錄。 強大且彈性的連結記錄，可讓您使用未在潛在客戶欄位和公司欄位中找到的資訊來建置這些資產，讓您的區段、智慧列示和促銷活動更為豐富。 自訂物件可以有下列其中一種關係：

* **一對一關係**：每個自訂物件都有單一Lead/Company物件記錄。
* **一對多關係**：每個自訂物件包含多個與銷售機會/公司相關的自訂物件記錄。
* **多對多關係：**&#x200B;多個自訂物件記錄可以與多個潛在客戶/公司物件連結。 例如，多個學生從課程目錄註冊了多個課程。

**快速提示**

* 在[這裡](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)瞭解更多設定自訂物件的資訊。
* 您可以使用Marketo自訂物件作為中介物件，亦即自訂物件的自訂物件。

### 為何選擇自訂物件？

自訂物件可讓您編譯和使用與公司或潛在客戶相關的唯一資料，但這不一定是有關公司或潛在客戶的靜態資訊。 雖然潛在客戶欄位與個人的潛在客戶資訊（_電子郵件地址_、_郵遞區號_&#x200B;等）、業務資訊（_職稱_、_產業_&#x200B;等）或系統導向資訊(_Marketo ID_、SFDC ID或建立日期等)有關，但自訂物件欄位是完全可自訂的。 例如，使用自訂物件來儲存如下資訊：

* 使用者的購買記錄。
* 購物車資訊。
* 無論客戶是否使用過您的限時優惠折扣代碼。
* 從調查結果、面試和活動出席中取得的補充資訊。
* 使用者的試用資訊，包括試用例項URL、開始日期、結束日期、使用者人數等。

### Marketo自訂物件限制

1. 依預設，Marketo可讓您建立10個自訂物件。 您可以透過額外訂閱費用提高上限。
1. 每個物件的預設欄位數為50，但您可以視需要請求Marketo支援其他欄位。 感謝您[Michael Florin](https://www.linkedin.com/in/michaelflorin/)在此輸入其他資訊。
1. 您可以在所有自訂物件上擁有的記錄數存在限制。 這取決於您對Marketo的訂閱。 此限制可透過額外訂閱費提高。
1. 如果使用API建立自訂物件，Marketo不允許從Marketo UI編輯CO結構描述。

**快速提示**

* Marketo API對自訂物件支援CRUD （建立、讀取、更新和刪除）作業。
* 您可以使用Velocity指令碼將此自訂物件資料用於電子郵件個人化。
* 您可以在[這裡](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportProgramMembersStatusUsingGET)找到所有與自訂物件相關的端點。


### 自訂物件術語

**自訂物件**：包含所有自訂物件記錄之群組的容器。 正式名稱為資料卡片集或自訂表格。 **自訂物件記錄**：資料記錄包含可連結至潛在客戶或公司的其他欄位資訊。 記錄可由標準潛在客戶或公司欄位，以及自訂物件記錄欄位組成。 正式名稱為資料卡或資料表格列。 **自訂物件記錄欄位**：可完全自訂的欄位，以收集唯一或暫存資訊。 這些欄位會建立並放置在自訂物件本身中。 正式名稱為資料卡欄位或資料庫表格欄位/欄。 **連結欄位**：自訂物件記錄欄位的特殊型別，用於定義自訂物件記錄與連結的潛在客戶/公司物件記錄之間的關係。 當您建立自訂物件時，您必須提供連結欄位，以將自訂物件記錄連線到正確的父記錄。

* 對於一對多或一對一的自訂結構，請使用自訂物件中的連結欄位，將其連線到個人或公司。
* 對於多對多的結構，您可以使用兩個連結欄位，由單獨建立的中介物件（也是自訂物件型別）連線。 一個連結會連線至資料庫中的人員或公司，另一個連結則連線至自訂物件。 在這種情況下，連結欄位不在自訂物件本身中。

### 自訂活動

**某人可與我們的組織互動的方式有數種。 他們可能會造訪您公司的網站、參加您的其中一個商展，或按一下您傳送之電子郵件中的連結。 這些動作是活動**，無論他們採取什麼動作，Marketo都會加以擷取，讓您的行銷和銷售團隊更能瞭解使用者的行為，以進行個人化和統一的參與。 **_自訂活動_** _可協助您追蹤與Marketo表單、電子郵件或登入頁面無關的活動_。 例如，如果您想要追蹤某人何時在網站上檢視視影片或進行意見調查，請使用自訂活動。 自訂活動與自訂物件不同。 值可以變更時使用自訂物件（即「汽車顏色」從藍色變更為紅色）。 當追蹤已發生的時間及其詳細資料無法變更時（即「購買的汽車」），請使用自訂活動。 依預設，可定義的自訂活動最大數量限製為10。 您可以透過額外訂閱費用提高此數目。 根據[Marketo資料保留原則](https://nation.marketo.com/t5/knowledgebase/tkb-p/support_solutions-documents)，自訂活動將在25個月後自動刪除。

**自訂活動：**&#x200B;您想要在Marketo中追蹤的非Marketo事件。 **自訂活動ID：** Marketo會指派數值ID給自訂活動，以便在嘗試使用Marketo API推送/提取活動資料時使用。 **自訂活動欄位：**&#x200B;活動中繼資料可儲存在活動欄位中。 例如，如果您正在追蹤視訊的檢視，欄位可能是頁面URL、視訊標題等。 **自訂活動主要欄位：**&#x200B;可當作智慧清單篩選條件使用的自訂活動欄位。

**快速提示**

* 自訂活動的API端點可在[這裡](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST)取得。

## 自訂物件與自訂活動

**自訂物件**

**自訂活動**

1

依預設，每個執行個體最多10個自訂物件。

預設為最多10個自訂活動。

2

每個自訂物件最多50個自訂物件欄位。

每個自訂活動型別最多可以有20個次要屬性。

3

最多1MM自訂物件記錄；可能非常取決於您的訂閱。

25個月後，將根據Marketo資料保留原則刪除自訂活動。

4

可在智慧列示和智慧行銷活動中作為篩選器和觸發器使用。

可在智慧列示和智慧行銷活動中作為篩選器和觸發器使用。

5

可用於個人化電子郵件內容。

無法用於個人化電子郵件內容。

6

可以對自訂物件記錄執行CRUD操作。

自訂活動中只允許建立和讀取。

7

值可以變更時使用自訂物件（即「汽車顏色」從藍色變更為紅色）。

當追蹤已發生的時間及其詳細資料無法變更時（即「購買的汽車」），請使用自訂活動。

8

自訂物件會告訴您事實。 即目前值。

自訂活動會告訴您過去發生的事件。

 

由&#x200B;_Amit_&#x200B;張貼於&#x200B;_2020-10-18_

## 2021年1月更新

2021年1月，我們將發行新的REST API並解決數項缺陷。 請參閱以下的完整更新清單。

* 新增[提交表單](/help/rest-api/leads.md)端點，可讓您執行程式化表單提交。 協力廠商表單現在可與Marketo表單整合，以運用現有的行銷工作流程。
* 新增[取得登入頁面完整內容](/help/rest-api/landing-pages.md)端點，此端點會傳回登入頁面的序列化HTML版本。 可讓您不需登入Marketo Engage，即可呈現完整個人化的登入頁面預覽。 這有助於簡化整合式應用程式內的編輯和翻譯工作流程。
* 您現在可以設定透過Velocity指令碼可存取的自訂物件數目。 您可以在[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/email-setup/change-custom-object-retrieval-limits-in-velocity-scripting)找到設定指示。

### 瑕疵解決方案

* 修正[Delete User](/help/rest-api/user-management.md)端點可讓您刪除自訂服務正在使用的僅限API的使用者的問題。 現在它會傳回錯誤「611，您無法刪除API服務中使用的API使用者」。 [LM-141893]
* 修正[Get Users](/help/rest-api/user-management.md)端點在某些情況下會傳回已刪除之使用者的問題。 [LM-141542]
* 修正[復製程式](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)端點的問題。 如果您指定的程式名稱超過255個字元，則會傳回「611， Unable to clone program error」。 現在它會傳回「701，名稱不能超過255個字元」。 [LM-143436]
* 修正[核准登陸頁面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)端點的問題。 當您核准已啟用行動版本的登入頁面時，在某些情況下，您會在案頭版本中看到行動版本的內容。 [LM-146867]
* 修正[取消核准登陸頁面](https://developer.adobe.com/marketo-apis/api/asset/#operation/unapproveLandingPageByIdUsingPOST)端點的問題，此問題可讓您取消核准一或多個表單正在用來作為後續頁面的登陸頁面。 現在會傳回錯誤「709，取消核准登陸頁面失敗。 一或多個表單正在使用登入頁面作為後續頁面，表單識別碼為：[_formId1，formId2，..._]&quot;。 [LM-143326]

由&#x200B;_David_&#x200B;張貼於&#x200B;_2021-01-15_

## Munchkin 160 Beta與Beacon API

**2021年1月27日：**&#x200B;部分將受「關聯銷售機會」淘汰影響的Marketo使用者收到錯誤電子郵件通知，指出他們已在其一或多個執行個體上啟用Munchkin Beta設定。 系統會保留此版本，直到通知正確的對象為止。 從Munchkin JavaScript版本160開始，[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API)會成為Munchkin與Marketo後端通訊的預設方式。 透過&#x200B;**useBeaconAPI**&#x200B;設定引數發行版本159,2020年夏季即可將此作為選項使用。 比起使用舊的XMLHttpRequest方法，信標API有幾項優點，但主要改進在於它是用於HTTP通訊的非封鎖非同步API，可用於所有現代網際網路瀏覽器。 雖然Munchkin的多數使用者不會注意到網站行為變更，但此更新可防止Munchkin在等候提交點按事件至後端時封鎖導覽，或更簡單地說，這幾乎消除了Munchkin在點按新頁面連結後導致瀏覽器「擱置」的可能性。 對部分Marketo客戶來說，這是罕見但令人沮喪的事件。

自2021年1月27日起，此版本的轉出處於保留狀態，等待重新排程。 雖然預計不會有任何與此變更相關的問題，且在測試時亦未發現任何問題，但Marketo無法測試Munchkin的所有可能部署設定，而您可能會想要預先測試這些變更，或在此版本正式發行前放棄此變更。 各種情況的說明如下。

### 測試信標API

如果您因預期即將推出版本，而想要測試更新的信標API，您可以將&#x200B;**useBeaconAPI**&#x200B;引數新增至外部測試頁面上的Munchkin設定，以測試更新。 此測試適用於一般可用或Beta版的Munchkin。 組態引數顯示在第7行`Munchkin.init()`方法引動的第二個引數中： `{ 'useBeaconAPI': true}`

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827', {"useBeaconAPI":true});
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

### 在Munchkin登陸頁面上停用Marketo Beta

若要在Marketo登陸頁面上停用Munchkin Beta，您必須存取訂閱「管理員」區段中的[Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)功能表，並將「登陸頁面上的Munchkin Beta」設定變更為「已停用」。

### 在外部頁面上停用Munchkin Beta

如果您已將Beta版本的Munchkin JavaScript部署至外部網頁，並希望在正式推出前放棄此變更，則需變更Munchkin JS程式碼片段，以鎖定**munchkin。****js**&#x200B;檔案而非**munchkin-beta版。****js**&#x200B;檔案。 在下列範例中，這是第11行之&#x200B;**s.src**&#x200B;變數的值。 您的程式碼片段可能與以下範例不盡相同，或是由標籤管理員在您的外部頁面上部署，因此您可能需要聯絡IT資源或是在啟用Munchkin追蹤的情況下管理您網站的人員。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';//This line should have the munchkin.js file, not munchkin-beta.js
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2021-01-08_

## 電子郵件V1的最終API淘汰

[電子郵件V1的淘汰大約在兩年前開始](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)，並且從2021年3月17日倫敦和荷蘭訂閱的維護發行三月起，以及其他所有訂閱的維護發行三月為2021年3月19日，所有V1電子郵件的API支援都將終止。 在此版本之後，任何嘗試透過Asset API與V1電子郵件互動都會導致錯誤，且不會採取任何動作。 自2021年2月24日起的所有已知剩餘使用者都已收到通知，但可能仍會有嘗試與這些資產互動的整合專案。 最常見的受影響整合型別是提供數位資產管理、翻譯和本地化的服務。 如果您發現這項變更導致整合失敗，[您仍可編輯並核准有問題的資產，以升級有問題的資產](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/transitioning-to-email-editor-2-0)。 電子郵件資產升級至V2後，您應可繼續將其與整合服務搭配使用。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2021-03-17_

## 2021年5月更新

2021年5月，我們將發佈新的REST API，以強化現有的REST API並解決數個缺陷。 請參閱以下的完整更新清單。

* 新增可讓您擷取、更新和刪除方案成員資格記錄的方案成員API。 如需詳細資訊，請參閱[REST API >潛在客戶資料庫>程式成員](/help/rest-api/program-members.md)。
* 新增大量自訂物件擷取API ，可讓您匯出與一對多關係中的潛在客戶相關聯的第一級Marketo自訂物件記錄。 如需詳細資訊，請參閱[REST API >大量擷取>大量自訂物件擷取](/help/rest-api/bulk-custom-object-extract.md)。
* 我們已增強[銷售機會API](/help/rest-api/leads.md)和[大量銷售機會擷取API](/help/rest-api/bulk-lead-extract.md)，以允許使用者擷取Adobe Experience Cloud ID (ECID)。 這可讓[從Adobe Experience Cloud](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/miscellaneous/set-up-adobe-experience-cloud-audience-sharing.html)同步受眾的使用者識別具有關聯ECID的潛在客戶。 這開啟了[整合的可能性](https://adobeexchangeec.zendesk.com/hc/en-us/articles/360024277392-Adobe-Experience-Cloud-Using-the-ECID-for-integration)與其他Adobe Experience Cloud產品。
* 我們已增強[大量銷售機會匯入API](/help/rest-api/bulk-lead-import.md)，以支援在匯入過程中將銷售機會新增至公司記錄。 若要這麼做，請將&#x200B;**externalCompanyId**&#x200B;欄位加入匯入檔案。
* 我們已增強數個計畫端點，以提供與Marketo Engage UI中發現的功能對等。 我們已增強[建立程式](/help/rest-api/assets.md)和[復製程式](https://developer.adobe.com/marketo-apis/api/asset/)端點，以允許事件程式的建立、複製或移動作業。 這適用於透過「巢狀」於其他方案型別下方來組織事件方案的使用者。 我們也增強了[刪除程式](https://developer.adobe.com/marketo-apis/api/asset/)端點，以允許刪除包含以下資產的程式：推播通知、應用程式內訊息、報告、具有內嵌式社交Assets的登陸頁面。
* 身為Marketo管理員，您可以[將特定欄位標示為「敏感」](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/home)，如此一來，表單中的值[絕不會預先填入](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/forms/form-fields/disable-pre-fill-for-a-form-field)，進而保護使用者的敏感資料。 我們已增強數個表單欄位端點，以提供與Marketo Engage UI中此功能的同等功能。

### 瑕疵解決方案

* 已修正刪除程式端點的問題。 如果您嘗試刪除共用資料夾中的程式，會傳回「611，系統錯誤」。 現在它會傳回「 &quot;Target program is in a shared folder and be delete. 在嘗試刪除之前，必須取消共用資料夾。」
* 修正原地復製程式端點的問題。 如果您嘗試複製流程步驟中包含DateTime的程式，會傳回「611，系統錯誤」。 現在已成功復製程式。
* 修正「建立程式」端點不慎允許您在電子郵件程式下建立程式（這是不允許的）的問題。
* 修正原地復製程式端點的問題。 如果您複製包含登陸頁面的程式，則目標程式中登陸頁面的名稱在程式名稱和登陸頁面名稱之間會遺漏底線。 例如`http://<_pod_\>.marketo.com/lp/<_munchkin_\>/<_program name_\>**_**<_LP name_\>.html`


由&#x200B;_David_&#x200B;張貼於&#x200B;_2021-05-07_

## 加入Adobe交易所限時優惠

Marketo Engage合作夥伴社群支援是我們客戶成功的支柱之一。 我們希望確保Marketo Engage整合生態系統在Exchange Marketplace中有妥善的呈現方式，並為LaunchPoint合作夥伴提供特別優惠。 我們在LaunchPoint合作夥伴的有限時間內提供免費的Exchange創新合作關係，到2022年底為止（價值約1.5萬美元），不會延長。 我們設計此優惠方案是為了鼓勵LaunchPoint合作夥伴在Exchange合作夥伴入口網站中建立其整合清單，以便在Adobe Exchange Marketplace中公開搜尋。 檢視您在2022年12月之前免費獲得的「創新合作關係」權益的完整清單。

1. 前往[Adobe Exchange合作夥伴支援中心](https://adobeexchangeec.zendesk.com/hc/en-us?mkt_tok=NjA4LURIVi05MTUAAAF-P5lIeVWOuBmKMS_uE_NpgFKtC0ukt7z_ksnq_Sbzb6mzXUuXpqpqQeujtPdZ24WcjMdptygQSR9XrYt_Cw)
1. 按一下右上角的「提交請求」
1. 在&#x200B;**中，請在下方選擇您的問題**&#x200B;下拉式清單中選擇[Adobe Exchange支援]
1. 在&#x200B;**您的電子郵件地址**&#x200B;中輸入您的電子郵件地址
1. 在&#x200B;**主旨**&#x200B;方塊中，輸入「LaunchPoint選件」
1. 在&#x200B;**Description**&#x200B;方塊中，輸入「LaunchPoint選件」
1. 在&#x200B;**支援型別**&#x200B;下拉式清單中，選取「方案支援」
1. 在&#x200B;**Adobe Exchange產品**&#x200B;下拉式清單中，選取「Adobe Exchange方案」
1. 提交表單。 我們的團隊會儘快與您聯絡！

由&#x200B;_David_&#x200B;張貼於&#x200B;_2021-07-22_

## 2021年8月更新

在2021年8月，我們將增強現有的REST API，並解決數項缺陷。 請參閱以下的完整更新清單。

* 我們已增強大量活動擷取API，以允許使用者針對6種不同的活動型別使用主要屬性進行篩選。 如需詳細資訊，請參閱[大量活動擷取](/help/rest-api/bulk-activity-extract.md)。
* 為了讓Marketo Sales Connect使用者能夠更深入存取其銷售活動資料，我們已啟用其他銷售活動屬性。 我們新增下列屬性至「傳送銷售電子郵件」、「開啟銷售電子郵件」及「按一下銷售電子郵件」活動：


* Marketo銷售人員ID - Sales Connect中人員記錄的唯一ID
* 銷售促銷活動名稱 — 銷售促銷活動的名稱（如果電子郵件是銷售促銷活動的一部分）
* Sales Campaign URL - Sales Connect URL for sales campaign （如果電子郵件是促銷活動的一部分）
* Sales Template Name - Sales Connect中電子郵件範本的名稱（如果使用範本）
* 銷售範本URL — 電子郵件範本的銷售連線URL （若使用範本）

### 電子郵件

* 我們已新增`earliestUpdatedAt`/`latestUpdatedAt`篩選器，以增強「取得電子郵件」端點。 這可讓您使用`updatedAt`欄位來搜尋電子郵件的子集，並允許增量同步。
* 我們已增強「取得電子郵件」、「依名稱取得電子郵件」、「依ID取得電子郵件」端點，以支援[Champion和Challenger](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/email-tests-champion-challenger/add-an-email-champion-challenger)型別電子郵件記錄的擷取。

### 瑕疵解決方案

* 修正「取得使用者」端點的問題。 未傳回已核發[行銷行事曆](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/marketing-calendar/understanding-the-calendar/issue-revoke-a-marketing-calendar-license)授權的使用者。 現在可正確傳回行銷行事曆使用者。
* 修正提交表單端點的問題。 如果出現重複的銷售機會記錄，會使用「提交表單」發出「1007，多個銷售機會符合查詢條件」錯誤。 提交表單現在會以[Forms 2.0 API](/help/javascript-api/forms-api-reference.md)的相同方式，更新最近更新的記錄。
* 改善「更新銷售機會欄位」和「建立銷售機會欄位」端點傳回的幾則誤導性錯誤訊息。 [LM-151890、LM-151888、LM-151889]
* 修正「依名稱取得銷售機會欄位」和「依銷售機會欄位端點」的問題。 兩個端點都可能會傳回稍微過時的資訊。 他們現在一律會傳回目前資訊。
* 修正使用「範圍」HTTP標頭進行部分擷取時，未傳回範圍中最後一個位元組的[大量擷取API](/help/rest-api/bulk-extract.md)問題。
* 已修正更新片段中繼資料端點的問題。 更新程式碼片段名稱或說明時，程式碼片段狀態會變更為「已核准並草稿」。 現在，在更新程式碼片段名稱或說明後，程式碼片段狀態會維持不變。
* 已修正「新增電子郵件模組」端點的問題。 新增包含程式碼片段的模組時，系統傳回「611，系統錯誤」。 現在可正確地將模組新增至電子郵件。
* 已修正刪除程式端點的問題。 刪除包含應用程式內訊息本機資產的程式時，系統傳回「611，系統錯誤」。 現在可以正確刪除程式。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2021-08-22_

## Munchkin 161版推出

Munchkin 161版將於2021年9月7日開始推出，至啟用Munchkin Beta訂閱的10%，9月16日再推出50%，9月30日再推出100%。 此變更將會影響Marketo登陸頁面，以及服務給外部登陸頁面（從已推出新版本的訂閱載入）的檔案munchkin-beta.js版本。 此版本已完全淘汰Munchkin關聯銷售機會方法，這是一種允許將個人資料提交到Marketo訂閱的功能，以及與已知個人記錄關聯的網頁瀏覽歷史記錄。 正在移除關聯銷售機會，以支援更現代且安全的替代方案，例如[Forms JS API](/help/javascript-api/forms-api-reference.md)、Form Submit API和[關聯銷售機會REST API](/help/rest-api/leads.md)。 如果您或您的組織使用此方法，應在2021年10月12日排程開始10月發行推出時，從使用量移轉出去。 如果您不想再選擇使用Munchkin測試版，您可以將「登陸頁面上的Munchkin Beta」功能切換為`disabled`Treasure Chest功能表[中的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)，以停用Marketo登陸頁面上的使用情形。 如果您已將Munchkin Beta JavaScript部署到外部網頁，並且希望切換到預設的Munchkin發行通道，則需要更新您的程式碼片段，以從munchkin.js而非munchkin-beta.js載入Munchkin JavaScript。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2021-08-24_

## Munchkin服務的TLS 1.0和TLS 1.1終止服務

自2021年10月21日起，用於向訪客提供Munchkin JavaScript的munchkin.marketo.net將不再接受使用[TLS 1.0或TLS 1.1的連線。](https://en.wikipedia.org/wiki/Transport_Layer_Security)這些加密標準不再被接受為Web安全性最佳實務的一部分，且不再受到現代瀏覽器版本的支援。 此變更預計不會造成負面影響。

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2021-10-04_

## 2021年10月更新

在2021年10月，我們將增強現有的REST API，並解決數項缺陷。 請參閱以下的完整更新清單。

* 我們已增強[提交表單](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST)端點，以支援方案成員自訂欄位做為表單提交的一部分。 可選擇將程式指定為新增表單的程式，和/或新增程式成員自訂欄位的程式，如[此處](/help/rest-api/leads.md)所述。
我們已增強[取得方案成員](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getProgramMembersUsingGET)端點，以支援以updatedAt屬性為基礎的日期範圍查詢。 若要這麼做，請依照[此處](/help/rest-api/program-members.md)的說明，傳遞開始和結束日期時間引數。
* 我們已增強[銷售機會欄位](/help/rest-api/leads.md) API以支援[敏感欄位](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/mark-a-field-as-sensitive)。 [依名稱取得潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldByNameUsingGET)、[取得潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldsUsingGET)、[建立潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)以及[更新潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updateLeadFieldUsingPOST)端點現在支援isSensitive屬性。

### 瑕疵解決方案

* 已修正[使用者管理](/help/rest-api/user-management.md) API的問題。 屬於設定為可與[銷售Insight](https://business.adobe.com/products/marketo/sales-insight.html)搭配使用的Marketo使用者。 這些使用者現在由[取得使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/getUsersUsingGET)端點傳回，現在可以使用[刪除使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST)端點刪除這些使用者。 [LM-155864]
* 已修正新增[RTF欄位](https://developer.adobe.com/marketo-apis/api/asset/#tag/Form-Fields/addRichTextFieldUsingPOST)端點的問題。 將超過65,000個字元的RTF欄位新增至電子郵件、登陸頁面、程式碼片段或表單時，系統傳回「611，系統錯誤」。 現在會傳回錯誤「701，無法完成作業。 &#39;content&#39;超過65,535位元組的最大長度&#39;。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2021-10-25_

## 2022年1月更新

在2022年1月，我們將增強現有的REST API，並解決數項缺陷。 請參閱以下的完整更新清單。

* 我們已增強[大量自訂物件擷取](/help/rest-api/bulk-custom-object-extract.md) API，以允許使用者使用&#x200B;**updatedAt**&#x200B;日期範圍進行篩選。
* 新增方案成員欄位中繼資料API，可讓您建立、更新和擷取方案成員欄位的中繼資料。 如需詳細資訊，請參閱[方案成員>欄位](/help/rest-api/program-members.md)。
* 新增公司欄位中繼資料API，可讓您擷取公司欄位的中繼資料。 如需詳細資訊，請參閱[公司>欄位](/help/rest-api/companies.md)。
* 新增機會欄位中繼資料API，可讓您擷取機會欄位的中繼資料。 如需詳細資訊，請參閱[機會>欄位](/help/rest-api/opportunities.md)。
* 新增具名帳戶欄位中繼資料API，可讓您擷取具名帳戶欄位的中繼資料。 如需詳細資訊，請參閱[具名帳戶>欄位](/help/rest-api/named-accounts.md)。
* 更新欄位中繼資料端點以傳回新的布林屬性&#x200B;**isApiCreated**，以指出欄位是否由REST API建立。

### 瑕疵解決方案

* 修正呼叫[建立潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)端點的時間，與智慧清單中有新建立潛在客戶欄位的時間之間的延遲問題。 [LM-152838]
* 修正[建立潛在客戶欄位](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)端點的問題，其中建立的欄位無法用於[新增欄位至Marketo Engage UI中的表單](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/forms/creating-a-form/add-a-field-to-a-form)的表單欄位下拉式清單。 [LM-158243]
* 修正指定isTriggerable=true引數時，[Get Campaigns](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET)端點未傳回可觸發行銷活動的問題。 [LM-158283]
* 修正了[依清單ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteTokenByNameUsingPOST)取得銷售機會端點在某些情況下會傳回錯誤「611，系統錯誤」的問題。 [LM-157214]
* 已清除[更新潛在客戶欄位](/help/rest-api/leads.md)端點傳回的幾則錯誤訊息。 [LM-151886、LM-151888、LM-151889]

由&#x200B;_David_&#x200B;張貼於&#x200B;_2022-01-27_

## 2022年3月更新

在2022年3月，我們將增強現有的REST API，並解決數項缺陷。 請參閱以下的完整更新清單。

* 我們已將&#x200B;**actionResult**&#x200B;欄位新增至大量活動擷取API產生的匯出檔案。 此欄位可用於區分成功、已略過和失敗的活動。
* 我們已將&#x200B;**isOpenTrackingDisabled**&#x200B;欄位新增至[電子郵件API](/help/rest-api/emails.md)的回應。 此欄位可用來判斷是否已啟用[停用開啟追蹤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-editor-v2-0-overview)功能。
* 我們新增了兩個端點，可讓您選擇性管理計畫標籤。 [更新程式標籤](/help/rest-api/programs.md)端點可讓您選擇性地更新程式標籤。 [刪除程式標籤](/help/rest-api/programs.md)端點可讓您選擇性地刪除程式標籤。
* 我們已將&#x200B;**isExecutable**&#x200B;引數新增至[複製Smart Campaign](/help/rest-api/smart-campaigns.md)端點。 此引數可讓您將程式復製為可執行程式。
* 我們已將&#x200B;**headStart**&#x200B;欄位新增至[程式API](/help/rest-api/programs.md)。 這可讓您建立、更新及擷取電子郵件程式的[開始時間](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/head-start-for-email-programs)設定。

### 瑕疵解決方案

* 修正[取得電子郵件動態內容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET)端點的問題。 嘗試從具有中斷範本關係的電子郵件中擷取具有動態內容的主旨行時，會傳回錯誤(709)，「API只允許對具有範本的電子郵件進行操作」。 端點現在會傳回動態內容。 [LM-152331]
* 已修正[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST)端點的問題。 使用externalSalesPersonId來關聯銷售人員與使用externalSalesPersonId的銷售機會，並使用action = createDuplicate時，銷售人員關聯將不會發生。 [LM-158990]


### Adobe IMS整合

* 已加入[Adobe IMS](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)的使用者無法使用所有[Marketo使用者管理API](/help/rest-api/user-management.md)。 呼叫與Adobe IMS整合的Marketo執行個體時，下列端點將會傳回錯誤： [邀請使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/inviteUserUsingPOST)、[依ID取得受邀請使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/getInvitedUserUsingGET)、[更新使用者屬性](https://developer.adobe.com/marketo-apis/api/user/#operation/updateUserAttributeUsingPOST)、[刪除使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST)以及[刪除受邀請使用者](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteInvitedUserUsingPOST)。 作為取代，應使用[Adobe User Management API](https://developer.adobe.com/umapi/)。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2022-03-14_

## 2022年5月更新 — 

在2022年5月，我們將增強現有的REST API，並解決數項缺陷。 請參閱以下的完整更新清單。

* 我們已新增在您的Marketo Engage執行個體中啟用[SFDC同步](/help/rest-api/companies.md)或[Microsoft Dynamics同步](/help/rest-api/opportunities.md)時，擷取[公司](/help/rest-api/sales-persons.md)、[商機](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync)和[銷售人員](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync)記錄的功能。
* 我們已更新[取得電子郵件動態內容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET)端點，讓您從電子郵件主旨列擷取[動態內容](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/using-dynamic-content-in-an-email)。 無論指定的電子郵件是否連結至電子郵件範本，這都可正常運作。

`POST /rest/asset/v1/form/{id}/field/State.json?values=[{"label":"Alaska"},{"value":"AK"},{"label":"West Virginia","value":"WV"},{"label":"Wyoming","value":"WY"}]`

* 我們已更新[新增表單欄位可見性規則](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllProgramMemberFieldsUsingGET)端點，允許您為&#x200B;**isNot**&#x200B;型別[不可見性規則](/help/rest-api/forms.md)新增多個比較值。 其範例如下：

`POST /rest/asset/v1/form/{id}/field/LastName/visibility.json?visibilityRule={"ruleType":"show","rules":[{"subjectField":"LastName","operator":"isNot","values":["A","B","C"]}`

### 瑕疵解決方案

* 修正了為[leadFormFields](/help/rest-api/leads.md)引數中的屬性傳遞「null」時，[提交表單](/help/rest-api/leads.md)端點發生的問題，傳回錯誤：「611，系統錯誤」。 現在它會正確傳回「1003，表單驗證失敗」錯誤。 [LM-162213]

由&#x200B;_David_&#x200B;張貼於&#x200B;_2022-05-09_

## 2022年8月更新

在2022年8月，我們將增強現有的REST API。 請參閱以下的完整更新清單。

我們已新增數個篩選器，可在呼叫「建立匯出程式成員工作」端點時使用。 請注意，許多篩選器可以搭配使用，以精簡擷取的資料集。

* **programIds**&#x200B;篩選器可用來指定最多10個程式識別碼，以協助改善輸送量。
* **isExhausted**&#x200B;篩選器可用來篩選已用完內容[的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content)人記錄。
* **nurtureCadence**&#x200B;篩選器可用來根據[參與計畫步調](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/program-flow-actions/change-engagement-program-cadence)來篩選記錄。
* **statusNames**&#x200B;篩選器可用來篩選一或多個[程式狀態](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/understanding-program-membership)的記錄。
* **updatedAt**&#x200B;篩選器可用來根據日期範圍篩選記錄。

### 公告

* [身分](https://developer.adobe.com/marketo-apis/api/identity/#operation/identityUsingGET)端點的行為已變更。 當您呼叫端點而且不包含&#x200B;**access_token**&#x200B;引數時，會傳回「603， Access被拒絕」錯誤。 先前，會傳回「600，空白存取權杖」錯誤。 請注意，「600，空白存取權杖」錯誤已被取代。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2022-09-03_

## 2022年10月更新

在2022年10月，我們將增強現有的REST API。 請參閱以下的完整更新清單。

* 我們已增強[大量銷售機會匯入API](/help/rest-api/bulk-lead-import.md)，以支援在匯入過程中將Lead新增至Sales Person記錄。 若要這麼做，請在匯入檔案中加入&#x200B;**externalSalesPersonId**&#x200B;欄位。
* 修正建立Score型別欄位時[建立潛在客戶欄位](/help/rest-api/leads.md)端點的問題。 這些欄位無法用於Marketo Engage UI中的[變更分數](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/change-score)流程動作。 [LM-166815]

### 公告

* 程式成員資格屬性`acquiredBy`已變更為可更新。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2022-10-18_

## Marketo Forms REST AP近期變更

從2022.R2版開始，目前排程在2023年3月24日當週，Adobe Marketo Engage Forms Asset API將一致地只傳回不含前置詞計畫名稱的表單名稱，無論該表單是否為計畫的子項。 這項變更會使Forms API的資產名稱行為與其他Adobe Marketo Engage Asset API一致。 為避免服務中斷，您應檢閱任何使用Marketo Engage Forms API的整合，並與您的整合經銷商合作，以瞭解是否需要任何變更來因應此要求。在此即將進行的變更之前，Forms API傳回的名稱在行銷活動中為方案子項的表單方案名稱的前置詞不一致。 例如，名為「表單1」的表單，是「方案1」的子項，其名稱可能由API傳回：方案1.表單1或表單1從2022.R2版本開始，表單名稱將一律不帶前置詞的程式名稱。 若使用相同的範例，名稱將一律為：表單1

由&#x200B;_Kenny_&#x200B;張貼於&#x200B;_2022-11-04_

## 2023年1月更新

2023年1月，我們對Admin UI進行了與API相關的增強，並發佈了兩則公告。 請參閱以下的完整更新清單。

管理員UI

### 大量潛在客戶擷取

* 我們已增強Marketo Engage管理UI，可讓您檢視訂閱的大量擷取API每日容量配置。 此外，您可以檢視API使用者過去7天的容量使用情形。 在[這裡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/bulk-export-api-information)可找到更多資訊。

### 瑕疵解決方案

* 已修正[刪除商機](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST)端點的問題。 在某些情況下，並未產生「從機會移除」活動。 [LM-172208]

### 公告

* 請參閱Marketo社群上有關REST API和變更HTTP回應訊息[原因片語](https://nation.marketo.com/t5/product-documents/upcoming-change-to-marketo-rest-api/ta-p/331698)的[本文章](https://www.rfc-editor.org/rfc/rfc7230#section-3.1.2)。
* 程式成員資格屬性&#x200B;**statusReason**&#x200B;已變更為可更新。

由&#x200B;_David_&#x200B;張貼於&#x200B;_2023-01-21_
