---
title: 銷售機會追蹤
description: 瞭解如何內嵌Marketo Munchkin JavaScript、追蹤造訪和點按、管理已知和匿名的銷售機會、跨網域Cookie，以及選擇退出智慧行銷活動。
feature: Munchkin Tracking Code, Javascript
exl-id: 7ece5133-9d32-4be3-a940-4ac0310c4d8b
TQID: https://experienceleague.adobe.com/nGUcLLgL9X7PBKf2E5IzppDj8e-SyEtxmkQaESd90mE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
subfeature_v2:
  - id: d0251300-e25f-466f-9856-7e11ce8fa7aa
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 718
ht-degree: 0%

---

# 潛在客戶追蹤API

Marketo的Munchkin JavaScript會追蹤Marketo登陸頁面和外部網頁上的頁面瀏覽次數和連結點按次數。 Marketo會將這些互動記錄為「造訪網頁」和「網頁上的點選連結」活動。

針對智慧行銷活動和智慧列示，使用觸發器和篩選器中的活動。

## 內嵌程式碼

您的Marketo執行個體提供預先設定的程式碼片段，用於追蹤外部頁面的活動。 內嵌程式碼的使用受此[授權合約](../munchkin-license.pdf)規範。

可用的追蹤程式碼型別有三種：

1. 簡單 — 同步載入。
1. 非同步 — 非同步載入。
1. 非同步jQuery — 非同步載入，且必須先載入jQuery。

使用非同步追蹤程式碼將Munchkin內嵌於外部頁面上。 若要取得最高的執行成功率，請將程式碼放入每個頁面的`<head>`元素中。

某些內容管理系統在嵌入任意指令碼時，可能會有特定方法或限制。

您的最終頁面應該在HTML檔案的`<head>`元素中包含類似下列範例的程式碼：

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

依預設，Marketo Munchkin會在頁面載入時執行下列動作：

1. 檢查目前的瀏覽器是否有Munchkin Cookie，並視需要建立一個。
1. 使用目前頁面和瀏覽器的資訊，將「瀏覽網頁」事件傳送至指定的Marketo執行個體。 此事件會在對應的Marketo記錄中記錄活動。
1. 當使用者選取連結時，會傳送「網頁上的點選連結」事件。

使用Munchkin [組態設定](configuration.md)來變更此行為。 例如，使用`cookieAnon`來控制Munchkin是否為所有造訪頁面的潛在客戶建立Cookie，或使用`clickTime`來變更點選延遲。

若要停用造訪活動，請將`apiOnly`設定為true。 截至162版（2022年8月），Munchkin除了追蹤`http/s`個連結外，還追蹤`tel`和`mailto`個連結的點按次數。

## 已知和匿名銷售機會

當潛在客戶首次造訪您網域上的頁面時，Marketo會建立匿名潛在客戶記錄。 此記錄的主要索引鍵是在使用者的瀏覽器中建立的Munchkin Cookie (`_mkto_trk`)。

Marketo會將該瀏覽器的後續網頁活動記錄在該匿名記錄上。 若要將活動與已知Marketo記錄建立關聯，必須發生下列其中一個事件：

- 潛在客戶必須從追蹤的Munchkin電子郵件連結，造訪在查詢字串中具有`mkt_tok`引數的Marketo追蹤頁面。
- 潛在客戶必須填寫Marketo表單。
- 必須傳送REST [關聯銷售機會](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/associateLeadUsingPOST)呼叫。

當其中一個事件發生時，Marketo會將Cookie和所有相關的網路活動與已知潛在客戶建立關聯。

Marketo會為每個瀏覽器建立匿名的網站活動記錄。 如果潛在客戶從新電腦或瀏覽器造訪您的網域，則必須再次產生關聯。

## 網域

Munchkin會根據每個網域建立及追蹤Cookie。 若要追蹤跨網域的已知銷售機會，必須在每個網域上發生銷售機會關聯事件。

例如，假設您控制`marketo.com`和`example.com`。 潛在客戶在`marketo.com`上提交表單，稍後再移至`example.com`。 `marketo.com`上的活動與已知潛在客戶相關聯，但`example.com`上的活動是匿名的。

已知的潛在客戶會跨子網域持續存在。 `www.example.com`上的已知銷售機會也是`info.example.com`上的已知銷售機會。

如果您的上層網域有兩個部分，例如`.co.uk`，請將`domainLevel`引數新增至您的Munchkin程式碼片段。 如需詳細資訊，請參閱[組態](configuration.md#domainlevel)。

## Cookie

Munchkin Cookie使用索引鍵`_mkto_trk`以及遵循下列其中一種模式的值：

`id:561-HYG-937&token:_mch-marketo.com-1374552656411-90718`

或

`id:561-HYG-937&token:_mch-marketo.com-97bf4361ef4433921a6da262e8df45a`

Munchkin Cookie特定於每個第二層網域，例如`example.com`。 預設Cookie生命週期為2年（730天）。

## Beta

若要選擇加入登陸頁面的Munchkin測試版管道，請前往[管理員 — > Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)並啟用「登陸頁面上的Munchkin Beta」設定。

此設定會將程式碼片段新增至&#x200B;**[!UICONTROL Admin]** -> **[!UICONTROL Munchkin]**&#x200B;功能表。 使用這些程式碼片段在外部網站上執行測試版。

## 選擇退出

訪客可在瀏覽器中將`querystring`引數「marketo_opt_out=true」新增至URL，選擇退出Munchkin追蹤。 Munchkin JavaScript偵測到此設定時，會嘗試使用值`true`設定新的「mkto_opt_out」Cookie。

然後Munchkin會刪除所有其他Marketo追蹤Cookie、不設定新Cookie且不會提出HTTP請求。
