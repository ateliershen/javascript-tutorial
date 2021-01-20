# 表單，FormData 物件

## 表單概述

表單（`<form>`）用來收集使用者提交的資料，傳送到伺服器。比如，使用者提交使用者名稱和密碼，讓伺服器驗證，就要透過表單。表單提供多種控制元件，讓開發者使用，具體的控制元件種類和用法請參考 HTML 語言的教程。本章主要介紹 JavaScript 與表單的互動。

```html
<form action="/handling-page" method="post">
  <div>
    <label for="name">使用者名稱：</label>
    <input type="text" id="name" name="user_name" />
  </div>
  <div>
    <label for="passwd">密碼：</label>
    <input type="password" id="passwd" name="user_passwd" />
  </div>
  <div>
    <input type="submit" id="submit" name="submit_button" value="提交" />
  </div>
</form>
```

上面程式碼就是一個簡單的表單，包含三個控制元件：使用者名稱輸入框、密碼輸入框和提交按鈕。

使用者點選“提交”按鈕，每一個控制元件都會生成一個鍵值對，鍵名是控制元件的`name`屬性，鍵值是控制元件的`value`屬性，鍵名和鍵值之間由等號連線。比如，使用者名稱輸入框的`name`屬性是`user_name`，`value`屬性是使用者輸入的值，假定是“張三”，提交到伺服器的時候，就會生成一個鍵值對`user_name=張三`。

所有的鍵值對都會提交到伺服器。但是，提交的資料格式跟`<form>`元素的`method`屬性有關。該屬性指定了提交資料的 HTTP 方法。如果是 GET 方法，所有鍵值對會以 URL 的查詢字串形式，提交到伺服器，比如`/handling-page?user_name=張三&user_passwd=123&submit_button=提交`。下面就是 GET 請求的 HTTP 頭資訊。

```http
GET /handling-page?user_name=張三&user_passwd=123&submit_button=提交
Host: example.com
```

如果是 POST 方法，所有鍵值對會連線成一行，作為 HTTP 請求的資料體傳送到伺服器，比如`user_name=張三&user_passwd=123&submit_button=提交`。下面就是 POST 請求的頭資訊。

```http
POST /handling-page HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 74

user_name=張三&user_passwd=123&submit_button=提交
```

注意，實際提交的時候，只要鍵值不是 URL 的合法字元（比如漢字“張三”和“提交”），瀏覽器會自動對其進行編碼。

點選`submit`控制元件，就可以提交表單。

```html
<form>
  <input type="submit" value="提交">
</form>
```

上面表單就包含一個`submit`控制元件，點選這個控制元件，瀏覽器就會把表單資料向伺服器提交。

注意，表單裡面的`<button>`元素如果沒有用`type`屬性指定型別，那麼預設就是`submit`控制元件。

```html
<form>
  <button>提交</button>
</form>
```

上面表單的`<button>`元素，點選以後也會提交表單。

除了點選`submit`控制元件提交表單，還可以用表單元素的`submit()`方法，透過指令碼提交表單。

```javascript
formElement.submit();
```

表單元素的`reset()`方法可以重置所有控制元件的值（重置為預設值）。

```javascript
formElement.reset()
```

## FormData 物件

### 概述

表單資料以鍵值對的形式向伺服器傳送，這個過程是瀏覽器自動完成的。但是有時候，我們希望透過指令碼完成這個過程，構造或編輯表單的鍵值對，然後透過指令碼傳送給伺服器。瀏覽器原生提供了 FormData 物件來完成這項工作。

`FormData()`首先是一個建構函式，用來生成表單的例項。

```javascript
var formdata = new FormData(form);
```

`FormData()`建構函式的引數是一個 DOM 的表單元素，建構函式會自動處理表單的鍵值對。這個引數是可選的，如果省略該引數，就表示一個空的表單。

下面是一個表單。

