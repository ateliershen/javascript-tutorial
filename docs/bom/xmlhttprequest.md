# XMLHttpRequest 物件

## 簡介

瀏覽器與伺服器之間，採用 HTTP 協議通訊。使用者在瀏覽器位址列鍵入一個網址，或者透過網頁表單向伺服器提交內容，這時瀏覽器就會向伺服器發出 HTTP 請求。

1999年，微軟公司釋出 IE 瀏覽器5.0版，第一次引入新功能：允許 JavaScript 指令碼向伺服器發起 HTTP 請求。這個功能當時並沒有引起注意，直到2004年 Gmail 釋出和2005年 Google Map 釋出，才引起廣泛重視。2005年2月，AJAX 這個詞第一次正式提出，它是 Asynchronous JavaScript and XML 的縮寫，指的是透過 JavaScript 的非同步通訊，從伺服器獲取 XML 文件從中提取資料，再更新當前網頁的對應部分，而不用重新整理整個網頁。後來，AJAX 這個詞就成為 JavaScript 指令碼發起 HTTP 通訊的代名詞，也就是說，只要用指令碼發起通訊，就可以叫做 AJAX 通訊。W3C 也在2006年釋出了它的國際標準。

具體來說，AJAX 包括以下幾個步驟。

1. 建立 XMLHttpRequest 例項
1. 發出 HTTP 請求
1. 接收伺服器傳回的資料
1. 更新網頁資料

概括起來，就是一句話，AJAX 透過原生的`XMLHttpRequest`物件發出 HTTP 請求，得到伺服器返回的資料後，再進行處理。現在，伺服器返回的都是 JSON 格式的資料，XML 格式已經過時了，但是 AJAX 這個名字已經成了一個通用名詞，字面含義已經消失了。

`XMLHttpRequest`物件是 AJAX 的主要介面，用於瀏覽器與伺服器之間的通訊。儘管名字裡面有`XML`和`Http`，它實際上可以使用多種協議（比如`file`或`ftp`），傳送任何格式的資料（包括字串和二進位制）。

`XMLHttpRequest`本身是一個建構函式，可以使用`new`命令生成例項。它沒有任何引數。

```javascript
var xhr = new XMLHttpRequest();
```

一旦新建例項，就可以使用`open()`方法指定建立 HTTP 連線的一些細節。

```javascript
xhr.open('GET', 'http://www.example.com/page.php', true);
```

上面程式碼指定使用 GET 方法，跟指定的伺服器網址建立連線。第三個引數`true`，表示請求是非同步的。

然後，指定回撥函式，監聽通訊狀態（`readyState`屬性）的變化。

```javascript
xhr.onreadystatechange = handleStateChange;

function handleStateChange() {
  // ...
}
```

上面程式碼中，一旦`XMLHttpRequest`例項的狀態發生變化，就會呼叫監聽函式`handleStateChange`

最後使用`send()`方法，實際發出請求。

```javascript
xhr.send(null);
```

上面程式碼中，`send()`的引數為`null`，表示傳送請求的時候，不帶有資料體。如果傳送的是 POST 請求，這裡就需要指定資料體。

一旦拿到伺服器返回的資料，AJAX 不會重新整理整個網頁，而是隻更新網頁裡面的相關部分，從而不打斷使用者正在做的事情。

注意，AJAX 只能向同源網址（協議、域名、埠都相同）發出 HTTP 請求，如果發出跨域請求，就會報錯（詳見《同源政策》和《CORS 通訊》兩章）。

下面是`XMLHttpRequest`物件簡單用法的完整例子。

```javascript
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = function(){
  // 通訊成功時，狀態值為4
  if (xhr.readyState === 4){
    if (xhr.status === 200){
      console.log(xhr.responseText);
    } else {
      console.error(xhr.statusText);
    }
  }
};

xhr.onerror = function (e) {
  console.error(xhr.statusText);
};

xhr.open('GET', '/endpoint', true);
xhr.send(null);
```

