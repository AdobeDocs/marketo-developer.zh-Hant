---
title: 大量活動擷取
feature: REST API
description: Marketo大量活動擷取REST API ，使用31天的日期範圍、活動和主要屬性篩選器為ETL和CRM匯出大量活動資料。
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1351'
ht-degree: 4%

---

# 大量活動擷取

[大量活動擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi/)

REST API的批次活動擷取集提供程式化介面，可從Marketo擷取大量活動資料。  對於不需要低延遲的情況，並且必須將大量活動資料從Marketo傳輸出，例如CRM整合、ETL、資料倉儲和資料封存。

## 權限

大量活動擷取API需要API使用者具有「唯讀活動」或「讀寫活動」許可權。

## 篩選器

| 篩選器型別 | 資料類型 | 必要 | 附註 |
| --- | --- | --- | --- |
| createdAt | 日期範圍 | 是 | 接受具有成員`startAt`和`endAt`的JSON物件。 `startAt`接受代表低浮水印的日期時間，而`endAt`接受代表高浮水印的日期時間。 範圍必須為31天或更少。 具有此篩選型別的工作會傳回在日期範圍內建立的所有可存取記錄。 日期時間應採用ISO-8601格式，不含毫秒。 |
| activityTypeIds | 陣列\[整數\] | 無 | 接受具有一個成員`activityTypeIds`的JSON物件。 該值必須為整數陣列，與所需的活動型別相對應。 不支援「刪除銷售機會」活動（請改用[取得刪除的銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET)端點）。 使用[取得活動型別端點](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)擷取活動型別識別碼。 |
| [primaryAttributeValueIds](#primaryattributevalueids-options) | 陣列\[整數\] | 無 | 接受具有一個成員`primaryAttributeValueIds`的JSON物件。 值是ID陣列，用於指定要篩選的主要屬性。 最多可以指定50個ID。 ID是潛在客戶欄位或資產的唯一識別碼，可透過呼叫適當的REST API端點來擷取。 例如，若要篩選「填寫表單」活動的特定表單，請將表單名稱傳遞至[依名稱取得表單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET)端點，以擷取表單ID。 以下是支援主要屬性篩選的活動型別清單。 |
| [primaryAttributeValues](#primaryattributevalues-options) | 陣列\[字串\] | 無 | 接受具有一個成員`primaryAttributeValues`的JSON物件。 值是名稱陣列，可指定要篩選的主要屬性。 最多可以指定50個名稱。 這些名稱是潛在客戶欄位或資產的唯一識別碼，可透過呼叫適當的REST API端點來擷取。 例如，若要篩選「填寫表單」活動的特定表單，請將表單ID傳遞至[依ID取得表單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端點以擷取表單名稱。 以下是支援主要屬性篩選的活動型別清單。 |

### primaryAttributeValueIds選項 {#primaryattributevalueids-options}

| 活動型別 | 主要屬性值ID | 擷取端點 | 資產群組 |
| --- | --- | --- | --- |
| 變更資料值 | 潛在客戶欄位ID | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 屬性名稱 |
| 變更分數 | 潛在客戶欄位ID | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 屬性名稱 |
| 進度中的變更狀態 | 方案ID | [依名稱取得程式](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET) | 行銷方案 |
| 新增至清單 | 靜態清單ID | [依名稱取得靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 靜態清單 |
| 從清單中移除 | 靜態清單ID | [依名稱取得靜態清單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 靜態清單 |
| 填寫表單 | 表單ID | [依名稱取得表單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET) | 網路表單 |

使用`primaryAttributeValueIds`時，`activityTypeIds`篩選器必須存在，並且僅包含符合相應資產群組的活動ID。 例如，如果您正在篩選網路表單資產，`activityTypeIds`中僅允許「填寫表單」活動型別ID。

範例要求內文：

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
| 變更資料值 | 潛在客戶欄位displayName | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 屬性名稱 |
| 變更分數 | 潛在客戶欄位displayName | [描述銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 屬性名稱 |
| 進度中的變更狀態 | 計畫名稱 | [依ID取得程式](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET) | 行銷方案 |
| 新增至清單 | 靜態清單名稱 | [依Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)取得靜態清單 | 靜態清單 |
| 從清單中移除 | 靜態清單名稱 | [依Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)取得靜態清單 | 靜態清單 |
| 填寫表單 | 表單名稱 | [依ID取得表單](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) | 網路表單 |

請注意，您必須使用「&lt;<em>方案</em>>」。&lt;<em>資產</em>>標籤法，用以指定下列資產群組的名稱：行銷計畫、靜態清單、網頁表單。 例如，名稱為「MPS Outbound」的表單位於名稱為「GL_OP_ALL_2021」的程式下，將會指定為「GL_OP_ALL_2021.MPS Outbound」。

範例要求內文：

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

使用`primaryAttributeValues`時，`activityTypeIds`篩選器必須存在，並且僅包含符合相應資產群組的活動ID。 例如，如果您正在篩選網頁表單資產，則在`activityTypeIds`中只允許使用「填寫表單」活動型別ID。 `primaryAttributeValues`和`primaryAttributeValueIds`不能一起使用。

## 選項

| 參數 | 資料類型 | 必要 | 附註 |
|---|---|---|---|
| 篩選 | 陣列[物件] | 是 | 接受篩選陣列。 陣列中必須包含正好一個`createdAt`篩選器。 可包含選用的`activityTypeIds`篩選器。 篩選器會套用至可存取的活動集，而匯出作業會傳回活動集。 |
| 格式 | 字串 | 無 | 接受下列其中一項：CSV、TSV、SSV匯出的檔案會分別呈現為逗號分隔值、定位字元分隔值或空格分隔值檔案（若有設定）。 如果未設定，則預設為CSV。 |
| columnHeaderName | 物件 | 無 | 包含欄位和欄標題名稱之索引鍵/值組的JSON物件。 索引鍵必須是匯出作業中包含的欄位名稱。 值是該欄位匯出的欄標題的名稱。 |
| 欄位 | 陣列[字串] | 無 | 包含欄位值的選擇性字串陣列。 列出的欄位會包含在匯出的檔案中。 預設會傳回下列欄位： <ul><li>`marketoGUIDleadId`</li><li> `activityDate` </li><li>`activityTypeId` </li><li>`campaignId`</li><li> `primaryAttributeValueId` </li><li>`primaryAttributeValue`</li><li> `attributes`</li></ul>。此引數可用來減少從上述清單指定子集而傳回的欄位數目： `"fields": ["leadId", "activityDate", "activityTypeId"]`。 可以指定額外的欄位`actionResult`以包含活動動作： `("succeeded", "skipped", or "failed")`。 |

## 建立工作

若要匯出記錄，您必須先定義工作以及要擷取的記錄集。  使用[建立匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST)端點建立作業。  匯出活動時，有兩個可套用的主要篩選器： `createdAt` （一律為必要）和`activityTypeIds` （選用）。  `createdAt`篩選器是用來定義使用`startAt`和`endAt`引數建立活動的日期範圍，這兩個引數都是日期時間欄位，分別代表最早允許的建立日期和最近允許的建立日期。  您也可以選擇使用`activityTypeIds`篩選器，僅篩選特定型別的活動。  這對於移除與您的使用案例無關的結果非常有用。

```
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

工作目前的狀態為「已建立」，但尚未在處理佇列中。  若要將其放入佇列以便開始處理，請使用建立狀態回應中的exportId呼叫[排入佇列匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST)端點。

```
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

現在，狀態為報告工作已排入佇列。  當背景工作可用於此工作時，狀態會切換為「處理」，且工作會開始從Marketo彙總記錄。

## 輪詢工作狀態

只能為同一API使用者建立的作業擷取作業狀態。

Marketo的大量活動擷取是非同步端點，因此必須輪詢作業狀態以判斷作業何時完成。  使用[取得匯出活動作業狀態](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET)端點進行輪詢，如下所示：

```
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

狀態列位可能會以下列其中一個值回應：

- 建立日期
- 已排入佇列
- 處理中
- 已取消
- 已完成
- 失敗

## 正在擷取您的資料

工作完成後，請使用[取得匯出活動檔案](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET)端點擷取您的資料。

```
GET /bulk/v1/activities/export/{exportId}/file.json
```

回應包含以設定作業方式格式化的檔案。 端點會以檔案內容回應。

如果請求的潛在客戶欄位為空（不包含任何資料），則會將`then null`放置在匯出檔案中的對應欄位中。  在下列範例中，傳回活動的`campaignId`欄位是空的。

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

為了支援擷取資料的部分擷取和便於恢復擷取，檔案端點可選擇性地支援型別`Range`的HTTP標頭`bytes`。  如果未設定標頭，則會傳回所有內容。  您可以閱讀更多有關搭配Marketo [大量擷取](bulk-extract.md)使用Range標頭的資訊。

## 取消工作

如果工作設定不正確或變得不必要，可以使用[取消匯出活動工作](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST)端點輕鬆取消工作：

```
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

此回應的狀態表示工作已取消。
