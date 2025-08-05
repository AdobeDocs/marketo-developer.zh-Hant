---
title: getleadchanges
feature: SOAP
description: getLeadChanges SOAP呼叫
exl-id: 23445684-d8d9-407b-8f19-cb69e806795c
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '401'
ht-degree: 3%

---

# getleadchanges

此API類似於`getLeadActivity`，只是它同時會在多個潛在客戶上運作。 此操作會檢查已建立的新銷售機會、銷售機會欄位更新和其他活動。

結果包含導致變更以及[資料流位置](stream-position.md)在大型結果集中分頁的活動。

您必須包含輸入引數，以識別要在結果中傳回的活動篩選器。 如果您想要所有活動，可傳遞空白值。 如需多個活動篩選器，請傳入活動篩選器清單。

某些活動型別範例為：「造訪網頁」、「填寫表單」和「按一下連結」。

在SOAP API 2_2版之後，您可以包含`leadSelector`。

對於`LastUpdateAtSelector`，`oldestUpdatedAt`值將對應至`oldestCreatedAt`中的`startPosition`值。 而`latestUpdatedAt`值會對應至`latestCreatedAt`中的`startPosition`值。

注意： `LeadKeySelector`中支援的潛在客戶數量限製為100。 如果銷售機會數量超過100，API會擲回錯誤的引數例外狀況並傳回SOAP錯誤。

## 請求

| 欄位名稱 | 必要/選用 | 說明 |
| --- | --- | --- |
| activityFilter->includeAttributes->activityType | 選用（已棄用）改用`activityNameFilter` | 將回應限製為僅包含指定的活動型別。 請參閱WSDL以瞭解所有活動型別。 |
| activityFilter->excludeAttributes->activityType | 選用 | 限制回應，以排除指定的活動型別。 請參閱WSDL以瞭解所有活動型別。 注意：您無法在同一呼叫中同時指定`includeAttributes`和`excludeAttributes`。 |
| activityNameFilter | 選用 | 將回應限製為僅包含指定的活動篩選器。 |
| batchSize | 選用 | 要傳回的最大記錄數。 系統限製為1,000或`batchSize`，以較小者為準。 |
| startPosition | 必要 | 用於分頁顯示大量活動回應。 |
| startPosition->offset | 選用 | 位移值是由先前的呼叫回應欄位newStartPosition->offset傳回。 |
| startPosition->oldestCreatedAt | 選用 | 用於篩選結果以僅包含自oldestCreatedAt以來建立的潛在客戶的時間戳記。 注意：您可以使用`LastUpdateAtSelector->oldestUpdatedAt`時間戳記來指定`oldestCreatedAt`。 |
| startPosition->activityCreatedAt | 選用 | 用於篩選結果以僅包含自activityCreatedAt以來具有活動的潛在客戶的時間戳記。 注意：您可以使用`LastUpdateAtSelector->latestUpdatedAt`時間戳記來指定`activityCreatedAt`。 |
| 銷售機會選擇器 | 選用 | 可以是下列3種型別之一： `LeadKeySelector`、`StaticListSelector`、`LastUpdateAtSelector` |
| LeadKeySelector： leadSelector->keyType | 必要 | 您要查詢的ID型別。 值包括`IDNUM`、`COOKIE`、`EMAIL`、`LEADOWNEREMAIL`、`SFDCACCOUNTID`、`SFDCCONTACTID`、`SFDCLEADID`、`SFDCLEADOWNERID`、`SFDCOPPTYID`。 |
| LeadKeySelector： leadSelector->keyValues->stringItem | 必要 | 索引鍵值清單。 即&quot;<lead@email.com>&quot; |
| StaticListSelector： leadSelector->staticListName | `leadSelector->staticListId`存在時為選用 | 靜態清單的名稱 |
| StaticListSelector： leadSelector->staticListId | `leadSelector->staticListName`存在時為選用 | 靜態清單的ID |

