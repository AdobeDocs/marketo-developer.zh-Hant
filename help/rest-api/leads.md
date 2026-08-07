---
title: 銷售機會
feature: REST API
description: 探索Marketo銷售機會REST API功能，包括說明、依ID或篩選器查詢、預設欄位、限制和擷取ECID。
exl-id: 0a2f7c38-02ae-4d97-acfe-9dd108a1f733
TQID: https://experienceleague.adobe.com/jZ-ecWTmHwq9gvp4fMaeuuGba6cgwYx0QCCyfkrEDHQ
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 2733
ht-degree: 3%

---

# 銷售機會

[潛在客戶端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads)

Marketo Leads API支援對潛在客戶記錄執行CRUD作業。 您也可以修改潛在客戶在靜態清單和計畫中的成員資格，並啟動潛在客戶的Smart Campaign處理。

## 說明

使用說明銷售機會來擷取可透過REST API使用的欄位，以及每個欄位的中繼資料：

- 資料類型
- REST API名稱
- 長度（如適用）
- 唯讀狀態
- 易記標籤

說明是欄位可用性和中繼資料的主要信任來源。

### 請求

```http
GET /rest/v1/leads/describe.json
```

### 回應

```json
{
   "requestId":"37ca#1475b74e276",
   "success":true,
   "result":[
      {
         "id":2,
         "displayName":"Company Name",
         "dataType":"string",
         "length":255,
         "rest":{
            "name":"company",
            "readOnly":false
         },
         "soap":{
            "name":"Company",
            "readOnly":false
         }
      }
}
```

實際回應在結果陣列中包含更多欄位。 每個專案代表潛在客戶記錄上可用的欄位，並至少包含id、displayName和資料型別。

只有當欄位對對應的API有效時，REST子物件才會出現。 `readOnly`屬性指出對應的API是否可以更新欄位。 出現時，length屬性會提供最大欄位長度，而dataType屬性會提供欄位的資料型別。

## 查詢

使用下列兩種主要方法之一來擷取銷售機會：

- 依ID取得銷售機會，會以一個銷售機會ID作為路徑引數，並傳回一個銷售機會記錄。
- 依篩選器型別取得銷售機會會尋找其選取欄位符合其中一個提供值的記錄。

針對「依ID取得銷售機會」，您可選擇傳入欄位引數，包含要傳回的欄位名稱逗號分隔清單。 如果要求省略欄位，回應會包含`email`、`updatedAt`、`createdAt`、`lastName`、`firstName`和`id`。 如果未傳回要求的欄位，則其值會隱含為空值。

### 請求

```http
GET /rest/v1/lead/{id}.json
```

### 回應

```json
{
   "requestId": "10226#14d3049e51b",
   "success": true,
   "result": [
      {
         "id": 318581,
         "updatedAt":"2015-05-07T11:47:30-08:00"
         "lastName": "Doe",
         "email": "jdoe@marketo.com",
         "createdAt": "2015-05-01T16:47:30-08:00",
         "firstName": "John"
      }
   ]
}
```

Get Lead by Id一律會在結果陣列的第一個位置傳回一個記錄。

依篩選型別取得銷售機會會傳回相同的記錄型別，每頁最多可傳回300筆記錄。 需要`filterType`和`filterValues`查詢引數。

`filterType`接受任何自訂欄位和最常用的欄位。 呼叫`Describe2`端點以擷取`filterType`允許的可搜尋欄位。 依自訂欄位搜尋時，支援的資料型別為`string`、`email`和`integer`。 使用Describe方法擷取欄位詳細資訊，例如說明和型別。

`filterValues`接受最多300個逗號分隔值。 此呼叫會傳回所選潛在客戶欄位符合其中一個值的記錄。 如果超過1,000個銷售機會符合篩選器，則API會傳回「1003，有太多結果符合篩選器」。

如果GET要求總數超過8KB，則API會在RFC 7231下傳回「414， URI過長」。 若要解決此限制，請將GET變更為POST，新增_method=GET引數，並將查詢字串放入要求內文中。

### 請求

```http
GET /rest/v1/leads.json?filterType=id&filterValues=318581,318592
```

### 回應

