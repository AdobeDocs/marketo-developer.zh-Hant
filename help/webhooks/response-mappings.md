---
title: 回應對應
feature: Webhooks
description: Marketo Webhook會對JSON和XML進行回應對應，將屬性對應至具有SOAP API名稱、點和陣列標籤法以及型別相容性的潛在客戶欄位。
exl-id: 95c6e33e-487c-464b-b920-3c67e248d84e
TQID: https://experienceleague.adobe.com/-OGDeKLPS1KmWGIKj6BGq5DGXoCSj5ip-dVr7-kKDro
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 373
ht-degree: 0%

---

# 回應對應

Marketo可以從JSON或XML翻譯webhook資料，並將值寫入潛在客戶欄位。 Marketo欄位引數一律使用欄位的[SOAP API名稱](../rest-api/fields.md)。

每個webhook都可以有不限數量的回應對應。 若要新增或編輯對應，請在webhook的「回應對應」窗格中選取[!UICONTROL Edit]：

![回應對應](assets/response-mapping.png)

回應對應會配對這些值：

- &quot;Response Attribute&quot;： XML或JSON檔案中所需屬性的路徑。
- 「Marketo欄位」： Marketo寫入回應屬性值的潛在客戶欄位。

若要透過Marketo回應對應存取屬性，其索引鍵只能包含英數字元、破折號(-)、底線(_)、冒號(：)和空格。

## JSON對應

使用點標籤法和陣列標籤法存取JSON屬性。 Marketo陣列標籤法只接受整數，不接受字串。

若要從JSON檔案中擷取資料，請將回應型別設為JSON：

```json
{ "foo":"bar"}
```

`foo`屬性位於JSON物件的第一層級。 在回應對應中使用其屬性`name`， `foo`：

![回應對應](assets/json-resp.png)

下列範例包含陣列：

```json
{
    "profileId" : 1234,
    "firstName" : "Jane",
    "lastName" : "Doe",
    "orders" : [
        {
            "orderId" : 5678,
            "orderDate" : "2015-01-01",
            "orderProductId" : "4982"
        },
        {
            "orderId" : 5678,
            "orderDate" : "2014-05-07",
            "orderProductId" : "4982"
        }
    ]
}
```

若要從orders陣列的第一個元素存取orderDate，請使用`orders[0].orderDate`。

## XML對應

使用點標籤法（類似JSON對應）存取個別XML元素的值。 請參考此範例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<example>
    <foo>bar</foo>
</example>
```

若要存取foo屬性，請使用`example.foo`。

在存取`foo`之前，請先參考範例專案。 對應必須參考屬性階層中的每個元素。

對於具有陣列的XML檔案，請考量以下範例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<elementList>
    <element>
        <foo>baz</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
</elementList>
```

父陣列為`elementList`。 每個子專案都包含`foo`屬性。 Marketo回應對應會將陣列參考為`elementList.element`，並透過`elementList.element[i]`存取其子系。

若要從elementList的第一個子系取得foo的值，請使用回應屬性`elementList.element[0].foo`。 此對應會將「baz」值傳回至指定欄位。

存取包含唯一和非唯一元素名稱的元素內的屬性會產生未定義的行為。 每個元素必須是單一屬性或陣列。 請勿混用不同型別。

## 類型

將屬性對應到欄位時，請確保webhook回應型別與目標欄位相容。 例如，Marketo不會將字串回應值寫入整數型別的欄位。 如需詳細資訊，請參閱[欄位型別](../rest-api/field-types.md)。
