---
title: 大量活動擷取
feature: REST API
description: Marketo大量活動擷取REST API ，使用31天的日期範圍、活動和主要屬性篩選器為ETL和CRM匯出大量活動資料。
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
TQID: https://experienceleague.adobe.com/lIlXNjatN-F77Dv3xsVkQ3hAWwLZ4wlSW0zKNkFJFMA
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: e64968b2-4ee5-47f9-8cae-0588f184b9ebid: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 1212
ht-degree: 4%

---

# 大量活動擷取

[大量活動擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi)

大量活動擷取REST API會從Marketo擷取大量活動資料。 將這些API用於不需要低延遲的流程，例如CRM整合、ETL、資料倉儲和資料封存。

## 權限

API使用者必須具有「唯讀活動」或「讀寫活動」許可權。

## 篩選器

| 篩選器型別 | 資料類型 | 必要 | 附註 |
| --- | --- | --- | --- |
| `createdAt` | 日期範圍 | 是 | 包含`startAt`和`endAt`的JSON物件。 `startAt`是低浮水印日期時間，`endAt`是高浮水印日期時間。 範圍必須為31天或更少。 此工作會傳回在日期範圍內建立的所有可存取記錄。 使用ISO-8601日期時間值（不含毫秒）。 |
| `activityTypeIds` | 陣列\[整數\] | 無 | 要求之活動型別的整數陣列。 不支援「刪除銷售機會」活動。 請改用[Get Deleted Leads](https://developer.adobe.com/marketo-apis/api/mapi#operation/getDeletedLeadsUsingGET)端點。 使用[取得活動型別端點](https://developer.adobe.com/marketo-apis/api/mapi#operation/getAllActivityTypesUsingGET)擷取活動型別識別碼。 |
| [`primaryAttributeValueIds`](#primaryattributevalueids-options) | 陣列\[整數\] | 無 | 主要屬性接受最多50個ID的陣列。 每個id可唯一識別潛在客戶欄位或資產。 呼叫適當的REST API端點以擷取id。 例如，若要篩選「填寫表單」活動的特定表單，請將表單名稱傳遞至[依名稱取得表單](https://developer.adobe.com/marketo-apis/api/asset#operation/getLpFormByNameUsingGET)端點，以擷取表單ID。 如需支援的活動型別，請參閱[primaryAttributeValueIds選項](#primaryattributevalueids-options)。 |
| [`primaryAttributeValues`](#primaryattributevalues-options) | 陣列\[字串\] | 無 | 接受主要屬性最多50個名稱的陣列。 每個名稱可唯一識別潛在客戶欄位或資產。 呼叫適當的REST API端點以擷取名稱。 例如，若要篩選「填寫表單」活動的特定表單，請將表單ID傳遞至[依ID取得表單](https://developer.adobe.com/marketo-apis/api/asset#operation/getLpFormByIdUsingGET)端點以擷取表單名稱。 如需支援的活動型別，請參閱[primaryAttributeValues選項](#primaryattributevalues-options)。 |

### primaryAttributeValueIds選項 {#primaryattributevalueids-options}

| 活動型別 | 主要屬性值ID | 擷取端點 | 資產群組 |
| --- | --- | --- | --- |
| 變更資料值 | 潛在客戶欄位ID | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_2) | 屬性名稱 |
| 變更分數 | 潛在客戶欄位ID | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_2) | 屬性名稱 |
| 進度中的變更狀態 | 方案ID | [依名稱取得程式](https://developer.adobe.com/marketo-apis/api/asset#operation/getProgramByNameUsingGET) | 行銷方案 |
| 新增至清單 | 靜態清單ID | [依名稱取得靜態清單](https://developer.adobe.com/marketo-apis/api/asset#operation/getStaticListByNameUsingGET) | 靜態清單 |
| 從清單中移除 | 靜態清單ID | [依名稱取得靜態清單](https://developer.adobe.com/marketo-apis/api/asset#operation/getStaticListByNameUsingGET) | 靜態清單 |
| 填寫表單 | 表單ID | [依名稱取得表單](https://developer.adobe.com/marketo-apis/api/asset#operation/getLpFormByNameUsingGET) | 網路表單 |

使用`primaryAttributeValueIds`時，您也必須包含`activityTypeIds`篩選器。 此篩選器僅可包含符合相應資產群組的活動ID。 例如，篩選網頁表單資產時，`activityTypeIds`只能包含「填寫表單」活動型別識別碼。

下列要求包含`primaryAttributeValueIds`篩選器：

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValueIds": [
      16,102,95,8
    ]
  }
}
```

`primaryAttributeValueIds`和`primaryAttributeValues`不能一起使用。

### primaryAttributeValues選項 {#primaryattributevalues-options}

| 活動型別 | 主要屬性值 | 擷取端點 | 資產群組 |
| --- | --- | --- | --- |
| 變更資料值 | 潛在客戶欄位displayName | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_2) | 屬性名稱 |
| 變更分數 | 潛在客戶欄位displayName | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#operation/describeUsingGET_2) | 屬性名稱 |
| 進度中的變更狀態 | 計畫名稱 | [依ID取得程式](https://developer.adobe.com/marketo-apis/api/asset#operation/getProgramByIdUsingGET) | 行銷方案 |
| 新增至清單 | 靜態清單名稱 | [依Id](https://developer.adobe.com/marketo-apis/api/asset#operation/getStaticListByIdUsingGET)取得靜態清單 | 靜態清單 |
| 從清單中移除 | 靜態清單名稱 | [依Id](https://developer.adobe.com/marketo-apis/api/asset#operation/getStaticListByIdUsingGET)取得靜態清單 | 靜態清單 |
| 填寫表單 | 表單名稱 | [依ID取得表單](https://developer.adobe.com/marketo-apis/api/asset#operation/getLpFormByIdUsingGET) | 網路表單 |

使用`&lt;program&gt;.&lt;asset&gt;`標籤法指定行銷方案、靜態清單和網頁表單資產群組的名稱。 例如，將「GL_OP_ALL_2021」程式中的「MPS出站」表單指定為「GL_OP_ALL_2021.MPS出站」。

下列要求包含`primaryAttributeValues`篩選器：

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValues": [
      "GL_OP_ALL_2021.MPS Outbound"
    ]
  }
}
```

使用`primaryAttributeValues`時，您也必須包含`activityTypeIds`篩選器。 此篩選器僅可包含符合相應資產群組的活動ID。 例如，篩選網頁表單資產時，`activityTypeIds`只能包含「填寫表單」活動型別識別碼。

`primaryAttributeValues`和`primaryAttributeValueIds`不能一起使用。

## 選項

| 參數 | 資料類型 | 必要 | 附註 |
| --- | --- | --- | --- |
| `filter` | 物件 | 是 | 包含套用至可存取活動集的篩選器的物件。 僅包含一個`createdAt`篩選器。 您也可以包含`activityTypeIds`篩選器。 匯出作業會傳回活動集合。 |
| `format` | 字串 | 無 | 匯出檔案格式：CSV、TSV或SSV。 這些值會分別產生逗號分隔、定位字元分隔或空格分隔的值。 預設值為CSV。 |
| `columnHeaderNames` | 物件 | 無 | 欄位和欄標題索引鍵值配對的JSON物件。 每個金鑰都必須為匯出作業中包含的欄位命名。 其值會設定該欄位的匯出欄標題。 |
| `fields` | 陣列\[字串\] | 無 | 要包含在匯出檔案中的欄位陣列。 依預設，回應包含`marketoGUID`、`leadId`、`activityDate`、`activityTypeId`、`campaignId`、`primaryAttributeValueId`、`primaryAttributeValue`和`attributes`。 若要傳回子集，請指定此清單中的欄位，例如`"fields": ["leadId", "activityDate", "activityTypeId"]`。 您也可以指定`actionResult`以包含活動動作： `("succeeded", "skipped", or "failed")`。 |

## 建立工作

建立匯出工作以定義要擷取的記錄。 使用[建立匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi#operation/createExportActivitiesUsingPOST)端點。

每個工作都需要`createdAt`篩選器。 其`startAt`和`endAt`日期時間引數會定義最早和最新的允許活動建立日期。 若要排除不相關的活動型別，請一併包含選用的`activityTypeIds`篩選器。

下列請求會針對日期範圍內選取的活動型別建立CSV匯出作業：

```http
POST /bulk/v1/activities/export/create.json
```

```json
{
   "format": "CSV",
   "filter": {
      "createdAt": {
         "startAt": "2017-07-01T23:59:59-00:00",
         "endAt": "2017-07-31T23:59:59-00:00"
      },
      "activityTypeIds": [
         1,
         12,
         13
      ]
   }
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

回應傳回`exportId`和「已建立」狀態。 建立的工作尚未在處理佇列中。

若要將工作加入佇列，請從建立回應呼叫[佇列匯出活動工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/enqueueExportActivitiesUsingPOST)端點（含`exportId`）。

```http
POST /bulk/v1/activities/export/{exportId}/enqueue.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Queued",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

回應狀態現在為「已排入佇列」。 當背景工作可供使用時，狀態會變更為「處理中」，且工作會開始從Marketo彙總記錄。

## 輪詢工作狀態

只能為同一API使用者建立的作業擷取作業狀態。

大量活動擷取會以非同步方式處理工作。 輪詢[取得匯出活動作業狀態](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportActivitiesStatusUsingGET)端點以判斷作業何時完成：

```http
GET /bulk/v1/activities/export/{exportId}/status.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 15423,
         "fileSize": 12342,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
   ]
}
```

`status`欄位會傳回下列其中一個值：

- `Created`
- `Queued`
- `Processing`
- `Canceled`
- `Completed`
- `Failed`

## 正在擷取您的資料

當作業狀態為「已完成」時，請使用[取得匯出活動檔案](https://developer.adobe.com/marketo-apis/api/mapi#operation/getExportActivitiesFileUsingGET)端點擷取匯出的資料：

```http
GET /bulk/v1/activities/export/{exportId}/file.json
```

回應內文包含為工作設定的格式的檔案。

如果要求的活動欄位不包含任何資料，`null`會顯示在對應的export-file欄位中。 下列範例顯示匯出的活動資料：

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

對於部分或可恢復擷取，檔案端點支援具有`bytes`範圍的選用HTTP `Range`標頭。 如果忽略此標頭，端點會傳回整個檔案。 如需使用`Range`標頭的詳細資訊，請參閱[大量擷取](bulk-extract.md)。

## 取消工作

若要停止設定錯誤或不必要的工作，請呼叫[取消匯出活動工作](https://developer.adobe.com/marketo-apis/api/mapi#operation/cancelExportActivitiesUsingPOST)端點：

```http
POST /bulk/v1/activities/export/{exportId}/cancel.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Cancelled",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "format": "CSV"
      }
   ]
}
```

回應狀態表示作業已取消。