```json
{
    "requestId": "12951#15699db5c97",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2016-05-17T22:11:45Z",
            "lastName": "Lincoln",
            "email": "abe@usa.gov",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "Abraham"
        },
        {
            "id": 318592,
            "updatedAt": "2016-05-17T22:20:51Z",
            "lastName": "Washington",
            "email": "george@usa.gov",
            "createdAt": "2015-04-06T16:29:21Z",
            "firstName": "George"
        }
    ],
    "success": true
}
```

此呼叫傳回其ID符合`filterValues`中值的記錄。

如果沒有相符的記錄，則回應會指出成功，並包含空的結果陣列。

### 回應

```json
{
"requestId": "177a1#1578b643357",
"result": [],
"success": true
}
```

「依ID取得銷售機會」和「依篩選型別取得銷售機會」都接受欄位查詢引數，該引數包含以逗號分隔的API欄位清單。 當欄位出現時，每個回應記錄都會包含列出的欄位。 如果省略，回應會包含`id`、`email`、`updatedAt`、`createdAt`、`firstName`和`lastName`。

## ADOBE ECID

啟用Adobe Experience Cloud受眾共用時，Cookie同步會將Adobe Experience Cloud ID (ECID)值與Marketo銷售機會建立關聯。 若要使用之前的潛在客戶擷取方法擷取關聯的ECID值，請在欄位引數中加入`ecids`。 例如 `&fields=email,firstName,lastName,ecids`。

## 建立和更新

潛在客戶API可以建立、更新及刪除潛在客戶記錄。 建立和更新作業使用相同的端點，並在要求中定義作業型別。 一個請求最多可建立或更新300筆記錄。

>[!NOTE]
>
> 不支援使用[同步銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncLeadUsingPOST)端點更新公司欄位。 請改用[同步處理公司](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncCompaniesUsingPOST)端點。

>[!NOTE]
>
> 在人員記錄上建立或更新電子郵件值時，電子郵件位址列位僅支援ASCII字元。

### 請求

```http
POST /rest/v1/leads.json
```

### 內文

```json
{
   "action":"createOnly",
   "lookupField":"email",
   "input":[
      {
         "email":"kjashaedd-1@klooblept.com",
         "firstName":"Kataldar-1",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-2@klooblept.com",
         "firstName":"Kataldar-2",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-3@klooblept.com",
         "firstName":"Kataldar-3",
         "postalCode":"04828"
      }
   ]
}
```

### 回應

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "id":50,
         "status":"created"
      },
      {
         "id":51,
         "status":"created"
      },
      {
         "id":52,
         "status":"created"
      }
   ]
}
```

此請求使用兩個重要欄位：

- `action`指定作業型別： `createOrUpdate`、`createOnly`、`updateOnly`或`createDuplicate`。 如果省略，則預設為`createOrUpdate`。
- `lookupField`指定動作為`createOrUpdate`或`updateOnly`時的金鑰。 如果省略，則預設為`email`。

依預設，作業使用預設的分割區。 選擇性`partitionName`引數只有在動作為`createOnly`或`createOrUpdate`時才有效。 若要使用`partitionName`作為額外的重複資料刪除條件，請將其納入自訂重複資料刪除規則的來源型別。

在更新期間，如果潛在客戶不存在於指定的資料分割中，或僅限API的使用者無法存取該資料分割，則API會傳回錯誤。

因為`id`是系統管理的唯一索引鍵，所以請只將其與`updateOnly`動作一起包含。

要求必須包含包含包含潛在客戶記錄陣列的`input`引數。 每個潛在客戶記錄都是一個JSON物件，具有任意數量的潛在客戶欄位。 每個記錄中的金鑰必須是唯一的，而且所有JSON字串都必須使用UTF-8編碼。

使用`externalCompanyId`將潛在客戶記錄連結至公司記錄。 使用`externalSalesPersonId`將潛在客戶記錄連結至銷售人員記錄。

當多個請求在第一個請求傳回之前使用相同的索引鍵值時，並行或定時相近的更新插入請求可能會建立重複記錄。 若要避免重複，請視情況使用`createOnly`或`updateOnly`。 或者，也可以先將呼叫排入佇列，然後等待每個呼叫傳回，再使用相同的索引鍵提交另一個更新插入。

## 欄位

潛在客戶物件包含標準欄位和選用自訂欄位。 每個Marketo Engage訂閱都有標準欄位，而使用者可視需要建立自訂欄位。

每個欄位定義都包含中繼資料屬性，例如顯示名稱、API名稱和資料型別。

使用下列端點來查詢、建立和更新潛在客戶物件上的欄位。 API使用者的角色必須具有「讀寫結構描述標準欄位」許可權、「讀寫結構描述自訂欄位」許可權，或同時具備兩者。

## 查詢欄位

依API名稱查詢一個潛在客戶欄位或查詢所有潛在客戶欄位。 回應會包含標準欄位、自訂欄位和隱藏欄位，視角色許可權而定。

## 依名稱

「依名稱取得銷售機會欄位」端點會擷取一個銷售機會欄位的中繼資料。 必填的fieldApiName路徑引數指定欄位的API名稱。

回應類似於Describe Lead回應，但包含其他中繼資料。 例如，isCustom屬性會指出欄位是否為自訂欄位。

### 請求

```http
GET /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### 回應

