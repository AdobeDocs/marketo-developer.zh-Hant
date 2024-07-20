---
title: getimporttolstatus
feature: SOAP
description: getImportToListStatus SOAP呼叫
exl-id: 17a62f65-dfa0-4f40-89b2-154f7734a2dc
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '76'
ht-degree: 7%

---

# getimporttolstatus

此函式檢查特定`importToList`函式呼叫的狀態。

先完成`importToList` API呼叫，然後定期輪詢`getImportToListStatus`。 不要每分鐘輪詢`getImportToListStatus`超過一次。

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| programName | 必要 | 包含靜態清單的程式名稱 |
| listName | 必要 | Marketo中要擷取匯入狀態的靜態清單名稱。 |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>ab61065aa420ecdc78936fd4940b8e2e96252c4f</requestSignature>
      <requestTimestamp>2013-08-05T15:58:38-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsGetImportToListStatus>
      <programName>Trav-Demo-Program</programName>
      <listName>Trav-Test-List</listName>
    </ns1:paramsGetImportToListStatus>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successGetImportToListStatus>
      <result>
        <!-- Possible status values: PROCESSING/COMPLETE/FAILED/CANCELED -->
        <status>PROCESSING</status>
        <startedTime>2013-08-05T17:58:10-05:00</startedTime>
        <endedTime xsi:nil="true" />
        <estimatedTime xsi:nil="true" />
        <estimatedRows>2</estimatedRows>
        <rowsImported>0</rowsImported>
        <rowsFailed>0</rowsFailed>
        <rowsIgnored>2</rowsIgnored>
        <importSummary xsi:nil="true" />
      </result>
    </ns1:successGetImportToListStatus>
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
  $request = new stdClass();
  $request->programName = "Trav-Demo-Program";
  $request->listName = "Trav-Test-List";
  $params = array("paramsGetImportToListStatus" => $request);
 
  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $response = $soapClient->__soapCall('getImportToListStatus', $params, $options, $authHdr);
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
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
 
public class GetImportToListStatus {
 
    public static void main(String[] args) {
        System.out.println("Executing Get Import To List Status");
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
            ParamsGetImportToListStatus request = new ParamsGetImportToListStatus();
             
            request.setProgramName("Trav-Demo-Program");
            request.setListName("Trav-Test-List");
             
            SuccessGetImportToListStatus result = port.getImportToListStatus(request, header);
 
            JAXBContext context = JAXBContext.newInstance(SuccessGetImportToListStatus.class);
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
    :program_name => "Trav-Demo-Program",
    :list_name => "Trav-Test-List"
}

response = client.call(:get_import_to_list_status, message: request)

puts response
```
