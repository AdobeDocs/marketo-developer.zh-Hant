---
title: 檔案
feature: REST API
description: 依ID或名稱查詢Marketo REST API檔案、瀏覽資料夾和位移、透過多部分上傳建立或更新、insertOnly、MIME型別、無串流
exl-id: 17361cdc-2309-442c-803c-34ce187aee1a
TQID: https://experienceleague.adobe.com/qH8zFwjJkTWHlCj1VHNiTiLK3mNOJFS83cnjEj2qjpA
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 274
ht-degree: 1%

---

# 檔案

[檔案端點參考](https://developer.adobe.com/marketo-apis/api/asset#tag/Files)

使用檔案REST API來管理影像、指令碼、檔案、樣式表和其他儲存在Marketo訂閱中的檔案。

Marketo檔案儲存空間並未針對頻寬密集的應用程式進行最佳化。 使用音訊與視訊專用的串流服務。

## 查詢

查詢檔案[依識別碼](https://developer.adobe.com/marketo-apis/api/asset#tag/Files/operation/getFileByIdUsingGET)、[依名稱](https://developer.adobe.com/marketo-apis/api/asset#tag/Files/operation/getFileByNameUsingGET)或[瀏覽](https://developer.adobe.com/marketo-apis/api/asset#tag/Files/operation/getFilesUsingGET)。

### 依Id

```http
GET /rest/asset/v1/file/{id}.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":null,
   "result":[
      {
         "id":147,
         "size":61346,
         "mimeType":"image/jpeg",
         "url":"http://mlm.devlocal.marketo.com/rs/test/assets/rYXNeQFFVu",
         "folder":{
            "type":"Email",
            "id":10613
         },
         "name":"rYXNeQFFVu",
         "description":null,
         "createdAt":"2014-12-09T22:33:57Z+0000",
         "updatedAt":"2014-12-09T22:33:57Z+0000"
      }
   ]
}
```

### 依名稱

使用必要的`name`引數指定檔案的名稱。

```http
GET /rest/asset/v1/file/byName.json?name=foo.png
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "9049#15918a76619",
    "result": [
        {
            "id": 46488,
            "size": 13987,
            "mimeType": "image/png",
            "url": "http://na-abm.marketo.com/rs/mktodemoaccount88/assets/foo.png",
            "folder": {
                "type": "Image",
                "id": 436,
                "name": "My Images"
            },
            "name": "foo.png",
            "description": "This is a test file.",
            "createdAt": "2015-05-06T22:16:58Z+0000",
            "updatedAt": "2015-05-06T22:19:29Z+0000"
        }
    ]
}
```

### 瀏覽

瀏覽端點接受三個可選引數：

- `folder` — 父資料夾為包含`id`和`type`屬性的JSON物件。
- `offset` — 開始擷取專案的位置。 預設值為0。 與`maxReturn`搭配使用。
- `maxReturn` — 要傳回的專案數上限。 預設值為20，最大值為200。

```http
GET /rest/asset/v1/files.json?folder={"id":436, "type": "Folder"}&maxReturn=3
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "17e4e#14e23372d80",
    "result": [
        {
            "id": 46484,
            "size": 1454,
            "mimeType": "text/plain",
            "url": "http://na-abm.marketo.com/rs/mktodemoaccount88/assets/websites.png",
            "folder": {
                "type": "Image",
                "id": 436,
                "name": "My Images - deverly"
            },
            "name": "websites.png",
            "description": "This is a test file.",
            "createdAt": "2015-05-06T20:15:58Z+0000",
            "updatedAt": "2015-06-22T02:12:36Z+0000"
        },
        {
            "id": 46486,
            "size": 4169,
            "mimeType": "image/png",
            "url": "http://na-abm.marketo.com/rs/mktodemoaccount88/assets/mobile.png",
            "folder": {
                "type": "Image",
                "id": 436,
                "name": "My Images - deverly"
            },
            "name": "mobile.png",
            "description": null,
            "createdAt": "2015-05-06T22:13:33Z+0000",
            "updatedAt": "2015-05-06T22:13:33Z+0000"
        },
        {
            "id": 46488,
            "size": 13987,
            "mimeType": "image/png",
            "url": "http://na-abm.marketo.com/rs/mktodemoaccount88/assets/foo.png",
            "folder": {
                "type": "Image",
                "id": 436,
                "name": "My Images - deverly"
            },
            "name": "foo.png",
            "description": "This is a test file.",
            "createdAt": "2015-05-06T22:16:58Z+0000",
            "updatedAt": "2015-05-06T22:19:29Z+0000"
        }
    ]
}
```

## 建立和更新

使用`multipart/form-data`要求[建立檔案](https://developer.adobe.com/marketo-apis/api/asset#tag/Files/operation/createFileUsingPOST)。 需要`name`、`folder`和`file`引數。 `description`和`insertOnly`引數是選用的。 為true時，`insertOnly`會防止要求更新具有相同名稱的現有檔案。

對於`file`引數，在`Content-Disposition`標頭中包含`filename`。 也包含檔案的`Content-Type`標頭。 Marketo在提供檔案時使用此MIME型別。

```http
POST /rest/asset/v1/files.json
```

```html
------WebKitFormBoundary2VyWOacQSupl4gUL
Content-Disposition: form-data; name="file"; filename="marketo.html"
Content-Type: text/html
<html>
<body>
<h1>Test Page - marketo.html</h1>
</body>
</html>
------WebKitFormBoundary2VyWOacQSupl4gUL
Content-Disposition: form-data; name="name"
marketo.html
------WebKitFormBoundary2VyWOacQSupl4gUL
Content-Disposition: form-data; name="folder"
{"id":436,"type":"Folder"}
------WebKitFormBoundary2VyWOacQSupl4gUL
Content-Disposition: form-data; name="description"
This is a test file
------WebKitFormBoundary2VyWOacQSupl4gUL—
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "278d#14e23316f63",
    "result": [
        {
            "id": 46960,
            "size": 69,
            "mimeType": "text/html",
            "url": "http://na-abm.marketo.com/rs/mktodemoaccount88/assets/marketo.html",
            "folder": {
                "type": "Image",
                "id": 436,
                "name": "My Images - deverly"
            },
            "name": "marketo.html",
            "description": "This is a test file",
            "createdAt": "2015-06-24T01:31:59Z+0000",
            "updatedAt": "2015-06-24T01:31:59Z+0000"
        }
    ]
}
```

若要[更新檔案](https://developer.adobe.com/marketo-apis/api/asset#tag/File-Contents/operation/updateContentUsingPOST)，請指定其識別碼。 `file`引數具有與檔案建立相同的需求。

```http
POST /rest/asset/v1/file/{id}/content.json
```

```html
------WebKitFormBoundary2VyWOacQSupl4gUL
Content-Disposition: form-data; name="file"; filename="marketo.html"
Content-Type: text/html
<html>
<body>
<h1>Test Page - marketo.html</h1>
</body>
</html>
------WebKitFormBoundary2VyWOacQSupl4gUL--
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": null,
    "result": [
        {
            "id": 67,
            "size": 512000,
            "mimeType": "image/png",
            "url": "http://pages.devlocal.marketo.com/rs/test/assets/aLZiwCkXor",
            "folder": {
                "type": "Email",
                "id": 10391
            },
            "name": "aLZiwCkXor",
            "description": null,
            "createdAt": "2014-12-18T09:03:43Z+0000",
            "updatedAt": "2015-01-07T04:40:20Z+0000"
        }
    ]
}
```
