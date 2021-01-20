# 同源限制

瀏覽器安全的基石是“同源政策”（[same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy)）。很多開發者都知道這一點，但瞭解得不全面。

## 概述

### 含義

1995年，同源政策由 Netscape 公司引入瀏覽器。目前，所有瀏覽器都實行這個政策。

最初，它的含義是指，A 網頁設定的 Cookie，B 網頁不能開啟，除非這兩個網頁“同源”。所謂“同源”指的是“三個相同”。

> - 協議相同
> - 域名相同
> - 埠相同（這點可以忽略，詳見下文）

舉例來說，`http://www.example.com/dir/page.html`這個網址，協議是`http://`，域名是`www.example.com`，埠是`80`（預設埠可以省略），它的同源情況如下。

- `http://www.example.com/dir2/other.html`：同源
- `http://example.com/dir/other.html`：不同源（域名不同）
- `http://v2.www.example.com/dir/other.html`：不同源（域名不同）
- `http://www.example.com:81/dir/other.html`：不同源（埠不同）
- `https://www.example.com/dir/page.html`：不同源（協議不同）

注意，標準規定埠不同的網址不是同源（比如8000埠和8001埠不是同源），但是瀏覽器沒有遵守這條規定。實際上，同一個網域的不同埠，是可以互相讀取 Cookie 的。

### 目的

同源政策的目的，是為了保證使用者資訊的安全，防止惡意的網站竊取資料。

設想這樣一種情況：A 網站是一家銀行，使用者登入以後，A 網站在使用者的機器上設定了一個 Cookie，包含了一些隱私資訊。使用者離開 A 網站以後，又去訪問 B 網站，如果沒有同源限制，B 網站可以讀取 A 網站的 Cookie，那麼隱私就洩漏了。更可怕的是，Cookie 往往用來儲存使用者的登入狀態，如果使用者沒有退出登入，其他網站就可以冒充使用者，為所欲為。因為瀏覽器同時還規定，提交表單不受同源政策的限制。

由此可見，同源政策是必需的，否則 Cookie 可以共享，網際網路就毫無安全可言了。

### 限制範圍

隨著網際網路的發展，同源政策越來越嚴格。目前，如果非同源，共有三種行為受到限制。

> （1） 無法讀取非同源網頁的 Cookie、LocalStorage 和 IndexedDB。
>
> （2） 無法接觸非同源網頁的 DOM。
>
> （3） 無法向非同源地址傳送 AJAX 請求（可以傳送，但瀏覽器會拒絕接受響應）。

另外，透過 JavaScript 指令碼可以拿到其他視窗的`window`物件。如果是非同源的網頁，目前允許一個視窗可以接觸其他網頁的`window`物件的九個屬性和四個方法。

- window.closed
- window.frames
- window.length
- window.location
- window.opener
- window.parent
- window.self
- window.top
- window.window
- window.blur()
- window.close()
- window.focus()
- window.postMessage()

上面的九個屬性之中，只有`window.location`是可讀寫的，其他八個全部都是隻讀。而且，即使是`location`物件，非同源的情況下，也只允許呼叫`location.replace()`方法和寫入`location.href`屬性。

雖然這些限制是必要的，但是有時很不方便，合理的用途也受到影響。下面介紹如何規避上面的限制。

## Cookie

Cookie 是伺服器寫入瀏覽器的一小段資訊，只有同源的網頁才能共享。如果兩個網頁一級域名相同，只是次級域名不同，瀏覽器允許透過設定`document.domain`共享 Cookie。

舉例來說，A 網頁的網址是`http://w1.example.com/a.html`，B 網頁的網址是`http://w2.example.com/b.html`，那麼只要設定相同的`document.domain`，兩個網頁就可以共享 Cookie。因為瀏覽器透過`document.domain`屬性來檢查是否同源。

```javascript
// 兩個網頁都需要設定
document.domain = 'example.com';
```

注意，A 和 B 兩個網頁都需要設定`document.domain`屬性，才能達到同源的目的。因為設定`document.domain`的同時，會把埠重置為`null`，因此如果只設置一個網頁的`document.domain`，會導致兩個網址的埠不同，還是達不到同源的目的。

現在，A 網頁透過指令碼設定一個 Cookie。

```javascript
document.cookie = "test1=hello";
```

B 網頁就可以讀到這個 Cookie。

```javascript
var allCookie = document.cookie;
```

注意，這種方法只適用於 Cookie 和 iframe 視窗，LocalStorage 和 IndexedDB 無法透過這種方法，規避同源政策，而要使用下文介紹 PostMessage API。

另外，伺服器也可以在設定 Cookie 的時候，指定 Cookie 的所屬域名為一級域名，比如`.example.com`。

```http
Set-Cookie: key=value; domain=.example.com; path=/
```

這樣的話，二級域名和三級域名不用做任何設定，都可以讀取這個 Cookie。

## iframe 和多視窗通訊