## XMLHttpRequest 的例項屬性

### XMLHttpRequest.readyState

`XMLHttpRequest.readyState`返回一個整數，表示例項物件的當前狀態。該屬性只讀。它可能返回以下值。

- 0，表示 XMLHttpRequest 例項已經生成，但是例項的`open()`方法還沒有被呼叫。
- 1，表示`open()`方法已經呼叫，但是例項的`send()`方法還沒有呼叫，仍然可以使用例項的`setRequestHeader()`方法，設定 HTTP 請求的頭資訊。
- 2，表示例項的`send()`方法已經呼叫，並且伺服器返回的頭資訊和狀態碼已經收到。
- 3，表示正在接收伺服器傳來的資料體（body 部分）。這時，如果例項的`responseType`屬性等於`text`或者空字串，`responseText`屬性就會包含已經收到的部分資訊。
- 4，表示伺服器返回的資料已經完全接收，或者本次接收已經失敗。

通訊過程中，每當例項物件發生狀態變化，它的`readyState`屬性的值就會改變。這個值每一次變化，都會觸發`readyStateChange`事件。

```javascript
var xhr = new XMLHttpRequest();

if (xhr.readyState === 4) {
  // 請求結束，處理伺服器返回的資料
} else {
  // 顯示提示“載入中……”
}
```

上面程式碼中，`xhr.readyState`等於`4`時，表明指令碼發出的 HTTP 請求已經完成。其他情況，都表示 HTTP 請求還在進行中。

### XMLHttpRequest.onreadystatechange

`XMLHttpRequest.onreadystatechange`屬性指向一個監聽函式。`readystatechange`事件發生時（例項的`readyState`屬性變化），就會執行這個屬性。

另外，如果使用例項的`abort()`方法，終止 XMLHttpRequest 請求，也會造成`readyState`屬性變化，導致呼叫`XMLHttpRequest.onreadystatechange`屬性。

下面是一個例子。

```javascript
var xhr = new XMLHttpRequest();
xhr.open( 'GET', 'http://example.com' , true );
xhr.onreadystatechange = function () {
  if (xhr.readyState !== 4 || xhr.status !== 200) {
    return;
  }
  console.log(xhr.responseText);
};
xhr.send();
```

### XMLHttpRequest.response

`XMLHttpRequest.response`屬性表示伺服器返回的資料體（即 HTTP 迴應的 body 部分）。它可能是任何資料型別，比如字串、物件、二進位制物件等等，具體的型別由`XMLHttpRequest.responseType`屬性決定。該屬性只讀。

如果本次請求沒有成功或者資料不完整，該屬性等於`null`。但是，如果`responseType`屬性等於`text`或空字串，在請求沒有結束之前（`readyState`等於3的階段），`response`屬性包含伺服器已經返回的部分資料。

```javascript
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    handler(xhr.response);
  }
}
```

### XMLHttpRequest.responseType

`XMLHttpRequest.responseType`屬性是一個字串，表示伺服器返回資料的型別。這個屬性是可寫的，可以在呼叫`open()`方法之後、呼叫`send()`方法之前，設定這個屬性的值，告訴瀏覽器如何解讀返回的資料。如果`responseType`設為空字串，就等同於預設值`text`。

`XMLHttpRequest.responseType`屬性可以等於以下值。

- ""（空字串）：等同於`text`，表示伺服器返回文字資料。
- "arraybuffer"：ArrayBuffer 物件，表示伺服器返回二進位制陣列。
- "blob"：Blob 物件，表示伺服器返回二進位制物件。
- "document"：Document 物件，表示伺服器返回一個文件物件。
- "json"：JSON 物件。
- "text"：字串。

上面幾種型別之中，`text`型別適合大多數情況，而且直接處理文字也比較方便。`document`型別適合返回 HTML / XML 文件的情況，這意味著，對於那些開啟 CORS 的網站，可以直接用 Ajax 抓取網頁，然後不用解析 HTML 字串，直接對抓取回來的資料進行 DOM 操作。`blob`型別適合讀取二進位制資料，比如圖片檔案。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);
xhr.responseType = 'blob';