```html
<form id="myForm" name="myForm">
  <div>
    <label for="username">使用者名稱：</label>
    <input type="text" id="username" name="username">
  </div>
  <div>
    <label for="useracc">賬號：</label>
    <input type="text" id="useracc" name="useracc">
  </div>
  <div>
    <label for="userfile">上傳檔案：</label>
    <input type="file" id="userfile" name="userfile">
  </div>
<input type="submit" value="Submit!">
</form>
```

我們用`FormData()`處理上面這個表單。

```javascript
var myForm = document.getElementById('myForm');
var formData = new FormData(myForm);

// 獲取某個控制元件的值
formData.get('username') // ""

// 設定某個控制元件的值
formData.set('username', '張三');

formData.get('username') // "張三"
```

### 例項方法

FormData 提供以下例項方法。

- `FormData.get(key)`：獲取指定鍵名對應的鍵值，引數為鍵名。如果有多個同名的鍵值對，則返回第一個鍵值對的鍵值。
- `FormData.getAll(key)`：返回一個數組，表示指定鍵名對應的所有鍵值。如果有多個同名的鍵值對，陣列會包含所有的鍵值。
- `FormData.set(key, value)`：設定指定鍵名的鍵值，引數為鍵名。如果鍵名不存在，會新增這個鍵值對，否則會更新指定鍵名的鍵值。如果第二個引數是檔案，還可以使用第三個引數，表示檔名。
- `FormData.delete(key)`：刪除一個鍵值對，引數為鍵名。
- `FormData.append(key, value)`：新增一個鍵值對。如果鍵名重複，則會生成兩個相同鍵名的鍵值對。如果第二個引數是檔案，還可以使用第三個引數，表示檔名。
- `FormData.has(key)`：返回一個布林值，表示是否具有該鍵名的鍵值對。
- `FormData.keys()`：返回一個遍歷器物件，用於`for...of`迴圈遍歷所有的鍵名。
- `FormData.values()`：返回一個遍歷器物件，用於`for...of`迴圈遍歷所有的鍵值。
- `FormData.entries()`：返回一個遍歷器物件，用於`for...of`迴圈遍歷所有的鍵值對。如果直接用`for...of`迴圈遍歷 FormData 例項，預設就會呼叫這個方法。

下面是`get()`、`getAll()`、`set()`、`append()`方法的例子。

```javascript
var formData = new FormData();

formData.set('username', '張三');
formData.append('username', '李四');
formData.get('username') // "張三"
formData.getAll('username') // ["張三", "李四"]

formData.append('userpic[]', myFileInput.files[0], 'user1.jpg');
formData.append('userpic[]', myFileInput.files[1], 'user2.jpg');
```

下面是遍歷器的例子。

```javascript
var formData = new FormData();
formData.append('key1', 'value1');
formData.append('key2', 'value2');

for (var key of formData.keys()) {
  console.log(key);
}
// "key1"
// "key2"

for (var value of formData.values()) {
  console.log(value);
}
// "value1"
// "value2"

for (var pair of formData.entries()) {
  console.log(pair[0] + ': ' + pair[1]);
}
// key1: value1
// key2: value2

// 等同於遍歷 formData.entries()
for (var pair of formData) {
  console.log(pair[0] + ': ' + pair[1]);
}
// key1: value1
// key2: value2
```

## 表單的內建驗證

### 自動校驗

表單提交的時候，瀏覽器允許開發者指定一些條件，它會自動驗證各個表單控制元件的值是否符合條件。

```html
<!-- 必填 -->
<input required>

<!-- 必須符合正則表示式 -->
<input pattern="banana|cherry">

<!-- 字串長度必須為6個字元 -->
<input minlength="6" maxlength="6">

<!-- 數值必須在1到10之間 -->
<input type="number" min="1" max="10">

<!-- 必須填入 Email 地址 -->
<input type="email">

<!-- 必須填入 URL -->
<input type="URL">
```

如果一個控制元件透過驗證，它就會匹配`:valid`的 CSS 偽類，瀏覽器會繼續進行表單提交的流程。如果沒有透過驗證，該控制元件就會匹配`:invalid`的 CSS 偽類，瀏覽器會終止表單提交，並顯示一個錯誤資訊。

