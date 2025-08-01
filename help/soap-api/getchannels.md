---
title: getChannels
feature: SOAP
description: getChannels SOAP呼叫
exl-id: bcaef85b-154b-4571-ad24-07a36707a6ef
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '92'
ht-degree: 5%

---

# getChannels

此函式擷取有關在Marketo中設定的頻道的下列資訊：

- 進度狀態
- 每個進度狀態的步驟編號
- 特定進度狀態是否已定義為成功

您可以取得所有管道的資訊，或指定特定的管道清單

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| tag->values->stringItem | 選用 | 您要查詢的管道清單。 如果省略`<tag>`，您將收到有關所有頻道的資訊 |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>1538ddb4e5208ac2feeb431e9b2b22caecc1de3f</requestSignature>
      <requestTimestamp>2013-08-05T11:21:17-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsGetChannels>
      <tag>
        <values>
          <stringItem>Webinar</stringItem>
          <stringItem>Blog</stringItem>
          <stringItem>Tradeshow</stringItem>
        </values>
      </tag>
    </ns1:paramsGetChannels>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successGetChannels>
      <result>
        <tags>
          <tagStatus>
            <tagValue>Blog</tagValue>
            <statusList>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Engaged</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>20</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Subscribed</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>30</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Visited</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>10</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
            </statusList>
          </tagStatus>
          <tagStatus>
            <tagValue>Tradeshow</tagValue>
            <statusList>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Attended Show</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>20</value>
                  </attrib>
                  <attrib>
                    <name>Check-in Status</name>
                    <value>Attended</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Engaged</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>40</value>
                  </attrib>
                  <attrib>
                    <name>Check-in Status</name>
                    <value>None</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Invited</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>10</value>
                  </attrib>
                  <attrib>
                    <name>Check-in Status</name>
                    <value>None</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Visited Booth</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>30</value>
                  </attrib>
                  <attrib>
                    <name>Check-in Status</name>
                    <value>None</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
            </statusList>
          </tagStatus>
          <tagStatus>
            <tagValue>Webinar</tagValue>
            <statusList>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Attended</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>70</value>
                  </attrib>
                  <attrib>
                    <name>Webinar Behavior</name>
                    <value>Attended</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Attended On-demand</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>75</value>
                  </attrib>
                  <attrib>
                    <name>Webinar Behavior</name>
                    <value>Attended On-demand</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Downloaded</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>5</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>true</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Invited</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>10</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>No Show</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>50</value>
                  </attrib>
                  <attrib>
                    <name>Webinar Behavior</name>
                    <value>No Show</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Pending Approval</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>20</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Registered</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>40</value>
                  </attrib>
                  <attrib>
                    <name>Webinar Behavior</name>
                    <value>Registered</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Rejected</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>30</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
              <progressionStatusItem>
                <progressionStatus>
                  <attrib>
                    <name>Status</name>
                    <value>Wait Listed</value>
                  </attrib>
                  <attrib>
                    <name>Step</name>
                    <value>30</value>
                  </attrib>
                  <attrib>
                    <name>Success</name>
                    <value>false</value>
                  </attrib>
                </progressionStatus>
              </progressionStatusItem>
            </statusList>
          </tagStatus>
```

## 範常式式碼 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = "";  // CHANGE ME
$marketoSecretKey   = "";  // CHANGE ME
$marketoNameSpace   = "http://www.marketo.com/mktows/";

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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}

// Create Request
$params = new stdClass();
$tagValues = array("Webinar","Blog", "Tradeshow");
$values = new stdClass();
$values->stringItem = $tagValues;
$tag = new stdClass();
$tag->values = $values;
$params->tag = $tag;
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('getChannels', array($params), $options, $authHdr);
  //      print_r($leads);
}
catch(Exception $ex) {
  var_dump($ex);
}
if ($debug) {
  print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
  print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
}

?>
```

## 程式碼範例 — Java

```java
import java.net.URL;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import javax.xml.namespace.QName;
import org.apache.commons.codec.binary.Hex;
import com.marketo.mktows.ArrayOfString;
import com.marketo.mktows.AuthenticationHeader;
import com.marketo.mktows.MktMktowsApiService;
import com.marketo.mktows.MktowsPort;
import com.marketo.mktows.ObjectFactory;
import com.marketo.mktows.ParamsGetChannels;
import com.marketo.mktows.SuccessGetChannels;
import com.marketo.mktows.SuccessGetLead;
import com.marketo.mktows.SuccessGetMultipleLeads;
import com.marketo.mktows.Tag;

public class GetChannels {

    public static void main(String[] args) {
        System.out.println("Executing Get Channels");
        try {
            URL marketoSoapEndPoint = new URL("CHANGEME" + "?WSDL");
            String marketoUserId = "CHANGEME";
            String marketoSecretKey = "CHANGEME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);

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
            ParamsGetChannels params = new ParamsGetChannels();
            Tag tags = new Tag();
            ArrayOfString tagArray = new ArrayOfString();
            tagArray.getStringItems().add("Webinar");
            tagArray.getStringItems().add("Blog");
            tagArray.getStringItems().add("Tradeshow");
            tags.setValues(tagArray);

            MktowsPort port = service.getMktowsApiSoapPort();
            SuccessGetChannels result = port.getChannels(params, header);

            JAXBContext context = JAXBContext.newInstance(SuccessGetLead.class);
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
request = {
    :tag => {
        :values => {
            :string_item => ["Webinar", "Blog", "Tradeshow"]
        }
    }
}

response = client.call(:get_channels, message: request)

puts response
```
