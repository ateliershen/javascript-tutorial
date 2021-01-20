# CORS 通訊

CORS 是一個 W3C 標準，全稱是“跨域資源共享”（Cross-origin resource sharing）。它允許瀏覽器向跨域的伺服器，發出`XMLHttpRequest`請求，從而克服了 AJAX 只能同源使用的限制。

## 簡介

CORS 需要瀏覽器和伺服器同時支援。目前，所有瀏覽器都支援該功能。

整個 CORS 通訊過程，都是瀏覽器自動完成，不需要使用者參與。對於開發者來說，CORS 通訊與普通的 AJAX 通訊沒有差別，程式碼完全一樣。瀏覽器一旦發現 AJAX 請求跨域，就會自動新增一些附加的頭資訊，有時還會多出一次附加的請求，但使用者不會有感知。因此，實現 CORS 通訊的關鍵是伺服器。只要伺服器實現了 CORS 介面，就可以跨域通訊。

## 兩種請求

CORS 請求分成兩類：簡單請求（simple request）和非簡單請求（not-so-simple request）。

只要同時滿足以下兩大條件，就屬於簡單請求。

（1）請求方法是以下三種方法之一。

> - HEAD
> - GET
> - POST

（2）HTTP 的頭資訊不超出以下幾種欄位。

> - Accept
> - Accept-Language
> - Content-Language
> - Last-Event-ID
> - Content-Type：只限於三個值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

凡是不同時滿足上面兩個條件，就屬於非簡單請求。一句話，簡單請求就是簡單的 HTTP 方法與簡單的 HTTP 頭資訊的結合。

這樣劃分的原因是，表單在歷史上一直可以跨域發出請求。簡單請求就是表單請求，瀏覽器沿襲了傳統的處理方式，不把行為複雜化，否則開發者可能轉而使用表單，規避 CORS 的限制。對於非簡單請求，瀏覽器會採用新的處理方式。

## 簡單請求

### 基本流程

對於簡單請求，瀏覽器直接發出 CORS 請求。具體來說，就是在頭資訊之中，增加一個`Origin`欄位。

下面是一個例子，瀏覽器發現這次跨域 AJAX 請求是簡單請求，就自動在頭資訊之中，新增一個`Origin`欄位。