```json
{
    "requestId": "cd97#1793ee0fec4",
    "result": [
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        }
    ],
    "success": true
}
```

## 瀏覽

「取得潛在客戶欄位」端點會擷取潛在客戶物件上所有欄位的中繼資料。 依預設，它最多會傳回300筆記錄。 使用`batchSize`查詢引數來減少此數目。

如果`moreResult`為true，則有更多結果可用。 在後續每次呼叫中傳遞傳回的`nextPageToken`，直到`moreResult`為false。

### 請求

```http
GET /rest/v1/leads/schema/fields.json
```

### 回應（已截斷）

```json
{
    "requestId": "142c3#1793eb976d8",
    "result": [
        {
            "displayName": "Salutation",
            "name": "salutation",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "First Name",
            "name": "firstName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Middle Name",
            "name": "middleName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Last Name",
            "name": "lastName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Date of Birth",
            "name": "dateOfBirth",
            "description": null,
            "dataType": "date",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Phone Number",
            "name": "phone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Mobile Phone Number",
            "name": "mobilePhone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Fax Number",
            "name": "fax",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Job Title",
            "name": "title",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Unsubscribed",
            "name": "unsubscribed",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        ...
    ],
    "success": true,
    "moreResult": false
}
```

## 建立欄位

「建立銷售機會欄位」端點會在銷售機會物件上建立一個或多個自訂欄位，並提供可與Marketo Engage UI比較的功能。 您可以使用此端點建立最多100個自訂欄位。

在生產執行個體中建立每個欄位之前，請仔細考慮每個欄位。 建立欄位後，您可以隱藏該欄位，但無法刪除該欄位。 未使用的欄位會新增待篩選專案到執行個體。

必要的輸入引數是潛在客戶欄位物件的陣列。 每個物件都需要下列屬性：

- `displayName`是欄位的UI顯示名稱。
- `name`是欄位的API名稱。
- `dataType`是欄位型別。

選擇性屬性為`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

name屬性必須是唯一的、以字母開頭，並且僅包含字母、數字或底線。 `displayName`必須是唯一的且不能包含特殊字元。

常見的慣例是將駝峰式大小寫套用至`displayName`以產生名稱。 例如，「我的自訂欄位」的`displayName`會產生「myCustomField」名稱。

### 請求

```http
POST /rest/v1/leads/schema/fields.json
```

### 內文

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "name": "acmeAccessCode",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      },
      {
        "displayName": "Acme Mail Date",
        "name": "acmeMailDate",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      }
  ]
}
```

### 回應

```json
{
    "requestId": "d9f1#17943666811",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "created"
        },
        {
            "name": "acmeMailDate",
            "status": "created"
        }
    ],
    "success": true
}
```

## 更新欄位

「更新潛在客戶欄位」端點會更新潛在客戶物件上的一個自訂欄位。 Marketo Engage UI中提供的大部分欄位更新也可透過API取得。 下表總結了兩者的差異。

