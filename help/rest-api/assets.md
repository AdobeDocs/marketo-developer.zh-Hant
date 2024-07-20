---
title: 資產
feature: REST API
description: 使用Marketo資產的API。
exl-id: 4273a5b1-1904-46e8-b583-fc6f46b388d2
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '876'
ht-degree: 1%

---

# 資產

Marketo提供的API可與Marketo內的大多數行銷和組織資產互動。

## 資產

Marketo資產包括：

- 資料夾
- 計畫
- 電子郵件
- 電子郵件範本
- 登陸頁面
- 登陸頁面範本
- 代碼片段
- Forms
- Token
- 檔案

## API

如需資產API端點的完整清單，包括引數和模型資訊，請參閱[資產API端點參考](endpoint-reference.md)。

## 查詢

Assets通常有三種擷取模式：依id、依名稱和依瀏覽。  依ID和依名稱都會針對指定的引數擷取單一資產，而瀏覽則會傳回，並允許分頁瀏覽該型別的整個資產清單。  個別型別的資產有不同的引數，可依這些引數篩選，因此請務必檢視其個別檔案以取得詳細資訊。

在某些情況下，某些資產型別的瀏覽端點將不會傳回子資產（例如標籤的可允許值），並且必須使用「依名稱」或「依ID」端點來個別擷取它們，以傳回完整的中繼資料集。  其他可能有完全不同的端點，用於擷取相依物件，例如表單欄位。

### 依Id

```
GET /rest/asset/v1/folder/{id}.json?type=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1241b#14e21ca814a",
    "result": [
        {
            "name": "Social Media",
            "description": null,
            "createdAt": "2011-03-04T17:01:32Z+0000",
            "updatedAt": "2011-03-04T17:01:32Z+0000",
            "url": null,
            "folderId": {
                "id": 341,
                "type": "Folder"
            },
            "folderType": "Email",
            "parent": {
                "id": 11,
                "type": "Folder"
            },
            "path": "/Design Studio/Default/Emails/Social Media",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 341
        }
    ]
}
```

### 依名稱

由於技術原因，Asset API無法搜尋包含逗號(，)的資產名稱。  建議您不要為所有資產型別使用逗號。

```
GET /rest/asset/v1/file/byName.json?name=My File
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":null,
   "result":[
      {
         "id":148,
         "size":270313,
         "mimeType":"image/jpeg",
         "url":"http://mlm.devlocal.marketo.com/rs/test/assets/piKLbhVFvW",
         "folder":{
            "type":"Email",
            "id":10614
         },
         "name":"My File",
         "description":null,
         "createdAt":"2014-12-09T22:33:57Z+0000",
         "updatedAt":"2014-12-09T22:33:57Z+0000"
      }
   ]
}
```

### 瀏覽

瀏覽資產將一律允許兩個查詢引數：

- offset — 傳回結果的整數位移。
- maxReturn — 限制傳回的記錄數。  若未設定，則預設為20，最大值為200。

```
GET /rest/asset/v1/emailTemplates.json?offset=10&maxReturn=50
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"33c4#14a1832b4a8",
   "result":[
      {
         "id":18,
         "name":"AAA0unit3CreateTestEmailTemplateName.2314673e-7bc2-47da-a1e8-66dfdd8a1f1d",
         "description":"AssetAPI: getTemplates test",
         "createdAt":"2014-11-03T19:52:58Z+0000",
         "updatedAt":"2014-11-03T19:52:58Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":177,
         "name":"ABfRGutnwN",
         "description":"HMmHkdTRrGaRpPakdgGKICxfMunCEWDUWiThgAbInfaBXxGxSFfjKQIwerngCHRlGTnAJhKPmwlXLcsjGPtWEiILGyeIJTNVHoHg",
         "createdAt":"2014-11-20T19:31:06Z+0000",
         "updatedAt":"2014-11-20T19:31:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":148,
         "name":"ADVHJBQLyw",
         "description":null,
         "createdAt":"2014-11-20T06:42:57Z+0000",
         "updatedAt":"2014-11-20T06:42:57Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

## 建立和更新

對於資料夾、權杖和檔案等簡單資產型別，通常只有一個端點可供建立，然後是額外的端點，用於依ID更新記錄。  Assets是以永遠必要的名稱建立，然後建立或更新回應會傳回任何中繼資料和ID。

例如，以下說明如何建立代號：

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&value=2015-04-01&type=date&folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "e3c2#14e280db5dc",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "April Fools",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

若要更新資料夾，請執行下列動作：

```
POST /rest/asset/v1/folder/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
type=Folder&description=This is a test (update 01)
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "c5b2#14e1f3954bf",
    "result": [
        {
            "name": "Learning - deverly",
            "description": "This is a test (update 01)",
            "createdAt": "2015-03-17T00:17:02Z+0000",
            "updatedAt": "2015-06-23T07:02:07Z+0000",
            "url": "https://app-abm.marketo.com/#MF1044A1",
            "folderId": {
                "id": 407,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Learning - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 407
        }
    ]
}
```

其他資產具有更複雜的結構，需要更新其他子區段或子物件，然後最終必須在投入使用前通過核准。  這些資產型別包括Forms、電子郵件、電子郵件範本、登入頁面及登入頁面範本。  這些區段分別會有一個端點用於建立記錄，接著會有其他端點用於更新中繼資料、內容和內容區段。

例如，若要建立登入頁面，您必須在核准前使用範本ID呼叫其建立端點，然後擷取其內容區段，並個別更新每個區段以新增內容，以便可即時部署。

### 複雜建立

登陸頁面必須先使用父範本建立登陸頁面資產。  這樣會建立新的登入頁面，其中包含每個內容區段的範本預設內容。

```
POST rest/asset/v1/landingPages.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

