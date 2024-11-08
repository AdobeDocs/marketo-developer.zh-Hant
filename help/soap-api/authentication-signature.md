---
title: 驗證簽章
feature: SOAP
description: 具有驗證簽名的API安全性
exl-id: d6bed8ee-77fa-440c-8f35-a71cf77f45d3
source-git-commit: 28b040f6473677abaaa0c73f1bb6e887e9e35a81
workflow-type: tm+mt
source-wordcount: '209'
ht-degree: 3%

---

# 驗證簽章

Marketo API安全性使用簡單但高度安全的模型，並以透過HTTPS傳輸的訊息的HMAC-SHA1簽名為基礎。 此模型的主要優點在於它提供無狀態驗證。

HMAC-SHA1簽名需要下列專案：

- 隨服務要求傳輸的使用者ID （也稱為存取金鑰）
- 使用共用機密金鑰和訊息內容計算並與服務要求一起傳輸的簽章
- 未隨服務要求傳輸的共用機密金鑰（也稱為加密金鑰）

使用者端程式會使用共用的秘密金鑰和部分要求訊息內容來計算HMAC-SHA1簽章。 使用者端必須包含SOAP標頭AuthenticationHeaderInfo，才能在SOAP訊息中傳遞驗證資訊。

下列虛擬程式碼會示範演演算法：

```javascript
// Request timestamp: a timestamp string in W3C WSDL date format
stringToEncrypt = requestTimestamp + clientAccessID;

signatureBytes = Encryter.encrypt('SHA1', secretKey, stringToEncrypt);

signature = toLowerCase( hexEncode(signatureBytes) );

authHeader = "<ns1:AuthenticationHeader>" +
    "<mktowsUserId>" + clientAccessID + "</mktowsUserId>" +
    "<requestSignature>" + signature + "</requestSignature>" +
    "<requestTimestamp>" + requestTimestamp + "</requestTimestamp>"
    "</ns1:AuthenticationHeader>";
```

## 請求標頭

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| `mktowsUserId` | 必填 | Marketo使用者端存取ID位於Marketo管理員SOAP API面板的「整合」下方。 |
| `requestSignature` | 必要 | 以共用機密金鑰、`requestTimestamp`和Marketo使用者ID為基礎的HMAC-SHA1簽章 |
| `requestTimestamp` | 必要 | 要求時間戳記(W3C WSDL日期格式，例如 「2013-06-09T14:04:54-08:00」) |
| `partnerId` | 選填 | LaunchPoint技術合作夥伴[API金鑰](../launchpoint-api.pdf)。 |

## 請求XML - getLeadActivity

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:mkt="http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>mktodemoaccount881_536240405411DF5316D5C9</mktowsUserId>
         <requestSignature>3f4b21eb586063dc65774a2733713cac342e9c81</requestSignature>
         <requestTimestamp>2017-03-09T17:40:00-08:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsGetLeadActivity>
         <leadKey>
            <keyType>IDNUM</keyType>
            <keyValue>318815</keyValue>
         </leadKey>
         <activityFilter>
               <includeTypes>
                   <activityType>NewLead</activityType>
               </includeTypes>
         </activityFilter>
        </mkt:paramsGetLeadActivity>
   </soapenv:Body>
</soapenv:Envelope>
```

## 回應XML — 成功

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successGetLeadActivity>
         <leadActivityList>
            <returnCount>1</returnCount>
            <remainingCount>0</remainingCount>
            <newStartPosition>
               <latestCreatedAt xsi:nil="true"/>
               <oldestCreatedAt xsi:nil="true"/>
               <activityCreatedAt xsi:nil="true"/>
               <offset>1</offset>
            </newStartPosition>
            <activityRecordList>
               <activityRecord>
                  <id>12025714</id>
                  <activityDateTime>2015-09-04T16:07:16-05:00</activityDateTime>
                  <activityType>New Lead</activityType>
                  <mktgAssetName/>
                  <activityAttributes>
                     <attribute>
                        <attrName>Source Type</attrName>
                        <attrType xsi:nil="true"/>
                        <attrValue>Web service API</attrValue>
                     </attribute>
                     <attribute>
                        <attrName>Source Info</attrName>
                        <attrType xsi:nil="true"/>
                        <attrValue>Web service API</attrValue>
                     </attribute>
                     <attribute>
                        <attrName>Created Date</attrName>
                        <attrType xsi:nil="true"/>
                        <attrValue>2015-09-04</attrValue>
                     </attribute>
                     <attribute>
                        <attrName>Lead ID</attrName>
                        <attrType xsi:nil="true"/>
                        <attrValue>318815</attrValue>
                     </attribute>
                  </activityAttributes>
                  <campaign/>
                  <personName xsi:nil="true"/>
                  <mktPersonId>318815</mktPersonId>
                  <foreignSysId xsi:nil="true"/>
                  <orgName xsi:nil="true"/>
                  <foreignSysOrgId xsi:nil="true"/>
               </activityRecord>
            </activityRecordList>
         </leadActivityList>
      </ns1:successGetLeadActivity>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope
```

## 回應XML — 失敗（無效的認證）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
  <SOAP-ENV:Body>
    <SOAP-ENV:Fault>
      <faultcode>SOAP-ENV:Client</faultcode>
      <faultstring>20014 - Authentication failed</faultstring>
      <detail>
        <ns1:serviceException xmlns:ns1="http://www.marketo.com/mktows/">
          <name>mktServiceException</name>
          <message>Authentication failed (20014)</message>
          <code>20014</code>
        </ns1:serviceException>
      </detail>
    </SOAP-ENV:Fault>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 範常式式碼 — PHP

```php
<?php
 
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
 
  print_r($authHdr)
 
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
 
public class AuthenticationHeader {
 
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
 
            JAXBContext context = JAXBContext.newInstance(AuthenticationHeader.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(header, System.out);
 
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
```
