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

[Swagger](https://swagger.io/) 或 [OpenAPI](https://www.openapis.org/) 定義是說明REST API所有方法和引數的資料檔案。 您可以在本機下載和使用這些資料檔案，作為您的個人API參考。

若要使用Marketo EngageSwagger/OpenAPI定義，請 `host` 每個檔案的欄位都必須更新，以符合Marketo Engage例項的REST API主機名稱。

首先，下載您要使用的OpenAPI定義：

* [資產](assets/swagger-asset.json)
* [銷售機會](assets/swagger-mapi.json)
* [身分](assets/swagger-identity.json)
* [User Management](assets/swagger-user.json)

接下來，向Marketo管理員取得您的REST API主機名稱。 前往 _管理員_-> _網站服務_ Marketo Engage功能表，並從「端點」欄位複製主機名稱。 此 `hostname` 是通訊協定之間的字串， `https://`、和 `/rest`，類似於 `AAA-999-AAA.mktorest.com`

在文字編輯器中開啟您的OpenAPI檔案，並找到 `host` JSON中的欄位，並將其值變更為您的REST API主機名稱： `"host":"localhost:8080"` 至 `"host":"AAA-999-AAA.mktorest.com"`.

儲存後，您可以在Swagger UI執行個體或任何其他OpenAPI軟體中執行定義檔案。