xhr.onload = function(e) {
  if (this.status === 200) {
    var blob = new Blob([xhr.response], {type: 'image/png'});
    // 或者
    var blob = xhr.response;
  }
};

xhr.send();
```

如果將這個屬性設為`ArrayBuffer`，就可以按照陣列的方式處理二進位制資料。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);
xhr.responseType = 'arraybuffer';

xhr.onload = function(e) {
  var uInt8Array = new Uint8Array(this.response);
  for (var i = 0, len = uInt8Array.length; i < len; ++i) {
    // var byte = uInt8Array[i];
  }
};

xhr.send();
```

如果將這個屬性設為`json`，瀏覽器就會自動對返回資料呼叫`JSON.parse()`方法。也就是說，從`xhr.response`屬性（注意，不是`xhr.responseText`屬性）得到的不是文字，而是一個 JSON 物件。

### XMLHttpRequest.responseText

`XMLHttpRequest.responseText`屬性返回從伺服器接收到的字串，該屬性為只讀。只有 HTTP 請求完成接收以後，該屬性才會包含完整的資料。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/server', true);

xhr.responseType = 'text';
xhr.onload = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(xhr.responseText);
  }
};

xhr.send(null);
```

### XMLHttpRequest.responseXML

`XMLHttpRequest.responseXML`屬性返回從伺服器接收到的 HTML 或 XML 文件物件，該屬性為只讀。如果本次請求沒有成功，或者收到的資料不能被解析為 XML 或 HTML，該屬性等於`null`。

該屬性生效的前提是 HTTP 迴應的`Content-Type`頭資訊等於`text/xml`或`application/xml`。這要求在傳送請求前，`XMLHttpRequest.responseType`屬性要設為`document`。如果 HTTP 迴應的`Content-Type`頭資訊不等於`text/xml`和`application/xml`，但是想從`responseXML`拿到資料（即把資料按照 DOM 格式解析），那麼需要手動呼叫`XMLHttpRequest.overrideMimeType()`方法，強制進行 XML 解析。

該屬性得到的資料，是直接解析後的文件 DOM 樹。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/server', true);

xhr.responseType = 'document';
xhr.overrideMimeType('text/xml');

xhr.onload = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(xhr.responseXML);
  }
};

xhr.send(null);
```

### XMLHttpRequest.responseURL

`XMLHttpRequest.responseURL`屬性是字串，表示傳送資料的伺服器的網址。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://example.com/test', true);
xhr.onload = function () {
  // 返回 http://example.com/test
  console.log(xhr.responseURL);
};
xhr.send(null);
```

注意，這個屬性的值與`open()`方法指定的請求網址不一定相同。如果伺服器端發生跳轉，這個屬性返回最後實際返回資料的網址。另外，如果原始 URL 包括錨點（fragment），該屬性會把錨點剝離。

### XMLHttpRequest.status，XMLHttpRequest.statusText

`XMLHttpRequest.status`屬性返回一個整數，表示伺服器迴應的 HTTP 狀態碼。一般來說，如果通訊成功的話，這個狀態碼是200；如果伺服器沒有返回狀態碼，那麼這個屬性預設是200。請求發出之前，該屬性為`0`。該屬性只讀。

- 200, OK，訪問正常
- 301, Moved Permanently，永久移動
- 302, Moved temporarily，暫時移動
- 304, Not Modified，未修改
- 307, Temporary Redirect，暫時重定向
- 401, Unauthorized，未授權
- 403, Forbidden，禁止訪問
- 404, Not Found，未發現指定網址
- 500, Internal Server Error，伺服器發生錯誤

基本上，只有2xx和304的狀態碼，表示伺服器返回是正常狀態。

```javascript
if (xhr.readyState === 4) {
  if ( (xhr.status >= 200 && xhr.status < 300)
    || (xhr.status === 304) ) {
    // 處理伺服器的返回資料
  } else {
    // 出錯
  }
}
```

`XMLHttpRequest.statusText`屬性返回一個字串，表示伺服器傳送的狀態提示。不同於`status`屬性，該屬性包含整個狀態資訊，比如“OK”和“Not Found”。在請求傳送之前（即呼叫`open()`方法之前），該屬性的值是空字串；如果伺服器沒有返回狀態提示，該屬性的值預設為“OK”。該屬性為只讀屬性。

### XMLHttpRequest.timeout，XMLHttpRequestEventTarget.ontimeout

`XMLHttpRequest.timeout`屬性返回一個整數，表示多少毫秒後，如果請求仍然沒有得到結果，就會自動終止。如果該屬性等於0，就表示沒有時間限制。

`XMLHttpRequestEventTarget.ontimeout`屬性用於設定一個監聽函式，如果發生 timeout 事件，就會執行這個監聽函式。

下面是一個例子。

```javascript
var xhr = new XMLHttpRequest();
var url = '/server';

