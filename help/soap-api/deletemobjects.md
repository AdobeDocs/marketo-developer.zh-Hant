---
title: deleteMObjects
feature: SOAP
description: Marketo SOAP deleteMoBject以刪除Opportunity和OpportunityPersonRole，以及要求和回應XML、程式碼範例，傳回DELETED UNCHANGED FAILED。
exl-id: 31511584-5366-4af1-975b-94417680c4d9
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '74'
ht-degree: 8%

---

# deleteMObjects

刪除一或多個[MObjects](marketo-objects.md)並傳回作業的結果(DELETED、UNCHANGED、FAILED)。

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| mObjectList->mObject->type | 必要 | 可以是`Opportunity`或`OpportunityPersonRole`其中之一 |
| mObjectList->mObject->id | 必要 | 要刪除的MObject識別碼 |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>ed4c982dfbb3d569cdda1b658ab3eb97452e1df7</requestSignature>
      <requestTimestamp>2013-08-05T14:33:48-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsDeleteMObjects>
      <mObjectList>
        <mObject>
          <type>Opportunity</type>
          <id>4</id>
        </mObject>
        <mObject>
          <type>Opportunity</type>
          <id>7</id>
        </mObject>
      </mObjectList>
    </ns1:paramsDeleteMObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successDeleteMObjects>
      <result>
        <mObjStatusList>
          <mObjStatus>
            <id>4</id>
            <status>DELETED</status>
          </mObjStatus>
          <mObjStatus>
            <id>7</id>
            <status>DELETED</status>
          </mObjStatus>
        </mObjStatusList>
      </result>
    </ns1:successDeleteMObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 範常式式碼 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint     = "";  // CHANGE ME
$marketoUserId       = "";    // CHANGE ME
$marketoSecretKey        = "";    // CHANGE ME
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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}

// Create Request
$params = new stdClass();
$mObj1 = new stdClass();
$mObj1->type="Opportunity";
$mObj1->id = "4";
$mObj2 = new stdClass();
$mObj2->type="Opportunity";
$mObj2->id = "7";
$params->mObjectList = array($mObj1, $mObj2);
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('deleteMObjects', array($params), $options, $authHdr);
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
import javax.xml.bind.Marshaller;


public class DeleteMObjects {


    public static void main(String[] args) {
        System.out.println("Executing Delete MObjects");
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
            ParamsDeleteMObjects request = new ParamsDeleteMObjects();

            MObject mobj = new MObject();
            mobj.setType("Opportunity");
            mobj.setId(4);

            MObject mobj2 = new MObject();
            mobj2.setType("Opportunity");
            mobj2.setId(7);

            ArrayOfMObject objList = new ArrayOfMObject();
            objList.getMObjects().add(mobj);
            objList.getMObjects().add(mobj2);

            request.setMObjectList(objList);

            SuccessDeleteMObjects result = port.deleteMObjects(request, header);
            JAXBContext context = JAXBContext.newInstance(SuccessDeleteMObjects.class);
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
    :m_object_list => {
          :m_object => {
            type: "Opportunity",
            id: "4" },
          :m_object! => {
            type: "Opportunity",
            id: "7" }
    }
}

response = client.call(:delete_m_objects, message: request)

puts response
```