`iframe`元素可以在當前網頁之中，嵌入其他網頁。每個`iframe`元素形成自己的視窗，即有自己的`window`物件。`iframe`視窗之中的指令碼，可以獲得父視窗和子視窗。但是，只有在同源的情況下，父視窗和子窗口才能通訊；如果跨域，就無法拿到對方的 DOM。

比如，父視窗執行下面的命令，如果`iframe`視窗不是同源，就會報錯。

```javascript
document
.getElementById("myIFrame")
.contentWindow
.document
// Uncaught DOMException: Blocked a frame from accessing a cross-origin frame.
```

上面命令中，父視窗想獲取子視窗的 DOM，因為跨域導致報錯。

反之亦然，子視窗獲取主視窗的 DOM 也會報錯。

```javascript
window.parent.document.body
// 報錯
```

這種情況不僅適用於`iframe`視窗，還適用於`window.open`方法開啟的視窗，只要跨域，父視窗與子視窗之間就無法通訊。

如果兩個視窗一級域名相同，只是二級域名不同，那麼設定上一節介紹的`document.domain`屬性，就可以規避同源政策，拿到 DOM。

對於完全不同源的網站，目前有兩種方法，可以解決跨域視窗的通訊問題。

> - 片段識別符（fragment identifier）
> - 跨文件通訊API（Cross-document messaging）

### 片段識別符

片段識別符號（fragment identifier）指的是，URL 的`#`號後面的部分，比如`http://example.com/x.html#fragment`的`#fragment`。如果只是改變片段識別符號，頁面不會重新重新整理。

父視窗可以把資訊，寫入子視窗的片段識別符號。

```javascript
var src = originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```

上面程式碼中，父視窗把所要傳遞的資訊，寫入 iframe 視窗的片段識別符號。

子視窗透過監聽`hashchange`事件得到通知。

```javascript
window.onhashchange = checkMessage;

function checkMessage() {
  var message = window.location.hash;
  // ...
}
```

同樣的，子視窗也可以改變父視窗的片段識別符號。

```javascript
parent.location.href = target + '#' + hash;
```

### window.postMessage()

上面的這種方法屬於破解，HTML5 為了解決這個問題，引入了一個全新的API：跨文件通訊 API（Cross-document messaging）。

這個 API 為`window`物件新增了一個`window.postMessage`方法，允許跨視窗通訊，不論這兩個視窗是否同源。舉例來說，父視窗`aaa.com`向子視窗`bbb.com`發訊息，呼叫`postMessage`方法就可以了。

```javascript
// 父視窗開啟一個子視窗
var popup = window.open('http://bbb.com', 'title');
// 父視窗向子視窗發訊息
popup.postMessage('Hello World!', 'http://bbb.com');
```

`postMessage`方法的第一個引數是具體的資訊內容，第二個引數是接收訊息的視窗的源（origin），即“協議 + 域名 + 埠”。也可以設為`*`，表示不限制域名，向所有視窗傳送。

子視窗向父視窗傳送訊息的寫法類似。

```javascript
// 子視窗向父視窗發訊息
window.opener.postMessage('Nice to see you', 'http://aaa.com');
```

父視窗和子視窗都可以透過`message`事件，監聽對方的訊息。

```javascript
// 父視窗和子視窗都可以用下面的程式碼，
// 監聽 message 訊息
window.addEventListener('message', function (e) {
  console.log(e.data);
},false);
```

`message`事件的引數是事件物件`event`，提供以下三個屬性。

> - `event.source`：傳送訊息的視窗
> - `event.origin`: 訊息發向的網址
> - `event.data`: 訊息內容

下面的例子是，子視窗透過`event.source`屬性引用父視窗，然後傳送訊息。

```javascript
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  event.source.postMessage('Nice to see you!', '*');
}
```

上面程式碼有幾個地方需要注意。首先，`receiveMessage`函式裡面沒有過濾資訊的來源，任意網址發來的資訊都會被處理。其次，`postMessage`方法中指定的目標視窗的網址是一個星號，表示該資訊可以向任意網址傳送。通常來說，這兩種做法是不推薦的，因為不夠安全，可能會被惡意利用。

`event.origin`屬性可以過濾不是發給本視窗的訊息。

```javascript
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  if (event.origin !== 'http://aaa.com') return;
  if (event.data === 'Hello World') {
    event.source.postMessage('Hello', event.origin);
  } else {
    console.log(event.data);
  }
}
```

### LocalStorage

透過`window.postMessage`，讀寫其他視窗的 LocalStorage 也成為了可能。

下面是一個例子，主視窗寫入 iframe 子視窗的`localStorage`。

```javascript
window.onmessage = function(e) {
  if (e.origin !== 'http://bbb.com') {
    return;
  }
  var payload = JSON.parse(e.data);
  localStorage.setItem(payload.key, JSON.stringify(payload.data));
};
```

上面程式碼中，子視窗將父視窗發來的訊息，寫入自己的 LocalStorage。

