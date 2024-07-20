---
title: Forms API參考
description: Forms API參考
feature: Forms, Javascript
exl-id: 0f8d242f-0b27-4087-b080-3d41ebaa25b3
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1327'
ht-degree: 1%

---

# Forms API參考

使用Forms 2.0 API時，您會與兩個主要物件互動。 `MktoForms2`物件與`Form`物件。 `MktoForms2`物件是Forms2功能的最上層公開可見名稱空間，包含建立、載入及擷取Form物件的函式。

## MktoForms2方法

<table>
  <tbody>
    <tr valign="top">
      <td><strong>方法</strong></td>
      <td><strong>說明</strong></td>
      <td><strong>引數</strong></td>
      <td><strong>傳回值</strong></td>
    </tr>
    <tr valign="top">
      <td>.loadForm(baseUrl， munchkinId， formId， callback)</td>
      <td>從Marketo伺服器載入表單描述項，並建立新的表單物件。</td>
      <td> baseUrl(String) — 您訂閱的Marketo伺服器執行個體的URL</td>
      <td>未定義</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>munchkinId （字串） — 訂閱的Munchkin ID</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>formId （字串或數字） — 要載入的表單表單版本ID (Vid)</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>回呼（選用） （函式） — 回呼函式，在已建構的Form物件載入及初始化後，將其傳遞至。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.lightbox（表單，選項）</td>
      <td>呈現含有Form物件的燈箱樣式模型對話方塊。</td>
      <td>form (Form Object) — 您要在Lightbox中演算的Form物件例項。</td>
      <td>含有.show()和.hide()方法的燈箱物件。</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>opts （選用）（物件） — 傳遞至Lightbox物件的選項物件</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>onSuccess(Function) — 在提交表單時觸發的回呼。</td>
      <td></td>
    </tr>
    <tr>
          <td></td>
      <td></td>
      <td>closeBtn(Boolean)預設true — 控制燈箱對話方塊上是否顯示關閉按鈕(X)。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.newForm(formData， callback)</td>
      <td>從表單描述項JS物件建立新的表單物件。 新增回呼函式，在擷取所有樣式表和已知潛在客戶資訊並建立表單物件後，呼叫此函式。</td>
      <td>formData （表單描述元物件） — 表單描述元物件，由Marketo Forms V2編輯器建立</td>
      <td>未定義</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>callback （選用）（函式） — 此callback是使用單一引數來呼叫，此引數是Form物件新建立的例項。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.getForm(formId)</td>
      <td>透過表單識別碼取得先前建立的表單物件</td>
      <td> formId （數字或字串） — 表單Vid識別碼。</td>
      <td>表單物件</td>
    </tr>
    <tr valign="top">
      <td>.allForms()</td>
      <td>擷取先前已在頁面上建構的所有表單物件的陣列。</td>
      <td>不適用</td>
      <td>表單物件的陣列</td>
    </tr>
    <tr valign="top">
      <td>.getPageFields()</td>
      <td>從URL和反向連結取得包含資料的JS物件，這些資料可能會對追蹤目的有所幫助。</td>
      <td>不適用</td>
      <td>物件</td>
    </tr>
    <tr valign="top">
      <td>.whenReady（回撥）</td>
      <td>針對頁面上「準備就緒」的每個表單新增一次呼叫的回呼。 整備是指表單存在、最初已轉譯且已呼叫其初始回呼。 如果呼叫此函式時已經有就緒的表單，則會立即呼叫傳遞的回呼。</td>
      <td>callback(Function) — 會傳遞單一引數（表單物件）作為回呼。</td>
      <td>MktoForms2物件</td>
    </tr>
    <tr valign="top">
      <td>.onFormRender(callback)</td>
      <td>新增每次頁面上任何表單轉譯時都會呼叫的回呼。 Forms會在最初建立時轉譯，然後每次可見性規則變更表單結構時都進行轉譯。</td>
      <td>callback （函式） — 會傳遞單一引數（已轉譯之表單的表單物件）。</td>
      <td>MktoForms2物件</td>
    </tr>
    <tr valign="top">
      <td>.whenRendered(callback)</td>
      <td>就像onFormRender一樣，這會新增每次呈現表單時呼叫的回呼。 此外，這也會立即呼叫已轉譯之所有表單的回呼。</td>
      <td>callback(Function) — 會傳遞單一引數（已演算表單的表單物件）。</td>
      <td></td>
    </tr>
</table>


## 表單方法

