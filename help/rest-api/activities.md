---
title: 活動
feature: REST API
description: 用於管理Marketo Engage活動的API。
exl-id: 1e69af23-2b0c-467a-897c-1dcf81343e73
source-git-commit: 6baf62bc8881470eca597899e3228c377fb597d0
workflow-type: tm+mt
source-wordcount: '2029'
ht-degree: 0%

---

# 活動

Marketo允許與潛在客戶記錄相關的各種活動型別。  幾乎所有變更、動作或流程步驟都會針對潛在客戶的活動記錄進行記錄，並可透過API擷取，或在「智慧清單」和「智慧行銷活動」篩選器和觸發器中運用。  活動一律透過leadId與潛在客戶記錄建立關聯，對應至記錄的Id欄位，並具有自己的唯一ID。

潛在活動型別非常多，可能因訂閱而異，每種都有獨特的定義。 雖然每個活動都有自己的唯一`id`、`leadId`和`activityDate`，但`primaryAttributeValueId`和`primaryAttributeValue`值在含義上有所不同。

Marketo也允許透過自訂活動中繼資料API建立自訂活動型別。 新增自訂活動可透過新增自訂活動API完成。

大部分活動都會在一段時間後清除。

## 說明

若要擷取執行個體的可用型別及其定義清單，您可以使用[取得活動型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)端點。

```
GET /rest/v1/activities/types.json
```

