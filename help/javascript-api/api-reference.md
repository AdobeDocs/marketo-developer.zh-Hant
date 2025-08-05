---
title: Munchkin API參考
description: 使用Munchkin Javascript API來自訂您的Munchkin資料。
feature: Munchkin Tracking Code, Javascript
exl-id: e9727691-5501-4223-bc98-2b4bacc33513
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '414'
ht-degree: 7%

---

# Munchkin API參考

Munchkin提供數個可透過Javascript手動呼叫的功能。 這些專案可允許自訂追蹤瀏覽器事件，例如視訊播放或非連結上的點選次數。

## 函數

Munchkin API由下列函式組成： `init`、`createTrackingCookie`、`munchkinFunction`。

<a name="munchkin_init"></a>

### Munchkin.init()

必須在任何其他函式之前呼叫`Munchkin.init()`。 它會在目前頁面上設定Munchkin，以將活動傳送至特定執行個體，並產生目前頁面的「造訪網頁」活動。

| 引數名稱 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| Munchkin ID | 必要 | 字串 | 「管理員>整合> Munchkin」功能表之下的Munchkin帳戶ID。 設定活動傳送到的目標執行個體。 |
| [組態設定](configuration.md) | 選用 | 物件 | 啟用Munchkin的替代行為設定。 |

```javascript
Munchkin.init('299-BYM-827');
```

### Munchkin.createTrackingCookie()

呼叫時，會檢查瀏覽器中是否存在`_mkto_trk` Cookie，若不存在，則會建立一個。 如果`cookieAnon`設為false，這適合用於追蹤特定動作期間的使用者，例如註冊或下載資產。

| 引數名稱 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| forceCreate | 必要 | 布林值 | 即使`cookieAnon`設為false，仍要建立Cookie。 |

```javascript
Munchkin.createTrackingCookie(true);
```

### Munchkin.munchkinFunction()

用於產生自訂追蹤行為，例如視訊播放器播放和暫停，或非標準導覽的頁面造訪，例如雜湊代碼。

| 引數名稱 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| 函式型別 | 必要 | 字串 | 決定要記錄的活動。 允許的值： `visitWebPage`、`clickLink`、`associateLead` |
| 資料 | 必要 | 物件 | 包含要記錄的活動的資料。 |

#### visitWebPage

使用`munchkinFunction()`呼叫`visitWebPage`會將目前使用者的「造訪」活動傳送至Marketo。 您可以自訂URL和`querystring`，它們會與第二個引數中的資料物件一起傳送。

| 資料屬性名稱 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| url | 必要 | 字串 | 用來記錄頁面瀏覽的URL檔案路徑。  此值會附加至目前的網域名稱，以建立完整頁面名稱。 例如，如果url為`/index.html`且網域名稱為`www.example.com`，則造訪的頁面會記錄為`www.example.com/index.html`。 |
| 參數 | 選用 | 字串 | 要記錄的所需引數的查詢字串。 |

例如 `foo=bar&biz=baz`。

```javascript
Munchkin.munchkinFunction('visitWebPage', {
        'url': '/Football/Team/Seahawks',
        'params': 'defense=legion_of_boom&qb=wilson'
    }
);
```

#### clickLink

使用`munchkinFunction()`呼叫`clickLink`會將目前使用者的點選活動傳送至Marketo。 您可以使用資料物件中的`href`屬性自訂點按URL。

| 資料屬性名稱 | 選用/必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| href | 必要 | 字串 | 用來記錄連結點選的URL檔案路徑。 此值會附加至目前的網域名稱，以建立完整連結。 |

例如，如果href為`/index.html`且網域名稱為`www.example.com`，則連結點選會記錄為`www.example.com/index.html`。

```javascript
Munchkin.munchkinFunction('clickLink', {
        'href': '/Football/Team/Seahawks'
    }
);
```

#### associateLead

此方法已淘汰，不再提供使用。
