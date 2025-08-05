---
title: 網頁個人化
description: 網頁個人化
feature: Web Personalization, Javascript
exl-id: b2c26b28-e9bf-4faf-8b6e-c102f41aeaa1
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '401'
ht-degree: 6%

---

# 網頁個人化

網路Personalization JavaScript API擴充了平台的自動個人化功能。 它允許事件追蹤和網頁的動態自訂。 其他功能： [自訂資料事件](custom-data-events.md)、[動態內容](web-personalization.md)、[取得訪客資料](get-visitor-data.md)、[排除特定機器人的標籤](#exclude_tag_for_specific_bots)。

- 您必須成為Web Personalization客戶，並在您的網站上部署[RTP標籤](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，才能使用使用者內容API。
- RTP不支援以帳戶為根據的行銷指定帳戶清單。 ABM清單和程式碼只與在RTP內管理的已上傳帳戶清單（CSV檔案）有關。

## 標籤設定

RTP標籤應插入個人化頁面的標頭。

```javascript
<!-- RTP tag -->
<script type='text/javascript'>
(function(c,h,a,f,e,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].p=e;c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b)})
(window,document,"rtp","[rtp-js-cdn-url]","[pod-url]","[accountId]");
</script>
<!-- End of RTP tag -->
```

## 帳戶設定

系統會在標籤層級自動呼叫此方法，以設定相關的帳戶ID。 當您想要在不同網域之間分割時，可以設定帳戶ID。

| 參數 | 選用/必要 | 類型 | 說明 |
|--------------|-------------------|--------|--------------|
| &#39;setAccount&#39; | 必要 | 字串 | 方法名稱。 |
| accountId | 必要 | 字串 | 帳戶ID。 |

```javascript
var accountId = '561-HYG-937';
rtp('setAccount', accountId);
```

## 事件傳送函式

此方法會傳送檢視事件，以用於頁面追蹤。 在以下範例中，目前頁面URL會以訪客頁面檢視受到追蹤。

透過在此方法中傳遞選用的「page」引數，可以覆寫目前頁面。

| 參數 | 選用/必要 | 類型 | 說明 |
|-----------|-------------------|--------|---------------------------------|
| &#39;傳送&#39; | 必要 | 字串 | 方法動作。 |
| &#39;檢視&#39; | 必要 | 字串 | 方法名稱。 |
| 頁面 | 選用 | 字串 | 相對路徑或完整頁面URL。 |

```javascript
// Example for Default Page
rtp('send', 'view');

// Example for Overriding Default Page
var page = 'my-page?param=1';
rtp('send', 'view', page);
```

## 排除特定機器人（使用者代理）的標籤

若要將特定瀏覽器排除在傳送資料至Web Personalization平台（在已識別機器人的情況下）之外，請將下列IF陳述式新增至標籤指令碼。

在以下程式碼範例中，「GoogleBot|msnbot」會作為機器人範例使用，以從網頁Personalization活動中排除。

```javascript
<!-- RTP tag -->
<script type='text/javascript'>
if(navigator.userAgent.match(/.(Googlebot|msnbot)./gi) == null){
    (function(c,h,a,f,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
    c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
    g.src=f+'?rh='+c.location.hostname+'&aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//[cdn-pod-X-url]/rtp-api/v1/rtp.js","[accountId]");

    rtp('send','view');
    rtp('get', 'campaign', true);
}
</script>
<!-- End of RTP tag -->
```

## 說明JavaScript呼叫

使用網頁JavaScript和預測性內容時，新增至網站的Personalization說明。

### 核心/相依JavaScript

| 名稱 | 說明 | 控制 |
|---------------------------|-------------|--------------------------------------------------------|
| rtp.js | - | 由Marketo控制 |
| jquery.min.js | v1.8.3 | 可透過聯絡Marketo客戶支援來停用 |
| jquery-custom-ui-min.js | v1.9.2 | 可透過聯絡Marketo客戶支援來停用 |
| query-ui-1.8.17-dialog.js | v1.9.2* | 可透過聯絡Marketo客戶支援來停用 |

*僅在jQuery UI缺少對話方塊時使用

### 隨選JavaScript

| 名稱 | 說明 | 控制 |
|-------------------------|-----------------------------------------------------------------------|-----------------------|
| ga-integration-2.0.1.js | 在啟用Google Analytics/Facebook/SiteCatalyst整合時使用 | 由Marketo控制 |
| insightera-bar-2.1.js | 在預測性內容推薦列已啟用時使用 | 由Marketo控制 |
| froogaloop2.min.js | 在啟用內容追蹤且頁面上存在Vimeo播放器時使用 | - |
| iframe-api-v1.js | 用於已啟用內容追蹤，且頁面上存在YouTube播放器的情況 | - |
