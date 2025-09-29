---
title: 登陸頁面範本
feature: REST API, Landing Pages
description: 透過REST API端點管理自由格式和引導式型別的Marketo登陸頁面範本、依id或名稱查詢、建立、更新HTML、複製、Munchkin。
exl-id: f9d1255e-ec13-4b75-96d5-b4cc9457a51b
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '552'
ht-degree: 1%

---

# 登陸頁面範本

[登入頁面範本端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates)

登陸頁面範本是個別Marketo登陸頁面的上層資源和相依性。 登入頁面會從父範本衍生出其內容的骨架。

## 範本型別

Marketo提供兩種型別的登陸頁面範本：自由格式和引導式。 自由表單登入頁面範本為衍生自這些範本的頁面提供鬆散結構的編輯體驗。 引導式範本提供高度結構化的體驗，可在範本層級限制元素型別和位置。 如需差異的詳細資訊，請參閱[此檔案](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/understanding-landing-pages/understanding-free-form-vs-guided-landing-pages)。

## 查詢

登入頁面範本支援[依識別碼](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByIdUsingGET)、[依名稱](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByNameUsingGET)和[瀏覽](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplatesUsingGET)之資產的標準查詢型別。 這些端點會傳回範本的中繼資料。 若要擷取範本的HTML內容，必須透過其ID為每個範本完成。

## 建立和更新

範本會建立為具有關聯中繼資料的空白資產。 建立範本時，必須包含名稱和資料夾，以及可選的說明templateType和enableMunchkin引數。 templateType可以是自由格式或引導式，預設為freeForm。 如需這些型別之間的差異，請參閱引導式與自由式表單一節。 enableMunchkin預設為false，若啟用，將無法在範本的任何子登陸頁面上執行Munchkin追蹤。

```
POST /rest/asset/v1/landingPageTemplates.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

範本的內容必須透過[更新登陸頁面範本內容](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLandingPageTemplateContentUsingPOST)端點個別填入。

### 更新中繼資料

可透過[更新登入頁面範本中繼資料](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLpTemplateUsingPOST)端點更新登入頁面範本的中繼資料。 名稱、說明和enableMunchkin設定可透過此方式更新。

### 更新內容

登陸頁面範本中的內容是整個HTML內容的破壞性更新。 內容必須以multipart/form-data形式傳遞，唯一引數為content。

```
POST /rest/asset/v1/landingPageTemplate/286/content.json
```

```
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

```
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

Marketo提供複製登入頁面範本的簡單方法。 這是application/x-www-url-formencoded POST要求。

`id`路徑引數指定要複製的來源登入頁面範本識別碼。

`name`引數是用來指定新登入頁面範本的名稱。

`folder`引數是用來指定新登入頁面範本所在的父資料夾。 其形式為內嵌JSON物件，包含  `id`和`type`。

選用的`description`引數可用來說明新的登入頁面範本。

```
POST /rest/asset/v1/landingPageTemplate/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

登陸頁面範本會遵循標準草稿核准模式，其中可能有草稿版本和/或核准版本。 每當將更新套用至範本時，都會先將更新套用至草稿版本，而且只有在範本獲得核准後才會即時顯示。

若要核准範本，它必須符合其型別的規則，無論是以自由形式引導。 如需有關建立和核准各自型別範本之需求的詳細資訊，請參閱各自建立檔案：

- [自由表單登陸頁面範本](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-free-form-landing-page-template)
- [引導式登陸頁面範本](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)
- [引導式範本範例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/guided-landing-page-template-list)

## 刪除

若要刪除範本，該範本必須已停止使用且未核准，這表示任何子登入頁面都不能參考該範本。  使用此API無法刪除內嵌社交按鈕的登陸頁面範本。