xhr.ontimeout = function () {
  console.error('The request for ' + url + ' timed out.');
};

xhr.onload = function() {
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      // 處理伺服器返回的資料
    } else {
      console.error(xhr.statusText);
    }
  }
};

xhr.open('GET', url, true);
// 指定 10 秒鐘超時
xhr.timeout = 10 * 1000;
xhr.send(null);
```

### 事件監聽屬性

XMLHttpRequest 物件可以對以下事件指定監聽函式。

- XMLHttpRequest.onloadstart：loadstart 事件（HTTP 請求發出）的監聽函式
- XMLHttpRequest.onprogress：progress事件（正在傳送和載入資料）的監聽函式
- XMLHttpRequest.onabort：abort 事件（請求中止，比如使用者呼叫了`abort()`方法）的監聽函式
- XMLHttpRequest.onerror：error 事件（請求失敗）的監聽函式
- XMLHttpRequest.onload：load 事件（請求成功完成）的監聽函式
- XMLHttpRequest.ontimeout：timeout 事件（使用者指定的時限超過了，請求還未完成）的監聽函式
- XMLHttpRequest.onloadend：loadend 事件（請求完成，不管成功或失敗）的監聽函式

下面是一個例子。

```javascript
xhr.onload = function() {
 var responseText = xhr.responseText;
 console.log(responseText);
 // process the response.
};

xhr.onabort = function () {
  console.log('The request was aborted');
};

xhr.onprogress = function (event) {
  console.log(event.loaded);
  console.log(event.total);
};

xhr.onerror = function() {
  console.log('There was an error!');
};
```

`progress`事件的監聽函式有一個事件物件引數，該物件有三個屬性：`loaded`屬性返回已經傳輸的資料量，`total`屬性返回總的資料量，`lengthComputable`屬性返回一個布林值，表示載入的進度是否可以計算。所有這些監聽函式裡面，只有`progress`事件的監聽函式有引數，其他函式都沒有引數。

注意，如果發生網路錯誤（比如伺服器無法連通），`onerror`事件無法獲取報錯資訊。也就是說，可能沒有錯誤物件，所以這樣只能顯示報錯的提示。

### XMLHttpRequest.withCredentials

`XMLHttpRequest.withCredentials`屬性是一個布林值，表示跨域請求時，使用者資訊（比如 Cookie 和認證的 HTTP 頭資訊）是否會包含在請求之中，預設為`false`，即向`example.com`發出跨域請求時，不會發送`example.com`設定在本機上的 Cookie（如果有的話）。

如果需要跨域 AJAX 請求傳送 Cookie，需要`withCredentials`屬性設為`true`。注意，同源的請求不需要設定這個屬性。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://example.com/', true);
xhr.withCredentials = true;
xhr.send(null);
```

