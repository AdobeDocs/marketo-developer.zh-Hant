---
title: 計畫成員
feature: REST API
description: 使用Marketo REST API讀取、建立、更新和刪除程式成員、管理標準和自訂欄位，以及使用可搜尋欄位進行查詢。
exl-id: 22f29a42-2a30-4dce-a571-d7776374cf43
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1726'
ht-degree: 2%

---

# 計畫成員

[程式成員端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members)

Marketo會公開API，以供讀取、建立、更新和刪除程式成員記錄。 方案成員記錄會透過銷售機會ID欄位與銷售機會記錄相關。 記錄由一組標準欄位和選擇性地最多20個其他自訂欄位組成。 這些欄位包含每個成員的程式特定資料，並可用於表單、篩選器、觸發器和流程動作。 您可以在Marketo Engage UI中程式[成員標籤](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/manage-and-view-members)中檢視此資料。

## 說明

[Describe程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端點遵循潛在客戶資料庫物件的標準模式。 `searchableFields`陣列為您提供一組有效的欄位，以供查詢。 `fields`陣列包含欄位中繼資料，包括REST API名稱、顯示名稱和欄位更新能力。

```
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

[取得方案成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMembersUsingGET)端點可讓您擷取方案的成員。 它需要`programId`路徑引數，以及`filterType`和`filterValues`查詢引數。

`programId`用於指定要搜尋的方案。

`filterType`用於指定要用作搜尋篩選的欄位。 它接受[Describe程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端點傳回之「searchableFields」清單中的任何欄位。 如果您指定的filterType是自訂欄位，則自訂欄位的dataType必須是&quot;string&quot;或&quot;integer&quot;。 如果您指定「leadId」以外的filterType，請求最多可以處理100,000個程式成員記錄。 視您的Marketo執行個體的設定方式而定，您會收到下列其中一個錯誤：

- 如果方案成員總數超過100,000，則會傳回錯誤：「1003，成員大小總計：100,001超過篩選允許的100,000限制」。
- 如果符合篩選器&#x200B;_的程式成員_&#x200B;總數超過100,000，會傳回錯誤：「1003，符合成員資格大小： 100,001超過此API允許的限制(100,000)」。

若要查詢成員計數超過限制的程式，請改用[大量程式成員擷取API](bulk-program-member-extract.md)。

`filterValues`用於指定要搜尋的值，並以逗號分隔格式接受最多300個值。 呼叫會搜尋程式成員的欄位符合其中一個filterValues的記錄。

或者，您可以透過將`updatedAt`指定為filterType搭配`startAt`和`endAt`日期時間引數來依日期範圍篩選。 範圍必須等於或少於7天。 日期時間應採用ISO-8601格式，不含毫秒。

選用的`fields`查詢引數接受[描述程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端點傳回的欄位API名稱清單（以逗號分隔）。 包含時，回應中的每個記錄都包含指定欄位。 省略時，傳回的預設欄位集是`acquiredBy`、`leadId`、`membershipDate`、`programId`和`reachedSuccess`。

依預設，最多會傳回300筆記錄。 您可以使用`batchSize`查詢引數來減少此數目。 如果&#x200B;**moreResult**&#x200B;屬性為true，表示有更多結果可用。 繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。 從此API傳回的`nextPageToken`應一律重複用於此呼叫的下一個反複專案。

如果GET請求的總長度超過8KB，則會傳回HTTP錯誤：「414， URI太長」。 暫行解決方法是將GET變更為POST、新增`_method=GET`引數，並將查詢字串放入要求內文中。

```
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

有兩個端點支援在方案成員上建立/更新操作。 一個僅允許您更新計畫成員狀態。 另一個可讓您更新標示為「可更新」的程式成員欄位集。 兩個端點都可讓您修改每個呼叫最多300個程式成員記錄。

### 方案成員狀態

[同步程式成員狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberStatusUsingPOST)端點用來建立或更新一或多個成員的程式狀態。

必要的`programId`路徑引數指定了包含要建立或更新之成員的程式。

必要的`statusName`引數指定要套用至潛在客戶清單的程式狀態。 statusName必須符合方案頻道的可用狀態。 可以使用[取得通道](https://developer.adobe.com/marketo-apis/api/asset/#tag/Channels/operation/getAllChannelsUsingGET)端點擷取有效的狀態。 如果潛在客戶狀態的步驟值大於指定的statusName，則會略過該潛在客戶。

必要的`input`引數是`leadId`的陣列，對應至方案成員。 您最多可以提交每個呼叫300個銷售機會ID。 對每個記錄執行更新插入操作。 如果leadId與方案成員相關聯，則會更新其成員資格狀態。 如果沒有，則會建立新的方案成員記錄，該記錄會與leadId相關聯，並會指派成員資格狀態。

端點以「已更新」、「已建立」或「已略過」的`status`回應。 如果略過，也會包含`reasons`陣列。 端點也會以`seq`欄位回應，此欄位是可用來將提交的記錄與回應順序產生關聯的索引。

如果通話成功，「變更方案狀態」活動會寫入潛在客戶的活動記錄中。

```
POST /rest/v1/programs/{programId}/members/status.json
```

```
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

[同步程式成員資料](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberDataUsingPOST)端點用於更新一或多個成員的程式成員欄位資料。 您可以修改任何自訂欄位或「可更新」的標準欄位（請參閱[描述程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端點）。

必要的`programId`路徑引數指定了包含要更新的成員的程式。

必要的`input`引數是陣列。 每個陣列元素包含`leadId`和一個或多個要更新的欄位（使用API名稱）。 對每個記錄執行更新操作。 leadId必須與方案成員相關聯。 欄位必須可更新。 您最多可以提交每個呼叫300個銷售機會ID。

端點以「已更新」或「已略過」的`status`回應。 如果略過，也會包含`reasons`陣列。 端點也會以`seq`欄位回應，此欄位是可用來將提交的記錄與回應順序產生關聯的索引。

如果呼叫成功，「變更方案成員資料」活動會寫入潛在客戶的活動記錄中。

```
POST /rest/v1/programs/{programId}/members.json
```

```
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

程式成員物件包含標準欄位和選擇性自訂欄位。 標準欄位會顯示在每個Marketo Engage訂閱中，而自訂欄位則由使用者視需要建立。 每個欄位定義都由一組描述該欄位的屬性組成。 屬性的範例為顯示名稱、API名稱和dataType。 這些屬性統稱為中繼資料。

下列端點可讓您查詢、建立和更新程式成員物件上的欄位。 這些API要求擁有的API使用者必須擁有具有&#x200B;**讀寫結構描述標準欄位**&#x200B;或&#x200B;**讀寫結構描述自訂欄位**&#x200B;許可權之一或兩者的角色。

### 查詢欄位

查詢方案成員欄位簡單明瞭。 您可以按API名稱查詢單一方案成員欄位，或查詢所有方案成員欄位集。 視使用的角色許可權而定，可擷取標準欄位和自訂欄位。 也會擷取隱藏欄位。

#### 依名稱

[依名稱取得程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldByNameUsingGET)端點會擷取程式成員物件上單一欄位的中繼資料。 必要的`fieldApiName`路徑引數指定欄位的API名稱。 回應類似於Describe Program Member端點，但包含其他中繼資料，例如`isCustom`屬性，其代表欄位是否為自訂欄位。

```
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

[取得程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldsUsingGET)端點會擷取程式成員物件上所有欄位的中繼資料。 依預設，最多會傳回300筆記錄。 您可以使用`batchSize`查詢引數來減少此數目。 如果`moreResult`屬性為true，則表示有更多結果可用。 繼續呼叫此端點，直到moreResult屬性傳回false （表示沒有可用的結果）。 從此API傳回的`nextPageToken`應一律重複用於此呼叫的下一個反複專案。

```
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

[建立程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/createProgramMemberFieldUsingPOST)端點會在程式成員物件上建立一或多個自訂欄位。 此端點提供的功能與Marketo Engage UI[中的](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/program-member-custom-fields)可用功能相當。 您可以使用此端點建立最多20個自訂欄位。

請仔細考慮您在使用API的Marketo Engage生產執行個體中建立的每個欄位。 欄位建立後，您就無法刪除（[您只能隱藏它](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/field-management/delete-a-custom-field-in-marketo)）。 未使用欄位數量激增是壞做法，會增加執行個體的雜亂。

必要的`input`引數是程式成員欄位物件的陣列。 每個物件包含一或多個屬性。 必要屬性為`displayName`、`name`和`dataType`，分別對應至欄位的UI顯示名稱、欄位的API名稱以及欄位型別。 您可以選擇指定`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

有一些規則與`name`和`displayName`命名相關。 `name`屬性必須是唯一的、以字母開頭，並且僅包含字母、數字或底線。 *`isplayName`必須是唯一的，而且不能包含特殊字元。 常見的命名慣例是將[駝峰式大小寫](https://en.wikipedia.org/wiki/Camel_case#)套用至`displayName`以產生`name`。 例如，「我的自訂欄位」的`displayName`會產生「myCustomField」的`name`。

```
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

[更新程式成員欄位](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/updateProgramMemberFieldUsingPOST)端點更新程式成員物件上的單一自訂欄位。 一般而言，使用Marketo Engage UI執行的欄位更新作業，可使用API達成。 下表總結了幾項差異。

| 屬性 | 可透過API更新嗎？ | 可透過UI更新嗎？ | 可透過API更新嗎？ | 可透過UI更新嗎？ |
|---|---|---|---|---|
| 資料型別 | no | no | no | 是 |
| 說明 | 是 | 是 | 是 | 是 |
| 顯示名稱 | no | no | 是 | 是 |
| isCustom | no | no | no | no |
| ishidden | no | 是 | 是（若由API建立） | 是 |
| isHtmlEncodingInEmail | 是 | 是 | 是 | 是 |
| isSensitive | 是 | 是 | 是 | 是 |
| length | no | no | no | no |
| 名稱 | no | no | no | no |

必要的`fieldApiName`路徑引數指定要更新的欄位的API名稱。 必要的`input`引數是包含單一潛在客戶欄位物件的陣列。 欄位物件包含一或多個屬性。

```
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

[刪除程式成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/deleteProgramMemberUsingPOST)端點用於刪除程式成員記錄。 必要的`programId`路徑引數指定包含要刪除之成員的程式。 要求內文包含`input`個潛在客戶ID陣列。 最多300個銷售機會id  允許每個呼叫。

端點以`status`的「已刪除」或「已略過」回應。 如果略過，也會包含`reasons`陣列。 端點也會以`seq`欄位回應，此欄位是可用來將提交的記錄與回應順序產生關聯的索引。

```
POST /rest/v1/programs/{programId}/members/delete.json
```

```
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
