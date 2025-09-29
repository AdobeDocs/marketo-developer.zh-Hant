---
title: 錯誤代碼
feature: REST API
description: 瞭解如何使用HTTP 413和414、回應6xx 7xx、記錄層級狀態、記錄最佳實務、重試和限制來處理Marketo REST API錯誤。
exl-id: a923c4d6-2bbc-4cb7-be87-452f39b464b6
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '2338'
ht-degree: 3%

---

# 錯誤代碼

以下是REST API錯誤碼清單，並說明如何將錯誤傳回至應用程式。

## 處理與記錄例外

針對Marketo進行開發時，發生非預期的例外狀況時，請務必記錄請求和回應。 雖然重新驗證可以安全地處理某些型別的例外（例如過期的驗證），但其他例外可能需要支援互動，而在此情況下將一律要求請求和回應。

## 錯誤型別

Marketo REST API在正常操作下可傳回三種不同型別的錯誤：

* HTTP-Level：這些錯誤以`4xx`程式碼表示。
* 回應層級：這些錯誤包含在JSON回應的「錯誤」陣列中。
* 記錄層級：這些錯誤包含在JSON回應的「結果」陣列中，並以「狀態」欄位和「原因」陣列依個別記錄指示。

對於回應層級和記錄層級錯誤型別，會傳回HTTP狀態碼200。 對於所有錯誤型別，不應評估HTTP原因片語，因為它是選用且可能會變更。

### HTTP層級錯誤

在正常作業情況下，Marketo應該只傳回兩個HTTP狀態程式碼錯誤： `413 Request Entity Too Large`和`414 Request URI Too Long`。 擷取錯誤、修改請求和重試都可以復原這些設定，但使用智慧編碼實務作法，您絕不應該在萬不得已的情況下遇到這些設定。

如果要求裝載超過1MB，Marketo會傳回413，若是Import Lead，會傳回10MB。 在大多數情況下，應該不會達到這些限制，但新增檢查至要求的大小，並移動任何記錄（這會導致限制超過新的要求）應該可以防止任何情況（這會導致任何端點傳回此錯誤）。

