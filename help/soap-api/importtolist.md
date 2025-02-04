---
title: importToList
feature: SOAP
description: importToList SOAP呼叫
exl-id: 7e4930a9-a78f-44a3-9e8c-eeca908080c8
source-git-commit: 8a019985fc9ce7e1aa690ca26bfa263cd3c48cfc
workflow-type: tm+mt
source-wordcount: '393'
ht-degree: 3%

---

# importToList

此函式可讓您將銷售機會清單匯入Marketo中的現有靜態清單，類似於Marketo UI中的匯入清單函式。

**匯入格式：**&#x200B;這些值與清單匯入中所使用的CSV結構相同。

**範例：**

| 電子郵件 | 第一 | 上次 |
| --- | --- | --- |
| joe@company.com | Joe | Smith |
| mary@company.com | Mary | 分類帳 |
| wanda@megacorp.com | 萬達 | Williams |

`displayName`值應在`importFileHeader`中使用，而不是`name`值。

**動態電子郵件內容：**&#x200B;您可以選擇性地以每個潛在客戶為基礎傳遞值，作為電子郵件中「我的Token」的取代。

| 電子郵件 | 第一 | 上次 | {{my.specialToken}} | {{my.otherToken}} |
| --- | --- | --- | --- | --- |
| joe@company.com | Joe | Smith | 魚 | 藍色 |
| mary@company.com | Mary | 分類帳 | 雞肉 | 棕色 |
| wanda@megacorp.com | 萬達 | Williams | 素菜 | 淡褐色 |

**重要：**&#x200B;如果您為潛在客戶新增代號，則必須指定使用這些代號的Smart Campaign。 下次指定的Smart Campaign執行時，將會使用您清單中的值，而非一般的「我的Token」值。 該單一Campaign執行後，便會捨棄代號。

`importToList`可能需要一些時間才能完成，尤其是大型清單。 如果您打算在其他API呼叫中使用新匯入的清單，您應該使用`importToListStatus`來檢查作業是否已完成。

**注意：**&#x200B;匯入CSV檔案中數值欄位的NULL值可能會產生這些欄位的「變更資料值」活動，即使欄位已經空白。 任何使用「資料值變更」篩選器或「資料值變更」觸發器的智慧行銷活動，都可能導致潛在客戶符合這些行銷活動的資格，即使資料實際上並未變更。 對這些篩選器/觸發器使用限制，以確保潛在客戶在執行匯入時不符合錯誤行銷活動的資格。

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| programName | 必要 | 包含靜態清單的程式名稱 |
| campaignName | 選填 | 如果使用「我的代號」覆寫，這是將使用這些代號的促銷活動名稱。 行銷活動必須在指定的方案內。 |
| listName | 必要 | 要新增銷售機會的Marketo中的靜態清單名稱 |
| importFileHeader | 必要 | 要匯入的潛在客戶的欄標題，包括潛在客戶屬性和我的權杖名稱。 |
| importFileRows->stringItem | 必要 | 逗號分隔值，每個潛在客戶一列 |
| importListMode | 必要 | 有效選項： `UPSERTLEADS`和`LISTONLY` |
| clearList | 選填 | 如果為true，則在匯入之前清除靜態清單；如果為false，則會附加潛在客戶。 |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>17bf0b9e412e58eec836dc557ca9433f666944b6</requestSignature>
      <requestTimestamp>2013-08-05T14:56:58-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsImportToList>
      <programName>Trav-Demo-Program</programName>
      <importFileHeader>Last Name,First Name,Job Title,Company Name,Email Address</importFileHeader>
      <importFileRows>
        <stringItem>Awesomesauce,Developer,Code Slinger,Marketo,dawesomesauce@marketo.com</stringItem>
        <stringItem>Doe,Jane,VP Marketing,Jane Consulting,jdoe@janeconsulting.com</stringItem>
      </importFileRows>
      <importListMode>UPSERTLEADS</importListMode>
      <listName>Trav-Test-List</listName>
      <clearList>false</clearList>
      <campaignName>Batch Campaign Example</campaignName>
    </ns1:paramsImportToList>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successImportToList>
      <result>
        <!-- Possible Values: COMPLETE/PROCESSING/FAILED/CANCELED -->
        <importStatus>PROCESSING</importStatus>
      </result>
    </ns1:successImportToList>
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
  $request->campaignName = "Batch Campaign Example";
  $request->importFileHeader = "Last Name,First Name,Job Title,Company Name,Email Address";
 
 
  $request->importFileRows = array("Awesomesauce,Developer,Code Slinger,Marketo,dawesomesauce@marketo.com","Doe,Jane,VP Marketing,Jane Consulting,jdoe@janeconsulting.com");
  $request->importListMode = "UPSERTLEADS"; // UPSERTLEADS or LISTONLY
  $request->listName = "Trav-Test-List";
  $request->clearList = false;
  $params = array("paramsImportToList" => $request);
 
  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $response = $soapClient->__soapCall('importToList', $params, $options, $authHdr);
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
import java.util.ArrayList;
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
 
public class ImportToList {
 
    public static void main(String[] args) {
        System.out.println("Executing Import To List");
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
            ParamsImportToList request = new ParamsImportToList();
             
            request.setProgramName("Trav-Demo-Program");
            request.setCampaignName("Batch Campaign Example");
            request.setImportFileHeader("Last Name,First Name,Job Title,Company Name,Email Address");
             
            ArrayOfString rows = new ArrayOfString();
            rows.getStringItems().add("Awesomesauce,Developer,Code Slinger,Marketo,dawesomesauce@marketo.com");
            rows.getStringItems().add("Doe,Jane,VP Marketing,Jane Consulting,jdoe@janeconsulting.com");
            request.setImportFileRows(rows);
             
            request.setImportListMode(ImportToListModeEnum.UPSERTLEADS);
            request.setListName("Trav-Test-List");
            request.setClearList(false);
             
            SuccessImportToList result = port.importToList(request, header);
 
 
            JAXBContext context = JAXBContext.newInstance(SuccessImportToList.class);
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
    :import_file_header => "Last Name,First Name,Job Title,Company Name,Email Address",
    :import_file_rows => {
        :string_item => ["Awesomesauce,Developer,Code Slinger,Marketo,dawesomesauce@marketo.com", "Doe,Jane,VP Marketing,Jane Consulting,jdoe@janeconsulting.com"] },
    :import_list_mode => "UPSERTLEADS",
    :list_name => "Trav-Test-List",
    :clear_list => "false",
    :campaign_name => "Batch Campaign Example"
}

response = client.call(:import_to_list, message: request)

puts response
```
