---
title: 活動
feature: REST API
description: 使用Marketo Engage活動REST API來列出活動型別、擷取具有分頁權杖的銷售機會活動，以及處理自訂和資料值的變更。
exl-id: 1e69af23-2b0c-467a-897c-1dcf81343e73
TQID: https://experienceleague.adobe.com/62keaj4uNoxIPCzr9AQzKrIsfuHBvC25knYisZRUvF4
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1758
ht-degree: 0%

---

# 活動

Marketo支援許多與潛在客戶記錄相關的活動型別。 幾乎所有變更、動作或流程步驟都會記錄在潛在客戶的活動記錄中。 您可以透過API擷取這些活動，或在「智慧清單」和「智慧行銷活動」篩選器和觸發器中使用這些活動。

每個活動都有唯一的`id`，並透過`leadId`連線到潛在客戶記錄，這與記錄的ID欄位相對應。 每個活動也有一個`activityDate`。

可用的活動型別因訂閱而異，每種型別都有自己的定義。 `primaryAttributeValueId`和`primaryAttributeValue`的含義取決於活動型別。

使用自訂活動中繼資料API來建立自訂活動型別。 使用新增自訂活動API來新增自訂活動記錄。

大部分活動都會在一段時間後清除。

## 說明

使用[取得活動型別](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)端點來擷取執行個體的可用活動型別及其定義。

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

實際回應包含更多定義。 此範例顯示「填寫表格」活動型別。 其主要屬性「Webform ID」是指已提交表單的Marketo ID，並將活動連結至該資產。

回應也會定義活動型別及其資料型別的每個可能屬性。 如果欄位為空，則會從個別活動記錄中忽略該屬性。

## 查詢

使用[取得潛在客戶活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET)端點來擷取活動。 首先，擷取活動擷取應該開始的日期時間的分頁Token。 在`nextPageToken`查詢引數中傳遞該權杖。

在`activityTypeIds`查詢引數中以逗號分隔的清單傳遞最多10個活動型別ID。

選擇性地使用以下其中一個引數來縮小查詢：

- `listId`將結果限製為特定靜態清單中的記錄。
- `leadIds`將結果限製為最多30個銷售機會的活動，並以逗號分隔清單提供。

>[!CAUTION]
>
>從2026-12-30開始，如果目標清單包含10,000個或更多銷售機會，對`Get Lead Activities`和包含`listId`引數的`Get Lead Changes`端點的呼叫將失敗（錯誤碼1003）。 為避免服務中斷，請確保呼叫的範圍已設定妥當，以避免此限制。 請參閱[移轉指南](migration.md)。

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

第一次呼叫時，請使用Get Paging Token API來取得`nextPageToken`。 對於每個後續呼叫，傳遞上一個回應傳回的`nextPageToken`。 此端點一律會傳回`nextPageToken`。

如果`moreResult`為true，則有更多結果可用。 繼續使用傳回的`nextPageToken`呼叫端點，直到`moreResult`為false。

將`moreResult`設定為true時，API可傳回少於300個活動專案。 在此情況下，請將傳回的`nextPageToken`包含在另一個呼叫中，以擷取較新的活動。

在每個結果陣列專案中，`marketoGUID`字串屬性會取代`id`整數屬性做為唯一識別碼。

### 資料值變更

使用[取得潛在客戶變更](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadChangesUsingGET)端點來擷取潛在客戶欄位的資料值變更記錄。 其介面與Get Lead Activities API有兩個不同之處：

- 端點沒有`activityTypeIds`引數，因為它只傳回資料值變更和新潛在客戶活動。
- 必要的`fields`查詢引數接受您要擷取其變更的逗號分隔欄位清單。

>[!CAUTION]
>
>從2026-12-30開始，如果目標清單包含10,000個或更多銷售機會，對`Get Lead Activities`和包含`listId`引數的`Get Lead Changes`端點的呼叫將失敗（錯誤碼1003）。 為避免服務中斷，請確保呼叫的範圍已設定妥當，以避免此限制。 請參閱[移轉指南](migration.md)。

```http
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

回應中的每個活動都有列示其變更的欄位陣列。 每個變更都會指定欄位的`id`和`name`，以及新值和舊值。

在每個結果陣列專案中，`marketoGUID`字串屬性會取代`id`整數屬性做為唯一識別碼。

### 已刪除的銷售機會

使用[取得已刪除的銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getDeletedLeadsUsingGET)端點從Marketo擷取已刪除的銷售機會活動。

```http
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

在每個結果陣列專案中，`marketoGUID`字串屬性會取代`id`整數屬性做為唯一識別碼。

### 逐頁瀏覽結果

依預設，此區段中的端點一次會傳回300個活動專案。 如果`moreResult`為true，則有更多結果可用。 在後續每次呼叫中傳遞傳回的`nextPageToken`，直到`moreResult`為false。

將`moreResult`設定為True時，端點可傳回少於300個活動專案。 在此情況下，請將傳回的`nextPageToken`包含在另一個呼叫中，以擷取較新的活動。 要求中的URL編碼`nextPageToken`。

## 自訂活動型別

自訂活動的運作方式與標準活動類似，但協力廠商可管理其結構。 自訂活動記錄透過`leadId`連結到潛在客戶記錄，且其主要和次要屬性是使用者定義的。

當自訂活動型別獲得核準時，Marketo會建立對應的智慧列示觸發器和篩選器。 然後，您可以根據目前或歷史自訂活動資料處理銷售機會。

- 自訂活動上限： 10
- 每個自訂活動的最大屬性數： 20

透過[取得銷售機會活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET) API擷取自訂活動資料，與擷取標準活動的方式相同。