<table>
<tbody>
<tr>
<td style="width: 26.5306%;" rowspan="2"><strong>屬性</strong></td>
<td style="width: 35%;" colspan="2"><strong>標準欄位</strong></td>
<td style="width: 38.2654%;" colspan="2"><strong>自訂欄位</strong></td>
</tr>
<tr>
<td style="width: 17.449%;"><strong>可透過API更新嗎？</strong></td>
<td style="width: 17.551%;"><strong>可透過UI更新嗎？</strong></td>
<td style="width: 19.3878%;"><strong>可透過API更新嗎？</strong></td>
<td style="width: 18.8776%;"><strong>可透過UI更新嗎？</strong></td>
</tr>
<tr>
<td style="width: 26.5306%;">資料型別</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">說明</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">顯示名稱</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isCustom</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
<tr>
<td style="width: 26.5306%;">ishidden</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是（若由API建立）</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isHtmlEncodingInEmail</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isSensitive</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">length</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
<tr>
<td style="width: 26.5306%;">名稱</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
</tbody>
</table>

必要的`fieldApiName`路徑引數指定要更新的欄位的API名稱。 必要的輸入引數是一個陣列，包含一個具有一個或多個屬性的潛在客戶欄位物件。

### 請求

```http
POST /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### 內文

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "description": "Acme Direct Mail Integration",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

### 回應

```json
{
    "requestId": "9f57#1794324f44c",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "updated"
        }
    ],
    "success": true
}
```

## 將銷售機會推送至Marketo

推播銷售機會是Sync Leads的替代方法，並提供更多類似於Marketo表單的觸發選項。 除了同步銷售機會欄位之外，端點還可以根據Cookie值關聯銷售機會。 從Marketo電子郵件傳遞按一下產生的`mkt_tok`值，或在呼叫中傳遞程式名稱。

端點也會建立與Marketo方案、行銷活動或兩者相關聯的可觸發活動。 使用此活動，從歸因於特定促銷活動或方案的潛在客戶擷取事件開始工作流程。

推播銷售機會使用與同步銷售機會相同的主要索引鍵和欄位API名稱。 它沒有動作引數，因為它一律會執行upsert。

需要`programName`和輸入引數。 輸入引數是潛在客戶物件的陣列，而產生的活動會歸因於已命名的計畫。 `lookupField`、`source`和`reason`引數為選用引數。 在`source`和`reason`中新增任意字串，以便在產生的活動中包含這些值。 您可以將這些值作為對應觸發程式（潛在客戶被推送至Marketo）和篩選器（潛在客戶被推送至Marketo）中的限制。

若要將先前的匿名活動與新建立的銷售機會建立關聯，請從銷售機會物件中省略Cookie屬性，並在推送銷售機會後呼叫Associate Lead 。 若要建立沒有活動歷史記錄的銷售機會，請在銷售機會物件中指定Cookie屬性。

### 請求

```http
POST /rest/v1/leads/push.json
```

### 內文

```json
{
    "programName": "Big Blue Thing Product Launch",
    "source": "Cool Sales Site",
    "reason": "Downloaded pricing sheet",
    "lookupField": "email",
    "input": [
        {
             "email": "Theresa.May@westminister.gov.uk",
             "country": "united kingdom",
             "firstName": "Theresa",
             "website": "www.brexit.com",
             "leadScore": 45,
             "jobTitle": "Prime Minister"
         },
         {
             "email": "Justin.Trudeau@ottowa.gov.ca",
             "country": "canada",
             "firstName": "Justin",
             "website": "www.take-off-eh.com",
             "leadScore": 92,
             "jobTitle": "Sonny"
         }
     ]
}
```

### 回應

```json
{
    "requestId": "939079529805",
    "success": true,
    "warnings": [],
    "result": [
       {
           "id": 483894,
           "status": "created"
       },
       {
           "id": 1087425,
           "status": "updated"
       },
       {
           "id": 3525,
           "reasons": [
                    {
                        "code": "501",
                        "message": "Bad stuff happened"
                    }
           ]
       }
    ]
}
```

若要傳遞`mkt_tok`引數，請將其值指派給輸入引數內潛在客戶記錄中的mktToken成員。

### 內文

```json
{
  "programName": "Big Blue Thing Product Launch",
  "source": "Cool Sales Site",
  "reason": "Downloaded pricing sheet",
  "lookupField": "mktToken",
  "input" : [
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Thelma"
     },
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Louise"
     }
   ]
}
```

## 提交表單

提交表單是同步銷售機會的替代方法，並提供等同於Marketo表單提交的功能。 使用它從歸因於特定行銷活動或計畫的潛在客戶擷取事件開始工作流程。

「提交表單」端點支援下列功能：

- 使用電子郵件欄位作為主索引鍵來更新插入潛在客戶記錄。
- 建立與方案、行銷活動或兩者相關聯的「填寫表單」活動。
- 根據Cookie值關聯銷售機會。
- 驗證表單欄位。

提交具有標準潛在客戶資料庫模式的表單。 在POST請求的JSON內文的所需輸入成員中傳遞一個物件記錄。 必要的`formId`成員包含目標Marketo表單識別碼。

使用選用的`programId`來識別接收潛在客戶的方案、方案成員自訂欄位或兩者。 如果`programId`存在，則銷售機會會連同表單中的任何方案成員欄位一起新增到方案中。 程式必須與表單位於相同的工作區。

如果表單不包含方案成員自訂欄位並省略`programId`，則潛在客戶不會新增到方案。 如果表單屬於程式、包含一或多個程式成員自訂欄位且省略`programId`，則端點會使用表單的程式。

必要的`leadFormFields`物件包含一或多個名稱/值配對，以供欄位填入。 每個欄位都必須以指定的格式定義，每個名稱必須是欄位的REST API名稱。 `email`欄位為必填。

選用的`visitorData`物件包含頁面瀏覽資料，包括`pageURL`、`queryString`、`leadClientIpAddress`和`userAgentString`。 用它來填入篩選器和觸發器的其他活動欄位。

選用的Cookie成員會將Munchkin Cookie與Marketo個人記錄建立關聯。 端點建立銷售機會時，會將先前的匿名活動與該銷售機會建立關聯，除非Cookie先前與其他已知記錄建立關聯。

如果先前已關聯Cookie，則會針對新記錄追蹤新活動，但舊活動會保留現有已知記錄。 若要建立沒有活動歷史記錄的銷售機會，請省略Cookie成員。

新潛在客戶會在表單所在的工作區的主要分割區中建立。

### 請求

```http
POST /rest/v1/leads/submitForm.json
```

### 標頭

```text
Content-Type: application/json
```

### 內文

```json
{
  "formId": 1029,
  "input": [
    {
      "leadFormFields": {
        "firstName": "Marge",
        "lastName": "Simpson",
        "email": "marge.simpson@fox.com",
        "pMCFField": "PMCF value"
      },
      "visitorData": {
        "pageURL": "https://na-sjst.marketo.com/lp/063-GJP-217/UnsubscribePage.html",
        "queryString": "Unsubscribed=yes",
        "leadClientIpAddress": "192.150.22.5",
        "userAgentString": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36"
      },
      "cookie": "id:063-GJP-217&token:_mch-marketo.com-1594662481190-60776"
    }
  ]
}
```

### 回應

```json
{
  "requestId": "10667#173bc585ca5",
  "result": [
    {
      "id": 319174,
      "status": "updated"
    }
  ],
  "success": true
}
```

下圖顯示Marketo Engage UI中對應的「填寫表單」活動詳細資料：

![填寫表單UI](assets/fill_out_form_activity_details.png)

## 合併

>[!NOTE]
>
>自2026年3月31日起，合併銷售機會API呼叫的`leadIds`引數中包含超過25個ID的呼叫將導致1080錯誤代碼，該呼叫將被跳過。 需要將超過25筆記錄合併為一份的工作，應分割為多項工作，以確保這些呼叫成功。
>

使用「合併銷售機會API」，將重複的記錄合併成一筆記錄。 合併結合了活動記錄、方案、行銷活動和清單成員資格、CRM資訊和欄位值。

傳遞成功銷售機會ID作為路徑引數。 傳遞一個`leadId`作為查詢引數，或在`leadIds`引數中最多傳遞25個以逗號分隔的ID。


### 請求

```http
POST /rest/v1/leads/{id}/merge.json?leadId=1324
```

### 回應

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

路徑引數中的潛在客戶是成功潛在客戶。 當欄位值衝突時，合併會使用獲勝者的值，除非該值為空白且失敗記錄的值為非。 `leadId`或`leadIds`引數中的潛在客戶是失敗的潛在客戶。

對於啟用SFDC-sync的訂閱，請使用`mergeInCRM`引數在CRM中執行合併。 如果兩個記錄都在SFDC中，且其中一個是CRM潛在客戶，而另一個是CRM聯絡人，則CRM聯絡人會獲勝，無論指定的獲勝者為何。 如果一個記錄在SFDC中，而另一個記錄僅存在於Marketo中，則SFDC領先優勢，無論指定的獲勝者為何。

## 關聯網頁活動

潛在客戶追蹤(Munchkin)會記錄訪客造訪您的網站和Marketo登陸頁面的造訪與點按。 這些活動會使用與潛在客戶瀏覽器中「_mkto_trk」Cookie對應的金鑰，允許Marketo追蹤相同人員的活動。

潛在客戶從Marketo電子郵件追蹤連結或提交Marketo表單時，通常會與潛在客戶記錄產生關聯。 若要將銷售機會與其他型別事件關聯，請使用「關聯銷售機會」端點。 將已知的潛在客戶記錄ID作為路徑引數傳遞，並在Cookie查詢引數中傳遞「_mkto_trk」Cookie值。

### 請求

```http
POST /rest/v1/leads/{id}/associate.json?cookie=id:287-GTJ-838%26token:_mch-marketo.com-1396310362214-46169
```

### 回應

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

如果此Cookie已與已知銷售機會相關聯，則對不同的銷售機會使用此API會針對新記錄記錄記錄新的網站活動。 現有網站活動未移至新記錄。
會籍

根據靜態清單或計畫中的成員資格來擷取潛在客戶記錄。 您也可以擷取包含特定潛在客戶的所有靜態清單、方案或智慧行銷活動。

回應結構和選用引數符合Get Leads by Filter Type，但此API不接受`filterType`或`filterValues`。

若要在Marketo UI中尋找清單ID，請導覽至清單並檢查其URL。 在`https://app-****.marketo.com/#ST1001A1`中，1001是清單`id`。

