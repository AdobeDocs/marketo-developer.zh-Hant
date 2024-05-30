---
title: "端點參考"
feature: REST API
description: 「Marketo API端點參考」
source-git-commit: 2454f126dc4275697ef6773420453ad8853eae73
workflow-type: tm+mt
source-wordcount: '216'
ht-degree: 0%

---


# 端點參考

- [資產](https://developer.adobe.com/marketo-apis/api/asset/)
- [身分](https://developer.adobe.com/marketo-apis/api/identity/)
- [潛在客戶資料庫](https://developer.adobe.com/marketo-apis/api/mapi/)
- [User Management](https://developer.adobe.com/marketo-apis/api/user/)

## 端點參考

Marketo使用Swagger為其REST API提供公用介面的正式定義。 Swagger為URL結構、請求模型和回應模型提供豐富的定義模型，並擁有用於API互動、測試和使用者端產生的開發工具生態系統。

端點參考使用 [Swagger-UI](https://swagger.io/tools/swagger-ui/) JavaScript套件，可在使用者端產生參考頁面。 每個公用端點都會列出，並提供回應模型的結構、必要的請求引數，以及必要的請求模型。

## 使用Marketo的Swagger定義

Swagger標準需要提供主機，或讓提供檔案的主機產生主機。 使用任何現有工具之前，請務必修正定義中的主機，因為Marketo會為定義提供空白的主機引數。 每個Marketo例項的REST API主機都是唯一的，並遵循模式：

`{Munchkin ID}.mktorest.com`

## 資產API

Marketo資產API使用 `application/x-www-url-formencoded` 需要POST方法的端點要求中的樣式引數。 但在某些情況下（例如資料夾引數），引數的值可能是JSON陣列或物件。 這些引數會在端點參考中註明。
