---
title: 代碼片段
feature: REST API, Snippets
description: 透過Marketo API管理代碼片段。
exl-id: 87901c29-ee59-4224-848d-3bd6a6c52718
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '438'
ht-degree: 1%

---

# 代碼片段

[程式碼片段端點參考](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets)

程式碼片段是可重複使用的HTML元件，可嵌入至電子郵件和登入頁面，且可針對動態內容分段。 程式碼片段沒有相關聯的範本，而且可以在Marketo內的其他資產中建立和部署。

## 查詢

查詢代碼片段會遵循資產的標準模式，不過沒有By Name方法。 [By Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets/operation/getSnippetByIdUsingGET)和[Browse](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets/operation/getSnippetUsingGET)方法都允許使用狀態列位來擷取已核准或草稿版本的程式碼片段。

### 依Id

```
GET /rest/asset/v1/snippet/{id}.json?status=approved
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"fa0f#14b04375f0a",
   "result":[
      {
         "id":83,
         "name":"BYkHVJEedl",
         "description":"yzSLvNFyrmeVmyLzqryUfGlDOJTnvyyfsQTXPDCGdCwcWUlfoCNApUqYgwZGElrUFoxBHJcMdXdqTKvtjtfsmPgokyRgVLeHyJCw",
         "createdAt":"2015-01-19T22:01:52Z+0000",
         "updatedAt":"2015-01-19T22:01:52Z+0000",
         "folder":{
            "type":"Folder",
            "value":662
         },
         "status":"approved",
         "workspace":"Default"
      }
   ]
}
```

### 瀏覽

```
GET /rest/asset/v1/snippets.json?maxReturn=3
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"f9cc#14b04376181",
   "result":[
      {
         "id":23,
         "name":"ADJvMLBMpS",
         "description":"XkzFUVLXVHrojLGLJVLPpwguOXuDvhAqaaSkBUVzgHrgDhqqRzyXlULIXSHJvfBHjCSaMwjyEdrdxcjFCRoNFVvdBBTDfSrUJzaR",
         "createdAt":"2015-01-15T20:10:39Z+0000",
         "updatedAt":"2015-01-15T20:10:39Z+0000",
         "url": null,
         "folder":{
            "type":"Folder",
            "value":620,
            "folderName": "Snippets"
         },
         "status":"draft",
         "workspace":"Default"
      },
      {
         "id":46,
         "name":"Biswa Snippet",
         "description":"",
         "createdAt":"2015-01-16T05:18:55Z+0000",
         "updatedAt":"2015-01-16T05:19:27Z+0000",
         "url": null,
         "folder":{
            "type":"Folder",
            "value":630,
            "folderName": "Snippets"
         },
         "status":"draft",
         "workspace":"Default"
      },
      {
         "id":12,
         "name":"dJJQkKbUYq",
         "description":"VXuHkYMREHrhxUSgYbKfaNeLisdFxOromCXQNrgmModvkuoyZdQjtAbXxDUbBvoDVCZmAVbasiHyWoWfTwgrGxnzpKepGrAUvfen",
         "createdAt":"2015-01-15T05:12:33Z+0000",
         "updatedAt":"2015-01-15T05:12:33Z+0000",
         "url": null,
         "folder":{
            "type":"Folder",
            "value":615,
            "folderName": "Snippets"
         },
         "status":"draft",
         "workspace":"Default"
      }
   ]
}
```

## 查詢內容

可以根據程式碼片段ID擷取指定程式碼片段的內容。

```
GET /rest/asset/v1/snippet/{id}/content.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"5c50#14b04376159",
   "result":[
      {
         "type":"HTML",
         "content":"draft testUpdateSnippetContent1 HTML Content"
      },
      {
         "type":"Text",
         "content":"draft testUpdateSnippetContent1 Text Content"
      }
   ]
}
```

呼叫會傳回內容區段的清單，  包含型別HTML或型別DynamicContent的區段，以及選擇性包含型別Text的區段。

## 建立和更新

程式碼片段會遵循複雜的資產建立模式，也就是呼叫[建立程式碼片段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets/operation/createSnippetUsingPOST)，而且其內容會個別進行，因此第一個呼叫必須是建立端點，並附上選用的說明。   資料以x-www-form-urlencoded傳遞，而非以JSON傳遞。