## 依銷售機會Id取得計畫

### 請求

```http
GET /rest/v1/list/{listId}/leads.json?batchSize=3
```

### 回應

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "nextPageToken":
"PS5VL5WD4UOWGOUCJR6VY7JQO2KUXL7BGBYXL4XH4BYZVPYSFBAONP4V4KQKN4SSBS55U4LEMAKE6===",
    "result":[
       {
            "id":50,
            "email":"kjashaedd@klooblept.com",
            "firstName":"Kataldar",
             "postalCode":"04828"
       },
       {
           "id":2343,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
           "postalCode":"04828"
       },
      {
           "id":88498,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
         "postalCode":"04828"
         }
    ]
}
```

## 依銷售機會Id取得清單

Get Lists by Lead Id端點接受潛在客戶記錄`id`路徑引數，並傳回包含潛在客戶的每個靜態清單。

### 請求

```http
GET /rest/v1/leads/{id}/listMembership.json?batchSize=3
```

### 回應

```json
{
    "requestId": "1184b#1706f0ec23f",
    "result": [
        {
            "listId": 3379,
            "createdAt": "2016-05-17T19:32:44Z",
            "updatedAt": "2016-05-17T19:32:44Z"
        },
        {
            "listId": 2792,
            "createdAt": "2009-05-19T18:29:15Z",
            "updatedAt": "2009-05-19T18:29:15Z"
        },
        {
            "listId": 42,
            "createdAt": "2009-04-22T19:24:22Z",
            "updatedAt": "2009-04-22T19:24:22Z"
        }
    ],
    "success": true,
    "nextPageToken": "BFRV7OMVSNJWDVKVTUFS3XHT4E======",
    "moreResult": true
}
```

## 方案

以與清單成員資格相同的方式擷取方案成員資格。 依程式ID取得銷售機會接受相同的選用要求引數，並需要`programId`路徑引數。

或者，傳遞包含逗號分隔欄位名稱清單的欄位引數。 如果省略欄位，回應會包含`email`、`updatedAt`、`createdAt`、`lastName`、`firstName`、`membership`和`id`。 如果未傳回要求的欄位，則其值會隱含為空值。

結果陣列中的每個專案都是具有名為「membership」的子物件的潛在客戶。 此物件說明潛在客戶與要求的方案的關係，並一律包含`progressionStatus`、`acquiredBy`、`reachedSuccess`和`membershipDate`。

如果父級方案是參與方案，則成員資格也包含`stream`、`nurtureCadence`和`isExhausted`，以說明潛在客戶在該方案中的位置和活動。

### 請求

```http
GET /rest/v1/leads/programs/{programId}.json?batchSize=3
```

### 回應

```json
{
    "requestId": "13ad4#1727b748a17",
    "result": [
        {
            "id": 319141,
            "firstName": "Meera",
            "lastName": "Reed",
            "email": "mree@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319142,
            "firstName": "Jon",
            "lastName": "Umber",
            "email": "jumb@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319143,
            "firstName": "Lyanna",
            "lastName": "Mormont",
            "email": "lmor@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        }
    ],
    "success": true,
    "nextPageToken": "SW3PTMBVFCNHSHJGZ7LQH3ZWNUOHKADJZ3MOQ2LOZZVNO3WEIUPDKPRTTHBSMW756KOCWURTOF2XS==="
}
```

「依銷售機會ID取得程式」端點會採用銷售機會記錄ID路徑引數，並傳回包含該銷售機會的每個程式。 使用選用的`filterType`和`filterValues`引數來篩選程式識別碼。

### 請求

```http
GET /rest/v1/leads/{id}/programMembership.json
```

### 回應

```json
{
    "requestId": "12e84#1706f13a379",
    "result": [
        {
            "id": 1044,
            "progressionStatus": "Sent",
            "isExhausted": false,
            "acquiredBy": false,
            "reachedSuccess": false,
            "membershipDate": "2016-05-27T19:50:29Z",
            "updatedAt": "2016-05-27T19:50:29Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## 智慧行銷活動

「依銷售機會ID取得Smart Campaigns」端點採用銷售機會記錄ID路徑引數，並傳回包含該銷售機會的每個Smart Campaign。

### 請求

```http
GET /rest/v1/leads/{id}/smartCampaignMembership.json?batchSize=3
```

### 回應

```json
{
    "requestId": "e7b0#1706f163632",
    "result": [
        {
            "smartCampaignId": 3746,
            "createdAt": "2018-06-01T18:00:04Z",
            "updatedAt": "2018-06-01T18:00:06Z"
        },
        {
            "smartCampaignId": 3678,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:41Z"
        },
        {
            "smartCampaignId": 3680,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:40Z"
        }
    ],
    "success": true,
    "nextPageToken": "TNGAH3NKDUFDHNXUVGTNBXJCQM======",
    "moreResult": true
}
```

## 刪除

使用「刪除銷售機會」端點可移除銷售機會記錄。 使用id屬性指定內文中的銷售機會id。 一個請求最多可以刪除300個銷售機會。 傳送Content-Type： application/json標頭。

### 請求

```http
POST /rest/v1/leads/delete.json
```

### 內文

```json
{
   "input":[
      {
         "id": 235
      },
      {
         "id":766
      }
   ]
}
```

### 回應

```json
{
  "requestId":"3608#16664333670",
  "result":[
    {
      "id":235,
      "status":"deleted"
    },
    {
      "id":766,
      "status":"deleted"
    }
  ],
  "success":true
}
```

## 關係

- 透過潛在客戶記錄上的externalCompanyId欄位公司
- 透過潛在客戶記錄上的externalSalesPersonId欄位進行SalesPerson
- 透過計畫會員資格的計畫
- 透過清單成員資格清單
- 透過活動中leadId欄位進行的活動
- 透過潛在客戶記錄上的個別區段欄位進行細分
- 透過lead記錄上的leadPartitionId欄位進行分割

## 逾時

潛在客戶端點具有30s逾時，以下端點除外：

- 同步處理銷售機會：90秒
- 關聯銷售機會：60秒
- 合併銷售機會：180秒
- 更新潛在客戶分割： 60秒
- 將銷售機會推送至Marketo：90年代
- 依篩選器型別取得銷售機會： 60秒
- 依清單ID取得銷售機會：60秒
