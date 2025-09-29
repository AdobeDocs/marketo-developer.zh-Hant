---
title: 設定
description: 使用Marketo API設定JavaScript Munchkin。 瞭解Munchkin.init設定，例如altIds、anonymizeIP、asyncOnly、cookie期限、domainLevel、Beacon API。
feature: Munchkin Tracking Code, Javascript
exl-id: 4700ce7b-f624-4f27-871e-9a050f203973
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '563'
ht-degree: 3%

---

# 設定

Munchkin可接受各種組態設定來自訂行為。 組態設定是在呼叫[Munchkin.init()](api-reference.md#munchkin_init)時作為第二個引數傳遞的JavaScript物件屬性

```json
Munchkin.init("AAA-BBB-CCC", {
        "configName":"configValue",
        "configName2":"configValue2"
    }
);
```

組態設定物件可包含下表中的任意數量屬性。

## 屬性

| 名稱 | 資料類型 | 說明 |
|---|---|---|
| altIds | 陣列 | 接受Munchkin ID字串的陣列。 啟用後，系統會根據目標訂閱的Munchkin ID，將所有網頁活動複製到目標訂閱。 |
| anonymizeip | 布林值 | 將新訪客在Marketo中記錄的IP位址匿名化。 |
| apiOnly | 布林值 | 若設為true，則`Munchkin.Init()`函式將不會呼叫`visitsWebPage`。 這對於需要每個`visitsWebPage`事件的完整控制權的單頁網頁應用程式非常有用。 |
| asyncOnly | 布林值 | 若設為true，會以非同步方式傳送XMLHttpRequest。 預設值為false。 |
| clickTime | 整數 | 設定點選後要封鎖的時間長度，以允許點選追蹤要求（以毫秒為單位）。 減少這項操作會降低點選追蹤的準確度。 預設值為350毫秒。 |
| cookieAnon | 布林值 | 如果設為false，會防止追蹤和Cookie建立新的匿名銷售機會。 潛在客戶具有Cookie，並在填寫Marketo表單後或從Marketo電子郵件點進時被追蹤。 預設為true。 |
| cookieLifeDays | 整數 | 將任何新建立的Munchkin追蹤Cookie的到期日設定為未來的這個天數。 預設值為730天（2年）。 |
| customName | 字串 | 自訂頁面名稱。 僅限系統使用。 |
| <a name="domainlevel"></a>domainLevel | 整數 | 設定設定Cookie的網域屬性時，要從頁面的網域使用的部分數量。例如，假設目前頁面網域為「www.example.com」。domainLevel： 2會將Cookie網域屬性設為「.example.com」domainLevel： 3會將Cookie網域屬性設為「。www.example.com」。背景:Munchkin會自動管理特定的雙字母上層網域。 在正常情況下，這預設為兩個部分，最上層網域是三個字母。 例如「www.example.com」，最右邊的兩個部分可用來設定Cookie「.example.com」。若為兩個字母的國家代碼，例如「.jp」、「.us」、「.cn」和「.uk」，則程式碼預設為三個部分。 例如，「www.example.co.jp」將使用三個最右邊的網域部分，「.example.co.jp」。如果網域模式需要不同的行為，則必須使用`domainLevel`引數來指定。 |
| domainSelectorV2 | 布林值 | 若設為true，會使用改良的方法判斷如何設定Cookie網域屬性。 |
| httpsOnly | 布林值 | 預設為false。 若設為true，則在透過https提供追蹤的頁面時，將Cookie設定為使用Secure設定。 |
| useBeaconAPI | 布林值 | 預設為false。 設定為True時，會使用[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API)來傳送非封鎖要求，而非[XMLHttpRequest](https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest)。 如果瀏覽器不支援此API，Munchkin會退回使用XMLHttpRequest。 |
| wsInfo | 字串 | 以字串作為工作區的目標。 在「管理員>整合> Munchkin」功能表中選取Workspace ，即可取得此工作區ID。 此設定僅適用於匿名潛在客戶記錄的初始建立。 一旦為該潛在客戶記錄建立了Munchkin Cookie值，wsInfo引數就無法用來變更其資料分割。 由於此設定只會影響匿名銷售機會，因此只與網頁報表中分割特定的[匿名訪客](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/reporting/basic-reporting/report-activity/display-people-or-anonymous-visitors-in-web-reports)有關。 |

## 範例

### 將活動傳送至多個訂閱

此範例會將所有Web活動傳送至具有Munchkin ID &quot;AAA-BBB-CCC&quot;和&quot;XXX-YYY-ZZZ&quot;的執行個體。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCCC', { 'altIds': ['XXX-YYY-ZZZ'] });
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
```

### 將追蹤設定為非同步

此範例會強制從主要執行緒非同步傳送所有XMLHttpRequest。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCC', { 'asyncOnly': true });
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin-beta.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```