當GET要求的URI超過8KB時，將會傳回414。 若要避免出現這種情況，請檢查查詢字串的長度，看看是否超過此限制。 如果它確實將您的要求變更為POST方法，則使用其他引數`_method=GET`將您的查詢字串輸入為要求內文。 這放棄了URI的限制。 在大多數情況下很少達到此限制，但在擷取具有長的個別篩選值（例如GUID）的大量記錄時，這比較常見。
[身分](https://developer.adobe.com/marketo-apis/api/identity/)端點可能傳回401未授權錯誤。 這通常是因為無效的使用者端ID或無效的使用者端密碼。 HTTP層級錯誤代碼

<table>
  <thead>
    <tr>
      <th> 回應代碼 </th>
      <th> 說明 </th>
      <th colspan="1"> 註解 </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="413"></a>413</td>
      <td>請求實體太大</td>
      <td>承載超過1MB限制。</td>
    </tr>
    <tr>
      <td><a name="414"></a>414</td>
      <td>請求URI太長</td>
      <td>請求的URI超過8k。 該請求應該重試為POST，在URL中包含param '_method=GET'，其餘的查詢字串在請求內文中。</td>
    </tr>
  </tbody>
</table>

#### 回應層級錯誤

當回應的`success`引數設為false，且結構如下列時，會出現回應層級錯誤：

```json
{
    "requestId": "e42b#14272d07d78",
    "success": false,
    "errors": [
        {
            "code": "601",
            "message": "Unauthorized"
        }
    ]
}
```

「錯誤」陣列中的每個物件都有兩個成員，`code`，這是從601到799的引號整數，以及提供錯誤純文字原因的`message`。 6xx程式碼一律會指出要求完全失敗且未執行。 601「存取權杖無效」即是範例，您可使用要求重新驗證並傳遞新的存取權杖來復原它。 7xx錯誤表示請求失敗，可能是因為未傳回任何資料，或是請求引數化不正確，例如包含無效的日期，或遺漏必要的引數。

#### 回應層級錯誤代碼

傳回此回應程式碼的API呼叫不會算入您的每日配額或速率限制中。

<table>
  <thead>
    <tr>
      <th> 回應代碼 </th>
      <th> 說明 </th>
      <th> 註解 </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="502"></a>502</td>
      <td>錯誤的閘道</td>
      <td>遠端伺服器傳回錯誤。 可能是逾時。 該請求應該以指數輪詢重試。</td>
    </tr>
    <tr>
      <td><a name="601"></a>601*</td>
      <td>存取權杖無效</td>
      <td>請求中包含存取權杖引數，但值不是有效的存取權杖。</td>
    </tr>
    <tr>
      <td><a name="602"></a>602*</td>
      <td>存取權杖已過期</td>
      <td>由於過期，包含在呼叫中的存取Token已失效。</td>
    </tr>
    <tr>
      <td><a name="603"></a>603</td>
      <td>存取遭拒</td>
      <td>驗證成功，但使用者沒有足夠的許可權呼叫此API。 [其他許可權](custom-services.md)可能需要指派給使用者角色，或可能啟用<a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-an-allowlist-for-ip-based-api-access">IP型API存取的允許清單</a>。</td>
    </tr>
    <tr>
      <td><a name="604"></a>604*</td>
      <td>請求逾時</td>
      <td>要求執行時間太長（例如，發生資料庫爭用），或超過呼叫標頭中指定的逾時期間。</td>
    </tr>
    <tr>
      <td><a name="605"></a>605*</td>
      <td>不支援HTTP方法</td>
      <td>Sync Leads端點不支援GET。 必須使用POST。</td>
    </tr>
    <tr>
      <td><a name="606"></a>606</td>
      <td>最大速率限制'%s'；超過'%s'秒中的值</td>
      <td>過去20秒的通話次數大於100</td>
    </tr>
    <tr>
      <td><a name="607"></a>607</td>
      <td>已達到每日配額</td>
      <td>今天的通話次數超過訂閱的配額（於每日中午12:00CST重設）。&gt;您可以在「管理員 — &gt;網站服務」功能表中找到您的配額。 您可以透過帳戶管理員增加配額。</td>
    </tr>
    <tr>
      <td><a name="608"></a>608*</td>
      <td>API暫時無法使用</td>
      <td></td>
    </tr>
    <tr>
      <td><a name="609"></a>609</td>
      <td>無效的JSON</td>
      <td>請求中包含的內文不是有效的JSON。</td>
    </tr>
    <tr>
      <td><a name="610"></a>610</td>
      <td>找不到請求的資源</td>
      <td>呼叫中的URI不符合REST API資源型別。 這通常是因為錯誤拼字或錯誤格式的請求URI</td>
    </tr>
    <tr>
      <td><a name="611"></a>611*</td>
      <td>系統錯誤</td>
      <td>所有未處理的例外狀況</td>
    </tr>
    <tr>
      <td><a name="612"></a>612</td>
      <td>無效的內容型別</td>
      <td>如果您看到此錯誤，請新增內容型別標頭，以指定JSON格式至您的請求。 例如，嘗試使用「content type： application/json」。 <a href="https://stackoverflow.com/questions/28181325/why-invalid-content-type">如需詳細資訊，請參閱此StackOverflow問題</a>。</td>
    </tr>
    <tr>
      <td><a name="613"></a>613</td>
      <td>無效的多部分請求</td>
      <td>POST的多部分內容格式不正確</td>
    </tr>
    <tr>
      <td><a name="614"></a>614</td>
      <td>無效的訂閱</td>
      <td>找不到目的地訂閱或無法連線。 這通常表示暫時無法存取。</td>
    </tr>
    <tr>
      <td><a name="615"></a>615</td>
      <td>已達到並行存取限制</td>
      <td>任何訂閱一次最多可處理10個請求。 如果已經有10個進行中的請求，則會傳回此值。</td>
    </tr>
    <tr>
      <td><a name="616"></a>616</td>
      <td>無效的訂閱型別</td>
      <td>存取自訂物件中繼資料API需要適當的Marketo訂閱型別。 如需詳細資訊，請洽詢您的CSM。</td>
    </tr>
    <tr>
      <td><a name="701"></a>701</td>
      <td>%s不能空白</td>
      <td>請求中報告的欄位不得為空白</td>
    </tr>
    <tr>
      <td><a name="702"></a>702</td>
      <td>找不到特定搜尋情境的資料</td>
      <td>沒有符合指定搜尋引數的記錄。
        注意：許多失敗的搜尋作業會傳回「success = true」且沒有錯誤，同時會設定警告資訊字串。</td>
    </tr>
    <tr>
      <td><a name="703"></a>703</td>
      <td>訂閱未啟用此功能</td>
      <td>使用者訂閱中未啟用的測試版功能</td>
    </tr>
    <tr>
      <td><a name="704"></a>704</td>
      <td>無效的日期格式</td>
      <td><ul>
          <li>指定的日期格式不正確</li>
          <li>指定的動態內容識別碼無效</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="709"></a>709</td>
      <td>違反商業規則</td>
      <td>無法完成呼叫，因為它違反了建立或更新資產的要求，例如，嘗試在沒有範本的情況下建立電子郵件。 嘗試下列動作時，也可能發生此錯誤：
        <ul>
          <li>擷取包含社交內容的登入頁面內容。</li>
          <li>複製包含特定資產型別的程式（如需詳細資訊，請參閱<a href="programs.md#clone">程式複製</a>）。</li>
          <li>核准沒有草稿的資產（即已核准）。</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="710"></a>710</td>
      <td>找不到上層資料夾</td>
      <td>找不到指定的父資料夾</td>
    </tr>
    <tr>
      <td><a name="711"></a>711</td>
      <td>不相容的資料夾型別</td>
      <td>指定的資料夾型別不正確，無法完成要求</td>
    </tr>
    <tr>
      <td><a name="712"></a>712</td>
      <td>「合併至人員帳戶」作業無效</td>
      <td>由於嘗試合併Salesforce個人帳戶的銷售機會，合併銷售機會呼叫失敗。  Salesforce個人帳戶必須合併到Salesforce中。</td>
    </tr>
    <tr>
      <td><a name="713"></a>713</td>
      <td>暫時性錯誤</td>
      <td>API呼叫時，系統資源暫時無法使用。 發生此錯誤時，建議您等候一段時間，然後重試要求。</td>
    </tr>
    <tr>
      <td><a name="714"></a>714</td>
      <td>找不到預設記錄型別</td>
      <td>合併銷售機會呼叫失敗，因為找不到預設記錄型別。</td>
    </tr>
    <tr>
      <td><a name="718"></a>718</td>
      <td>找不到ExternalSalesPersonID</td>
      <td>已使用不存在的「ExternalSalesPersonID」值進行Sync Opportunities呼叫。</td>
    </tr>
    <tr>
      <td>719</td>
      <td>鎖定等待逾時例外狀況</td>
      <td>已發出復製程式呼叫，且等候鎖定時逾時。</td>
    </tr>
  </tbody>
</table>

### 記錄層級 {#record_level_errors}

記錄層級錯誤表示無法完成個別記錄的作業，但要求本身有效。 含有記錄層級錯誤的回應會遵循以下模式：

#### 回應

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "id":50,
         "status":"created"
      },
      {
         "id":51,
         "status":"created"
      },
      {
         "status":"skipped",
         "reasons":[
            {
               "code":"1005",
               "message":"Lead already exists"
            }
         ]
      }
   ]
}
```

呼叫結果陣列中包含的記錄排序方式，與請求的輸入陣列相同。
成功請求中的每個記錄都可能依個別情況成功或失敗，由回應結果陣列中所包含每個記錄的狀態列位表示。 這些記錄的「狀態」欄位將「略過」，且會出現「原因」陣列。 每個原因都包含「代碼」成員和「訊息」成員。 程式碼一律為1xxx，而訊息會指出略過記錄的原因。 例如，Sync Leads請求的「action」設為「createOnly」，但已提交記錄中的索引鍵之一已有潛在客戶。 此案例會傳回代碼1005，而如上所示，訊息為「潛在客戶已存在」。

#### 記錄層級錯誤代碼

>[!NOTE]
>
><table>
><tbody>
>    <tr>
>      <td>回應代碼</td>
>      <td>說明</td>
>      <td>註解</td>
>    </tr>
>    <tr>
>      <td><a name="1001"></a>1001</td>
>      <td>無效的值'%s'。 需要'%s'型別</td>
>      <td>每當引數值的型別不符時，就會產生錯誤。 例如，為integer引數指定的字串值。</td>
>    </tr>
>    <tr>
>      <td><a name="1002"></a>1002</td>
>      <td>遺失必要引數'%s'的值</td>
>      <td>請求中缺少必要引數時會產生錯誤</td>
>    </tr>
>    <tr>
>      <td><a name="1003"></a>1003</td>
>      <td>無效的資料</td>
>      <td>當提交的資料不是指定端點或模式的有效型別時；例如當使用指定為createOnly的動作提交潛在客戶的ID時，或當在批次促銷活動上使用請求促銷活動時。</td>
>    </tr>
>    <tr>
>      <td><a name="1004"></a>1004</td>
>      <td>找不到銷售機會</td>
>      <td>對於syncLead，當動作為"updateOnly"且找不到潛在客戶時</td>
>    </tr>
>    <tr>
>      <td><a name="1005"></a>1005</td>
>      <td>潛在客戶已存在</td>
>      <td>對於syncLead，當動作為"createOnly"且潛在客戶已存在時</td>
>    </tr>
>    <tr>
>      <td><a name="1006"></a>1006</td>
>      <td>找不到欄位'%s'</td>
>      <td>呼叫中包含的欄位不是有效欄位。</td>
>    </tr>
>    <tr>
>      <td><a name="1007"></a>1007</td>
>      <td>多個銷售機會符合查詢條件</td>
>      <td>有多個銷售機會符合查詢條件。 僅當索引鍵符合單一記錄時才能執行更新</td>
>    </tr>
>    <tr>
>      <td><a name="1008"></a>1008</td>
>      <td>拒絕存取資料分割'%s'</td>
>      <td>自訂服務的使用者無法存取含有記錄所在資料分割的工作區。</td>
>    </tr>
>    <tr>
>      <td><a name="1009"></a>1009</td>
>      <td>必須指定資料分割名稱</td>
>      <td></td>
>    </tr>
>    <tr>
>      <td><a name="1010"></a>1010</td>
>      <td>不允許資料分割更新</td>
>      <td>指定的記錄已經存在於不同的潛在客戶分割中。</td>
>    </tr>
>    <tr>
>      <td><a name="1011"></a>1011</td>
>      <td>欄位'%s'不受支援</td>
>      <td>當查閱欄位或以不支援的標準欄位指定的「filterType」時（例如：firstName、lastName）</td>
>    </tr>
>    <tr>
>      <td><a name="1012"></a>1012</td>
>      <td>無效的Cookie值'%s'</td>
>      <td>呼叫<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST">關聯銷售機會</a>時，可能會發生'cookie'引數的無效值。
>        當使用'filterType=cookies'並有'filterValues'引數的無效值依篩選型別<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET">呼叫</a>取得銷售機會時，也會發生這種情況。</td>
>    </tr>
>    <tr>
>      <td><a name="1013"></a>1013</td>
>      <td>找不到物件</td>
>      <td>依ID取得物件（清單、促銷活動）會傳回此錯誤代碼</td>
>    </tr>
>    <tr>
>      <td><a name="1014"></a>1014</td>
>      <td>無法建立物件</td>
>      <td>建立物件（清單）失敗</td>
>    </tr>
>    <tr>
>      <td><a name="1015"></a>1015</td>
>      <td>潛在客戶不在清單中</td>
>      <td>指定的潛在客戶不是目標清單的成員</td>
>    </tr>
>    <tr>
>      <td><a name="1016"></a>1016</td>
>      <td>匯入次數過多</td>
>      <td>有太多匯入已排入佇列。 最多允許10個</td>
>    </tr>
>    <tr>
>      <td><a name="1017"></a>1017</td>
>      <td>物件已存在</td>
>      <td>建立失敗，因為記錄已經存在</td>
>    </tr>
>    <tr>
>      <td><a name="1018"></a>1018</td>
>      <td>CRM已啟用</td>
>      <td>無法執行動作，因為執行個體已啟用原生CRM整合。</td>
>    </tr>
>    <tr>
>      <td><a name="1019"></a>1019</td>
>      <td>匯入進行中</td>
>      <td>目標清單已匯入</td>
>    </tr>
>    <tr>
>      <td><a name="1020"></a>1020</td>
>      <td>要程式化的翻制專案太多</td>
>      <td>訂閱已達當天排程程式中的「cloneToProgramName」使用配額</td>
>    </tr>
>    <tr>
>      <td><a name="1021"></a>1021</td>
>      <td>不允許公司更新</td>
>      <td>在syncLead期間不允許公司更新</td>
>    </tr>
>    <tr>
>      <td><a name="1022"></a>1022</td>
>      <td>使用中的物件</td>
>      <td>當物件正由另一個物件使用時，不允許刪除</td>
>    </tr>
>    <tr>
>      <td><a name="1025"></a>1025</td>
>      <td>找不到程式狀態</td>
>      <td>已為「變更銷售機會計畫狀態」指定狀態，但該狀態不符合計畫頻道可用的狀態。</td>
>    </tr>
>    <tr>
>      <td><a name="1026"></a>1026</td>
>      <td>未啟用自訂物件</td>
>      <td>無法執行動作，因為執行個體未啟用自訂物件整合。</td>
>    </tr>
>    <tr>
>      <td><a name="1027"></a>1027</td>
>      <td>已達到最大活動型別限制</td>
>      <td>訂閱已達到可用自訂活動型別的最大數量。</td>
>    </tr>
>    <tr>
>      <td><a name="1028"></a>1028</td>
>      <td>已達到最大欄位限制</td>
>      <td>自訂活動最多有20個次要屬性。</td>
>    </tr>
>    <tr>
>      <td><a name="1029"></a>1029</td>
>      <td><ul>
>          <li>佇列中有太多工作</li>
>          <li>超出匯出每日配額</li>
>          <li>工作已排入佇列</li>
>        </ul></td>
>      <td><ul>
>          <li>在任何指定時間，佇列中的訂閱最多允許10個大量擷取工作。</li>
>          <li>根據預設，擷取工作限製為每天500MB （CST每天凌晨12:00重設）。</li>
>          <li>匯出ID已排入佇列。</li>
>        </ul></td>
>    </tr>
>    <tr>
>      <td><a name="1035"></a>1035</td>
>      <td>不支援的篩選器型別</td>
>      <td>在某些訂閱中，不支援下列大量銷售機會擷取篩選器型別： updatedAt、smartListId、smartListName。</td>
>    </tr>
>    <tr>
>      <td><a name="1036"></a>1036</td>
>      <td>在輸入中發現重複的物件</td>
>      <td>已呼叫使用相同的外部索引鍵更新兩個或多個記錄。 例如，同步公司呼叫時，多個公司使用相同的externalCompanyId。</td>
>    </tr>
>    <tr>
>      <td><a name="1037"></a>1037</td>
>      <td>已略過銷售機會</td>
>      <td>已略過Lead，因為它已經處於或超過此狀態。</td>
>    </tr>
>    <tr>
>      <td><a name="1042"></a>1042</td>
>      <td>無效的runAt日期</td>
>      <td>為排程行銷活動指定的runAt日期太久未來（最多兩年）。</td>
>    </tr>
>    <tr>
>      <td><a name="1048"></a>1048</td>
>      <td>自訂物件捨棄草稿失敗</td>
>      <td>已進行呼叫以捨棄自訂物件的草稿版本。</td>
>    </tr>
>    <tr>
>      <td><a name="1049"></a>1049</td>
>      <td>建立活動失敗</td>
>      <td>屬性陣列太長。
>        傳遞至記錄的屬性陣列超過65536個位元組的最大長度</td>
>    </tr>
>    <tr>
>      <td><a name="1076"></a>1076</td>
>      <td>具有mergeInCRM旗標的<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合併銷售機會</a>呼叫為4。</td>
>      <td>您正在建立重複記錄。 建議您改用現有記錄。
>        這是Marketo在Salesforce中合併時收到的錯誤訊息。</td>
>    </tr>
>    <tr>
>      <td><a name="1077"></a>1077</td>
>      <td>由於「SFDC欄位」長度，<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合併銷售機會</a>呼叫失敗</td>
>      <td>mergeInCRM設為true的合併潛在客戶呼叫失敗，因為「SFDC欄位」超過允許的字元限制。 若要更正，請縮短「SFDC欄位」的長度，或將mergeInCRM設為false。</td>
>    </tr>
>    <tr>
>      <td><a name="1078"></a>1078</td>
>      <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合併銷售機會</a>呼叫失敗，因為實體已刪除，不是銷售機會/連絡人，或欄位篩選條件不符。</td>
>      <td>合併失敗，無法在原生同步的CRM中執行合併操作
>        這是Marketo在Salesforce中合併時收到的錯誤訊息。</td>
>    </tr>
>    <tr>
>      <td><a name="1079"></a>1079</td>
>      <td>由於重複記錄中的個人化URL衝突，<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合併銷售機會</a>呼叫失敗</td>
>      <td>合併銷售機會通話會指定多個具有相同個人化URL的銷售機會。 若要解決問題，請使用Marketo Engage使用者介面來合併這些記錄。</td>
>    </tr>
>  </tbody>
></table>