```json
  "requestId": "6e78#148ad3b76f1",
  "success": true,
  "result": [
    {
      "id": 2,
      "name": "Fill Out Form",
      "description": "User fills out and submits form on web page",
      "primaryAttribute": {
        "name": "Webform ID",
        "dataType": "integer"
      },
      "attributes": [
        {
          "name": "Client IP Address",
          "dataType": "string"
        },
        {
          "name": "Form Fields",
          "dataType": "text"
        },
        {
          "name": "Query Parameters",
          "dataType": "string"
        },
        {
          "name": "Referrer URL",
          "dataType": "string"
        },
        {
          "name": "User Agent",
          "dataType": "string"
        },
        {
          "name": "Webpage ID",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

現實世界的回應包括更多的定義。 在此範例中，顯示的型別是「填寫表單」，其主要屬性為「Webform ID」，這指回所填寫表單的Marketo ID，且可用來與Marketo中的該特定資產建立關聯。 此外，對於此型別的特定活動記錄及其資料型別，每個可能的屬性都有定義。 請注意，如果欄位為空，則個別活動記錄中會忽略該特定屬性。

## 查詢

若要從Marketo擷取活動，請呼叫[取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET)端點。 您必須先擷取要開始擷取活動的日期時間的分頁Token。 然後在`nextPageToken`查詢引數中傳遞分頁權杖。 此外，您最多可在`activityTypeIds`查詢引數中傳入10個活動型別ID作為逗號分隔清單。

您可以選擇加入listId查詢引數，將搜尋範圍縮小至特定靜態清單中所包含的記錄，或是加入leadIds查詢引數，僅從指定的銷售機會集合中搜尋活動。 您最多可以將30個銷售機會Id以逗號分隔清單的形式傳遞。

```
GET /rest/v1/activities.json?activityTypeIds=1&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3I3LCWXH3Y6IIZ7YSGQLXHCPVE5Q====
```

```json
{
  "requestId": "24fd#15188a88d7f",
  "result": [
    {
      "id": 102988,
      "marketoGUID": "102988",
      "leadId": 1,
      "activityDate": "2023-01-16T23:32:19Z",
      "activityTypeId": 1,
      "primaryAttributeValueId": 71,
      "primaryAttributeValue": "localhost/munchkintest2.html",
      "attributes": [
        {
          "name": "Client IP Address",
          "value": "10.0.19.252"
        },
        {
          "name": "Query Parameters",
          "value": ""
        },
        {
          "name": "Referrer URL",
          "value": ""
        },
        {
          "name": "User Agent",
          "value": "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
        },
        {
          "name": "Webpage URL",
          "value": "/munchkintest2.html"
        }
      ]
    }
  ],
  "success": true,
  "nextPageToken": "WQV2VQVPPCKHC6AQYVK7JDSA3J62DUSJ3EXJGDPTKPEBFW3SAVUA====",
  "moreResult": false
}
```

第一次呼叫時，請使用[取得分頁權杖API]取得`nextPageToken`。 對於對此端點的後續呼叫，請使用回應中的`nextPageToken returned`。 此端點一律會傳回`the nextPageToken`。

如果`moreResult`屬性為true，則表示有更多結果可用。 繼續呼叫此端點，直到`moreResult`屬性傳回false，這表示沒有可用的結果。 從此API傳回的`nextPageToken`應一律重複用於此呼叫的下一個反複專案。

在某些情況下，此API的回應可能會少於300個活動專案，但也會將`moreResult`屬性設定為true。  這表示有更多活動可傳回，而且將傳回的`nextPageToken`納入後續呼叫中，可查詢端點以取得較新的活動。

請注意，在每個結果陣列專案中，`id`整數屬性會由`marketoGUID`字串屬性取代為唯一識別碼。 

### 資料值變更

針對資料值變更活動，提供專門的活動API版本。 [Get Lead Changes](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET)端點只傳回資料值變更記錄到Lead欄位的活動。 此介面與Get Lead Activities API相同，有兩個差異：

* 沒有`activityTypeIds`引數，因為端點只會傳回資料值變更和新潛在客戶活動。
* `fields`查詢引數為必要項，您可以在此傳遞逗號分隔的欄位清單，以指出您要擷取變更的欄位。

```
GET /rest/v1/activities/leadchanges.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&fields=firstName,lastName,department
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 1078,
      "marketoGUID": "1078",
      "leadId": 775,
      "activityDate": "2014-09-17T22:31:49+0000",
      "activityTypeId": 13,
      "fields": [
        {
          "id": 48,
          "name": "firstName",
          "newValue": "FirstName_6176",
          "oldValue": "FirstName_4914"
        }
      ],
      "attributes": [
        {
          "name": "Reason",
          "value": "Web service API"
        },
        {
          "name": "Source",
          "value": "Web service API"
        },
        {
          "name": "Lead ID",
          "value": 775
        }
      ]
    }
  ]
}
```

回應中的每個活動都有一個欄位陣列，包括活動中的變更清單，這會指定已變更欄位的`id`和`name`，以及與變更相關的新舊值。

請注意，在每個結果陣列專案中，`id`整數屬性會由`marketoGUID`字串屬性取代為唯一識別碼。

### 已刪除的銷售機會

還有一個特殊的端點[取得已刪除的銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET)，用於從Marketo擷取已刪除的活動。

```
GET /rest/v1/activities/deletedleads.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 2,
      "marketoGUID": "2",
      "leadId": 6,
      "activityDate": "2013-09-26T06:56:35+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 6,
      "primaryAttributeValue": "Owyliphys Iledil",
      "attributes": []
    },
    {
      "id": 3,
      "marketoGUID": "3",
      "leadId": 9,
      "activityDate": "2013-12-28T00:39:45+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 4,
      "primaryAttributeValue": "First Last",
      "attributes": []
    }
  ]
}
```

請注意，在每個結果陣列專案中，`id`整數屬性會由`marketoGUID`字串屬性取代為唯一識別碼。

### 逐頁瀏覽結果

依預設，本節中提到的端點一次會傳回300個活動專案。  如果`moreResult`屬性為true，則有更多結果可用。 呼叫端點，直到`moreResult`屬性傳回false為止，這表示沒有其他可用的結果。 從此端點傳回的`nextPageToken`應一律重複用於此呼叫的下一個反複專案。

在某些情況下，此端點可能會以少於300個活動專案回應，但也會將`moreResult`屬性設定為true。  這表示有其他活動可傳回，而且將傳回的`nextPageToken`納入後續呼叫中，可查詢端點以取得較新的活動。 請注意，`nextPageToken`需要在請求中進行URL編碼。

## 自訂活動型別

自訂活動的功能與標準活動類似，只是結構描述是由第三方管理，而非Marketo。 自訂活動的執行個體會透過`leadId`連結潛在客戶記錄，就像標準活動一樣，但主要和次要屬性都是任意定義的。 在核准自訂活動型別時，會建立對應的智慧清單觸發器和篩選器，以便可以根據目前或歷史自訂活動資料處理潛在客戶。

* 自訂活動最大數量：10
* 每個自訂活動的屬性數上限： 20

擷取自訂活動資料的方式與標準活動相同，透過[取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET) API。

## 查詢型別

除了標準Get Activity Types端點之外，[Get Custom Activity Types](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getCustomActivityTypeUsingGET)和[Describe Custom Activity Type](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/describeCustomActivityTypeUsingGET)端點也會傳回在Marketo執行個體中布建之活動型別的詳細資訊，以及有關指定型別之屬性的中繼資料。 一般[Get活動型別](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)仍會傳回有關自訂活動的中繼資料，但不會指出指定型別是否為自訂型別。

### 取得型別

```
GET /rest/v1/activities/external/types.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved"
    }
  ]
}
```

### 說明型別

對於型別說明，您必須傳遞`apiName`作為路徑引數。 依預設，您會取得活動的核准版本。 您可以選擇傳遞`draft=true`引數以擷取活動的草稿版本。

```
GET /rest/v1/activities/external/type/{apiName}/describe.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