為了讓這個屬性生效，伺服器必須顯式返回`Access-Control-Allow-Credentials`這個頭資訊。

```javascript
Access-Control-Allow-Credentials: true
```

`withCredentials`屬性開啟的話，跨域請求不僅會發送 Cookie，還會設定遠端主機指定的 Cookie。反之也成立，如果`withCredentials`屬性沒有開啟，那麼跨域的 AJAX 請求即使明確要求瀏覽器設定 Cookie，瀏覽器也會忽略。

注意，指令碼總是遵守同源政策，無法從`document.cookie`或者 HTTP 迴應的頭資訊之中，讀取跨域的 Cookie，`withCredentials`屬性不影響這一點。

### XMLHttpRequest.upload

XMLHttpRequest 不僅可以傳送請求，還可以傳送檔案，這就是 AJAX 檔案上傳。傳送檔案以後，透過`XMLHttpRequest.upload`屬性可以得到一個物件，透過觀察這個物件，可以得知上傳的進展。主要方法就是監聽這個物件的各種事件：loadstart、loadend、load、abort、error、progress、timeout。

假定網頁上有一個`<progress>`元素。

```http
<progress min="0" max="100" value="0">0% complete</progress>
```

檔案上傳時，對`upload`屬性指定`progress`事件的監聽函式，即可獲得上傳的進度。

```javascript
function upload(blobOrFile) {
  var xhr = new XMLHttpRequest();
  xhr.open('POST', '/server', true);
  xhr.onload = function (e) {};

  var progressBar = document.querySelector('progress');
  xhr.upload.onprogress = function (e) {
    if (e.lengthComputable) {
      progressBar.value = (e.loaded / e.total) * 100;
      // 相容不支援 <progress> 元素的老式瀏覽器
      progressBar.textContent = progressBar.value;
    }
  };

  xhr.send(blobOrFile);
}

upload(new Blob(['hello world'], {type: 'text/plain'}));
```

## XMLHttpRequest 的例項方法

### XMLHttpRequest.open()

`XMLHttpRequest.open()`方法用於指定 HTTP 請求的引數，或者說初始化 XMLHttpRequest 例項物件。它一共可以接受五個引數。

```javascript
void open(
   string method,
   string url,
   optional boolean async,
   optional string user,
   optional string password
);
```

- `method`：表示 HTTP 動詞方法，比如`GET`、`POST`、`PUT`、`DELETE`、`HEAD`等。
- `url`: 表示請求傳送目標 URL。
- `async`: 布林值，表示請求是否為非同步，預設為`true`。如果設為`false`，則`send()`方法只有等到收到伺服器返回了結果，才會進行下一步操作。該引數可選。由於同步 AJAX 請求會造成瀏覽器失去響應，許多瀏覽器已經禁止在主執行緒使用，只允許 Worker 裡面使用。所以，這個引數輕易不應該設為`false`。
- `user`：表示用於認證的使用者名稱，預設為空字串。該引數可選。
- `password`：表示用於認證的密碼，預設為空字串。該引數可選。

注意，如果對使用過`open()`方法的 AJAX 請求，再次使用這個方法，等同於呼叫`abort()`，即終止請求。

下面傳送 POST 請求的例子。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('POST', encodeURI('someURL'));
```

### XMLHttpRequest.send()

`XMLHttpRequest.send()`方法用於實際發出 HTTP 請求。它的引數是可選的，如果不帶引數，就表示 HTTP 請求只有一個 URL，沒有資料體，典型例子就是 GET 請求；如果帶有引數，就表示除了頭資訊，還帶有包含具體資料的資訊體，典型例子就是 POST 請求。

下面是 GET 請求的例子。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET',
  'http://www.example.com/?id=' + encodeURIComponent(id),
  true
);
xhr.send(null);
```

上面程式碼中，`GET`請求的引數，作為查詢字串附加在 URL 後面。

下面是傳送 POST 請求的例子。