```css
input:invalid {
  border-color: red;
}
input,
input:valid {
  border-color: #ccc;
}
```

### checkValidity()

除了提交表單的時候，瀏覽器自動校驗表單，還可以手動觸發表單的校驗。表單元素和表單控制元件都有`checkValidity()`方法，用於手動觸發校驗。

```javascript
// 觸發整個表單的校驗
form.checkValidity()

// 觸發單個表單控制元件的校驗
formControl.checkValidity()
```

`checkValidity()`方法返回一個布林值，`true`表示透過校驗，`false`表示沒有透過校驗。因此，提交表單可以封裝為下面的函式。

```javascript
function submitForm(action) {
  var form = document.getElementById('form');
  form.action = action;
  if (form.checkValidity()) {
    form.submit();
  }
}
```

### willValidate 屬性

控制元件元素的`willValidate`屬性是一個布林值，表示該控制元件是否會在提交時進行校驗。

```javascript
// HTML 程式碼如下
// <form novalidate>
//   <input id="name" name="name" required />
// </form>

var input = document.querySelector('#name');
input.willValidate // true
```

### validationMessage 屬性

控制元件元素的`validationMessage`屬性返回一個字串，表示控制元件不滿足校驗條件時，瀏覽器顯示的提示文字。以下兩種情況，該屬性返回空字串。

- 該控制元件不會在提交時自動校驗
- 該控制元件滿足校驗條件

```javascript
// HTML 程式碼如下
// <form><input type="text" required></form>
document.querySelector('form input').validationMessage
// "請填寫此欄位。"
```

下面是另一個例子。

```javascript
var myInput = document.getElementById('myinput');
if (!myInput.checkValidity()) {
  document.getElementById('prompt').innerHTML = myInput.validationMessage;
}
```

### setCustomValidity()

控制元件元素的`setCustomValidity()`方法用來定製校驗失敗時的報錯資訊。它接受一個字串作為引數，該字串就是定製的報錯資訊。如果引數為空字串，則上次設定的報錯資訊被清除。

這個方法可以替換瀏覽器內建的表單驗證報錯資訊，引數就是要顯示的報錯資訊。

```html
<form action="somefile.php">
  <input
    type="text"
    name="username"
    placeholder="Username"
    pattern="[a-z]{1,15}"
    id="username"
  >
  <input type="submit">
</form>
```

上面的表單輸入框，要求只能輸入小寫字母，且不得超過15個字元。如果輸入不符合要求（比如輸入“ABC”），提交表單的時候，Chrome 瀏覽器會彈出報錯資訊“Please match the requested format.”，禁止表單提交。下面使用`setCustomValidity()`方法替換掉報錯資訊。

```javascript
var input = document.getElementById('username');
input.oninvalid = function (event) {
  event.target.setCustomValidity(
    '使用者名稱必須是小寫字母，不能為空，最長不超過15個字元'
  );
}
```

上面程式碼中，`setCustomValidity()`方法是在`invalid`事件的監聽函式裡面呼叫。該方法也可以直接呼叫，這時如果引數不為空字串，瀏覽器就會認為該控制元件沒有透過校驗，就會立刻顯示該方法設定的報錯資訊。

```javascript
/* HTML 程式碼如下
<form>
  <p><input type="file" id="fs"></p>
  <p><input type="submit"></p>
</form>
*/

document.getElementById('fs').onchange = checkFileSize;

function checkFileSize() {
  var fs = document.getElementById('fs');
  var files = fs.files;
  if (files.length > 0) {
     if (files[0].size > 75 * 1024) {
       fs.setCustomValidity('檔案不能大於 75KB');
       return;
     }
  }
  fs.setCustomValidity('');
}
```

上面程式碼一旦發現檔案大於 75KB，就會設定校驗失敗，同時給出自定義的報錯資訊。然後，點選提交按鈕時，就會顯示報錯資訊。這種校驗失敗是不會自動消除的，所以如果所有檔案都符合條件，要將報錯資訊設為空字串，手動消除校驗失敗的狀態。

