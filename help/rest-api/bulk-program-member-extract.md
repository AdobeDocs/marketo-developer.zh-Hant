---
title: 大量程式成員擷取
feature: REST API
description: 使用Marketo大量程式成員擷取REST API ，匯出大型成員記錄以進行ETL、資料倉儲和封存，並包含許可權和欄位中繼資料。
exl-id: 6e0a6bab-2807-429d-9c91-245076a34680
TQID: https://experienceleague.adobe.com/w4qaVTKSe0EORaSiURB6WbJXi29JUdEgfkb2dnfuVFw
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 1081
ht-degree: 2%

---

# 大量程式成員擷取

[大量程式成員擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members)

大量程式成員擷取REST API會從Marketo擷取大量程式成員記錄。 使用這些API在Marketo和外部系統、ETL、資料倉儲和封存之間持續交換資料。

## 權限

API使用者必須擁有具有「唯讀銷售機會」許可權和/或「讀寫銷售機會」許可權的角色。

## 說明

使用[描述方案成員](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeProgramMemberUsingGET2)來判斷哪些欄位可用，並擷取其中繼資料。 `name`屬性包含REST API欄位名稱。

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

## 篩選器

方案成員匯出支援多個篩選器選項。 當作業指定多個篩選器型別時，API會將其與AND作業結合。

每個工作都必須指定`programId`或`programIds`。 所有其他篩選器皆為選用專案。 `updatedAt`篩選器需要並非所有訂閱都可用的基礎結構。

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
      <td>接受方案的ID。 工作會傳回工作開始處理時屬於方案成員的所有可存取記錄。使用<a href="https://developer.adobe.com/marketo-apis/api/asset#tag/Programs">取得程式</a>端點擷取程式識別碼。無法搭配programIds篩選器使用。</td>
    </tr>
    <tr>
      <td>programIds</td>
      <td>Array[整數]</td>
      <td>接受最多10個計畫ID的陣列。 工作會傳回工作開始處理時屬於程式成員的所有可存取記錄。額外的欄位「programId」會新增至匯出檔案作為第一個欄位。 此欄位會識別從中擷取方案成員資格記錄的方案。使用<a href="https://developer.adobe.com/marketo-apis/api/asset#tag/Programs">取得程式</a>端點擷取程式識別碼。無法搭配programId篩選器使用。</td>
    </tr>
    <tr>
      <td>isExhausted</td>
      <td>布林值</td>
      <td>接受用於篩選已用完內容</a>的<a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content">人之方案成員資格記錄的布林值。</td>
    </tr>
    <tr>
      <td>nurtureCadence</td>
      <td>字串</td>
      <td>接受用於篩選特定Nurture步調的計畫成員資格記錄的字串。允許值為：
        <ul>
          <li>pause — 節奏已暫停</li>
          <li>標準 — 步調正常</li>
        </ul></td>
    </tr>
    <tr>
      <td>statusName</td>
      <td>Array[字串]</td>
      <td>接受程式成員狀態名稱的陣列。 多個狀態名稱會同時進行「或」運算。具有此篩選型別的工作會傳回其程式成員狀態符合任何指定狀態名稱的所有可存取記錄。 預設狀態名稱和使用者定義的狀態名稱都可以使用。如果statusNames篩選器與「programIds」篩選器一起使用，則會檢查每個方案的成員資格記錄，其狀態符合任何狀態名稱。 如果在任何程式中找不到狀態名稱，則會傳回「1003， Invalid Data」錯誤。
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
              <td>成員</td>
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
              <td>已取消訂閱</td>
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
      <td>接受具有成員startAt和endAt的JSON物件。 startAt接受代表低浮水印的日期時間，而endAt接受代表高浮水印的日期時間。 範圍必須為31天或更少。 日期時間應採用ISO-8601格式，不含毫秒。具有此篩選型別的工作會傳回日期範圍內最近更新的所有可存取記錄。</td>
    </tr>
  </tbody>
</table>

部分訂閱不支援此篩選型別。 如果無法使用，[建立匯出程式成員工作]端點會傳回`1035, Unsupported filter type for target subscription`。 請聯絡Marketo支援，為您的訂閱請求此功能。

## 選項

「建立匯出程式成員工作」端點提供下列選項：

- 指定要包含在匯出檔案中的欄位。
- 重新命名匯出的欄標題。
- 指定匯出檔案格式。

| 參數 | 資料類型 | 必要 | 附註 |
| --- | --- | --- | --- |
| 欄位 | 陣列[字串] | 是 | 欄位引數接受字串的JSON陣列。 列出的欄位會包含在匯出的檔案中。 下列欄位型別可以匯出： `LeadCustom` `LeadProgram` MemberCustom `ProgramMember`。 使用REST API名稱來指定欄位，此名稱可使用「描述銷售機會2」和/或「描述方案成員」端點來擷取。 |
| columnHeaderName | 物件 | 無 | 包含欄位和欄標題名稱之索引鍵/值組的JSON物件。 索引鍵必須是匯出作業中包含的欄位名稱。 值是該欄位匯出的欄標題的名稱。 |
| 格式 | 字串 | 無 | 接受以下其中之一：CSV、TSV、SSV。 匯出的檔案會分別呈現為逗號分隔值、定位字元分隔值或空格分隔值檔案（如果設定）。 如果未設定，則預設為CSV。 |

## 建立工作

使用[建立匯出程式成員工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/createExportProgramMembersUsingPOST)端點來定義匯出工作。 指定包含程式識別碼和要匯出的`fields`的`filter`。 您也可以指定`format`和`columnHeaderNames`。

```http
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

回應會確認工作已建立，但匯出不會自動啟動。 將傳回的`exportId`傳遞至[排入佇列匯出程式成員工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/enqueueExportProgramMembersUsingPOST)端點以啟動工作：

```http
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

排入佇列的回應最初傳回`Queued`狀態。 匯出位置可用時，狀態會變更為`Processing`。

## 輪詢工作狀態

您只能擷取相同API使用者所建立之工作的狀態。

因為匯出是以非同步方式執行，請使用[取得匯出程式成員工作狀態](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportLeadsStatusUsingGET)端點來輪詢其進度。 狀態只會每60秒更新一次，因此請勿更頻繁地輪詢。

狀態可以是`Created`、`Queued`、`Processing`、`Canceled`、`Completed`或`Failed`。

```http
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

此回應顯示工作仍在處理中，因此檔案無法使用。 當工作狀態變更為`Completed`時，檔案已可供下載。

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

若要擷取已完成的程式成員匯出，請將`exportId`傳遞至[取得匯出程式成員檔案](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportProgramMembersFileUsingGET)端點。

端點會以為作業設定的格式傳回檔案。 如果要求的程式成員欄位不包含任何資料，則對應的匯出欄位會包含`null`。

```http
GET /bulk/v1/program/members/export/{exportId}/file.json
```

```text
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

對於部分或可恢復擷取，檔案端點支援範圍型別為`bytes`的可選HTTP `Range`標頭。 如果您未設定標頭，端點會傳回整個檔案。 如需詳細資訊，請參閱[大量擷取](bulk-extract.md)。

## 取消工作

若要取消設定錯誤或不再需要的工作，請呼叫[取消匯出程式成員工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/cancelExportProgramMembersUsingPOST)端點：

```http
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

回應狀態表示作業已取消。