```javascript
var xhr = new XMLHttpRequest();
var data = 'email='
  + encodeURIComponent(email)
  + '&password='
  + encodeURIComponent(password);

xhr.open('POST', 'http://www.example.com', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send(data);
```

注意，所有 XMLHttpRequest 的監聽事件，都必須在`send()`方法呼叫之前設定。

`send`方法的引數就是傳送的資料。多種格式的資料，都可以作為它的引數。

```javascript
void send();
void send(ArrayBufferView data);
void send(Blob data);
void send(Document data);
void send(String data);
void send(FormData data);
```

如果`send()`傳送 DOM 物件，在傳送之前，資料會先被序列化。如果傳送二進位制資料，最好是傳送`ArrayBufferView`或`Blob`物件，這使得透過 Ajax 上傳檔案成為可能。

下面是傳送表單資料的例子。`FormData`物件可以用於構造表單資料。

```javascript
var formData = new FormData();

formData.append('username', '張三');
formData.append('email', 'zhangsan@example.com');
formData.append('birthDate', 1940);

var xhr = new XMLHttpRequest();
xhr.open('POST', '/register');
xhr.send(formData);
```

上面程式碼中，`FormData`物件構造了表單資料，然後使用`send()`方法傳送。它的效果與傳送下面的表單資料是一樣的。

```html
<form id='registration' name='registration' action='/register'>
  <input type='text' name='username' value='張三'>
  <input type='email' name='email' value='zhangsan@example.com'>
  <input type='number' name='birthDate' value='1940'>
  <input type='submit' onclick='return sendForm(this.form);'>
</form>
```

下面的例子是使用`FormData`物件加工表單資料，然後再發送。

```javascript
function sendForm(form) {
  var formData = new FormData(form);
  formData.append('csrf', 'e69a18d7db1286040586e6da1950128c');

  var xhr = new XMLHttpRequest();
  xhr.open('POST', form.action, true);
  xhr.onload = function() {
    // ...
  };
  xhr.send(formData);

  return false;
}

var form = document.querySelector('#registration');
sendForm(form);
```

### XMLHttpRequest.setRequestHeader()

`XMLHttpRequest.setRequestHeader()`方法用於設定瀏覽器傳送的 HTTP 請求的頭資訊。該方法必須在`open()`之後、`send()`之前呼叫。如果該方法多次呼叫，設定同一個欄位，則每一次呼叫的值會被合併成一個單一的值傳送。

該方法接受兩個引數。第一個引數是字串，表示頭資訊的欄位名，第二個引數是欄位值。

```javascript
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.setRequestHeader('Content-Length', JSON.stringify(data).length);
xhr.send(JSON.stringify(data));
```

上面程式碼首先設定頭資訊`Content-Type`，表示傳送 JSON 格式的資料；然後設定`Content-Length`，表示資料長度；最後傳送 JSON 資料。

### XMLHttpRequest.overrideMimeType()

`XMLHttpRequest.overrideMimeType()`方法用來指定 MIME 型別，覆蓋伺服器返回的真正的 MIME 型別，從而讓瀏覽器進行不一樣的處理。舉例來說，伺服器返回的資料型別是`text/xml`，由於種種原因瀏覽器解析不成功報錯，這時就拿不到資料了。為了拿到原始資料，我們可以把 MIME 型別改成`text/plain`，這樣瀏覽器就不會去自動解析，從而我們就可以拿到原始文字了。

```javascript
xhr.overrideMimeType('text/plain')
```

注意，該方法必須在`send()`方法之前呼叫。

修改伺服器返回的資料型別，不是正常情況下應該採取的方法。如果希望伺服器返回指定的資料型別，可以用`responseType`屬性告訴伺服器，就像下面的例子。只有在伺服器無法返回某種資料型別時，才使用`overrideMimeType()`方法。