## 建立型別

每個自訂活動型別都需要顯示名稱、API名稱、觸發程式名稱、篩選器名稱和主要屬性。

為了確保型別與Marketo慣例的一致性並避免衝突，在建立型別時請務必遵循一些准則：

**顯示名稱：**&#x200B;活動型別的顯示名稱應簡短描述活動記錄所代表的內容，例如「傳送電子郵件」或「變更資料值」。 這些名稱通常應採用不定式，即「出席事件」。  顯示名稱接受英數字元、空格和底線。 顯示名稱必須至少包含一個字母。

**API名稱：** API名稱包含英數字元（長度上限為255）。 如果您是LaunchPoint合作夥伴，應在活動型別API名稱前面加上代表名稱空間。 這是為了避免與客戶布建的型別發生衝突。  慣例是使用所有小寫或駝峰式大小寫來協助區分其他文字字串。

**描述：**&#x200B;對於可能具有非明顯行為的活動，應包含活動型別相對於潛在客戶所代表之內容的描述。

**觸發程式名稱：**&#x200B;每個活動型別都必須有唯一的、人類可讀的觸發程式名稱。 觸發程式名稱應以第三人稱現在時態顯示，例如「出席事件」。 LaunchPoint合作夥伴應在活動中加入其公司名稱，例如「Attends網路研討會 — Acme公司」。

**篩選器名稱：**  每個活動型別都必須有人類看得懂的唯一篩選器名稱。 篩選器名稱應為第三人稱過去時，例如「已出席活動」。 LaunchPoint合作夥伴應在活動中加入其公司名稱，即「已出席的網路研討會 — Acme公司」。

**主要屬性：**&#x200B;自訂活動的主要屬性應該是活動型別的最重要欄位。 例如，對於「已出席事件」活動，這會是事件的名稱。 預設情況下，主要屬性會作為引數包含在該活動型別的每個觸發程式或篩選器中，該值會顯示在人員記錄的活動日誌中，而無需向下鑽研至活動。

建立自訂活動時，會建立為草稿，必須先獲得核准，才能用來新增該型別的活動記錄。 所有更新都會隱含套用至型別的草稿版本。 若要反映此型別即時版本中的變更，必須將其核准。 當自訂活動型別獲得核准且使用中時，對上述欄位可能不會進行任何變更。

建立型別時，描述引數是選用的，而下列所有引數都是必要引數： `apiName`、`name`、`triggerName`、`filterName`、`primaryAttribute`。

```
POST /rest/v1/activities/external/type.json
```