```
POST /rest/asset/v1/snippets.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Test Snippet 09 - deverly&folder={"id":395,"type":"Folder"}&description=This is a test snippet
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "bd57#14e231ee3a1",
    "result": [
        {
            "id": 13,
            "name": "Test Snippet 09 - deverly",
            "description": "This is a test snippet",
            "createdAt": "2015-06-24T01:11:43Z+0000",
            "updatedAt": "2015-06-24T01:11:43Z+0000",
            "url": "https://app-abm.marketo.com/#SN13B2ZN395",
            "folder": {
                "type": "Folder",
                "value": 395,
                "folderName": "Snippets"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

在程式碼片段中新增或取代內容是透過id完成。 內容可以是Text、HTML或DynamicContent型別。 如果型別是文字，則內容引數為純文字端點，如果是HTML，則為所要的標示文字。 如果型別設為DynamicContent，則內容引數應設為，與程式碼片段關聯的區段ID。

```
POST /rest/asset/v1/snippet/{id}/content.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
type=HTML&content=draft testUpdateSnippetContent1 HTML Content
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"73d9#14b04376139",
   "result":[
      {
         "id":82
      }
   ]
}
```

[中繼資料](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets/operation/updateSnippetUsingPOST)的更新也由ID完成。 只能更新名稱和說明：

```
POST /rest/asset/v1/snippet/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Test Snippet&description=New Description
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"9ad0#14b043762b1",
   "result":[
      {
         "id":82,
         "name":"Test Snippet",
         "description":"New Description",
         "createdAt":"2015-01-19T22:01:52Z+0000",
         "updatedAt":"2015-01-19T22:01:53Z+0000",
         "url": "https://app-abm.marketo.com/#SN3B2ZN395",
         "folder":{
            "type":"Folder",
            "value":662,
            "folderName": "Snippets"
         },
         "status":"draft",
         "workspace":"Default"
      }
   ]
}
```

## 動態內容

程式碼片段遵循動態內容的標準模式，但本身僅代表一個完整內容區段，因此每個程式碼片段可能僅包含一個動態區段，以及所選用於細分中每個區段的內部區段清單。 動態內容可單獨透過代碼片段ID進行查詢，因為代碼片段中可能只有一個動態內容區段。

```
GET /rest/asset/v1/snippet/{id}/dynamicContent.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "ae3#14c2b499111",
    "result": [
        {
            "createdAt": "2015-03-13T06:24:35Z+0000",
            "updatedAt": "2015-03-17T20:29:42Z+0000",
            "id": 70,
            "segmentation": 1001,
            "content": [
                {
                    "id": "Nzk*",
                    "segmentId": 1001,
                    "segmentName": "Area",
                    "content": "Sample HTML for Area",
                    "type": "HTML"
                },
                {
                    "id": "Nzk*",
                    "segmentId": 1001,
                    "segmentName": "Area",
                    "content": "Sample Text for Area",
                    "type": "Text"
                },
                {
                    "id": "Nzk*",
                    "segmentId": 1002,
                    "segmentName": "Default",
                    "content": "Sample HTML for Default",
                    "type": "HTML"
                },
                {
                    "id": "Nzk*",
                    "segmentId": 1002,
                    "segmentName": "Default",
                    "content": "Sample Text for Default",
                    "type": "Text"
                }
            ]
        }
    ]
}
```

## 核准

程式碼片段有可用於核准、取消核准和捨棄草稿的端點，這些端點會遵循標準資產模式。 程式碼片段必須處於草稿狀態才能獲得核准。

### 核准

```
POST /rest/asset/v1/snippet/{id}/approveDraft.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "11903#14db1af2f6c",
    "result": [
        {
            "id": 3,
            "name": "Test Snippet 02 - deverly",
            "description": "hey this is a test snippet!",
            "createdAt": "2015-06-02T00:32:37Z+0000",
            "updatedAt": "2015-06-02T00:32:37Z+0000",
            "url": "https://app-abm.marketo.com/#SN3B2ZN395",
            "folder": {
                "type": "Folder",
                "value": 395,
                "folderName": "Snippets"
            },
            "status": "approved",
            "workspace": "Default"
        }
    ]
}
```

### 取消核准

`unapprove`端點只能用於已核准的程式碼片段。

```
POST /rest/asset/v1/snippet/{id}/unapprove.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "7d20#14db1c7a2a9",
    "result": [
        {
            "id": 89,
            "name": "Test Snippet 01 - deverly",
            "description": "",
            "createdAt": "2015-05-15T19:01:22Z+0000",
            "updatedAt": "2015-05-15T19:07:07Z+0000",
            "url": "https://app-abm.marketo.com/#SN1B2ZN395",
            "folder": {
                "type": "Folder",
                "value": 395,
                "folderName": "Snippets"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

### 捨棄草稿

程式碼片段必須處於草稿狀態才能捨棄。  無法捨棄已核准的程式碼片段。

```
POST /rest/asset/v1/snippet/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"674c#14b043760de",
   "result":[
      {
         "id":88
      }
   ]
}
```

## 原地複製

[使用API復製程式碼片段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Snippets/operation/cloneSnippetUsingPOST)很簡單，並遵循標準模式，具有必要名稱、原始程式碼片段和資料夾的識別碼，以及選用的說明。  如果不存在核准的版本，則會複製草稿版本。

```
POST /rest/asset/v1/snippet/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Test Snippet Clone 3 - deverly&folder={"id":395,"type":"Folder"}&description=This is a test snippet
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "21c9#14e2327e33d",
    "result": [
        {
            "id": 14,
            "name": "Test Snippet Clone 3 - deverly",
            "description": "This is a test snippet",
            "createdAt": "2015-06-24T01:21:33Z+0000",
            "updatedAt": "2015-06-24T01:21:33Z+0000",
            "url": "https://app-abm.marketo.com/#SN14B2ZN395",
            "folder": {
                "type": "Folder",
                "value": 395,
                "folderName": "Snippets"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```
