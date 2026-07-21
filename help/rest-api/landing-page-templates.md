---
title: 登陸頁面範本
feature: REST API, Landing Pages
description: 透過REST API端點管理自由格式和引導式型別的Marketo登陸頁面範本、依id或名稱查詢、建立、更新HTML、複製、Munchkin。
exl-id: f9d1255e-ec13-4b75-96d5-b4cc9457a51b
TQID: https://experienceleague.adobe.com/U9K1MG-q2gIgJMgfM3lt1S4olETt8ln9seOIKZUncBY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 519
ht-degree: 2%

---

# 登陸頁面範本

[登陸頁面範本端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates)

登陸頁面範本是Marketo登陸頁面的父級資源。 每個登入頁面都會從其上層範本衍生其初始內容結構。

## 範本型別

Marketo提供自由格式和引導式登陸頁面範本。 自由格式範本提供鬆散結構的編輯體驗。 引導式範本可在範本層級限制元素型別和位置。

如需詳細比較，請參閱[瞭解自由格式與引導式登入頁面](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/demand-generation/landing-pages/understanding-landing-pages/understanding-free-form-vs-guided-landing-pages)。

## 查詢

依識別碼[&#128279;](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplateByIdUsingGET)、[依名稱](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplateByNameUsingGET)或[瀏覽](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplatesUsingGET)查詢登入頁面範本。 這些端點會傳回範本中繼資料。 依ID分別擷取每個範本的HTML內容。

## 建立和更新

範本會建立為含有中繼資料的空白資產。 需要`name`和`folder`引數。 `description`、`templateType`和`enableMunchkin`引數為選用引數。

`templateType`值可以是`freeform`或`guided`，且預設為`freeForm`。 `enableMunchkin`值預設為`false`。 啟用後，這可防止在範本的子登陸頁面上追蹤Munchkin。

```http
POST /rest/asset/v1/landingPageTemplates.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=New LPT - PHP&folder={"id":12,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "11b7#14dfe1e3bcf",
    "result": [
        {
            "id": 286,
            "name": "assetAPITest",
            "description": "test",
            "createdAt": "2015-06-16T20:45:03Z+0000",
            "updatedAt": "2015-06-16T20:45:03Z+0000",
            "url": "https://app-devlocal1.marketo.com/#LT286B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 12,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

使用[更新登入頁面範本內容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/updateLandingPageTemplateContentUsingPOST)端點個別新增範本內容。

### 更新中繼資料

使用[更新登入頁面範本中繼資料](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/updateLpTemplateUsingPOST)端點來變更名稱、說明或`enableMunchkin`設定。

### 更新內容

更新範本內容會取代所有現有HTML內容。 在`content`引數中傳遞取代為`multipart/form-data`。

```http
POST /rest/asset/v1/landingPageTemplate/286/content.json
```

```html
content-type: multipart/form-data; boundary=--------------------------435851813185237176536801
----------------------------435851813185237176536801
Content-Disposition: form-data; name="content"; filename="content.txt"
Content-Type: text/plain

<html>
<head>
</head>
<body>
<div>Placeholder Content</div>
</body>
</html>
----------------------------435851813185237176536801--
```

```json
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e0dc60bbc",
  "result": [
    {
      "id": 286
    }
  ]
}
```

## 原地複製

複製具有`application/x-www-url-formencoded` POST要求的登入頁面範本。

`id`路徑引數指定來源登入頁面範本。

`name`引數指定新登入頁面範本的名稱。

`folder`引數指定新範本的父資料夾。 將其傳遞為包含`id`和`type`的內嵌JSON物件。

選用的`description`引數說明新範本。

```http
POST /rest/asset/v1/landingPageTemplate/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Standard Template Clone&folder={"type": "Folder", "id": 732}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "dee6#1683e9fd410",
    "warnings": [],
    "result": [
        {
            "id": 61,
            "name": "Standard Template Clone",
            "createdAt": "2019-01-11T20:34:48Z+0000",
            "updatedAt": "2019-01-11T20:34:48Z+0000",
            "url": "https://app-abm.marketo.com/#LT61B2ZN732",
            "folder": {
                "type": "Folder",
                "value": 732,
                "folderName": "Test LP Template Clone"
            },
            "status": "draft",
            "workspace": "Default",
            "templateType": "freeForm",
            "enableMunchkin": true
        }
    ]
}
```

## 核准

登入頁面範本使用標準草稿與已核准模型。 更新會先套用至草稿，並僅在範本核准後上線。

在核准之前，範本必須符合其引導式或自由表單型別的需求。 請參閱下列資源：

- [自由表單登陸頁面範本](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-free-form-landing-page-template)
- [引導式登陸頁面範本](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)
- [引導式範本範例](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/guided-landing-page-template-list)

## 刪除

若要刪除範本，請確保該範本未核准，且沒有子登入頁面參考該範本。 您無法使用此API來刪除內嵌社交按鈕的登陸頁面範本。
