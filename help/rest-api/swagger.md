---
title: 下載Swagger定義
feature: REST API, Programs
description: 下載Swagger定義檔案以供本機使用。
source-git-commit: 85062243d57a3fc6d15251163e926495858edf2a
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# 下載Swagger定義

[Swagger](https://swagger.io/)或[OpenAPI](https://www.openapis.org/)定義是描述REST API所有方法與引數的資料檔案。 您可以在本機下載和使用這些資料檔案，作為您的個人API參考。

若要使用Marketo EngageSwagger/OpenAPI定義，每個檔案的`host`欄位都必須更新，以符合Marketo Engage執行個體中的REST API主機名稱。

首先，下載您要使用的OpenAPI定義：

* [資產](assets/swagger-asset.json)
* [銷售機會](assets/swagger-mapi.json)
* [身分](assets/swagger-identity.json)
* [User Management](assets/swagger-user.json)

接下來，向Marketo管理員取得您的REST API主機名稱。 移至Marketo Engage中的&#x200B;_管理員_-> _網站服務_&#x200B;功能表，並從[端點]欄位複製主機名稱。 `hostname`是通訊協定`https://`和`/rest`之間的字串，看起來像`AAA-999-AAA.mktorest.com`

在文字編輯器中開啟您的OpenAPI檔案，並在JSON中尋找`host`欄位，並將其值變更為您的REST API主機名稱： `"host":"localhost:8080"`至`"host":"AAA-999-AAA.mktorest.com"`。

儲存後，您可以在Swagger UI執行個體或任何其他OpenAPI軟體中執行定義檔案。