```javascript
var xhr = new XMLHttpRequest();
xhr.onload = function(e) {
  var arraybuffer = xhr.response;
  // ...
}
xhr.open('GET', url);
xhr.responseType = 'arraybuffer';
xhr.send();
```

### XMLHttpRequest.getResponseHeader()

`XMLHttpRequest.getResponseHeader()`方法返回 HTTP 頭資訊指定欄位的值，如果還沒有收到伺服器迴應或者指定欄位不存在，返回`null`。該方法的引數不區分大小寫。

```javascript
function getHeaderTime() {
  console.log(this.getResponseHeader("Last-Modified"));
}

var xhr = new XMLHttpRequest();
xhr.open('HEAD', 'yourpage.html');
xhr.onload = getHeaderTime;
xhr.send();
```

如果有多個欄位同名，它們的值會被連線為一個字串，每個欄位之間使用“逗號+空格”分隔。

### XMLHttpRequest.getAllResponseHeaders()

`XMLHttpRequest.getAllResponseHeaders()`方法返回一個字串，表示伺服器發來的所有 HTTP 頭資訊。格式為字串，每個頭資訊之間使用`CRLF`分隔（回車+換行），如果沒有收到伺服器迴應，該屬性為`null`。如果發生網路錯誤，該屬性為空字串。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'foo.txt', true);
xhr.send();

xhr.onreadystatechange = function () {
  if (this.readyState === 4) {
    var headers = xhr.getAllResponseHeaders();
  }
}
```

上面程式碼用於獲取伺服器返回的所有頭資訊。它可能是下面這樣的字串。

```http
date: Fri, 08 Dec 2017 21:04:30 GMT\r\n
content-encoding: gzip\r\n
x-content-type-options: nosniff\r\n
server: meinheld/0.6.1\r\n
x-frame-options: DENY\r\n
content-type: text/html; charset=utf-8\r\n
connection: keep-alive\r\n
strict-transport-security: max-age=63072000\r\n
vary: Cookie, Accept-Encoding\r\n
content-length: 6502\r\n
x-xss-protection: 1; mode=block\r\n
```

然後，對這個字串進行處理。

```javascript
var arr = headers.trim().split(/[\r\n]+/);
var headerMap = {};

arr.forEach(function (line) {
  var parts = line.split(': ');
  var header = parts.shift();
  var value = parts.join(': ');
  headerMap[header] = value;
});

headerMap['content-length'] // "6502"
```

### XMLHttpRequest.abort()

`XMLHttpRequest.abort()`方法用來終止已經發出的 HTTP 請求。呼叫這個方法以後，`readyState`屬性變為`4`，`status`屬性變為`0`。

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://www.example.com/page.php', true);
setTimeout(function () {
  if (xhr) {
    xhr.abort();
    xhr = null;
  }
}, 5000);
```

上面程式碼在發出5秒之後，終止一個 AJAX 請求。

## XMLHttpRequest 例項的事件

### readyStateChange 事件

`readyState`屬性的值發生改變，就會觸發 readyStateChange 事件。

我們可以透過`onReadyStateChange`屬性，指定這個事件的監聽函式，對不同狀態進行不同處理。尤其是當狀態變為`4`的時候，表示通訊成功，這時回撥函式就可以處理伺服器傳送回來的資料。

### progress 事件

上傳檔案時，XMLHttpRequest 例項物件本身和例項的`upload`屬性，都有一個`progress`事件，會不斷返回上傳的進度。

```javascript
var xhr = new XMLHttpRequest();

function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total;
  } else {
    console.log('無法計算進展');
  }
}

xhr.addEventListener('progress', updateProgress);

xhr.open();
```

### load 事件、error 事件、abort 事件

load 事件表示伺服器傳來的資料接收完畢，error 事件表示請求出錯，abort 事件表示請求被中斷（比如使用者取消請求）。