父視窗傳送訊息的程式碼如下。

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
win.postMessage(
  JSON.stringify({key: 'storage', data: obj}),
  'http://bbb.com'
);
```

加強版的子視窗接收訊息的程式碼如下。

```javascript
window.onmessage = function(e) {
  if (e.origin !== 'http://bbb.com') return;
  var payload = JSON.parse(e.data);
  switch (payload.method) {
    case 'set':
      localStorage.setItem(payload.key, JSON.stringify(payload.data));
      break;
    case 'get':
      var parent = window.parent;
      var data = localStorage.getItem(payload.key);
      parent.postMessage(data, 'http://aaa.com');
      break;
    case 'remove':
      localStorage.removeItem(payload.key);
      break;
  }
};
```

加強版的父視窗傳送訊息程式碼如下。

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
// 存入物件
win.postMessage(
  JSON.stringify({key: 'storage', method: 'set', data: obj}),
  'http://bbb.com'
);
// 讀取物件
win.postMessage(
  JSON.stringify({key: 'storage', method: "get"}),
  "*"
);
window.onmessage = function(e) {
  if (e.origin != 'http://aaa.com') return;
  console.log(JSON.parse(e.data).name);
};
```

## AJAX

同源政策規定，AJAX 請求只能發給同源的網址，否則就報錯。

除了架設伺服器代理（瀏覽器請求同源伺服器，再由後者請求外部服務），有三種方法規避這個限制。

> - JSONP
> - WebSocket
> - CORS

### JSONP

JSONP 是伺服器與客戶端跨源通訊的常用方法。最大特點就是簡單易用，沒有相容性問題，老式瀏覽器全部支援，服務端改造非常小。

它的做法如下。

第一步，網頁新增一個`<script>`元素，向伺服器請求一個指令碼，這不受同源政策限制，可以跨域請求。

```html
<script src="http://api.foo.com?callback=bar"></script>
```

注意，請求的指令碼網址有一個`callback`引數（`?callback=bar`），用來告訴伺服器，客戶端的回撥函式名稱（`bar`）。

第二步，伺服器收到請求後，拼接一個字串，將 JSON 資料放在函式名裡面，作為字串返回（`bar({...})`）。

第三步，客戶端會將伺服器返回的字串，作為程式碼解析，因為瀏覽器認為，這是`<script>`標籤請求的指令碼內容。這時，客戶端只要定義了`bar()`函式，就能在該函式體內，拿到伺服器返回的 JSON 資料。

下面看一個例項。首先，網頁動態插入`<script>`元素，由它向跨域網址發出請求。

```javascript
function addScriptTag(src) {
  var script = document.createElement('script');
  script.setAttribute('type', 'text/javascript');
  script.src = src;
  document.body.appendChild(script);
}

window.onload = function () {
  addScriptTag('http://example.com/ip?callback=foo');
}

function foo(data) {
  console.log('Your public IP address is: ' + data.ip);
};
```

上面程式碼透過動態新增`<script>`元素，向伺服器`example.com`發出請求。注意，該請求的查詢字串有一個`callback`引數，用來指定回撥函式的名字，這對於 JSONP 是必需的。

伺服器收到這個請求以後，會將資料放在回撥函式的引數位置返回。

```javascript
foo({
  'ip': '8.8.8.8'
});
```

由於`<script>`元素請求的指令碼，直接作為程式碼執行。這時，只要瀏覽器定義了`foo`函式，該函式就會立即呼叫。作為引數的 JSON 資料被視為 JavaScript 物件，而不是字串，因此避免了使用`JSON.parse`的步驟。

### WebSocket

WebSocket 是一種通訊協議，使用`ws://`（非加密）和`wss://`（加密）作為協議字首。該協議不實行同源政策，只要伺服器支援，就可以透過它進行跨源通訊。

下面是一個例子，瀏覽器發出的 WebSocket 請求的頭資訊（摘自[維基百科](https://en.wikipedia.org/wiki/WebSocket)）。

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

上面程式碼中，有一個欄位是`Origin`，表示該請求的請求源（origin），即發自哪個域名。

正是因為有了`Origin`這個欄位，所以 WebSocket 才沒有實行同源政策。因為伺服器可以根據這個欄位，判斷是否許可本次通訊。如果該域名在白名單內，伺服器就會做出如下回應。

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

### CORS

CORS 是跨源資源分享（Cross-Origin Resource Sharing）的縮寫。它是 W3C 標準，屬於跨源 AJAX 請求的根本解決方法。相比 JSONP 只能發`GET`請求，CORS 允許任何型別的請求。

下一章將詳細介紹，如何透過 CORS 完成跨源 AJAX 請求。

## 參考連結

- Mozilla Developer Network, [Window.postMessage](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage)
- Jakub Jankiewicz, [Cross-Domain LocalStorage](http://jcubic.wordpress.com/2014/06/20/cross-domain-localstorage/)
- David Baron, [setTimeout with a shorter delay](http://dbaron.org/log/20100309-faster-timeouts): 利用 window.postMessage 可以實現0毫秒觸發回撥函式