```json
{
  "apiName": "attendConference",
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attends Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 更新型別

更新型別非常類似，只是apiName是作為path引數唯一需要的引數。

```
POST /rest/v1/activities/external/type/{apiName}.json
```

```json
{
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attend Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 核准型別

您可以使用核准自訂活動型別、捨棄自訂活動型別草稿和刪除自訂活動型別來管理型別，就像標準Marketo資產一樣。


## 自訂活動型別屬性

每個自訂活動型別可以有0到20個次要屬性。 次要屬性可具有Marketo欄位的任何有效欄位型別。 它們會從父型別中分別新增、更新和移除，但在活動型別使用中時可以編輯，然後核准。 在即時型別上編輯欄位時，則核准後建立的該型別的所有活動都會有新的次要屬性集。 變更將不會回溯套用至共用該型別的現有活動。

移除屬性時請務必謹慎，因為這會影響其可用於相應篩選器的可用性。

次要屬性清單的更新會使用每個屬性的API名稱作為主索引鍵。 屬性的API名稱不能變更，必須刪除並使用所需的API名稱再次新增。

屬性的有效資料型別為：字串、布林值、整數、浮點數、連結、電子郵件、貨幣、日期、日期時間、電話、文字。

變更活動型別的主要屬性時，應該先將`isPrimary`設定為false，將任何現有的主要屬性降級。

### 建立屬性

建立屬性需要必要的`apiName`路徑引數。 `name`和`dataType`引數也是必要的。` The description and` `isPrimary`引數是選用的。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/create.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendees",
      "name": "Number of Attendees",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### 更新屬性

執行屬性更新時，屬性的`apiName`是主索引鍵。 `apiName`引數必須存在，更新才能成功（也就是說，您無法使用更新變更`apiName`引數）。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/update.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendee",
      "name": "Number of Attendee",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendee",
          "name": "Number of Attendee",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### 刪除屬性

刪除屬性需要自訂活動API名稱的必要`apiName`路徑引數。  屬性引數也是必要的，它是屬性物件的陣列。  每個物件都必須包含自訂活動型別API名稱的`apiName`引數。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/delete.json
```

```json
{ "attributes":[ { "apiName":"conferenceDate" }, { "apiName":"numberOfAttendees" } ] }
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 新增自訂活動

自訂活動是與Marketo中個別人員記錄相關的歷史活動的一次性寫入記錄。 這些活動有一個結構描述，可由Marketo管理員管理或透過API整合從遠端管理。 透過[新增自訂活動](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/addCustomActivityUsingPOST)端點將自訂活動新增到潛在客戶記錄中，並透過其`leadId`欄位與每個潛在客戶記錄相關聯。 自訂活動可透過潛在客戶的活動記錄在使用者介面中檢視，或透過指定自訂活動的型別ID透過「取得潛在客戶活動」端點來擷取。

自訂活動適用於記錄與單一人員記錄相關的資料，且不需要更新或覆寫。 例如，將出席活動的人記錄為「已出席活動」活動。 對於與可能變更之人員（例如學生註冊）相關的記錄，應改用自訂物件，因為這些物件可以更新，而自訂活動則可能未更新。

輸入成員是活動物件的陣列。 一次最多可提交300個活動記錄。

需要`leadId`、`activityDate`、`activityTypeId`、`primaryAttributeValue`和屬性成員。 屬性陣列必須包含非主要屬性。 可使用name （欄位名稱）或apiName （API名稱）以及與您設定之值對應的值來指定此專案。

```
POST /rest/v1/activities/external.json
```

```json
{
  "input": [
    {
      "leadId": 1001,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 1200,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 3000,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Contest Form",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 50,
      "marketoGUID": "50",
      "status": "added"
    },
    {
      "id": 51,
      "marketoGUID": "51",
      "status": "added"
    },
    {
      "status": "skipped",
      "errors": [
        {
          "code": "1004",
          "message": "Lead not found"
        }
      ]
    }
  ]
}
```

## 逾時

除非在下面註明，否則活動端點的逾時值為30秒。

* 取得分頁權杖： 300秒 
* 新增自訂活動：90秒
