---
title: 大量活動擷取
feature: REST API
description: 從Marketo批次處理活動資料。
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1381'
ht-degree: 1%

---

# 大量活動擷取

[大量活動擷取端點參考](https://developer.adobe.com/marketo-apis/api/mapi/)

REST API的批次活動擷取集提供程式化介面，可從Marketo擷取大量活動資料。  對於不需要低延遲的情況，並且必須將大量活動資料從Marketo傳輸出，例如CRM整合、ETL、資料倉儲和資料封存。

## 權限

大量活動擷取API需要API使用者具有「唯讀活動」或「讀寫活動」許可權。

## 篩選器

<table>
  <tbody>
    <tr>
      <td>篩選器型別</td>
      <td>資料類型</td>
      <td>必要</td>
      <td>附註</td>
    </tr>
    <tr>
      <td>createdAt</td>
      <td>日期範圍</td>
      <td>是</td>
      <td>接受具有成員startAt和endAt的JSON物件。 startAt接受代表低浮水印的日期時間，而endAt接受代表高浮水印的日期時間。 範圍必須是31天或更短。具有此篩選型別的工作會傳回在日期範圍內建立的所有可存取記錄。日期時間應該是ISO-8601格式，不帶毫秒。</td>
    </tr>
    <tr>
      <td>activityTypeIds</td>
      <td>Array[整數]</td>
      <td>否</td>
      <td>接受具有一個成員activityTypeIds的JSON物件。 值必須是整數陣列，對應於所需的活動型別。不支援「刪除銷售機會」活動（請改用<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET">取得刪除的銷售機會</a>端點）。使用<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET">取得活動型別</a>端點擷取活動型別識別碼。</td>
    </tr>
    <tr>
      <td>primaryAttributeValueIds</td>
      <td>Array[整數]</td>
      <td>否</td>
      <td>接受具有一個成員primaryAttributeValueIds的JSON物件。 此值是ID陣列，用於指定要篩選的主要屬性。 最多可指定50個ID。這些ID是潛在客戶欄位或資產的唯一識別碼，可透過呼叫適當的REST API端點來擷取。 例如，若要篩選「填寫表單」活動的特定表單，請將表單名稱傳遞給<a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">依名稱取得表單</a>端點，以擷取表單ID。以下是支援主要屬性篩選的活動型別清單。
        <table>
          <tbody>
            <tr>
              <td>活動型別</td>
              <td>主要屬性值ID</td>
              <td>擷取端點</td>
              <td>資產群組</td>
            </tr>
            <tr>
              <td>變更資料值</td>
              <td>潛在客戶欄位ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述銷售機會</a></td>
              <td>屬性名稱</td>
            </tr>
            <tr>
              <td>變更分數</td>
              <td>潛在客戶欄位ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述銷售機會</a></td>
              <td>屬性名稱</td>
            </tr>
            <tr>
              <td>進度中的變更狀態</td>
              <td>方案ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET">依名稱取得計畫</a></td>
              <td>行銷方案</td>
            </tr>
            <tr>
              <td>新增至清單</td>
              <td>靜態清單ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">依名稱取得靜態清單</a></td>
              <td>靜態清單</td>
            </tr>
            <tr>
              <td>從清單移除</td>
              <td>靜態清單ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">依名稱取得靜態清單</a></td>
              <td>靜態清單</td>
            </tr>
            <tr>
              <td>填寫表單</td>
              <td>表單ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">依名稱取得表單</a></td>
              <td>網路表單</td>
            </tr>
          </tbody>
        </table>
        使用primaryAttributeValueIds時，activityTypeIds篩選器必須存在，並且僅包含符合相對應資產群組的活動ID。例如：如果您正在篩選網頁表單資產，activityTypeIds中僅允許「填寫表單」活動型別ID。範例請求內文：{"filter"：{"createdAt"：{"startAt"： "2021-07-01T23:59:59-00:00"，"end2 1-07-02T23:59:59-00:00"}，"activityTypeIds"：[2]，"primaryAttributeValueIds" ： [16,102,95,8]}}primaryAttributeValueIds和primaryAttributeValues不能一起使用。</td>
    </tr>
    <tr>
      <td>primaryAttributeValues</td>
      <td>Array[字串]</td>
      <td>否</td>
      <td>接受具有一個成員primaryAttributeValues的JSON物件。 值是名稱陣列，可指定要篩選的主要屬性。 最多可指定50個名稱。這些名稱是潛在客戶欄位或資產的唯一識別碼，可透過呼叫適當的REST API端點來擷取。 例如，若要篩選「填寫表單」活動的特定表單，請將表單ID傳遞至<a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">依ID取得表單</a>端點以擷取表單名稱。以下是支援主要屬性篩選的活動型別清單。
        <table>
          <tbody>
            <tr>
              <td>活動型別</td>
              <td>主要屬性值</td>
              <td>擷取端點</td>
              <td>資產群組</td>
            </tr>
            <tr>
              <td>變更資料值</td>
              <td>潛在客戶欄位displayName</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述銷售機會</a></td>
              <td>屬性名稱</td>
            </tr>
            <tr>
              <td>變更分數</td>
              <td>潛在客戶欄位displayName</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述銷售機會</a></td>
              <td>屬性名稱</td>
            </tr>
            <tr>
              <td>進度中的變更狀態</td>
              <td>計畫名稱</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET">依ID取得計畫</a></td>
              <td>行銷方案</td>
            </tr>
            <tr>
              <td>新增至清單</td>
              <td>靜態清單名稱</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">依Id取得靜態清單</a></td>
              <td>靜態清單</td>
            </tr>
            <tr>
              <td>從清單移除</td>
              <td>靜態清單名稱</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">依Id取得靜態清單</a></td>
              <td>靜態清單</td>
            </tr>
            <tr>
              <td>填寫表單</td>
              <td>表單名稱</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">依ID取得表單</a></td>
              <td>網路表單</td>
            </tr>
          </tbody>
        </table>
        請注意，您必須使用「&lt;<em>方案</em>&gt;」。&lt;<em>資產</em>&gt;」標籤法，用來唯一指定下列資產群組的名稱：行銷計畫、靜態清單、Web表單。例如：位於名為「GL_OP_ALL_2021」的計畫下且名稱為「MPS Outbound」的表單，將會指定為「GL_OP_ALL_2021.MPS Outbound」。範例要求內文：{"filter"：{"createdAt"：{"startAt"： "2021-07 -01T23:59:59-00:00"，"endAt"： "2021-07-02T23:59:59-00:00"}，"activityTypeIds"：[2]，"primaryAttributeValues"：["GL_OP_ALL_2021.MPS Outbound"]}使用primaryAttributeValues時，actics過濾器必須存在並且僅包含匹配相應活動ID資產組。 例如，如果您正在篩選網頁表單資產，activityTypeIds.primaryAttributeValues中只允許使用「填寫表單」活動型別ID，而primaryAttributeValueIds則無法同時使用。</td>
    </tr>
  </tbody>
</table>

## 選項

| 引數 | 資料類型 | 必要 | 附註 |
|---|---|---|---|
| 篩選 | 陣列[物件] | 是 | 接受篩選陣列。 陣列中必須包含正好一個createdAt篩選器。 其中可能包括選用的activityTypeIds篩選器。這些篩選器會套用至可存取的活動集，而匯出作業會傳回活動集。 |
| 格式 | 字串 | 否 | 接受下列其中一項：CSV、TSV、SSV如果設定，匯出的檔案將分別呈現為逗號分隔值、定位字元分隔值或空格分隔值檔案。如果未設定，則預設為CSV。 |
| columnHeaderName | 物件 | 否 | 包含欄位和欄標題名稱之索引鍵/值組的JSON物件。 索引鍵必須是匯出作業中包含的欄位名稱。 值是該欄位匯出的欄標題的名稱。 |
| 欄位 | 陣列[字串] | 否 | 包含欄位值的選擇性字串陣列。 列出的欄位會包含在匯出的檔案中。預設會傳回下列欄位： `marketoGUIDleadId` `activityDate` `activityTypeId` `campaignId` `primaryAttributeValueId` `primaryAttributeValueattributes`。此引數可用於減少從上述清單指定子集所傳回的欄位數目。範例： &quot;fields&quot;： [&quot;leadId&quot;， &quot;activityDate&quot;， &quot;activityTypeId&quot;]可指定其他欄位&quot;actionResult&quot;以包含活動動作(&quot;succeeded&quot;， &quot;skipped&quot;， &quot;failed&quot;)。 |


## 建立工作

若要匯出記錄，您必須先定義工作以及要擷取的記錄集。  使用[建立匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST)端點建立作業。  匯出活動時，有兩個可套用的主要篩選器： `createdAt` （一律為必要）和`activityTypeIds` （選用）。  createdAt篩選器是用來使用`startAt`和`endAt`引數定義建立活動的日期範圍，這兩個引數都是日期時間欄位，分別代表最早允許的建立日期和最近允許的建立日期。  您也可以選擇使用`activityTypeIds`篩選器，僅篩選特定型別的活動。  這對於移除與您的使用案例無關的結果非常有用。

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

工作目前的狀態為「已建立」，但尚未在處理佇列中。  若要將其放入佇列以便開始處理，我們必須使用建立狀態回應中的exportId來呼叫[排入佇列匯出活動作業](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST)端點。

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

現在，狀態為報告工作已排入佇列。  當背景工作可用於此工作時，狀態會切換為「處理」，且工作將開始從Marketo彙總記錄。

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

- 建立時間
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

如果請求的潛在客戶欄位為空（不包含任何資料），則會將`then null`放置在匯出檔案中的對應欄位中。  在以下範例中，傳回活動的campaignId欄位為空白。

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

為了支援擷取資料的部分擷取和便於恢復擷取，檔案端點可選擇性地支援型別`bytes`的HTTP標頭`Range`。  如果未設定標頭，將會傳回所有內容。  您可以閱讀更多有關搭配Marketo [大量擷取](bulk-extract.md)使用Range標頭的資訊。

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