#### 取得區段

若要填入登入頁面的內容，您必須擷取內容區段清單，然後針對任何偏離範本的區段執行個別更新。

```
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

#### 更新區段

```
POST /rest/asset/v1/landingPage/{id}/content/{contentId}.json?type=Form&value=1
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "5c37#154ea32cf11",
    "result": [
        {
            "id": 174
        }
    ]
}
```

## 核准

許多資產型別都有相關的草稿和核准系統，包括電子郵件、登陸頁面、代碼片段、Forms及其對應的範本。  嘗試核准資產時，將會根據一組特定的驗證規則來評估資產，然後將其設為已核准狀態，或傳回失敗原因。  對於這些型別的資產，每當對特定資產的內容進行更新時，都會對資產的草稿進行變更，這不會影響已核准的版本。  如此可安全地變更內容，而不會影響資產的即時版本。  然後可以使用核准端點將變更套用至即時版本。  這也會清除資產的草稿狀態，直到套用任何其他更新為止。

```
POST /rest/asset/v1/emailTemplate/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"abe2#14a1832a97d",
   "result":[
      {
         "id":338,
         "name":"lvAVYMZqPS",
         "description":"fZLJQSJRvnYbjGTUpIHHqDOuQgQzXQcWIXoOUPwrVLdMHKcbRqwLoSLkWZTUmaMiCIJSfQiufnnrgITUIqjuAPBLpmliiKuIUFYG",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Approved",
         "workspace":"Default"
      }
   ]
}
```

成功的核准會以更新版本取代先前的即時版本。

也可以透過每個有效資產型別的端點來捨棄草稿。  若在處於已核准草稿狀態的資產上使用此專案，將會捨棄目前的草稿及其任何暫止的變更。  在目前沒有核准版本的資產上使用此專案，將不會產生任何效用並傳回錯誤。  僅限草稿的資產可以刪除，但無法捨棄。

```
POST /rest/asset/v1/emailTemplate/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"17bfa#14a1832b3c4",
   "result":[
      {
         "id":344,
         "name":"LkilkvKrkp",
         "description":"yAyUEXuWMtdhpODUmnCkGjpBcyEKnYucxaSoTyYeQzyNbYanxCXWPOzwiIWmeXPUwjfGAUmgnxlhgOPluVqwNittuvxJmNTaHxYM",
         "createdAt":"2014-12-05T02:06:23Z+0000",
         "updatedAt":"2014-12-05T02:06:23Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

如果Assets處於僅核准狀態，也可以取消核准。  這將會刪除資產的任何即時版本，並將資產回覆成僅限草稿的狀態，同時也會捨棄任何相關的草稿。  此動作只能在Marketo中從未使用過的情況下（例如傳送電子郵件流程步驟中參照的電子郵件，或嵌入電子郵件中的程式碼片段），對大部分資產執行。

```
POST /rest/asset/v1/email/{id}/unapprove.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"3514#14a1832b0fa",
   "result":[
      {
         "id":1364
      }
   ]
}
```

## 刪除

具有核准和草稿狀態（表單除外）的Assets在核準時不可刪除，並且在刪除之前必須未核准。  一般而言，只有當資產未核准且無法使用，以及在資料夾中為空資產的情況下，才能執行刪除。  一個顯著的例外是程式，只要程式及其內容不在程式範圍以外的任何地方使用，程式可以與其所有子項內容一起刪除。

```
POST /rest/asset/v1/program/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16501#14db042c6b7",
    "result": [
        {
            "id": 1109
        }
    ]
}
```

## 逾時

資產API的逾時時間為300秒
