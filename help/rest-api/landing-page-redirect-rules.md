---
title: 登陸頁面重新導向規則
feature: REST API, Landing Pages
description: 使用Marketo Asset REST API來建立、查詢、更新及刪除登陸頁面重新導向規則，並包含篩選器、分頁、主機名稱選項和非Marketo目標。
exl-id: f63aa5ef-5872-4401-be75-6fb9b2977734
TQID: https://experienceleague.adobe.com/2gePbKA3xeoRdnL8mNnObN-GPTX00Ii4-zcM0lBjs-o
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 626
ht-degree: 3%

---

# 登陸頁面重新導向規則

[登陸頁面重新導向規則端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules)

使用登陸頁面重新導向規則REST API來查詢、建立、更新及刪除登陸頁面重新導向URL。

重新導向規則會將一個登陸頁面URL傳送到另一個頁面URL。 來源和目的地可以是Marketo或非Marketo頁面。 如需相關產品檔案，請參閱[Marketo Engage檔案](https://experienceleague.adobe.com/docs/marketo/using/home.html)。

## 查詢

查詢登入頁面重新導向規則[依據ID](#by_id)或[瀏覽](#browse)。

### 依Id

[依ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRuleByIdUsingGET)識別碼取得登陸頁面重新導向規則」端點採用一個重新導向規則`id`路徑引數，並傳回相符的記錄。

```http
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

[取得登陸頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRulesUsingGET)端點會傳回登陸頁面重新導向規則記錄。

使用選用的查詢引數來篩選結果。

`offset`引數是整數，指定要傳回的專案數目上限（預設為20）。 最大值為200。 `maxReturn`引數是整數，指定從何處開始擷取專案。 可與位移搭配使用（預設為0）。

`hostname`引數會依據登陸頁面主機名稱進行篩選。

`redirectToLandingPageId`整數會依目的地登陸頁面ID進行篩選。 `redirectToPath`引數會依目的地登陸頁面路徑篩選。

`earliestUpdatedAt`和`latestUpdatedAt`引數設定了低日期時間與高日期時間範圍。 端點會傳回在範圍內建立或更新之規則。

```http
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

使用`application/x-www-form-urlencoded`個POST要求呼叫[建立登陸頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/createLandingPageRedirectRuleUsingPOST)端點。 此請求有三個必要的引數。

`hostname`引數指定登入頁面主機名稱。 它必須屬於品牌領域或別名，且不能超過255個字元。

`redirectFrom`引數會將來源登陸頁面指定為具有型別/值配對的JSON物件。 Marketo登陸頁面的`type`屬性可以是`landingPageId`，非Marketo頁面的`path`。

| 參數 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| &#39;get&#39; | 必要 | 字串 | 方法動作。 |
| &#39;訪客&#39; | 必要 | 字串 | 方法名稱。 |
| callback | 必要 | 功能 | 針對每個傳回的行銷活動觸發的回呼函式。 |

`redirectTo`引數會將目的地指定為具有型別/值配對的JSON物件。 Marketo登陸頁面的`type`屬性可以是`landingPageId`，非Marketo頁面的`url`。

| 登陸頁面型別 | redirectTo型別 | 範例 |
| --- | --- | --- |
| Marketo | landingPageId | {&quot;type&quot;：&quot;landingPageId&quot;，&quot;value&quot;：&quot;1774&quot;} |
| 非Marketo | url | {&quot;type&quot;：&quot;url&quot;，&quot;value&quot;：&quot;www.contactLogs.com&quot;} |

如需詳細資訊，請參閱[將Marketo登入頁面重新導向至其他頁面](https://experienceleague.adobe.com/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-actions/redirect-a-marketo-landing-page-to-another-page.html)。

```http
POST /rest/asset/v1/redirectRules.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[更新登陸頁面重新導向規則](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/updateLandingPageRedirectRuleUsingPOST)端點需要一個重新導向規則`id`路徑引數。 以`application/x-www-form-urlencoded` POST要求的形式傳送更新。

傳遞一或多個這些引數以選取要更新的屬性： `hostname`、`redirectFrom`或`redirectTo`。

回應會傳回更新的重新導向規則記錄。

```http
POST /rest/asset/v1/redirectRule/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

依ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/deleteLandingPageRedirectRuleUsingPOST)端點的[刪除登陸頁面重新導向規則需要一個重新導向規則`id`路徑引數。

```http
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

[取得登陸頁面網域](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageDomainsUsingGET)端點傳回登陸頁面網域記錄。

使用兩個選用的查詢引數來篩選結果。

`offset`引數是整數，指定要傳回的最大專案數（預設為20，最大為200）。

`maxReturn`引數是整數，指定從何處開始擷取專案。 可與`offset`搭配使用（預設為0）。

```http
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
