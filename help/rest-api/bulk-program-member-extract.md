---
title: 「大量程式成員擷取」
feature: REST API
description: 「批次處理成員資料擷取。」
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '1142'
ht-degree: 2%

---


# 大量程式成員擷取

[大量程式成員擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members)

REST API的批次程式成員擷取集提供程式化介面，可從Marketo中擷取大型程式成員記錄集。 此為建議使用的介面，用於需要在Marketo與一或多個外部系統之間持續交換資料（用於ETL、資料倉儲和封存）的使用案例。

## 權限

大量程式成員擷取API要求擁有的API使用者必須擁有具有唯讀銷售機會或讀寫銷售機會許可權之一或兩者的角色。

## 說明

[描述方案成員](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2) 是欄位是否可供使用的主要信任來源，以及有關這些欄位的中繼資料。 此 `name` 屬性包含REST API名稱。

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

## 篩選器

計畫成員支援各種篩選選項。 可以為一個作業指定多個篩選器型別，在這種情況下，它們會一起進行AND運算。 您必須指定 `programId` 或 `programIds` 篩選。 所有其他篩選器皆為選用專案。 此 `updatedAt` 篩選器需要其他基礎架構元件，這些元件尚未推出至所有訂閱。

<table>
  <tbody>
    <tr>
      <td>篩選器型別</td>
      <td>資料類型</td>
      <td>附註</td>
    </tr>
    <tr>
      <td>programId</td>
      <td>整數</td>
      <td>接受方案的ID。 工作會傳回工作開始處理時屬於程式成員的所有可存取記錄。使用 <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">取得計畫</a> endpoint.無法與programIds篩選器搭配使用。</td>
    </tr>
    <tr>
      <td>programIds</td>
      <td>Array[整數]</td>
      <td>接受最多10個計畫ID的陣列。 作業會傳回作業開始處理時屬於程式成員的所有可存取記錄。匯出檔案中會新增一個額外欄位「programId」，作為第一個欄位。 此欄位會識別從中擷取計畫會員資格記錄的計畫。使用 <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">取得計畫</a> endpoint.Cannot be used with programId filter.</td>
    </tr>
    <tr>
      <td>isExhausted</td>
      <td>布林值</td>
      <td>接受用於篩選計畫成員資格記錄的布林值 <a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content">已耗盡內容的人</a>.</td>
    </tr>
    <tr>
      <td>nurtureCadence</td>
      <td>字串</td>
      <td>接受用來篩選特定Nurture步調之程式成員資格記錄的字串。允許的值有：
        <ul>
          <li>pause — 節奏已暫停</li>
          <li>標準 — 步調正常</li>
        </ul></td>
    </tr>
    <tr>
      <td>statusName</td>
      <td>Array[字串]</td>
      <td>接受程式成員狀態名稱的陣列。 多個狀態名稱會同時進行OR。具有此篩選型別的工作會傳回其程式成員狀態符合任何指定狀態名稱的所有可存取記錄。 可以使用預設狀態名稱和使用者定義的狀態名稱。如果statusNames篩選器與'programIds'篩選器一起使用，則會檢查每個方案的成員資格記錄，其狀態符合任何狀態名稱。 如果在任何程式中找不到狀態名稱，則會傳回「1003， Invalid Data」錯誤。
        <table>
          <tbody>
            <tr>
              <td>已出席</td>
              <td>已出席隨選</td>
              <td>已退回</td>
            </tr>
            <tr>
              <td>已點按</td>
              <td>已聯絡</td>
              <td>已轉換</td>
            </tr>
            <tr>
              <td>已參與</td>
              <td>填寫表單</td>
              <td>受影響的</td>
            </tr>
            <tr>
              <td>已邀請</td>
              <td>會員</td>
              <td>無節目</td>
            </tr>
            <tr>
              <td>不在計畫中</td>
              <td>在清單上</td>
              <td>已開啟</td>
            </tr>
            <tr>
              <td>已註冊</td>
              <td>註冊中</td>
              <td>註冊錯誤</td>
            </tr>
            <tr>
              <td>已傳送</td>
              <td>已訂閱</td>
              <td>退訂</td>
            </tr>
            <tr>
              <td>已檢視</td>
              <td>已造訪</td>
              <td>造訪的攤位</td>
            </tr>
            <tr>
              <td>輪候</td>
              <td>網頁內容</td>
              <td></td>
            </tr>
          </tbody>
        </table></td>
    </tr>
    <tr>
      <td>更新時間*</td>
      <td>日期範圍</td>
      <td>接受具有成員startAt和endAt的JSON物件。 startAt接受代表低浮水印的日期時間，而endAt接受代表高浮水印的日期時間。 範圍必須為31天或更少。 日期時間應採用ISO-8601格式，不含毫秒。具有此篩選型別的作業會傳回日期範圍內最近更新的所有可存取記錄。</td>
    </tr>
  </tbody>
</table>

部分訂閱無法使用篩選器型別。 如果您的訂閱無法使用，您在呼叫「建立匯出程式成員工作」端點時會收到錯誤（「1035，目標訂閱不受支援的篩選器型別」）。 客戶可以聯絡Marketo支援，以便在他們的訂閱中啟用此功能。

## 選項

「建立匯出程式成員作業」端點提供數個格式選項。 這些選項讓使用者能夠：

- 指定要包含在匯出檔案中的欄位
- 重新命名這些欄位的欄標題
- 指定匯出檔案的格式