```http
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

上面的頭資訊中，`Origin`欄位用來說明，本次請求來自哪個域（協議 + 域名 + 埠）。伺服器根據這個值，決定是否同意這次請求。

如果`Origin`指定的源，不在許可範圍內，伺服器會返回一個正常的 HTTP 迴應。瀏覽器發現，這個迴應的頭資訊沒有包含`Access-Control-Allow-Origin`欄位（詳見下文），就知道出錯了，從而丟擲一個錯誤，被`XMLHttpRequest`的`onerror`回撥函式捕獲。注意，這種錯誤無法透過狀態碼識別，因為 HTTP 迴應的狀態碼有可能是200。

如果`Origin`指定的域名在許可範圍內，伺服器返回的響應，會多出幾個頭資訊欄位。

```http
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```

上面的頭資訊之中，有三個與 CORS 請求相關的欄位，都以`Access-Control-`開頭。

**（1）`Access-Control-Allow-Origin`**

該欄位是必須的。它的值要麼是請求時`Origin`欄位的值，要麼是一個`*`，表示接受任意域名的請求。

**（2）`Access-Control-Allow-Credentials`**

該欄位可選。它的值是一個布林值，表示是否允許傳送 Cookie。預設情況下，Cookie 不包括在 CORS 請求之中。設為`true`，即表示伺服器明確許可，瀏覽器可以把 Cookie 包含在請求中，一起發給伺服器。這個值也只能設為`true`，如果伺服器不要瀏覽器傳送 Cookie，不傳送該欄位即可。

**（3）`Access-Control-Expose-Headers`**

該欄位可選。CORS 請求時，`XMLHttpRequest`物件的`getResponseHeader()`方法只能拿到6個伺服器返回的基本欄位：`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`。如果想拿到其他欄位，就必須在`Access-Control-Expose-Headers`裡面指定。上面的例子指定，`getResponseHeader('FooBar')`可以返回`FooBar`欄位的值。

### withCredentials 屬性

上面說到，CORS 請求預設不包含 Cookie 資訊（以及 HTTP 認證資訊等），這是為了降低 CSRF 攻擊的風險。但是某些場合，伺服器可能需要拿到 Cookie，這時需要伺服器顯式指定`Access-Control-Allow-Credentials`欄位，告訴瀏覽器可以傳送 Cookie。

```http
Access-Control-Allow-Credentials: true
```

同時，開發者必須在 AJAX 請求中開啟`withCredentials`屬性。

```javascript
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```

否則，即使伺服器要求傳送 Cookie，瀏覽器也不會發送。或者，伺服器要求設定 Cookie，瀏覽器也不會處理。

但是，有的瀏覽器預設將`withCredentials`屬性設為`true`。這導致如果省略`withCredentials`設定，這些瀏覽器可能還是會一起傳送 Cookie。這時，可以顯式關閉`withCredentials`。

```javascript
xhr.withCredentials = false;
```

需要注意的是，如果伺服器要求瀏覽器傳送 Cookie，`Access-Control-Allow-Origin`就不能設為星號，必須指定明確的、與請求網頁一致的域名。同時，Cookie 依然遵循同源政策，只有用伺服器域名設定的 Cookie 才會上傳，其他域名的 Cookie 並不會上傳，且（跨域）原網頁程式碼中的`document.cookie`也無法讀取伺服器域名下的 Cookie。

## 非簡單請求

### 預檢請求

非簡單請求是那種對伺服器提出特殊要求的請求，比如請求方法是`PUT`或`DELETE`，或者`Content-Type`欄位的型別是`application/json`。

非簡單請求的 CORS 請求，會在正式通訊之前，增加一次 HTTP 查詢請求，稱為“預檢”請求（preflight）。瀏覽器先詢問伺服器，當前網頁所在的域名是否在伺服器的許可名單之中，以及可以使用哪些 HTTP 方法和頭資訊欄位。只有得到肯定答覆，瀏覽器才會發出正式的`XMLHttpRequest`請求，否則就報錯。這是為了防止這些新增的請求，對傳統的沒有 CORS 支援的伺服器形成壓力，給伺服器一個提前拒絕的機會，這樣可以防止伺服器收到大量`DELETE`和`PUT`請求，這些傳統的表單不可能跨域發出的請求。

下面是一段瀏覽器的 JavaScript 指令碼。

```javascript
var url = 'http://api.alice.com/cors';
var xhr = new XMLHttpRequest();
xhr.open('PUT', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();
```

上面程式碼中，HTTP 請求的方法是`PUT`，並且傳送一個自定義頭資訊`X-Custom-Header`。

瀏覽器發現，這是一個非簡單請求，就自動發出一個“預檢”請求，要求伺服器確認可以這樣請求。下面是這個“預檢”請求的 HTTP 頭資訊。

```http
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

“預檢”請求用的請求方法是`OPTIONS`，表示這個請求是用來詢問的。頭資訊裡面，關鍵欄位是`Origin`，表示請求來自哪個源。

除了`Origin`欄位，“預檢”請求的頭資訊包括兩個特殊欄位。

**（1）`Access-Control-Request-Method`**

該欄位是必須的，用來列出瀏覽器的 CORS 請求會用到哪些 HTTP 方法，上例是`PUT`。

**（2）`Access-Control-Request-Headers`**

該欄位是一個逗號分隔的字串，指定瀏覽器 CORS 請求會額外發送的頭資訊欄位，上例是`X-Custom-Header`。

### 預檢請求的迴應

伺服器收到“預檢”請求以後，檢查了`Origin`、`Access-Control-Request-Method`和`Access-Control-Request-Headers`欄位以後，確認允許跨源請求，就可以做出迴應。

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

上面的 HTTP 迴應中，關鍵的是`Access-Control-Allow-Origin`欄位，表示`http://api.bob.com`可以請求資料。該欄位也可以設為星號，表示同意任意跨源請求。

```http
Access-Control-Allow-Origin: *
```

如果伺服器否定了“預檢”請求，會返回一個正常的 HTTP 迴應，但是沒有任何 CORS 相關的頭資訊欄位，或者明確表示請求不符合條件。

```http
OPTIONS http://api.bob.com HTTP/1.1
Status: 200
Access-Control-Allow-Origin: https://notyourdomain.com
Access-Control-Allow-Method: POST
```

上面的伺服器迴應，`Access-Control-Allow-Origin`欄位明確不包括髮出請求的`http://api.bob.com`。

這時，瀏覽器就會認定，伺服器不同意預檢請求，因此觸發一個錯誤，被`XMLHttpRequest`物件的`onerror`回撥函式捕獲。控制檯會打印出如下的報錯資訊。

```bash
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```

伺服器迴應的其他 CORS 相關欄位如下。

```http
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 1728000
```

**（1）`Access-Control-Allow-Methods`**

該欄位必需，它的值是逗號分隔的一個字串，表明伺服器支援的所有跨域請求的方法。注意，返回的是所有支援的方法，而不單是瀏覽器請求的那個方法。這是為了避免多次“預檢”請求。

**（2）`Access-Control-Allow-Headers`**

如果瀏覽器請求包括`Access-Control-Request-Headers`欄位，則`Access-Control-Allow-Headers`欄位是必需的。它也是一個逗號分隔的字串，表明伺服器支援的所有頭資訊欄位，不限於瀏覽器在“預檢”中請求的欄位。

**（3）`Access-Control-Allow-Credentials`**

該欄位與簡單請求時的含義相同。

**（4）`Access-Control-Max-Age`**

該欄位可選，用來指定本次預檢請求的有效期，單位為秒。上面結果中，有效期是20天（1728000秒），即允許快取該條迴應1728000秒（即20天），在此期間，不用發出另一條預檢請求。

### 瀏覽器的正常請求和迴應

一旦伺服器通過了“預檢”請求，以後每次瀏覽器正常的 CORS 請求，就都跟簡單請求一樣，會有一個`Origin`頭資訊欄位。伺服器的迴應，也都會有一個`Access-Control-Allow-Origin`頭資訊欄位。

下面是“預檢”請求之後，瀏覽器的正常 CORS 請求。

```http
PUT /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
X-Custom-Header: value
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

上面頭資訊的`Origin`欄位是瀏覽器自動新增的。

下面是伺服器正常的迴應。

```http
Access-Control-Allow-Origin: http://api.bob.com
Content-Type: text/html; charset=utf-8
```

上面頭資訊中，`Access-Control-Allow-Origin`欄位是每次迴應都必定包含的。

## 與 JSONP 的比較

CORS 與 JSONP 的使用目的相同，但是比 JSONP 更強大。JSONP 只支援`GET`請求，CORS 支援所有型別的 HTTP 請求。JSONP 的優勢在於支援老式瀏覽器，以及可以向不支援 CORS 的網站請求資料。

## 參考連結

- [Using CORS](http://www.html5rocks.com/en/tutorials/cors/), Monsur Hossain
- [HTTP access control (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS), MDN
- [CORS](https://frontendian.co/cors), Ryan Miller
- [Do You Really Know CORS?](http://performantcode.com/web/do-you-really-know-cors), Grzegorz Mirek
