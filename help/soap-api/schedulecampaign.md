---
title: scheduleCampaign
feature: SOAP, Smart Campaigns
description: scheduleCampaign SOAP呼叫
exl-id: a9ef2c16-34ef-4e0f-b765-e332335b0b81
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '252'
ht-degree: 3%

---

# scheduleCampaign

此函式設定批次Smart Campaign的排程，以立即或在未來日期執行。 需要現有的Smart Campaign才能成功完成。 這可以與importToList搭配使用，上傳銷售機會清單，然後針對新建立的清單執行批次行銷活動。

## 選擇性程式代號：

與requestCampaign函式類似，您可以將我的權杖陣列傳遞至此API呼叫，以覆寫現有權杖。 行銷活動執行後，會捨棄代號。

如果您將此選用引數與[importToList](importtolist.md)搭配使用，Token會依下列順序排列優先順序：

1. 每個潛在客戶權杖的importToList
1. 每個行銷活動代號的scheduleCampaign
1. 程式中的我的Token

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| programName | 必要 | 包含程式的名稱 |
| campaignName | 必要 | 智慧行銷活動的名稱 |
| campaignRunAt | 選用 | 執行已排程行銷活動的時間（W3C WSDL日期格式）。 |
| cloneToProgramName | 選用 | 此屬性出現時，會複製行銷活動的父級方案，並排程新建立的行銷活動。 屬性會為產生的程式指定所需的名稱。 注意：使用此欄位時，每天僅允許10次呼叫。 |
| programTokenList->attrib->name | 選用 | 您要為其傳送新值的Token名稱。 使用完整的Token格式，就像在Marketo UI中一樣。 即&quot;{{my.message}}&quot; |
| programTokenList->attrib->value | 選用 | 關聯權杖名稱的值。 |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809224544BFABAE58E5D27</mktowsUserId>
      <requestSignature>b578495dfdd03231455bb7671f02d2fe0a9edf79</requestSignature>
      <requestTimestamp>2013-08-03T21:39:34-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsScheduleCampaign>
      <programName>Trav-Demo-Program</programName>
      <campaignName>Batch Campaign Example</campaignName>
      <campaignRunAt>2013-08-03T21:39:34-07:00</campaignRunAt>
      <cloneToProgramName>TestProgramCloneFromSOAP</cloneToProgramName>
      <programTokenList>
        <attrib>
          <name>{{my.message}}</name>
          <value>Updated message</value>
        </attrib>
        <attrib>
          <name>{{my.other token}}</name>
          <value>Value for other token</value>
        </attrib>
      </programTokenList>
    </ns1:paramsScheduleCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successScheduleCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successScheduleCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 範常式式碼 — PHP

```php
 <?php

  $debug = true;

  $marketoSoapEndPoint     = "";  // CHANGE ME
  $marketoUserId           = "";  // CHANGE ME
  $marketoSecretKey        = "";  // CHANGE ME
  $marketoNameSpace        = "http://www.marketo.com/mktows/";

  // Create Signature
  $dtzObj = new DateTimeZone("America/Los_Angeles");
  $dtObj  = new DateTime('now', $dtzObj);
  $timeStamp = $dtObj->format(DATE_W3C);
  $encryptString = $timeStamp . $marketoUserId;
  $signature = hash_hmac('sha1', $encryptString, $marketoSecretKey);

  // Create SOAP Header
  $attrs = new stdClass();
  $attrs->mktowsUserId = $marketoUserId;
  $attrs->requestSignature = $signature;
  $attrs->requestTimestamp = $timeStamp;
  $authHdr = new SoapHeader($marketoNameSpace, 'AuthenticationHeader', $attrs);
  $options = array("connection_timeout" => 20, "location" => $marketoSoapEndPoint);
  if ($debug) {
    $options["trace"] = true;
  }

  // Create Request
  $params = new stdClass();
  $params->programName = "Trav-Demo-Program";
  $params->campaignName = "Batch Campaign Example";
  $dtObj = new DateTime('now', $dtzObj);
  $params->campaignRunAt = $dtObj->format(DATE_W3C);
  $params->cloneToProgramName = "TestProgramCloneFromSOAP";

  $token = new stdClass();
  $token->name = "{{my.message}}";
  $token->value = "Updated message";

  $params->programTokenList = array("attrib" => $token);
  $params = array("paramsScheduleCampaign" => $params);

  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $response = $soapClient->__soapCall('scheduleCampaign', $params, $options, $authHdr);
  }
  catch(Exception $ex) {
    var_dump($ex);
  }
  if ($debug) {
    print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
    print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
  }
  print_r($response);

?>
```

## 程式碼範例 — Java

```java
import com.marketo.mktows.*;
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

public class ScheduleCampaign {


    public static void main(String[] args) {
        System.out.println("Executing Schedule Campaign");
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
            ParamsScheduleCampaign request = new ParamsScheduleCampaign();

            request.setProgramName("Trav-Demo-Program");
            request.setCampaignName("Batch Campaign Example");

            // Create setCampaignRunAt timestamp
            GregorianCalendar gc = new GregorianCalendar();
            gc.setTimeInMillis(new Date().getTime());

            DatatypeFactory factory = DatatypeFactory.newInstance();
            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<XMLGregorianCalendar> setCampaignRunAtValue = objectFactory.createParamsScheduleCampaignCampaignRunAt(factory.newXMLGregorianCalendar(gc));
            request.setCampaignRunAt(setCampaignRunAtValue);

            request.setCloneToProgramName("TestProgramCloneFromSOAP");

            ArrayOfAttrib aoa = new ArrayOfAttrib();

            Attrib attrib = new Attrib();
            attrib.setName("{{my.message}}");
            attrib.setValue("Updated message");

            aoa.getAttribs().add(attrib);

            JAXBElement<ArrayOfAttrib> arrayOfAttrib = objectFactory.createParamsScheduleCampaignProgramTokenList(aoa);
            request.setProgramTokenList(arrayOfAttrib);

            SuccessScheduleCampaign result = port.scheduleCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessScheduleCampaign.class);
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

## 程式碼範例 — Ruby

```ruby
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "http://www.marketo.com/mktows/"

#Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

#Create SOAP Header
headers = {
    'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
    "requestTimestamp"  => requestTimestamp
    }
}

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request =  {
    :program_name => "Trav-Demo-Program",
    :campaign_name => "Batch Campaign Example",
    :campaign_run_at => requestTimestamp,
    :clone_to_program_name => "TestProgramCloneFromSOAP",
    :program_token_list => {
        :attrib => {
            :name => "{{my.message}}",
            :value => "Updated message" },
        :attrib! => {
            :name => "{{my.other token}}",
            :value => "Value for other token" }
    }
}

response = client.call(:schedule_campaign, message: request)

puts response
```