| 引數 | 資料類型 | 必填 | 附註 |
|---|---|---|---|
| 欄位 | 陣列[字串] | 是 | 欄位引數接受字串的JSON陣列。 列出的欄位會包含在匯出的檔案中。 可以匯出下列欄位型別：`LeadCustom` `LeadProgram` Membercustom `ProgramMember`. 使用REST API名稱來指定欄位，您可以使用「描述銷售機會2」和/或「描述方案成員」端點來擷取該欄位。 |
| columnHeaderName | 物件 | 否 | 包含欄位和欄標題名稱之索引鍵/值組的JSON物件。 索引鍵必須是匯出作業中包含的欄位名稱。 值是該欄位匯出的欄標題的名稱。 |
| 格式 | 字串 | 否 | 接受以下其中之一：CSV、TSV、SSV。 匯出的檔案會分別呈現為逗號分隔值、定位字元分隔值或空格分隔值檔案（如果設定）。 如果未設定，則預設為CSV。 |


## 建立工作

工作的引數是在使用開始匯出之前定義的 [建立匯出程式成員工作](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/createExportProgramMembersUsingPOST) 端點。 我們必須定義 `filter` 包含程式id，以及 `fields` 匯出所需。 我們可選擇定義 `format` ，以及 `columnHeaderNames`.

```
POST /bulk/v1/program/members/export/create.json
```

```json
{ 
   "format": "CSV",
   "fields": [ 
        "firstName",
        "lastName",
        "email",
        "membershipDate",
        "program",
        "statusName",
        "leadId",
        "reachedSuccess",
        "leadCustomField01",
        "leadCustomField02",
        "pMCustomField01",
        "pMCustomField02"
   ],
   "filter": { 
      "programId":1044
   }
}
```

```json
{
    "requestId": "4d44#16f92734f6e",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Created",
            "createdAt": "2020-01-11T02:33:48Z"
        }
    ],
    "success": true
}
```

這會傳回狀態回應，指出工作已建立。 工作已定義並建立，但尚未開始。 若要這麼做，請 [將匯出程式成員工作排入佇列](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/enqueueExportProgramMembersUsingPOST) 必須使用呼叫端點 `exportId` 從建立狀態回應：

```
POST /bulk/v1/program/members/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "d70b#16f9273ae32",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z"
        }
    ],
    "success": true
}
```

這將以初始回應 `status` 的「已排入佇列」數量，之後當有可用的匯出位置時，便會設為「正在處理」。

## 輪詢工作狀態

注意：只能為相同API使用者建立的作業擷取狀態。

由於這是非同步端點，在建立作業後，我們必須輪詢其狀態以判斷其進度。 使用 [取得匯出程式成員工作狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET) 端點。 狀態只會每60秒更新一次，因此不建議使用低於此值的輪詢頻率，並且在幾乎所有情況下仍然會太高。 狀態列位可能會以下列任一專案回應：已建立、已排入佇列、正在處理、已取消、已完成、失敗。

```
GET /bulk/v1/program/members/export/{exportId}/status.json
```

```json
{
    "requestId": "9a40#16f9274d250",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
        }
    ],
    "success": true
}
```

狀態端點回應指出工作仍在處理中，因此檔案尚不可擷取。 工作執行後 `status` 「已完成」的變更可供下載。

```json
{
    "requestId": "11ad1#16f9ff6da23",
    "result": [
        {
            "exportId": "1118dc83-273b-4d44-becb-4d212fece550",
            "format": "CSV",
            "status": "Completed",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
            "finishedAt": "2020-01-11T02:36:12Z",
            "numberOfRecords": 13,
            "fileSize": 1752,
            "fileChecksum": "sha256:b3c8e70e6e501cf1025e345a66b409d4fd07364c7da773cfa68a2b68ce1a7212"
        }
    ],
    "success": true
}
```

## 正在擷取您的資料

若要擷取已完成程式成員匯出的檔案，只需呼叫 [取得匯出程式成員檔案](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/getExportProgramMembersFileUsingGET) 端點，含您的 `exportId`.

回應包含以設定作業方式格式化的檔案。 端點會以檔案內容回應。 如果請求的方案成員欄位為空（不包含資料），則 `null` 會放置在匯出檔案的對應欄位中。

```
GET /bulk/v1/program/members/export/{exportId}/file.json
```

```
firstName,lastName,email,Member Date,Program,Status,Lead Id,Success,leadCustomField01,leadCustomField02,pMCustomField01,pMCustomField02
Meera,Reed,mree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1789,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jon,Umber,jumb@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1790,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Lyanna,Mormont,lmor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1791,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickon,Stark,rsta@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1792,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Hodor,null,hodor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1793,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Osha,null,osha@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1794,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jojen,Reed,Jree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1795,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickard,Karstark,rkar@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1796,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Maester,Luwin,mluw@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1797,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rodrik,Cassel,rcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1798,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jory,Cassel,jcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1799,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Septa,Mordane,smor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1800,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
```

為了支援擷取資料的部份擷取和便於恢復擷取，檔案端點可選擇性地支援型別位元組的HTTP標頭範圍。 如果未設定標頭，將會傳回所有內容。 您可以深入瞭解如何搭配Marketo使用Range標頭 [大量擷取](bulk-extract.md).

## 取消工作

如果工作設定錯誤或變得不必要，可以使用輕鬆取消 [取消匯出方案成員工作](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/cancelExportProgramMembersUsingPOST) 端點：

```
POST /bulk/v1/program/members/export/{exportId}/cancel.json
```

```json
{
    "requestId": "bb4f#16f86727f89",
    "result": [
        {
            "exportId": "f0d3520c-3a60-4568-9e71-2e619d3805a4",
            "format": "CSV",
            "status": "Cancelled",
            "createdAt": "2020-01-07T21:47:35Z"
        }
    ],
    "success": true
}
```

這會以回應 `status` 表示工作已取消。