## 請求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>59e88471266a7495590941586ef6a96849ea8e11</requestSignature>
      <requestTimestamp>2013-08-02T17:22:53-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsGetLeadChanges>
      <startPosition>
        <oldestCreatedAt>2013-08-01T00:13:13+00:00</oldestCreatedAt>
      </startPosition>
      <activityNameFilter>
        <stringItem>Visit Webpage</stringItem>
        <stringItem>Click Link</stringItem>
      </activityNameFilter>
      <batchSize>10</batchSize>
    </ns1:paramsGetLeadChanges>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 回應XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successGetLeadChanges>
      <result>
        <returnCount>10</returnCount>
        <remainingCount>576</remainingCount>
        <newStartPosition>
          <latestCreatedAt xsi:nil="true" />
          <oldestCreatedAt>2013-08-01T00:13:13+00:00</oldestCreatedAt>
          <activityCreatedAt xsi:nil="true" />
          <offset>ID:1086173</offset>
        </newStartPosition>
        <leadChangeRecordList>
          <leadChangeRecord>
            <id>1037189</id>
            <activityDateTime>2013-08-01T00:00:09-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Lead Score</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>402</attrValue>
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>403</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Japanese form fillout.test time zone action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Step ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>19582</attrValue>
              </attribute>
              <attribute>
                <attrName>Choice Number</attrName>
                <attrType xsi:nil="true" />
                <attrValue>0</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>84105</attrValue>
              </attribute>
            </activityAttributes>
            <campaign>Japanese form fillout.test time zone</campaign>
            <mktPersonId>84105</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1037190</id>
            <activityDateTime>2013-08-01T02:37:17-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Marketo Social Facebook Reach</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Marketo Social Facebook Reach</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>574</attrValue>
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>571</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Form fill-out, URL: <unknown></attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1086478</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1086478</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1037193</id>
            <activityDateTime>2013-08-01T11:19:26-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Lead Score</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Visit any web page action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Step ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>11156</attrValue>
              </attribute>
              <attribute>
                <attrName>Choice Number</attrName>
                <attrType xsi:nil="true" />
                <attrValue>0</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090235</attrValue>
              </attribute>
            </activityAttributes>
            <campaign>Visit any web page</campaign>
            <mktPersonId>1090235</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1082831</id>
            <activityDateTime>2013-08-01T13:10:17-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>First Name</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>First Name</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>George</attrValue>
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Travis</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>SOAP API</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1089965</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1082843</id>
            <activityDateTime>2013-08-01T13:10:19-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Last Name</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Last Name</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>of the Jungle</attrValue>
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Kaufman</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>SOAP API</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1089965</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1086153</id>
            <activityDateTime>2013-08-01T13:21:51-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Urgency</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Urgency</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>25</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Operational Campaign - ML action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Source</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead update</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090236</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1090236</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1086166</id>
            <activityDateTime>2013-08-01T13:21:53-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Relative Urgency</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Relative Urgency</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Operational Campaign - ML action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Source</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead update</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090236</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1090236</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1086167</id>
            <activityDateTime>2013-08-01T13:21:53-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Relative Score</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Relative Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Operational Campaign - ML action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Source</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead update</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090236</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1090236</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1086169</id>
            <activityDateTime>2013-08-01T13:21:53-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Priority</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Priority</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>70</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Operational Campaign - ML action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Source</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead update</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090236</attrValue>
              </attribute>
            </activityAttributes>
            <mktPersonId>1090236</mktPersonId>
          </leadChangeRecord>
          <leadChangeRecord>
            <id>1086173</id>
            <activityDateTime>2013-08-01T13:21:53-05:00</activityDateTime>
            <activityType>Change Data Value</activityType>
            <mktgAssetName>Lead Score</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Attribute Name</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Lead Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Old Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>New Value</attrName>
                <attrType xsi:nil="true" />
                <attrValue>25</attrValue>
              </attribute>
              <attribute>
                <attrName>Reason</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Changed by Smart Campaign Operational Campaign - ML action Change Score</attrValue>
              </attribute>
              <attribute>
                <attrName>Step ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12259</attrValue>
              </attribute>
              <attribute>
                <attrName>Choice Number</attrName>
                <attrType xsi:nil="true" />
                <attrValue>0</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090236</attrValue>
              </attribute>
            </activityAttributes>
            <campaign>Operational Campaign - ML</campaign>
            <mktPersonId>1090236</mktPersonId>
          </leadChangeRecord>
        </leadChangeRecordList>
      </result>
    </ns1:successGetLeadChanges>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 範常式式碼 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = "";  // CHANGE ME
$marketoSecretKey   = ""; // CHANGE ME
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
$options = array("connection_timeout" => 600, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}

// Create Request
$params = new stdClass();
$filter = new stdClass();

$keyValues = array("Visit Webpage", "Click Link");
$filter->stringItem = $keyValues;
$params->activityNameFilter = $filter;

$streamPos = new stdClass();
$streamPos->oldestCreatedAt = "2013-08-01T00:13:13+00:00";
$params->startPosition = $streamPos;
$params->batchSize = 10;
$authHdr = new SoapHeader($marketoNameSpace, 'AuthenticationHeader', $attrs);
$client = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
$doPage = true;
while($doPage) {
  $success = $client->__soapCall('getLeadChanges', array($params), null, $authHdr);
  $result = $success->result;
  $doPage = ($result->remainingCount > 0);
  $streamPos = $result->newStartPosition;
  $params->startPosition = $streamPos;
  if ($debug) {
    print "RAW request:\n" .$client->__getLastRequest() ."\n";
    print "RAW response:\n" .$client->__getLastResponse() ."\n";
  }
}
return;
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
import javax.xml.datatype.XMLGregorianCalendar;
import javax.xml.datatype.DatatypeFactory;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import java.util.GregorianCalendar;


public class GetLeadChanges {

    public static void main(String[] args) {
        System.out.println("Executing Get Lead Changes");
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
            String encryptString = requestTimestamp + marketoUserId;

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
            ParamsGetLeadChanges request = new ParamsGetLeadChanges();

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> batchSize = objectFactory.createParamsGetLeadActivityBatchSize(10);
            request.setBatchSize(batchSize);

            ArrayOfString activities = new ArrayOfString();
            activities.getStringItems().add("Visit Webpage");
            activities.getStringItems().add("Click Link");

            JAXBElement<ArrayOfString> activityFilter = objectFactory.createParamsGetLeadChangesActivityNameFilter(activities);
            request.setActivityNameFilter(activityFilter);

            // Create oldestCreateAt timestamp from 5 days ago
            GregorianCalendar gc = new GregorianCalendar();
            gc.setTimeInMillis(new Date().getTime());
            gc.add( GregorianCalendar.DAY_OF_YEAR, -5);

            DatatypeFactory factory = DatatypeFactory.newInstance();
            JAXBElement<XMLGregorianCalendar> oldestCreateAtValue =objectFactory.createStreamPositionOldestCreatedAt(factory.newXMLGregorianCalendar(gc));

            StreamPosition sp = new StreamPosition();
            sp.setOldestCreatedAt(oldestCreateAtValue);
            request.setStartPosition(sp);

            SuccessGetLeadChanges result = port.getLeadChanges(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessGetLeadChanges.class);
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
    :start_position => {
        :oldest_created_at => "2013-08-01T00:13:13+00:00" },
    :activity_name_filter => {
        :stringItem => ["Visit Webpage", "Click Link"] },
    :batch_size => "10"
}

response = client.call(:get_lead_changes, message: request)

puts response
```
