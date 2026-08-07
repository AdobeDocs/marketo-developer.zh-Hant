---
title: 電子郵件
feature: REST API
description: 瞭解如何使用Marketo Asset REST API，依ID、名稱或檔案夾瀏覽來查詢和管理電子郵件資產，並附上預測性內容和A/B測試限制的附註。
exl-id: 6875730d-c74a-42cf-a3d2-dad7a3ac535d
TQID: https://experienceleague.adobe.com/t2FyPbwS836MvOe5rL0rVS7ibtzzZMmXwmgHBDZEr8Q
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 1763
ht-degree: 1%

---

# 電子郵件

[電子郵件端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails)

使用電子郵件REST端點來查詢和管理電子郵件資產。

如果電子郵件包含[Marketo預測性內容](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/predictive-content/working-with-predictive-content/understanding-predictive-content)，則下列端點會失敗，錯誤碼為709，並顯示對應的錯誤訊息：

- [取得電子郵件內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailContentByIdUsingGET)
- [更新電子郵件內容區段](https://developer.adobe.com/marketo-apis/api/asset#operation/updateEmailComponentContentUsingPOST)
- [核准電子郵件草稿](https://developer.adobe.com/marketo-apis/api/asset#operation/approveDraftUsingPOST)

## 查詢

電子郵件支援與範本相同的查詢模式：識別碼[&#128279;](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailByIdUsingGET)的[、名稱](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailByNameUsingGET)的以及[瀏覽的](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailUsingGET)。 by-name和瀏覽端點也支援資料夾篩選。

如果電子郵件屬於使用[A/B測試](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/email-test-a-b-test/add-an-a-b-test)的電子郵件程式，則下列端點不會傳回該電子郵件：

- [依ID取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailByIdUsingGET)
- [依名稱取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailByNameUsingGET)
- [取得電子郵件](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailUsingGET)

通話表示成功，但包含警告`No assets found for the given search criteria.`

### 依ID

```http
GET /rest/asset/v1/email/1351.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"9ad0#14a1832af8c",
   "result":[
      {
         "id":1356,
         "name":"sakZxhxkwV",
         "description":"sample description",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "subject":{
            "type":"Text",
            "value":"sample subject"
         },
         "fromName":{
            "type":"Text",
            "value":"RBxEtmdQZz"
         },
         "fromEmail":null,
         "replyEmail":{
            "type":"Text",
            "value":"Qlikf@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":10421
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":false,
         "template":338,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
         "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### 依名稱

依名稱查詢時，可選擇傳遞資料夾，以將搜尋限制在該資料夾。

```http
GET /rest/asset/v1/email/byName.json?name=My Email&folder={"id":1056,"type"="Folder"}
```

```json
{
   "success":true,
   "warnings":[
   ],
   "errors":[
   ],
   "requestId":"3a7f#14c484de875",
   "result":[
      {
         "id":1032,
         "name":"My Email",
         "description":"eCjxjIHmYPLtecoSphkvIXlrygOBDLhgyQKnsKMpiKWgSCKhkPMUFvFPUvEylmFiLjQGnffXGaiNLxAwiFOmIDvxEINoaSYascJw",
         "createdAt":"2015-03-23T20:23:25Z+0000",
         "updatedAt":"2015-03-23T20:23:25Z+0000",
         "subject":{
            "type":"Text",
            "value":"ezyKBmDcyCcUIrXASrLSvRuWQgWpRZxQstJoStgMSLEBASGKMpAnVeWrgJsaVFoFJUEXhEIPpDAWpzajzingUruFpiMcRRwtoBzU"
         },
         "fromName":{
            "type":"Text",
            "value":"dAiqRNJOdY"
         },
         "fromEmail":{
            "type":"Text",
            "value":"ilZxG@testmail.com"
         },
         "replyEmail":{
            "type":"Text",
            "value":"VYsCS@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":1056
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":"draft",
         "template":32,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
        "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### 瀏覽

電子郵件瀏覽遵循標準Asset API模式，並支援這些選擇性篩選器：

- `status`： `Approved`或`Draft`。
- `folder`：包含`id`和`type`的JSON物件。
- `earliestUpdatedAt`和`latestUpdatedAt`：更新時間範圍。
- `maxReturn`：要傳回的結果數。 預設值為20，最大值為200。
- `offset`：與`maxReturn`搭配使用以分頁瀏覽大型結果集。 預設值為0。

```http
GET /rest/asset/v1/emails.json?maxReturn=3&folder={"id":341,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "17576#14e22eb29cb",
    "result": [
        {
            "id": 2137,
            "name": "Social Sharing in Email",
            "description": "",
            "createdAt": "2011-03-04T17:12:42Z+0000",
            "updatedAt": "2011-03-04T19:04:36Z+0000",
            "url": null,
            "subject": {
                "type": "Text",
                "value": "Republish this content to your favorite social site!"
            },
            "fromName": {
                "type": "Text",
                "value": "Demo Master Marketo"
            },
            "fromEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 341,
                "folderName": "Social Media"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": true,
            "status": "approved",
            "template": null,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
               {
                 "attributeId": "157",
                 "objectName": "lead",
                 "displayName": "Lead Owner Email Address",
                 "apiName": null
               }
             ],
            "preHeader": "My awesome preheader!"
        }
    ]
}
```

## 查詢內容

若要[擷取電子郵件的可編輯區段](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailContentByIdUsingGET)，請查詢其內容。 選擇性地依狀態篩選，以從「已核准」或「草擬」版本傳回區段。

```http
GET /rest/asset/v1/email/1356/content.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"8a44#14c484de8c8",
   "result":[
      {
         "htmlId":"edit_text_3",
         "value":[
            {
               "type":"HTML",
               "value":"Content from testCreateEmailTemplate2"
            },
            {
               "type":"Text",
               "value":"Content from testCreateEmailTemplate2"
            }
         ],
         "contentType":"Text"
      }
   ]
}
```

區段可以有`dynamicContent`型別。 如需詳細資訊，請參閱[動態內容](dynamic-content.md)。

## 查詢副本欄位

呼叫[取得電子郵件副本欄位](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailCCFieldsUsingGET)端點以擷取目標執行個體中啟用電子郵件副本的欄位。

```http
GET /rest/asset/v1/email/ccFields.json
```

```json
{
   "success":true,
   "errors":[ ],
   "requestId":"e54b#16796fdbd4e",
   "warnings":[ ],
   "result":[
      {
         "attributeId":"157",
         "objectName":"lead",
         "displayName":"Lead Owner Email Address",
         "apiName":"leadOwnerEmailAddress"
      },
      {
         "attributeId":"396",
         "objectName":"company",
         "displayName":"Account Owner Email Address",
         "apiName":"accountOwnderEmailAddress"
      }
   ]
}
```

## 建立和更新

[從來源範本建立電子郵件](https://developer.adobe.com/marketo-apis/api/asset#operation/createEmailUsingPOST)。 電子郵件的可編輯區段來自範本的HTML元素，這些元素具有`mktEditable`類別和唯一的`id`屬性。

「建立電子郵件」呼叫需要下列引數：

- `name`：電子郵件名稱。
- `template`：來源範本。
- `folder`：父資料夾。

選擇性建立引數是`subject`、`fromName`、`fromEmail`、`replyEmail`、`operational`和`isOpenTrackingDisabled`。 省略這些引數時，將會套用下列預設值：

- `subject`是空的。
- `fromName`、`fromEmail`和`replyEmail`使用執行個體預設值。
- `operational`和`isOpenTrackingDisabled`是`false`。

`isOpenTrackingDisabled`引數會判斷傳送的電子郵件是否包含開啟追蹤畫素。

```http
POST /rest/asset/v1/emails.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=My New Email 02 - deverly&folder={"id":1017,"type":"Program"}&template=24&description=This is a test email&subject=Hey There&fromName=SomeBody&fromEmail=somebody@marketo.com&replyEmail=somebody@marketo.com
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "My New Email 02 - deverly",
            "description": "This is a test email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

若要[更新電子郵件](https://developer.adobe.com/marketo-apis/api/asset#operation/updateEmailContentUsingPOST)，請傳遞其ID並更新電子郵件的說明或名稱。

```http
POST /rest/asset/v1/email/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
description=This is an Email&name=Updated Email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "Updated Email",
            "description": "This is an Email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

### 內容區段、型別和更新

個別更新每個電子郵件內容區段。 使用[更新電子郵件內容](https://developer.adobe.com/marketo-apis/api/asset#operation/updateEmailContentUsingPOST)端點來更新`subject`、`fromName`、`fromEmail`和`replyEmail`。 此端點也可讓您設定這些值，以使用動態內容而非靜態內容。

每個引數都是型別/值JSON物件。 型別是`Text`或`DynamicContent`。 此值是動態內容所使用的對應文字或分段ID。 以POST形式與`application/x-www-form-urlencoded`傳送資料，而非以JSON傳送。 您也可以使用更新電子郵件內容來設定`isOpenTrackingDisabled`。

```http
POST /rest/asset/v1/email/{id}/content.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
subject={"type":"Text","value":"Gettysburg Address"}&fromEmail={"type":"Text","value":"abe@testmail.com"}&fromName={"type":"Text","value":"Abe Lincoln"}&replyTO={"type":"Text","value":"replies@testmail.com"}
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"c865#14a1832afac",
   "result":[
      {
         "id":1356
      }
   ]
}
```

若要設定區段以使用動態內容，請先使用「取得電子郵件內容」擷取其區段ID。

### 更新可編輯區段

依其`htmlId`更新可編輯的區段。 電子郵件`id`和區段`htmlId`是必要的路徑引數。 `type`、`value`和`textValue`引數為選用引數。

`type`決定`value`的內容：

- `Text`： `value`是包含區段HTML內容的字串。
- `DynamicContent`： `value`是JSON物件，其中包含`type`設定為`DynamicContent`、`segmentation`設定為分段ID，以及`default`設定為預設HTML內容。
- `Snippet`：支援的區段型別。

選用的`textValue`引數包含區段的文字版本。 以POST形式與`application/x-www-form-urlencoded`傳送資料，而非以JSON傳送。

```http
POST /rest/asset/v1/email/{id}/content/{htmlId}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
type=Text&value=<h1>Hello World!</h1>&textValue=Hello World!
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "155ac#14d58dfa9ad",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

如果內嵌程式碼片段的「自動複製到文字」功能已停用，則更新HTML程式碼片段然後更新其他區段的文字版本，會導致電子郵件文字版本反映更新的程式碼片段HTML。 當自動複製停用時，它無法如預期保留先前的程式碼片段文字。

## 模組

在電子郵件編輯器1.0中，模組是範本中定義的電子郵件區段。 模組可以包含元素、變數及其他HTML內容，如[電子郵件範本語法](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Modules)中所述。

使用模組API來管理電子郵件中的模組。 針對使用HTTP POST的模組端點，將要求內文格式化為`application/x-www-form-urlencoded`，而非JSON。

大部分模組端點都需要`moduleId`做為路徑引數。 [取得電子郵件內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailContentByIdUsingGET)端點傳回`htmlId`屬性中的模組ID。 請參閱[查詢](#modules_query)。

### 查詢

若要使用模組，請指定可唯一識別模組的`moduleId`。 您可能還需要整數模組索引，以說明模組在電子郵件中的順序。

若要[擷取模組ID及其索引](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailContentByIdUsingGET)，請將電子郵件ID指定為路徑引數。

下列範例會根據範本選擇器UI的[入門範本]區段中的`Skeleton`範本，來查詢1.0電子郵件。

```http
GET /rest/asset/v1/email/{moduleId}/content.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "3d79#158da6492bd",
  "result": [
    {
      "htmlId": "free-image",
      "contentType": "Module",
      "index": 1,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "single",
      "value": {
        "width": "600",
        "altText": "",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 600px"
      },
      "contentType": "Image",
      "parentHtmlId": "free-image",
      "isLocked": false
    },
    {
      "htmlId": "video",
      "contentType": "Module",
      "index": 2,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "video2",
      "value": {

      },
      "contentType": "Video",
      "parentHtmlId": "video",
      "isLocked": false
    },
    {
      "htmlId": "free-text",
      "contentType": "Module",
      "index": 3,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "text",
      "value": [
        {
          "type": "HTML",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        },
        {
          "type": "Text",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "free-text",
      "isLocked": false
    },
    {
      "htmlId": "two-articles",
      "contentType": "Module",
      "index": 6,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "article3",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text2",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "article4",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle2",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text3",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "footer",
      "contentType": "Module",
      "index": 7,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "footerText",
      "value": [
        {
          "type": "HTML",
          "value": "<p style=\"text-align: center;\"><span style=\"color: #333333;\"><strong>Acme, Inc<\/strong><\/span><\/p> \n<div style=\"text-align: center;\">\n  You received this because you have subscribed to our newsletter. Click \n <a href=\"{{system.unsubscribeLink}}\" target=\"_blank\" class=\"mktNoTrack\">here<\/a> to unsubscribe. \n <br> \n<\/div>"
        },
        {
          "type": "Text",
          "value": "Acme, Inc \n You received this because you have subscribed to our newsletter. Click here <{{system.unsubscribeLink}}> to unsubscribe."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "footer",
      "isLocked": false
    },
    {
      "htmlId": "spacer",
      "contentType": "Module",
      "index": 0,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "CTA",
      "contentType": "Module",
      "index": 4,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "hr",
      "contentType": "Module",
      "index": 5,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    }
  ]
}
```

結果陣列同時包含模組和HTML元素說明。 模組元素有`Module`的`contentType`且包含`index`。 `htmlId`屬性包含`moduleId`。

在`Skeleton`範例中，下表將每個`moduleId`對應至電子郵件中的索引。

| moduleId （亦即htmlId） | 索引 |
| --- | --- |
| 分隔符號 | 0 |
| free-image | 1 |
| 視訊 | 2 |
| 任意文字 | 3 |
| CTA | 4 |
| 小時 | 5 |
| 兩篇文章 | 6 |
| 頁尾 | 7 |

#### 新增

若要[新增模組](https://developer.adobe.com/marketo-apis/api/asset#operation/addModuleUsingPOST)，請從電子郵件的範本中選取現有的模組。 將電子郵件ID和`moduleId`指定為路徑引數。 必要的`index`查詢引數會決定模組的位置。 如果`index`超過最大的現有索引，API會將模組附加至電子郵件。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/add.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
index=10
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "1063e#158d6ad2c3f",
    "result": [
        {
            "id": 1028
        }
    ]
}
```

#### 刪除

若要[刪除模組](https://developer.adobe.com/marketo-apis/api/asset#operation/deleteModuleUsingPOST)，請指定電子郵件識別碼和`moduleId`作為路徑引數。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/delete.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "2356#158d6f6104a",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### 複製

若要[複製模組](https://developer.adobe.com/marketo-apis/api/asset#operation/duplicateModuleUsingPOST)，請指定電子郵件識別碼和`moduleId`作為路徑引數。 API會將重複專案放置在原始模組下方，並將剩餘模組往下移。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "e740#158d705d967",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### 重新排列

若要[重新排列模組](https://developer.adobe.com/marketo-apis/api/asset#operation/rearrangeModulesUsingPOST)，請提交包含每個模組及其所需位置的陣列。 每個陣列元素都是格式為`{ "index": <_index_>, "moduleId": "<_moduleId_>" }`的JSON物件，其中`<_index_>`是以零為主的模組位置，`<_moduleId_>`是模組識別碼。

```http
POST /rest/asset/v1/email/{id}/content/rearrange.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
positions=[ {"index": 0, "moduleId": "free-image"}, {"index": 1, "moduleId": "title"}, {"index": 2, "moduleId": "mkvideo"}, {"index": 3, moduleId": "free-text"}, {"index": 4, "moduleId": "blankSpace"}, {"index": 5, "moduleId": "Separator"}, {"index": 6, "moduleId": "callToAction"}, {"index": 7, "moduleId": "blankSpace2"}, {"index": 8, "moduleId": "blankSpace3"} ]
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "e67a#158d72d1cde",
    "result":[
        {
            "id": 1030
        }
    ]
}
```

#### 重新命名

若要[重新命名模組](https://developer.adobe.com/marketo-apis/api/asset#operation/renameUsingPOST)，請在`name`引數中傳遞新名稱。 指定電子郵件識別碼和現有的`moduleId`作為路徑引數。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/rename.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=MarketoVideo
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors": [ ],
    "requestId":"11521#158d740abc0",
    "result": [
        {
            "id": 1030
        }
    ]
}
```

## 變數

在電子郵件編輯器1.0中，變數會儲存電子郵件元素的值。 依照[電子郵件範本語法](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Variables)的說明，將Marketo專屬語法新增至HTML以定義每個變數。 使用變數API來管理電子郵件中的變數。

### 查詢

若要[擷取變數](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailVariablesUsingGET)，請將電子郵件識別碼指定為路徑引數。

下列範例會根據範本選擇器UI的[入門範本]區段中的`Skeleton`範本，來查詢1.0電子郵件。

```http
GET /rest/asset/v1/email/{id}/variables.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [  ],
  "requestId": "756#158dade55e8",
  "result": [
    {
      "name": "twoArticlesSpacer5",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer6",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer7",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText2",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer8",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer2",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "hrBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "freeTextBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink2",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "hrBorderColor",
      "value": "#e6e6e6",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "freeImageBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "footerSpacer",
      "value": "10",
      "moduleScope": false
    },
    {
      "name": "ctaLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "hrBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize2",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer4",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer3",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer",
      "value": "20",
      "moduleScope": false
    }
  ]
}
```

結果陣列中的每個元素都說明一個變數。

變數可以有整個電子郵件的全域範圍，或模組的本機範圍。 每個變數都包含`name`、`value`和`moduleScope`屬性。 全域變數的布林值`moduleScope`屬性是`false`，而區域變數是`true`。 區域變數也包含其關聯模組的`moduleId`。

#### 更新

若要[更新變數](https://developer.adobe.com/marketo-apis/api/asset#operation/updateVariableUsingPOST)，請在`value`引數中傳遞新值。 將電子郵件ID和變數名稱指定為路徑引數。 更新模組變數時，也傳遞`moduleId`以識別關聯的模組。

下列範例會更新全域變數`hrBorderSize`。

```http
POST /rest/asset/v1/email/{id}/variable/{name}.json
```

```text
Content-Type: application/x-www-form-urlencoded; charset=utf-8
```

```text
value=2
```

```json
{
    "success":true,
    "warnings":[ ],
    "errors":[ ],
    "requestId":"feb5#158db4be57e",
    "result": [
        {
            "name":"hrBorderSize",
            "value":"2",
            "moduleScope":false
        }
    ]
}
```

下列範例會將模組`CTA`中的本機變數`ctaLinkText`更新為`Click this button!`。

```http
POST /rest/asset/v1/email/1032/variable/ctaLinkText.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
value=Click this button!&moduleId=CTA
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "7f34#158dc28d2f7",
    "result": [
        {
            "name":"ctaLinkText",
            "value":"Click this button!",
            "moduleScope":true,
            "moduleId":"CTA"
        }
    ]
}
```

## 核准

電子郵件會遵循標準資產核准生命週期。 個別的端點可讓您核准草稿、取消核准已核准的版本，或捨棄現有的草稿。

### 核准

核准端點會根據Marketo電子郵件規則驗證電子郵件。 `from name`、`from email`、`reply to email`和`subject`必須在核准前填入。

```http
POST /rest/asset/v1/email/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"15dbf#14a1832ae86",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### 取消核准

只對核准的電子郵件使用`unapprove`作業。

```http
POST /rest/asset/v1/email/{id}/unapprove.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"3514#14a1832b0fa",
   "result":[
      {
         "id":1364
      }
   ]
}
```

#### 捨棄

您只能捨棄處於草稿狀態的電子郵件。 您無法捨棄已核准的電子郵件。

```http
POST /rest/asset/v1/email/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"182c0#14a1832af4f",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### 刪除

```http
POST /rest/asset/v1/email/{id}/delete.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"169cd#14a1832adba",
   "result":[
      {
         "id":1361
      }
   ]
}
```

## 原地複製

若要複製電子郵件，請使用下列引數傳送`application/x-www-form-urlencoded` POST要求：

- `name`：必要。 複製的電子郵件名稱。
- `folder`：必要。 內嵌JSON物件，具有`id`和`type`。
- `description`：選擇性。 複製的電子郵件說明。

如果來源電子郵件沒有核准的版本，端點會複製草稿版本。

```http
POST /rest/asset/v1/email/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Clone of Social Sharing in Email&folder={"id":239,"type":"Folder"}&description=This is a test of clone email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "bd49#15706f43d96",
    "result": [
        {
            "id": 2250,
            "name": "Clone of Social Sharing in Email",
            "description": "This is a test of clone email",
            "createdAt": "2016-09-07T23:20:52Z+0000",
            "updatedAt": "2016-09-07T23:20:52Z+0000",
            "url": "https://app-abm.marketo.com/#EM2250B2",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 239,
                "folderName": "Tradeshows and Events"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false
        }
    ]
}
```

## 傳送範例

若要傳送範例電子郵件，請在`emailAddress`查詢引數中指定收件者。 您也可以包含下列選用引數：

- `leadId`：從資料庫模擬特定銷售機會。
- `textOnly`：只傳送電子郵件的文字版本。

```http
POST /rest/asset/v1/email/{id}/sendSample.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
emailAddress=abe@testmail.com&textOnly=true
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "360b#14cce7d2708",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

## 預覽電子郵件

使用[取得電子郵件完整內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getEmailFullContentUsingGET)端點來擷取收件者將會收到之電子郵件的即時預覽。 此端點僅支援1.0版本的電子郵件。

必要的`id`路徑引數可識別要預覽的電子郵件。 端點也接受三個選用的查詢引數：

- `status`：接受`draft`或`approved`。 預設值是已核准電子郵件的已核准版本，或未核准電子郵件的草稿版本。
- `type`：接受`Text`或`HTML`。 預設值為 `HTML`。
- `leadId`：接受潛在客戶的整數ID，並預覽電子郵件，就像該潛在客戶已收到電子郵件一樣。

```http
GET /rest/asset/v1/email/{id}/fullContent.json
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": null,
   "result": [
      {
         "id": 339,
         "status": "draft",
         "content": "<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 1.01 Transitional//EN\" \"http://www.w1.org/TR/html4/loose.dtd\">\n<html>\n  <head>\n    <meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\"/>\n    <title></title>\n  </head>\n  <body>\n      <div style=\"font: 14px tahoma; width: 100%\" class=\"mktEditable\" id=\"edit_text_3\">\n        Content from testCreateEmailTemplate2\n    </div>\n  </body>\n</html>"
      }
   ]
}
```

## 取代HTML

使用[更新電子郵件完整內容](https://developer.adobe.com/marketo-apis/api/asset#operation/createEmailFullContentUsingPOST)端點來取代電子郵件資產中的所有內容。 此端點僅支援已在UI中使用編輯程式碼函式，且不再連結至其上層範本的1.0版電子郵件。

此端點主要適用於復製為計劃一部分的資產，無法使用標準內容端點變更。 不支援含有動態內容的電子郵件。 如果電子郵件仍連結至其範本，端點會傳回錯誤。

在路徑中傳送包含電子郵件`id`的`multipart/form-data`請求。 要求內文必須包含一個`content`引數，且此引數必須包含完整的HTML電子郵件檔案以及內容型別`text/html`。

格式錯誤的HTML檔案會產生警告並可能阻止核准。 包含JavaScript或`<script>`標籤會造成呼叫失敗並出現錯誤。

```http
POST /rest/asset/v1/email/{id}/fullContent.json
```

```text
content-type: multipart/form-data; boundary=--------------------------116301888604800085728247
content-length: 599
```

```html
----------------------------116301888604800085728247
Content-Disposition: form-data; name="content"; filename="email_content.html"
Content-Type: text/html

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 1.01 Transitional//EN" "http://www.w1.org/TR/html4/loose.dtd">
 <html>
 <head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
 <title></title>
 </head>
 <body>
 <div style="font: 14px tahoma; width: 100%" class="mktEditable" id="edit_text_3">
 EMAIL TEST CONTENT
 </div>
 </body>
 </html>
----------------------------116301888604800085728247--
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": "15dbf#14a1832ae86",
   "result": [
      {
         "id": 1001
      }
   ]
}
```
