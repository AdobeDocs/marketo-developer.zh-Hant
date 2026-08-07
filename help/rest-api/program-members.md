---
title: 計畫成員
feature: REST API
description: 使用Marketo REST API讀取、建立、更新和刪除程式成員、管理標準和自訂欄位，以及使用可搜尋欄位進行查詢。
exl-id: 22f29a42-2a30-4dce-a571-d7776374cf43
TQID: https://experienceleague.adobe.com/scEHyXYq9C7cCS1kIX810wG7ahT9fsa448NwIfBmzQM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 1632
ht-degree: 2%

---

# 計畫成員

[程式成員端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members)

Marketo提供讀取、建立、更新和刪除程式成員記錄的API。 潛在客戶識別碼欄位將方案成員記錄與潛在客戶記錄相關聯。

每個記錄包含標準欄位，最多可包含20個自訂欄位。 這些欄位會儲存程式特定的成員資料，以用於表單、篩選器、觸發器和流程動作。 您可以在Marketo Engage UI中程式[成員標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/manage-and-view-members)中檢視此資料。

## 說明

[Describe程式成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeProgramMemberUsingGET2)端點遵循Lead資料庫物件的標準模式。

- `searchableFields`陣列識別對查詢有效的欄位。
- `fields`陣列包含中繼資料，例如REST API名稱、顯示名稱，以及欄位是否可更新。

```http
GET /rest/v1/programs/members/describe.json
```