```javascript
var xhr = new XMLHttpRequest();

xhr.addEventListener('load', transferComplete);
xhr.addEventListener('error', transferFailed);
xhr.addEventListener('abort', transferCanceled);

xhr.open();

function transferComplete() {
  console.log('資料接收完畢');
}

function transferFailed() {
  console.log('資料接收出錯');
}

function transferCanceled() {
  console.log('使用者取消接收');
}
```

### loadend 事件

`abort`、`load`和`error`這三個事件，會伴隨一個`loadend`事件，表示請求結束，但不知道其是否成功。

```javascript
xhr.addEventListener('loadend', loadEnd);

function loadEnd(e) {
  console.log('請求結束，狀態未知');
}
```

### timeout 事件

伺服器超過指定時間還沒有返回結果，就會觸發 timeout 事件，具體的例子參見`timeout`屬性一節。

## Navigator.sendBeacon()

使用者解除安裝網頁的時候，有時需要向伺服器發一些資料。很自然的做法是在`unload`事件或`beforeunload`事件的監聽函式裡面，使用`XMLHttpRequest`物件傳送資料。但是，這樣做不是很可靠，因為`XMLHttpRequest`物件是非同步傳送，很可能在它即將傳送的時候，頁面已經解除安裝了，從而導致傳送取消或者傳送失敗。

解決方法就是`unload`事件裡面，加一些很耗時的同步操作。這樣就能留出足夠的時間，保證非同步 AJAX 能夠傳送成功。

```javascript
function log() {
  let xhr = new XMLHttpRequest();
  xhr.open('post', '/log', true);
  xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  xhr.send('foo=bar');
}

window.addEventListener('unload', function(event) {
  log();

  // a time-consuming operation
  for (let i = 1; i < 10000; i++) {
    for (let m = 1; m < 10000; m++) { continue; }
  }
});
```

上面程式碼中，強制執行了一次雙重迴圈，拖長了`unload`事件的執行時間，導致非同步 AJAX 能夠傳送成功。

類似的還可以使用`setTimeout`。下面是追蹤使用者點選的例子。

```javascript
// HTML 程式碼如下
// <a id="target" href="https://baidu.com">click</a>
const clickTime = 350;
const theLink = document.getElementById('target');

function log() {
  let xhr = new XMLHttpRequest();
  xhr.open('post', '/log', true);
  xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  xhr.send('foo=bar');
}

theLink.addEventListener('click', function (event) {
  event.preventDefault();
  log();

  setTimeout(function () {
    window.location.href = theLink.getAttribute('href');
  }, clickTime);
});
```

上面程式碼使用`setTimeout`，拖延了350毫秒，才讓頁面跳轉，因此使得非同步 AJAX 有時間發出。

這些做法的共同問題是，解除安裝的時間被硬生生拖長了，後面頁面的載入被推遲了，使用者體驗不好。

為了解決這個問題，瀏覽器引入了`Navigator.sendBeacon()`方法。這個方法還是非同步發出請求，但是請求與當前頁面執行緒脫鉤，作為瀏覽器程序的任務，因此可以保證會把資料發出去，不拖延解除安裝流程。

```javascript
window.addEventListener('unload', logData, false);

function logData() {
  navigator.sendBeacon('/log', analyticsData);
}
```

`Navigator.sendBeacon`方法接受兩個引數，第一個引數是目標伺服器的 URL，第二個引數是所要傳送的資料（可選），可以是任意型別（字串、表單物件、二進位制物件等等）。

```javascript
navigator.sendBeacon(url, data)
```

這個方法的返回值是一個布林值，成功傳送資料為`true`，否則為`false`。

該方法傳送資料的 HTTP 方法是 POST，可以跨域，類似於表單提交資料。它不能指定回撥函式。

下面是一個例子。

```javascript
// HTML 程式碼如下
// <body onload="analytics('start')" onunload="analytics('end')">

function analytics(state) {
  if (!navigator.sendBeacon) return;

  var URL = 'http://example.com/analytics';
  var data = 'state=' + state + '&location=' + window.location;
  navigator.sendBeacon(URL, data);
}
```