<table>
  <tbody>
    <tr valign="top">
      <td><strong>方法</strong></td>
      <td><strong>說明</strong></td>
      <td><strong>引數</strong></td>
      <td><strong>傳回值</strong></td>
    </tr>
    <tr valign="top">
      <td>.render(formElem)</td>
      <td>轉譯表單物件，傳回jQuery物件以包裝包含表單的表單元素。 如果傳遞formElem，則會以該形式作為表單元素，否則會建立新表單元素。</td>
      <td>formElem （選用） — 要呈現的jQuery物件包裝表單元素。</td>
      <td> 包含已轉譯表單的jQuery物件包裝表單元素。</td>
    </tr>
    <tr valign="top">
      <td>.getId()</td>
      <td>取得表單的識別碼。</td>
      <td>不適用</td>
      <td>數字 — 此表單所代表的表單物件識別碼</td>
    </tr>
    <tr valign="top">
      <td>.getFormElem()</td>
      <td>取得已轉譯表單的jQuery包裝表單元素。</td>
      <td>不適用</td>
      <td>jQuery物件包裝的表單元素；如果表單尚未以render()方法轉譯，則為null。</td>
    </tr>
    <tr valign="top">
      <td>.validate()</td>
      <td>強制表單驗證，醒目提示可能存在的任何錯誤並傳回結果。 不提交表單。</td>
      <td>不適用</td>
      <td>布林值 — 如果傳遞的表單上的所有驗證器，則傳回true，否則傳回false。</td>
    </tr>
    <tr valign="top">
      <td>.onValidate(callback)</td>
      <td>新增會在觸發驗證時呼叫的驗證回呼。</td>
      <td>callback(Function) — 在任何驗證發生時都會觸發的回呼。 系統會傳遞一個引數，此布林值會指出驗證是否成功。</td>
      <td>表單物件 — 與呼叫方法的表單物件相同，以做為鏈結之用。</td>
    </tr>
    <tr valign="top">
      <td>.submit()</td>
      <td>觸發表單的提交事件。 這會從提交流程開始，執行驗證、引發任何onSubmit事件、提交表單，以及在表單提交成功時引發任何onSuccess事件。</td>
      <td>不適用</td>
      <td>表單物件 — 與呼叫方法的表單物件相同，以做為鏈結之用。</td>
    </tr>
    <tr valign="top">
      <td>.onSubmit(callback)</td>
      <td>新增在提交表單時呼叫的回呼。 提交開始時，在知道請求是否成功之前，就會觸發此動作。</td>
      <td>callback — 提交表單時將呼叫的函式。 此回呼將傳遞一個引數，即此Form物件。</td>
      <td>表單物件 — 與呼叫方法的表單物件相同，以做為鏈結之用。</td>
    </tr>
    <tr valign="top">
      <td>.onSuccess(callback)</td>
      <td>新增回呼，此回呼會在表單成功提交後、潛在客戶轉送至後續追蹤頁面之前呼叫。 可用於防止在成功提交後將潛在客戶轉送到後續追蹤頁面。</td>
      <td>callback — 成功提交表單時將呼叫的函式。 此回呼將傳遞兩個引數。 JS物件，其中包含已提交的值和使用者將轉送到的後續頁面的字串URL，如果沒有已設定的後續頁面，則為空字串或空字串。 特殊行為：如果此回呼傳回「false」(使用===測量)，則訪客「不會」轉送到後續頁面，「不會」重新載入頁面。 這可讓實作者對後續追蹤URL執行額外處理，或使用JavaScript在頁面上採取行動而非離開頁面。</td>
      <td>表單物件 — 與呼叫方法的表單物件相同，以做為鏈結之用。</td>
    </tr>
    <tr valign="top">
      <td>.submittable(canSubmit) <em>也可用為：</em> <em>.submitable(canSubmit)</em></td>
      <td>取得或設定是否可提交表單。 如果呼叫時沒有引數，它會取得值；如果呼叫時有一個引數，則會設定值。這可用來防止表單提交，同時必須符合一般表單之外的其他條件。</td>
      <td>canSubmit （選用）（布林值） — 將表單設為可提交或不可提交。</td>
      <td>布林值或表單物件 — 如果呼叫時沒有引數，會傳回布林值，指出表單是否可提交。 若以一個引數呼叫，會傳回此表單物件以供鏈結之用。 </td>
    </tr>
    <tr valign="top">
      <td>.allFieldsFilled()</td>
      <td>如果表單中的所有欄位都設定了非空白值，則傳回true。</td>
      <td>不適用</td>
      <td>布林值 — 如果所有欄位都有非空白/空白/未設定/null值，則為True，否則為false。</td>
    </tr>
    <tr valign="top">
      <td>.setValues(vals)</td>
      <td>在表單中的一或多個欄位上設定值。</td>
      <td>vals - JS物件。 對於物件中的每個索引鍵/值組，名為key的表單欄位將設定為value。</td>
      <td>未定義</td>
    </tr>
    <tr valign="top">
      <td>.getValues()</td>
      <td>取得表單中所有欄位的所有值。</td>
      <td>不適用</td>
      <td>物件 — JS物件包含索引鍵/值配對，代表表單中欄位的名稱和值。</td>
    </tr>
    <tr valign="top">
      <td>.addHiddenFields(values)</td>
      <td>在表單中新增輸入type=hidden欄位。</td>
      <td>values - JS物件，包含索引鍵/值配對，代表要新增至表單的隱藏欄位名稱和值。</td>
      <td>未定義</td>
    </tr>
    <tr valign="top">
      <td>.vals(values)</td>
      <td>jQuery樣式.vals() setter/getter。 如果呼叫時沒有引數，則等同於呼叫getValues()。 若以一個引數呼叫，則等於呼叫setValues()</td>
      <td>值（選用） — 物件</td>
      <td>未定義</td>
    </tr>
    <tr valign="top">
      <td>.showErrorMessage(msg， elem)</td>
      <td>顯示錯誤訊息，指向專案。</td>
      <td>msg (HTML字串) — 包含您要顯示之錯誤文字的字串。</td>
            <td>表單物件 — 此表單物件，用於鏈結。</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>elem （選用）（jQuery物件） — 要指向的錯誤元素。 如果未設定，則會使用表單的提交按鈕。</td>
<td></td>
    </tr>
  </tbody>
</table>