### validity 屬性

控制元件元素的屬性`validity`屬性返回一個`ValidityState`物件，包含當前校驗狀態的資訊。

該物件有以下屬性，全部為只讀屬性。

- `ValidityState.badInput`：布林值，表示瀏覽器是否不能將使用者的輸入轉換成正確的型別，比如使用者在數值框裡面輸入字串。
- `ValidityState.customError`：布林值，表示是否已經呼叫`setCustomValidity()`方法，將校驗資訊設定為一個非空字串。
- `ValidityState.patternMismatch`：布林值，表示使用者輸入的值是否不滿足模式的要求。
- `ValidityState.rangeOverflow`：布林值，表示使用者輸入的值是否大於最大範圍。
- `ValidityState.rangeUnderflow`：布林值，表示使用者輸入的值是否小於最小範圍。
- `ValidityState.stepMismatch`：布林值，表示使用者輸入的值不符合步長的設定（即不能被步長值整除）。
- `ValidityState.tooLong`：布林值，表示使用者輸入的字數超出了最長字數。
- `ValidityState.tooShort`：布林值，表示使用者輸入的字元少於最短字數。
- `ValidityState.typeMismatch`：布林值，表示使用者填入的值不符合型別要求（主要是型別為 Email 或 URL 的情況）。
- `ValidityState.valid`：布林值，表示使用者是否滿足所有校驗條件。
- `ValidityState.valueMissing`：布林值，表示使用者沒有填入必填的值。

下面是一個例子。

```javascript
var input = document.getElementById('myinput');
if (input.validity.valid) {
  console.log('透過校驗');
} else {
  console.log('校驗失敗');
}
```

下面是另外一個例子。

```javascript
var txt = '';
if (document.getElementById('myInput').validity.rangeOverflow) {
  txt = '數值超過上限';
}
document.getElementById('prompt').innerHTML = txt;
```

如果想禁止瀏覽器彈出表單驗證的報錯資訊，可以監聽`invalid`事件。

```javascript
var input = document.getElementById('username');
var form  = document.getElementById('form');

var elem = document.createElement('div');
elem.id  = 'notify';
elem.style.display = 'none';
form.appendChild(elem);

input.addEventListener('invalid', function (event) {
  event.preventDefault();
  if (!event.target.validity.valid) {
    elem.textContent   = '使用者名稱必須是小寫字母';
    elem.className     = 'error';
    elem.style.display = 'block';
    input.className    = 'invalid animated shake';
  }
});

input.addEventListener('input', function(event){
  if ( 'block' === elem.style.display ) {
    input.className = '';
    elem.style.display = 'none';
  }
});
```

上面程式碼中，一旦發生`invalid`事件（表單驗證失敗），`event.preventDefault()`用來禁止瀏覽器彈出預設的驗證失敗提示，然後設定定製的報錯提示框。

### 表單的 novalidate 屬性

表單元素的 HTML 屬性`novalidate`，可以關閉瀏覽器的自動校驗。

```html
<form novalidate>
</form>
```

這個屬性也可以在腳本里設定。

```javascript
form.noValidate = true;
```

如果表單元素沒有設定`novalidate`屬性，那麼提交按鈕（`<button>`或`<input>`元素）的`formnovalidate`屬性也有同樣的作用。

```html
<form>
  <input type="submit" value="submit" formnovalidate>
</form>
```

## enctype 屬性

表單能夠用四種編碼，向伺服器傳送資料。編碼格式由表單的`enctype`屬性決定。

假定表單有兩個欄位，分別是`foo`和`baz`，其中`foo`欄位的值等於`bar`，`baz`欄位的值是一個分為兩行的字串。

```
The first line.
The second line.
```

下面四種格式，都可以將這個表單傳送到伺服器。

**（1）GET 方法**

如果表單使用`GET`方法傳送資料，`enctype`屬性無效。

```html
<form
  action="register.php"
  method="get"
  onsubmit="AJAXSubmit(this); return false;"
>
</form>
```

資料將以 URL 的查詢字串發出。

