---
title: 銷售機會追蹤
description: 潛在客戶追蹤API
feature: Munchkin Tracking Code, Javascript
exl-id: 7ece5133-9d32-4be3-a940-4ac0310c4d8b
source-git-commit: 8ad3e3f0958ea705375651b1c8a75967d807ca80
workflow-type: tm+mt
source-wordcount: '764'
ht-degree: 0%

---

# 潛在客戶追蹤API

Marketo的Munchkin JavaScript可追蹤一般使用者頁面瀏覽次數，以及您的Marketo登陸頁面和外部網頁的點選次數。 這些記錄在Marketo中記錄為「造訪網頁」和「網頁上的點選連結」活動，然後可用於智慧行銷活動和智慧清單的觸發器和篩選器。

## 內嵌程式碼

您的Marketo執行個體會自動提供預先設定的追蹤程式碼片段，以將程式碼內嵌於外部頁面上，藉此將活動追蹤回Marketo執行個體。 內嵌程式碼的使用受此[授權合約](../munchkin-license.pdf)規範。

可用的追蹤程式碼型別有三種：

1. 簡單 — 同步載入
1. 非同步 — 非同步載入
1. 非同步jQuery — 以非同步方式載入，並要求jQuery預先載入

強烈建議使用非同步追蹤程式碼將Munchkin內嵌於外部頁面上。 若要確保執行的最高成功率，請將非同步追蹤程式碼內嵌在每個頁面的`<head>`中。

某些內容管理系統在嵌入任意指令碼時，可能會有特定方法或限制。

如需參考，您的最終頁面應該在HTML檔案的`<head>`中包含類似以下的程式碼：

```html
<head>
    <script type="text/javascript">
    (function() {
        var didInit = false;
        function initMunchkin() {
            if(didInit === false) {
                didInit = true;
                Munchkin.init('CHANGE-ME');
            }
        }
        var s = document.createElement('script');
        s.type = 'text/javascript';
        s.async = true;
        s.src = '//munchkin.marketo.net/munchkin.js';
        s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
                initMunchkin();
            }
        };
        s.onload = initMunchkin;
        document.getElementsByTagName('head')[0].appendChild(s);
        })();
    </script>
    ...
</head>
```

## Munchkin行為

Marketo Munchkin的預設行為是在頁面載入時執行以下操作：

1. 檢視目前的瀏覽器是否有Munchkin Cookie，如果不在，請建立一個。
1. 使用目前頁面和瀏覽器的資訊，將「造訪網頁」事件傳送至指定的Marketo執行個體。 這會將活動記錄到Marketo中的對應記錄。
1. 針對使用者在連結上所點選的任何專案，傳送「網頁上的點選連結」事件。

Munchkin的行為可透過使用Munchkin [組態設定](configuration.md)來修改，例如是否使用`cookieAnon`設定造訪頁面時為所有潛在客戶建立Cookie，或使用`clickTime`設定修改點選延遲。 您可將apiOnly設定設為true，以停用造訪活動的傳送。 自162版（2022年8月）起，除了`http/s`個連結外，還會追蹤`tel`點按和`mailto`個連結。

## 已知和匿名銷售機會

當潛在客戶第一次造訪您網域上的頁面時，系統會在Marketo中建立新的匿名潛在客戶記錄。 此記錄的主要索引鍵是在使用者的瀏覽器中建立的Munchkin Cookie (`_mkto_trk`)。 該瀏覽器上的所有後續網頁活動都會針對此匿名記錄進行記錄。 若要與Marketo中的已知記錄相關聯，必須發生以下任一情況：

- 潛在客戶必須從追蹤的Munchkin電子郵件連結，造訪在查詢字串中具有`mkt_tok`引數的Marketo追蹤頁面。
- 潛在客戶必須填寫Marketo表單。
- 必須傳送REST [關聯銷售機會](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST)呼叫。

當其中一項條件達成時，Cookie和所有相關的網路活動都會與已知潛在客戶建立關聯。

系統會為每個個別瀏覽器建立新的匿名網站活動記錄，因此如果潛在客戶第一次使用新電腦和/或瀏覽器造訪您的網域，則必須再次建立此關聯。

## 網域

Munchkin會根據每個網域建立及追蹤個別Cookie，因此若要跨網域進行已知銷售機會追蹤，必須針對每個網域進行銷售機會關聯事件。 例如，如果我控制兩個網域`marketo.com`和`example.com`，而潛在客戶在`marketo.com`上填寫表單，然後導覽至`example.com`之後，則他們在`marketo.com`上的活動會在已知潛在客戶記錄上追蹤，但他們在`example.com`上的活動是匿名的。 但是，已知銷售機會會跨子網域持續存在，因此`www.example.com`的已知銷售機會也是`info.example.com`的已知銷售機會。

如果您的上層網域是兩個部分（例如`.co.uk`），則請在您的Munchkin程式碼片段中新增domainLevel引數，讓程式碼能夠正確追蹤。 如需詳細資訊，請參閱[這裡](configuration.md#domainlevel)。

## Cookie

Munchkin Cookie使用索引鍵`_mkto_trk`，其值遵循此模式：

`id:561\-HYG\-937&token:_mch\-marketo.com\-1374552656411\-90718`

或

`id:561\-HYG\-937&token:_mch\-marketo.com\-97bf4361ef4433921a6da262e8df45a`

Munchkin Cookie是每個第二層網域（即`example.com`）專屬的。 Cookie的預設有效期為2年（730天）。

## Beta

若要選擇加入登陸頁面的Munchkin測試版管道，請前往您的[管理員 — > Treasure Chest](https://experienceleague.adobe.com/zh-hant/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)功能表，並啟用「登陸頁面上的Munchkin Beta」設定。 這會提供&#x200B;**[!UICONTROL Admin]** ->中的新程式碼片段  **[!UICONTROL Munchkin]**&#x200B;功能表可讓您在外部網站上使用測試版。

## 選擇退出

訪客可在瀏覽器中將`querystring`引數「marketo_opt_out=true」新增至URL，以完全選擇退出Munchkin追蹤。 Munchkin JavaScript偵測到此設定時，會嘗試將新的Cookie &quot;mkto_opt_out&quot;設定為值`true`。 刪除所有其他Marketo追蹤Cookie，不設定新的Cookie，且在偵測到此設定時，Munchkin不會提出任何HTTP請求。