## 查詢型別

使用[取得自訂活動型別](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getCustomActivityTypeUsingGET)來擷取在Marketo執行個體中布建之型別的詳細資訊。 使用[描述自訂活動型別](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/describeCustomActivityTypeUsingGET)來擷取特定型別的屬性中繼資料。

標準[Get Activity Types](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)端點也傳回自訂活動中繼資料，但無法識別型別是否為自訂型別。

### 取得型別

```http
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

若要描述型別，請傳遞`apiName`作為路徑引數。 依預設，端點會傳回活動的核准版本。 若要擷取草稿版本，請傳遞選用的`draft=true`引數。

```http
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

每個自訂活動型別都需要顯示名稱、API名稱、觸發程式名稱、篩選器名稱和主要屬性。 使用下列准則來保持型別與Marketo慣例一致，並避免命名衝突：

- **顯示名稱：**&#x200B;簡要說明活動記錄所代表的意義，例如「傳送電子郵件」或「變更資料值」。 使用無限表單，例如「出席事件」。 顯示名稱可接受英數字元、空格和底線，而且必須至少包含一個字母。

- **API名稱：**&#x200B;使用英數字元，最大長度為255。 如果您是LaunchPoint合作夥伴，請在活動型別API名稱前面加上代表名稱空間，以避免與客戶布建的型別發生衝突。 使用小寫或駝峰式大小寫來區分API名稱與其他字串。

- **描述：**&#x200B;對於行為不明顯的活動，請說明活動型別相對於潛在客戶的代表內容。

- **觸發程式名稱：**&#x200B;以第三人稱現在時態提供人類看得懂的唯一名稱，例如「出席一個事件」。 LaunchPoint合作夥伴應包含其公司名稱，例如「Attends網路研討會 — Acme公司」。

- **篩選器名稱：**&#x200B;以第三人稱過去式提供人類看得懂的唯一名稱，例如「已出席活動」。 LaunchPoint合作夥伴應包含其公司名稱，例如「已出席的網路研討會 — Acme公司」。

- **主要屬性：**&#x200B;選取活動型別的最重要欄位。 若為「已出席的活動」活動，此欄位是活動名稱。 根據預設，主要屬性會在活動型別的每個觸發程式或篩選器中顯示為引數。 其值也會顯示在人員的活動記錄中，不需要向下切入活動。

新的自訂活動型別會建立為草稿。 在新增該型別的活動記錄之前核准型別。 更新會套用至草稿版本，而且必須先經過核准，才會出現在即時版本中。 在自訂活動型別獲得核准且使用中後，無法變更前面的欄位。

建立型別時，說明引數是選用的。 必要的引數為`apiName`、`name`、`triggerName`、`filterName`和`primaryAttribute`。

```http
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

若要更新型別，請傳遞必要的apiName作為路徑引數。 可在要求內文中提供其他欄位。

```http
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

使用核准自訂活動型別、捨棄自訂活動型別草稿和刪除自訂活動型別來管理型別，就像使用標準Marketo資產一樣。

## 自訂活動型別屬性

每個自訂活動型別可以有0到20個次要屬性。 次要屬性可使用任何有效的Marketo欄位型別。 從父型別個別新增、更新和移除次要屬性。

您可以在活動型別正在使用時編輯屬性，然後核准變更。 核准後建立的活動會使用新的次要屬性集。 變更不會回溯套用至該型別的現有活動。

移除屬性也會移除其在對應篩選器中的可用性。

更新次要屬性清單時，會將每個屬性的API名稱當作主索引鍵。 若要變更API名稱，請刪除屬性，然後使用所需的API名稱再次新增該屬性。

屬性的有效資料型別為：字串、布林值、整數、浮點數、連結、電子郵件、貨幣、日期、日期時間、電話、文字。

在變更活動型別的主要屬性之前，請先將`isPrimary`設定為false，將現有的主要屬性降級。

### 建立屬性

若要建立屬性，請傳遞必要的`apiName`路徑引數。 `name`和`dataType`引數也是必要的。 說明和`isPrimary`引數是選用的。

```http
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

更新屬性時，屬性`apiName`是主索引鍵，而且必須已經存在。 您無法利用更新變更`apiName`。

```http
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

若要刪除屬性，請為自訂活動傳遞必要的`apiName`路徑引數。 也可以傳遞必要的屬性引數，做為屬性物件的陣列。 每個物件都必須包含自訂活動型別的`apiName`引數。

```http
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

自訂活動是個人記錄歷史活動的一次寫入記錄。 Marketo管理員可以在Marketo中管理其結構，或API整合可以遠端管理它。

使用[新增自訂活動](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/addCustomActivityUsingPOST)端點將自訂活動新增到潛在客戶記錄。 `leadId`欄位會將每個活動與潛在客戶建立關聯。 檢視潛在客戶活動記錄中的自訂活動，或透過指定自訂活動型別ID以透過「取得潛在客戶活動」擷取自訂活動。

對同一個人不需要更新或覆寫的相關資料使用自訂活動。 例如，將事件出席記錄為「已出席事件」活動。

針對可變更的人員相關記錄（例如學生註冊）使用自訂物件。 可以更新自訂物件，但不能更新自訂活動。

輸入成員是活動物件的陣列。 您一次最多可以提交300筆活動記錄。

需要`leadId`、`activityDate`、`activityTypeId`、`primaryAttributeValue`和屬性成員。 屬性陣列必須包含非主要屬性。 請為其指定名稱（欄位名稱）或apiName （API名稱），並為要設定的值指定值。

```http
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

活動端點的逾時為30秒，以下端點除外：

- 取得分頁權杖： 300秒
- 新增自訂活動：90秒