```http
?foo=bar&baz=The%20first%20line.%0AThe%20second%20line.
```

**（2）application/x-www-form-urlencoded**

如果表單用`POST`方法傳送資料，並省略`enctype`屬性，那麼資料以`application/x-www-form-urlencoded`格式傳送（因為這是預設值）。

```html
<form
  action="register.php"
  method="post"
  onsubmit="AJAXSubmit(this); return false;"
>
</form>
```

傳送的 HTTP 請求如下。

```http
Content-Type: application/x-www-form-urlencoded

foo=bar&baz=The+first+line.%0D%0AThe+second+line.%0D%0A
```

上面程式碼中，資料體裡面的`%0D%0A`代表換行符（`\r\n`）。

**（3）text/plain**

如果表單使用`POST`方法傳送資料，`enctype`屬性為`text/plain`，那麼資料將以純文字格式傳送。

```html
<form
  action="register.php"
  method="post"
  enctype="text/plain"
  onsubmit="AJAXSubmit(this); return false;"
>
</form>
```

傳送的 HTTP 請求如下。

```http
Content-Type: text/plain

foo=bar
baz=The first line.
The second line.
```

**（4）multipart/form-data**

如果表單使用`POST`方法，`enctype`屬性為`multipart/form-data`，那麼資料將以混合的格式傳送。

```html
<form
  action="register.php"
  method="post"
  enctype="multipart/form-data"
  onsubmit="AJAXSubmit(this); return false;"
>
</form>
```

傳送的 HTTP 請求如下。

```http
Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--
```

這種格式也是檔案上傳的格式。

## 檔案上傳

使用者上傳檔案，也是透過表單。具體來說，就是透過檔案輸入框選擇本地檔案，提交表單的時候，瀏覽器就會把這個檔案傳送到伺服器。

```html
<input type="file" id="file" name="myFile">
```

此外，還需要將表單`<form>`元素的`method`屬性設為`POST`，`enctype`屬性設為`multipart/form-data`。其中，`enctype`屬性決定了 HTTP 頭資訊的`Content-Type`欄位的值，預設情況下這個欄位的值是`application/x-www-form-urlencoded`，但是檔案上傳的時候要改成`multipart/form-data`。

```html
<form method="post" enctype="multipart/form-data">
  <div>
    <label for="file">選擇一個檔案</label>
    <input type="file" id="file" name="myFile" multiple>
  </div>
  <div>
    <input type="submit" id="submit" name="submit_button" value="上傳" />
  </div>
</form>
```

上面的 HTML 程式碼中，file 控制元件的`multiple`屬性，指定可以一次選擇多個檔案；如果沒有這個屬性，則一次只能選擇一個檔案。

```javascript
var fileSelect = document.getElementById('file');
var files = fileSelect.files;
```

然後，新建一個 FormData 例項物件，模擬傳送到伺服器的表單資料，把選中的檔案新增到這個物件上面。

```javascript
var formData = new FormData();

for (var i = 0; i < files.length; i++) {
  var file = files[i];

  // 只上傳圖片檔案
  if (!file.type.match('image.*')) {
    continue;
  }

  formData.append('photos[]', file, file.name);
}
```

最後，使用 Ajax 向伺服器上傳檔案。

```javascript
var xhr = new XMLHttpRequest();

xhr.open('POST', 'handler.php', true);

xhr.onload = function () {
  if (xhr.status !== 200) {
    console.log('An error occurred!');
  }
};

xhr.send(formData);
```

除了傳送 FormData 例項，也可以直接 AJAX 傳送檔案。

```javascript
var file = document.getElementById('test-input').files[0];
var xhr = new XMLHttpRequest();

xhr.open('POST', 'myserver/uploads');
xhr.setRequestHeader('Content-Type', file.type);
xhr.send(file);
```

## 參考連結

- [HTML5 Form Validation With the “pattern” Attribute](https://webdesign.tutsplus.com/tutorials/html5-form-validation-with-the-pattern-attribute--cms-25145), Thoriq Firdaus