```json
{
    "requestId": "f813#1791563c7cc",
    "result": [
        {
            "name": "API Program Membership",
            "description": "Map for API program membership fields",
            "createdAt": "2021-03-20T01:30:05Z",
            "updatedAt": "2021-03-20T01:30:05Z",
            "dedupeFields": [
                "leadId",
                "programId"
            ],
            "searchableFields": [
                [
                    "leadId"
                ],
                [
                    "myCustomField"
                ],
                [
                    "reachedSuccess"
                ],
                [
                    "statusName"
                ]
            ],
            "fields": [
                {
                    "name": "acquiredBy",
                    "displayName": "acquiredBy",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "attendanceLikelihood",
                    "displayName": "attendanceLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "createdAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "isExhausted",
                    "displayName": "isExhausted",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadId",
                    "displayName": "leadId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "membershipDate",
                    "displayName": "membershipDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "nurtureCadence",
                    "displayName": "nurtureCadence",
                    "dataType": "string",
                    "length": 4,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "program",
                    "displayName": "program",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "programId",
                    "displayName": "programId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccess",
                    "displayName": "reachedSuccess",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccessDate",
                    "displayName": "reachedSuccessDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "registrationLikelihood",
                    "displayName": "registrationLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusName",
                    "displayName": "statusName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusReason",
                    "displayName": "statusReason",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "trackName",
                    "displayName": "trackName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "updatedAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "waitlistPriority",
                    "displayName": "waitlistPriority",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "myCustomField",
                    "displayName": "myCustomField",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "registrationCode",
                    "displayName": "registrationCode",
                    "dataType": "string",
                    "length": 100,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "webinarUrl",
                    "displayName": "webinarUrl",
                    "dataType": "string",
                    "length": 2000,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

## 查詢

使用[取得方案成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/getProgramMembersUsingGET)端點來擷取方案的成員。 此要求需要`programId`路徑引數以及`filterType`和`filterValues`查詢引數。

`programId`指定要搜尋的方案。

`filterType`指定要做為搜尋篩選的欄位。 它接受[Describe程式成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeProgramMemberUsingGET2)端點傳回之「searchableFields」清單中的任何欄位。 對於自訂欄位，dataType必須是&quot;string&quot;或&quot;integer&quot;。

當filterType不是「leadId」時，請求最多可以處理100,000個程式成員記錄。 根據您的Marketo執行個體設定，您會收到以下其中一個錯誤：

- 如果方案成員總數超過100,000，則會傳回錯誤：「1003，成員大小總計：100,001超過篩選允許的100,000限制」。
- 如果符合篩選器&#x200B;_的程式成員_&#x200B;總數超過100,000，會傳回錯誤：「1003，符合成員資格大小： 100,001超過此API允許的限制(100,000)」。

若要查詢成員資格計數超過限制的程式，請改用[大量程式成員擷取API](bulk-program-member-extract.md)。

`filterValues`指定要搜尋的值，並接受最多300個逗號分隔的值。 呼叫會搜尋程式成員欄位符合其中一個filterValues的記錄。

或者，透過將`updatedAt`指定為filterType並提供`startAt`和`endAt`日期時間引數來依日期範圍篩選。 範圍必須等於或少於7天。 日期時間值使用不含毫秒的ISO-8601格式。

選用的`fields`查詢引數接受[描述程式成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeProgramMemberUsingGET2)端點傳回的欄位API名稱清單（以逗號分隔）。 包含時，每個回應記錄都包含指定的欄位。 省略時，回應預設會傳回`acquiredBy`、`leadId`、`membershipDate`、`programId`和`reachedSuccess`。

依預設，端點最多會傳回300筆記錄。 使用`batchSize`查詢引數來減少此數目。

如果&#x200B;**moreResult**&#x200B;屬性為true，則有更多結果可用。 繼續使用傳回的`nextPageToken`呼叫端點，直到moreResult為false。

如果GET要求的總長度超過8KB，端點會傳回HTTP錯誤「414， URI太長」。 若要解決此限制，請將請求從GET變更為POST、新增`_method=GET`引數，並將查詢字串放入請求內文中。

```http
GET /rest/v1/programs/{programId}/members.json?filterType=statusName&filterValues=Influenced
```

```json
{
    "requestId": "109da#17915eec072",
    "result": [
        {
            "seq": 0,
            "leadId": 1789,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 1,
            "leadId": 1790,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 2,
            "leadId": 1791,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 3,
            "leadId": 1792,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 4,
            "leadId": 1793,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 5,
            "leadId": 1794,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 6,
            "leadId": 1795,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 7,
            "leadId": 1796,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 8,
            "leadId": 1797,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 9,
            "leadId": 1798,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 10,
            "leadId": 1799,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 11,
            "leadId": 1800,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## 建立和更新

兩個端點支援方案成員的建立和更新操作：

- 一個端點只會更新程式成員狀態。
- 一個端點更新標籤為「可更新」的程式成員欄位。

每個端點最多可以修改300個程式成員記錄。

### 方案成員狀態

使用[同步程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncProgramMemberStatusUsingPOST)端點可建立或更新一或多個成員的程式狀態。

必要的引數包括：

- `programId`：指定包含要建立或更新之成員的程式的路徑引數。
- `statusName`：指定要套用至潛在客戶清單的程式狀態。 statusName必須符合方案頻道的可用狀態。 使用[Get Channels](https://developer.adobe.com/marketo-apis/api/asset#operation/getAllChannelsUsingGET)端點擷取有效的狀態。 如果潛在客戶狀態的步驟值大於指定的statusName，則請求會跳過該潛在客戶。
- `input`：對應至方案成員的`leadId`值陣列。 您最多可以提交每個呼叫300個銷售機會ID。

端點會對每個記錄執行更新插入。 如果leadId與方案成員相關聯，端點會更新其成員資格狀態。 否則，它會建立方案成員記錄，將該記錄與leadId相關聯，並指派成員資格狀態。

回應包含`status`個「已更新」、「已建立」或「已略過」。 略過的結果也包含`reasons`陣列。 `seq`欄位是將每個提交的記錄與回應順序關聯的索引。

如果通話成功，「變更方案狀態」活動會寫入潛在客戶的活動記錄中。

```http
POST /rest/v1/programs/{programId}/members/status.json
```

```text
Content-Type: application/json
```

```json
{
    "statusName":"Influenced",
    "input":[
        {
            "leadId": 1800
        },
        {
            "leadId": 1801
        },
        {
            "leadId": 1235
        }
    ]
}
```

```json
{
    "requestId": "14b2d#17916378ec5",
    "result": [
        {
            "seq": 0,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead skipped because it is already in or past this status"
                }
            ]
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1801
        },
        {
            "seq": 2,
            "status": "created",
            "leadId": 1235
        }
    ],
    "success": true
}
```

### 方案成員資料

使用[同步程式成員資料](https://developer.adobe.com/marketo-apis/api/mapi#operation/syncProgramMemberDataUsingPOST)端點更新一或多個成員的程式成員欄位資料。 您可以修改任何自訂欄位，或任何由[描述程式成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeProgramMemberUsingGET2)端點標籤為「可更新」的標準欄位。

必要的引數包括：

- `programId`：指定包含要更新之成員的程式的路徑引數。
- `input`：其元素包含`leadId`和一個或多個欄位，以透過API名稱更新的陣列。 您最多可以提交每個呼叫300個銷售機會ID。

端點會更新每個記錄。 leadId必須與方案成員相關聯，且每個欄位都必須可更新。

回應包含`status`個「已更新」或「已略過」。 略過的結果也包含`reasons`陣列。 `seq`欄位是將每個提交的記錄與回應順序關聯的索引。

如果呼叫成功，「變更方案成員資料」活動會寫入潛在客戶的活動記錄中。

```http
POST /rest/v1/programs/{programId}/members.json
```

```text
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1789,
            "registrationCode": "dcff5f12-a7c7-11eb-bcbc-0242ac130002"
        },
        {
            "leadId": 1790,
            "registrationCode": "c0404b78-d3fd-47bf-82c4-d16f3852ab3a"
        },
        {
            "leadId": 1003,
            "registrationCode": "aa880c57-75b8-426b-a33a-fbf6302d7cb4"
        }
    ]
}
```

```json
{
    "requestId": "edc3#1791659b8d2",
    "result": [
        {
            "seq": 0,
            "status": "updated",
            "leadId": 1789
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1790
        },
        {
            "seq": 2,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1013",
                    "message": "Membership not found"
                }
            ]
        }
    ],
    "success": true
}
```

## 欄位

程式成員物件包含標準欄位和選擇性自訂欄位。 標準欄位會顯示在每個Marketo Engage訂閱中，而使用者會視需要建立自訂欄位。

每個欄位都由顯示名稱、API名稱和dataType等屬性定義。 這些屬性統稱為中繼資料。

下列端點查詢、建立和更新程式成員物件上的欄位。 API使用者必須具有具有&#x200B;**讀寫結構描述標準欄位**&#x200B;許可權、**讀寫結構描述自訂欄位**&#x200B;許可權或兩者的角色。

### 查詢欄位

依API名稱查詢一個方案成員欄位或擷取所有方案成員欄位。 角色許可權會決定回應可包含標準欄位和/或自訂欄位。 回應也包含隱藏欄位。

#### 依名稱

[依名稱取得程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/getProgramMemberFieldByNameUsingGET)端點會擷取程式成員物件上某個欄位的中繼資料。 必要的`fieldApiName`路徑引數指定欄位的API名稱。

此回應類似於「描述方案成員」回應，但包含其他中繼資料。 例如，`isCustom`屬性指出欄位是否為自訂欄位。

```http
GET /rest/v1/programs/members/schema/fields/{fieldApiName}.json
```

```json
{
    "requestId": "15416#17e955554de",
    "result": [
        {
            "displayName": "Status",
            "name": "statusName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true
}
```

#### 瀏覽

[取得程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/getProgramMemberFieldsUsingGET)端點會擷取程式成員物件上所有欄位的中繼資料。 依預設，它最多會傳回300筆記錄。 使用`batchSize`查詢引數來減少此數目。

如果`moreResult`屬性為true，則有更多結果可用。 繼續使用傳回的`nextPageToken`呼叫端點，直到moreResult為false。

```http
GET /rest/v1/programs/members/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "102f6#17e9557f123",
    "result": [
        {
            "displayName": "Acquired By",
            "name": "acquiredBy",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Cadence",
            "name": "nurtureCadence",
            "description": null,
            "dataType": "string",
            "length": 4,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Exhausted",
            "name": "isExhausted",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Member Date",
            "name": "membershipDate",
            "description": null,
            "dataType": "datetime",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Program",
            "name": "program",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "BC7J6EPVLT6T4B5FKUU3APCYN4======",
    "moreResult": true
}
```

### 建立欄位

[建立程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/createProgramMemberFieldUsingPOST)端點會在程式成員物件上建立自訂欄位。 它提供的功能與[Marketo Engage UI](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/program-member-custom-fields)類似。 您可以使用此端點建立最多20個自訂欄位。

在生產Marketo Engage例項中建立欄位之前，請仔細考慮每個欄位。 建立欄位後，您便無法刪除它；[您只能隱藏它](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/field-management/delete-a-custom-field-in-marketo)。 未使用的欄位會新增待篩選專案到執行個體。

必要的`input`引數是程式成員欄位物件的陣列。 每個物件包含一或多個屬性。

- 必要的屬性為`displayName`、`name`和`dataType`。 它們分別對應至UI顯示名稱、API名稱和欄位型別。
- 選擇性屬性為`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

`name`和`displayName`屬性有下列命名規則：

- `name`屬性必須是唯一的、以字母開頭，並且僅包含字母、數字或底線。
- *`isplayName`必須是唯一的，而且不能包含特殊字元。

常見的慣例是將[駝峰式大小寫](https://en.wikipedia.org/wiki/Camel_case#)套用至`displayName`以產生`name`。 例如，「我的自訂欄位」的`displayName`會產生「myCustomField」的`name`。

```http
POST /rest/v1/programs/members/schema/fields.json
```

```json
{
  "input": [
    {
        "displayName": "PMCF Custom Field 03",
        "name": "pMCFCustomField03",
        "description": "My third custom field",
        "dataType": "string"
    }
  ]
}
```

```json
{
    "requestId": "13a7#17e955fcb44",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "created"
        }
    ],
    "success": true
}
```

### 更新欄位

[更新程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi#operation/updateProgramMemberFieldUsingPOST)端點會更新程式成員物件上的一個自訂欄位。 Marketo Engage UI中提供的大部分欄位更新也可透過API取得。 下表總結了兩者的差異。

| 屬性 | 可透過API更新嗎？ | 可透過UI更新嗎？ | 可透過API更新嗎？ | 可透過UI更新嗎？ |
| --- | --- | --- | --- | --- |
| 資料型別 | no | no | no | 是 |
| 說明 | 是 | 是 | 是 | 是 |
| 顯示名稱 | no | no | 是 | 是 |
| isCustom | no | no | no | no |
| ishidden | no | 是 | 是（若由API建立） | 是 |
| isHtmlEncodingInEmail | 是 | 是 | 是 | 是 |
| isSensitive | 是 | 是 | 是 | 是 |
| length | no | no | no | no |
| 名稱 | no | no | no | no |

此請求需要下列引數：

- `fieldApiName`：指定要更新的欄位API名稱的路徑引數。
- `input`：包含一潛在客戶欄位物件的陣列，該物件具有一或多個屬性。

```http
POST /rest/v1/programs/members/schema/fields/pMCFCustomField03.json
```

```json
{
  "input": [
      {
        "displayName": "Lunch Preference",
        "description": "Attendee food preference",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

```json
{
    "requestId": "215f#17e95663955",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "updated"
        }
    ],
    "success": true
}
```

## 刪除

使用[刪除程式成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/deleteProgramMemberUsingPOST)端點刪除程式成員記錄。 必要的`programId`路徑引數指定了包含要刪除之成員的程式。

要求內文包含`input`個潛在客戶ID陣列。 每個呼叫最多允許300個銷售機會ID。

回應包含`status`個「已刪除」或「已略過」。 略過的結果也包含`reasons`陣列。 `seq`欄位是將每個提交的記錄與回應順序關聯的索引。

```http
POST /rest/v1/programs/{programId}/members/delete.json
```

```text
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1235
        },
        {
            "leadId": 77
        }
    ]
}
```

```json
{
    "requestId": "302a#17916619417",
    "result": [
        {
            "seq": 0,
            "status": "deleted",
            "leadId": 1235
        },
        {
            "seq": 1,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead not in program"
                }
            ]
        }
    ],
    "success": true
}
```
