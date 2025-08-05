---
title: 登陸頁面重新導向規則
feature: REST API, Landing Pages
description: 透過API設定登陸頁面重新導向規則。
exl-id: f63aa5ef-5872-4401-be75-6fb9b2977734
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '641'
ht-degree: 2%

---

# 登陸頁面重新導向規則

[登陸頁面重新導向規則端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules)

Marketo提供了一組REST API，可在登陸頁面重新導向URL上執行CRUD操作。 這些API遵循資產API的標準介面模式，提供查詢、建立、更新和刪除選項。

登陸頁面重新導向規則提供將登陸頁面URL重新導向至其他頁面URL的功能。 您可以重新導向Marketo登陸頁面、非Marketo登陸頁面，或它們的組合。 如需重新導向登陸頁面規則的其他資訊，請參閱[這裡](https://experienceleague.adobe.com/docs/marketo/using/home.html?lang=zh-Hant)。

## 查詢

查詢登入頁面重新導向規則會遵循[的標準查詢型別（依據ID](#by_id)和[瀏覽](#browse)）。

### 依Id

[依ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRuleByIdUsingGET)取得登陸頁面重新導向規則端點採用單一登陸頁面規則重新導向`id`路徑引數，並傳回單一登陸頁面重新導向規則記錄。

```
GET /rest/asset/v1/redirectRule/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "3d0#1707b2521e4",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

### 瀏覽

[取得登陸頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRulesUsingGET)端點傳回登陸頁面重新導向規則記錄清單。

有數個可選的查詢引數可傳遞以篩選結果。

`offset`引數是整數，指定要傳回的專案數目上限（預設為20）。 最大值為200。 `maxReturn`引數是整數，指定從何處開始擷取專案。 可與位移搭配使用（預設為0）。

`hostname`引數可用來篩選登入頁面的主機名稱。

`redirectToLandingPageId`是整數，可用於篩選您要重新導向之登陸頁面的識別碼。 `redirectToPath`可用來篩選您重新導向的登入頁面路徑。

`earliestUpdatedAt`和`latestUpdatedAt`引數可讓您設定高與低日期時間浮水印，以傳回在指定範圍內已更新或最初建立的登陸頁面重新導向規則。

```
GET /rest/asset/v1/redirectRules.json&maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "12213#1707b27efb5",
    "warnings": [],
    "result": [
        {
            "id": 5,
            "redirectFromUrl": "https://www.kirtideep.contact/LandingPage2.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5406
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:26:29Z+0000",
            "updatedAt": "2019-11-14T06:26:29Z+0000"
        },
        {
            "id": 6,
            "redirectFromUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectTo": {
                "type": "url",
                "value": "www.contactLogs.com"
            },
            "redirectToUrl": "www.contactLogs.com",
            "createdAt": "2019-11-14T06:27:10Z+0000",
            "updatedAt": "2019-11-14T06:27:10Z+0000"
        },
        {
            "id": 7,
            "redirectFromUrl": "https://www.kirtideep.contact/contact/log/check",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "path",
                "value": "/contact/log/check"
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:27:49Z+0000",
            "updatedAt": "2019-11-14T06:27:49Z+0000"
        }
    ]
}
```

## 建立

[建立登入頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/createLandingPageRedirectRuleUsingPOST)端點會使用application/x-www-form-urlencoded POST執行，該POST有下列三個必要引數。

`hostname`引數會指定登入頁面的主機名稱。 這應該屬於品牌領域或別名。 長度上限為255個字元。

`redirectFrom`引數指定來源登陸頁面。 這是包含型別/值配對的JSON物件，可判斷來源是Marketo登陸頁面，還是非Marketo登陸頁面。 `type`屬性可以是&quot;landingPageId&quot;或&quot;path&quot;。

| 參數 | 選用/必要 | 類型 | 說明 |
|---|---|---|---|
| &#39;get&#39; | 必要 | 字串 | 方法動作。 |
| &#39;訪客&#39; | 必要 | 字串 | 方法名稱。 |
| callback | 必要 | 功能 | 針對每個傳回的行銷活動觸發的回呼函式。 |

`redirectTo`引數指定目標登陸頁面。 這是包含型別/值配對的JSON物件，可判斷來源是Marketo登陸頁面，還是非Marketo登陸頁面。 `type`屬性可以是&quot;landingPageId&quot;或&quot;url&quot;。

| 登陸頁面型別 | redirectTo型別 | 範例 |
|---|---|---|
| Marketo | landingPageId | {&quot;type&quot;：&quot;landingPageId&quot;，&quot;value&quot;：&quot;1774&quot;} |
| 非Marketo | url | {&quot;type&quot;：&quot;url&quot;，&quot;value&quot;：&quot;www.contactLogs.com&quot;} |

如需建立登入頁面重新導向規則的詳細資訊，請參閱[這裡](https://experienceleague.adobe.com/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-actions/redirect-a-marketo-landing-page-to-another-page.html?lang=zh-Hant)。

```
POST /rest/asset/v1/redirectRules.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
hostname=calqeauto.com&redirectFrom={"type":"landingPageId", "value":"5483"}&redirectTo={"type":"landingPageId", "value":"5559"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d7c6#1707b223522",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

## 更新

[更新登陸頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/updateLandingPageRedirectRuleUsingPOST)端點採用單一登陸頁面重新導向規則`id`路徑引數。 此端點會使用application/x-www-form-urlencoded POST執行。

如同上述的建立呼叫，傳遞一或多個下列查詢引數以指定要更新規則的屬性： `hostname`、`redirectFrom`、`redirectTo`。

更新的登陸頁面重新導向規則記錄會傳回回應中。

```
POST /rest/asset/v1/redirectRule/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
redirectTo={"type":"landingPageId", "value":"5561"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "57b2#1707b3852d7",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5561
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage3.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T07:20:53Z+0000"
        }
    ]
}
```

## 刪除

依ID[端點的](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/deleteLandingPageRedirectRuleUsingPOST)刪除登陸頁面重新導向規則採用單一登陸頁面規則重新導向`id`路徑引數。

```
POST /rest/asset/v1/redirectRule/{id}/delete.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "d505#154d01c8364",
  "result": [
    {
      "id": 2
    }
  ]
}
```

## 瀏覽登陸頁面網域

[取得登陸頁面網域](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageDomainsUsingGET)端點傳回登陸頁面網域記錄清單。

有兩個可選的查詢引數可傳遞以篩選結果。

`offset`引數是整數，指定要傳回的最大專案數（預設為20，最大為200）。

`maxReturn`引數是整數，指定從何處開始擷取專案。 可與`offset`搭配使用（預設為0）。

```
POST /rest/asset/v1/landingPageDomains.json?maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6eb8#1707b43d3cb",
    "warnings": [],
    "result": [
        {
            "hostname": "calqeauto.com",
            "type": "domain"
        },
        {
            "hostname": "www.google.com",
            "type": "domain-alias"
        },
        {
            "hostname": "www.kirti.com",
            "type": "domain-alias"
        }
    ]
}
```
