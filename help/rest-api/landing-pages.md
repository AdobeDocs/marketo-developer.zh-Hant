---
title: 登陸頁面
feature: REST API, Landing Pages
description: 使用Marketo REST API來查詢中繼資料和內容、建立、更新、核准、刪除和複製登入頁面，包括引導式和自由表單型別。
exl-id: 2f986fb0-0a6b-469f-b199-1c526cd5a882
TQID: https://experienceleague.adobe.com/NssOtB6BEMGOQzzauLI7AszLpN3fVcEeJcr9VNTkpJE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: aeb0d5a176ffdd0910ee533353593bba95f91d08
workflow-type: tm+mt
source-wordcount: 864
ht-degree: 2%

---

# 登陸頁面

[登陸頁面端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages)

登陸頁面是Marketo託管的網頁。 使用登入頁面REST API來查詢及管理其中繼資料、內容、生命週期和預覽。

## 查詢

依名稱](https://developer.adobe.com/marketo-apis/api/asset#operation/getLandingPageByNameUsingGET)、[依識別碼](https://developer.adobe.com/marketo-apis/api/asset#operation/getLandingPageByIdUsingGET)或[瀏覽](https://developer.adobe.com/marketo-apis/api/asset#operation/browseLandingPagesUsingGET)查詢登入頁面[。 這些查詢只會傳回中繼資料。 依頁面ID個別查詢登入頁面的內容區段。

查詢登入頁面內容會傳回其可用的內容區段。 區段必須出現在此清單中，您才能進行更新。

```http
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

引導式登入頁面包含由其範本定義的區段。 自由表單頁面不包含預先定義的區段，因此在編輯前請先新增其內容。

`content`屬性的格式取決於`type`屬性以及欄位是靜態或動態。

## 建立和更新

[從範本建立登入頁面](https://developer.adobe.com/marketo-apis/api/asset#operation/createLandingPageUsingPOST)。 需要頁面名稱、範本ID和目的地資料夾。 如需選擇性中繼資料，請參閱端點參考。

[登入頁面內容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content)端點支援這些內容型別： `richText`、`HTML`、`Form`、`Image`、`Rectangle`和`Snippet`。

```http
POST rest/asset/v1/landingPages.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

可以使用[更新登陸頁面中繼資料端點](https://developer.adobe.com/marketo-apis/api/asset#operation/updateLandingPageUsingPOST)來更新登陸頁面中繼資料。

## 核准

登入頁面使用標準草稿和已核准模型。 更新會套用至草稿，並僅在核准後上線。

## 刪除

刪除登入頁面之前，請確定該頁面未核准，且沒有其他Marketo資產參考它。 使用[刪除登陸頁面](https://developer.adobe.com/marketo-apis/api/asset#operation/deleteLandingPageByIdUsingPOST)端點個別刪除頁面。 您無法使用此API來刪除內嵌社交按鈕的頁面。

## 原地複製

複製具有`application/x-www-url-formencoded` POST要求的登入頁面。

`id`路徑引數指定來源登陸頁面。

`name`引數會指定新的登陸頁面名稱。

`folder`引數指定父資料夾。 將其傳遞為包含`id`和`type`的內嵌JSON物件。

`template`引數指定來源登入頁面範本識別碼。

選用的`description`引數說明新的登陸頁面。

```http
POST /rest/asset/v1/landingPage/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=MyNewLandingPage&folder={"type":"Program","id":1119}&template=57
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1078d#1683e4881c6",
    "warnings": [],
    "result": [
        {
            "id": 3291,
            "name": "MyNewLandingPage",
            "createdAt": "2019-01-11T18:59:25Z+0000",
            "updatedAt": "2019-01-11T18:59:25Z+0000",
            "folder": {
                "type": "Program",
                "value": 1119,
                "folderName": "DefaultProgramWithGuidedLP"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 57,
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "http://na-abm.marketo.com/lp/284-RPR-133/DefaultProgramWithGuidedLPPerkutoTestLP-Clone-1.html",
            "computedUrl": "https://app-abm.marketo.com/#LP3291A1LA1"
        }
    ]
}
```

## 管理內容區段

內容區段依其`index`屬性排序，並根據使用者端的CSS規則顯示。 使用[新增](https://developer.adobe.com/marketo-apis/api/asset#operation/addLandingPageContentUsingPOST)、[更新](https://developer.adobe.com/marketo-apis/api/asset#operation/updateLandingPageContentUsingPOST)和[刪除](https://developer.adobe.com/marketo-apis/api/asset#operation/removeLandingPageContentUsingPOST)端點來管理區段。 使用[取得登入頁面內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getLandingPageContentUsingGET)來查詢這些內容。

每個區段都有`type`和`value`引數。 `type`決定預期的`value`。 將資料以POST `x-www-form-urlencoded`傳遞至這些端點，而非以JSON傳遞。

**區段型別**

| 類型 | 價值 |
| --- | --- |
| 動態內容 | 區段的ID。 |
| 表單 | 表單的ID。 |
| HTML | 文字HTML內容。 |
| 影像 | 影像資產的識別碼。 |
| 矩形 | 空白。 |
| RTF | 文字HTML內容。  只能包含RTF元素。 |
| 程式碼片段 | 程式碼片段的ID。 |
| 社交按鈕 | 社交按鈕的id。 |
| 影片 | 視訊的ID。 |

針對自由格式頁面，新增每個必要的內容區段。 Marketo會將它們內嵌在ID為`mktoContent`的`div`元素中。

引導式頁面可包含由[取得登入頁面內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getLandingPageContentUsingGET)傳回的預先定義元素。 使用對應的端點來新增元素或[更新其內容](https://developer.adobe.com/marketo-apis/api/asset#operation/updateLandingPageContentUsingPOST)。

### 動態內容

若要讓區段成為動態區段，請先確定該區段會顯示在登入頁面的內容清單中。 然後使用[更新登入頁面內容區段](https://developer.adobe.com/marketo-apis/api/asset#operation/updateLandingPageContentUsingPOST)來將其型別設定為`DynamicContent`。

Marketo會建立基礎動態區段，這些區段繼承轉換後元素的基底型別和內容。

```http
GET /rest/asset/v1/landingPage/{id}/dynamicContent/RVMtNDg=.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "46e#1560fa169d9",
  "result": [
    {
      "createdAt": "2016-07-21",
      "updatedAt": "2016-07-21",
      "segmentation": 1007,
      "segments": [
        {
          "segmentId": 1018,
          "segmentName": "Default",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        },
        {
          "segmentId": 1017,
          "segmentName": "New Segment",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        }
      ]
    }
  ]
}
```

[每個個別區段的內容](https://developer.adobe.com/marketo-apis/api/asset#operation/updateLandingPageDynamicContentUsingPOST)會根據區段ID進行更新。

```http
POST /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
segment=New Segment&value=New Content
```

```json
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e08fe7cbbc",
  "result": [
    {
      "id": 1012
    }
  ]
}
```

## 變數

引導式登入頁面支援包含元素值的可編輯變數。 在登入頁面編輯器中修改變數：

![登陸頁面變數](assets/landing-page-variables.png)

變數是引導式登入頁面範本之`<head>`元素中的中繼標籤。 支援的型別為String、Color和Boolean。 下列範例會定義每種型別的一個變數：

```html
<head>
  <meta charset="utf-8">
  <meta class="mktoString" mktoName="My String Variable" id="stringVar" default="Hello World!">
  <meta class="mktoColor" mktoName="My Color Variable" id="colorVar" default="#ffffff">
  <meta class="mktoBoolean" mktoName="My Boolean Variable" id="boolVar" default="true">
</head>
```

如需詳細資訊，請參閱[建立引導式登陸頁面範本](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)檔案中的「可編輯變數」一節。

### 查詢

將登陸頁面ID傳遞至「取得登陸頁面變數」端點，擷取引導式登陸頁面的變數。

```http
GET /rest/asset/v1/landingPage/{id}/variables.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "10843#15a6d7e5fa1",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello World!",
            "type": "string"
        },
        {
            "id": "colorVar",
            "value": "#FFFFFF",
            "type": "color"
        },
        {
            "id": "boolVar",
            "value": "true",
            "type": "boolean"
        }
    ]
}
```

此引導式登陸頁面包含三個變數： `stringVar`、`colorVar`和`boolVar`。

### 更新

將登陸頁面ID、變數ID和變數值傳遞至「更新登陸頁面變數」端點，以更新引導式登陸頁面的變數。

```http
POST /rest/asset/v1/landingPage/{id}/variable/{variableId}.json?value={newValue}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "2b07#15a6db77da3",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello Brave New World!",
            "type": "String"
        }
    ]
}
```

## 預覽登陸頁面

使用[取得登入頁面完整內容](https://developer.adobe.com/marketo-apis/api/asset#operation/getLandingPageFullContentUsingGET)來擷取瀏覽器轉譯的預覽。 登入頁面`id`路徑引數為必要項。 端點也接受兩個選用的查詢引數：

- `segmentation`：包含`segmentationId`和`segmentId`的JSON物件陣列。 預覽代表符合這些區段的銷售機會。
- `leadId`：整數銷售機會識別碼。 預覽代表指定的銷售機會。

```http
GET /rest/asset/v1/landingPage/{id}/fullContent.json?leadId=1001&segmentation=[{"segmentationId":1030,"segmentId":1103}]
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "119ab#17692849f1e",
  "warnings": [],
  "result": [
    {
      "id": 1023,
      "content": "<!DOCTYPE html>\n<html>\n <head>\n <meta charset=\"utf-8\">\n \n \n <meta name=\"robots\" content=\"index, nofollow\">\n <title></title>\n <style>\n body {background:#FFFFFF} \n #myConditionalDisplayArea {\n display: true;\n }\n </style>\n <link rel=\"shortcut icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n<link rel=\"icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n\n\n<style>.mktoGen.mktoImg {display:inline-block; line-height:0;}</style>\n </head>\n <body id=\"bodyId\">\n \n Hello Brave New World!\n <div class=\"mktoText\" id=\"exampleText\"><div>This is an example editable text area.</div>\n<div>Lead Full Name = Hanna Crawford</div>\n<div><br /></div>\n <script type=\"text/javascript\" src=\"//munchkin.marketo.net//munchkin.js\"></script><script>Munchkin.init('123-ABC-456', {customName: 'Test-Landing-Page-APIs_Guided-Landing-Page---deverly', PURL_VISIT_TOKEN, wsInfo: 'j1RR'});</script>\n<div id=\"mktoClickBlockingDiv\"></div>\n </body>\n</html>\n"
    }
  ]
}
```
