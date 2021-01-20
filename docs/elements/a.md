# <a> 元素

`<a>`元素用來設定連結。除了網頁元素的通用介面（`Node`介面、`Element`介面、`HTMLElement`介面），它還繼承了`HTMLAnchorElement`介面和`HTMLHyperlinkElementUtils`介面。

## 屬性

### URL 相關屬性

`<a>`元素有一系列 URL 相關屬性，可以用來操作連結地址。這些屬性的含義，可以參見`Location`物件的例項屬性。

- hash：片段識別符（以`#`開頭）
- host：主機和埠（預設埠80和443會省略）
- hostname：主機名
- href：完整的 URL
- origin：協議、域名和埠
- password：主機名前的密碼
- pathname：路徑（以`/`開頭）
- port：埠
- protocol：協議（包含尾部的冒號`:`）
- search：查詢字串（以`?`開頭）
- username：主機名前的使用者名稱

```javascript
// HTML 程式碼如下
// <a id="test" href="http://user:passwd@example.com:8081/index.html?bar=1#foo">test</a>
var a = document.getElementById('test');
a.hash // "#foo"
a.host // "example.com:8081"
a.hostname // "example.com"
a.href // "http://user:passed@example.com:8081/index.html?bar=1#foo"
a.origin // "http://example.com:8081"
a.password // "passwd"
a.pathname // "/index.html"
a.port // "8081"
a.protocol // "http:"
a.search // "?bar=1"
a.username // "user"
```

除了`origin`屬性是隻讀的，上面這些屬性都是可讀寫的。

### accessKey 屬性

`accessKey`屬性用來讀寫`<a>`元素的快捷鍵。

```javascript
// HTML 程式碼如下
// <a id="test" href="http://example.com">test</a>
var a = document.getElementById('test');
a.accessKey = 'k';
```

上面程式碼設定`<a>`元素的快捷鍵為`k`，以後只要按下這個快捷鍵，瀏覽器就會跳轉到`example.com`。

注意，不同的瀏覽器在不同的作業系統下，喚起快捷鍵的功能鍵組合是[不一樣](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/accesskey)的。比如，Chrome 瀏覽器在 Linux 系統下，需要按下`Alt + k`，才會跳轉到`example.com`。

### download 屬性

`download`屬性表示當前連結不是用來瀏覽，而是用來下載的。它的值是一個字串，表示使用者下載得到的檔名。

```javascript
// HTML 程式碼如下
// <a id="test" href="foo.jpg">下載</a>
var a = document.getElementById('test');
a.download = 'bar.jpg';
```

上面程式碼中，`<a>`元素是一個圖片連結，預設情況下，點選後圖片會在當前視窗載入。設定了`download`屬性以後，再點選這個連結，就會下載對話方塊，詢問使用者儲存位置，而且下載的檔名為`bar.jpg`。

### hreflang 屬性

`hreflang`屬性用來讀寫`<a>`元素的 HTML 屬性`hreflang`，表示連結指向的資源的語言，比如`hreflang="en"`。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com" hreflang="en">test</a>
var a = document.getElementById('test');
a.hreflang // "en"
```

### referrerPolicy 屬性

`referrerPolicy`屬性用來讀寫`<a>`元素的 HTML 屬性`referrerPolicy`，指定當使用者點選連結時，如何傳送 HTTP 頭資訊的`referer`欄位。

HTTP 頭資訊的`referer`欄位，表示當前請求是從哪裡來的。它的格式可以由`<a>`元素的`referrerPolicy`屬性指定，共有三個值可以選擇。

- `no-referrer`：不傳送`referer`欄位。
- `origin`：`referer`欄位的值是`<a>`元素的`origin`屬性，即協議 + 主機名 + 埠。
- `unsafe-url`：`referer`欄位的值是`origin`屬性再加上路徑，但不包含`#`片段。這種格式提供的資訊最詳細，可能存在資訊洩漏的風險。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com" referrerpolicy="no-referrer">test</a>
var a = document.getElementById('test');
a.referrerPolicy // "no-referrer"
```

### rel 屬性

`rel`屬性用來讀寫`<a>`元素的 HTML 屬性`rel`，表示連結與當前文件的關係。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com" rel="license">license.html</a>
var a = document.getElementById('test');
a.rel // "license"
```

### tabIndex 屬性

`tabIndex`屬性的值是一個整數，用來讀寫當前`<a>`元素在文件裡面的 Tab 鍵遍歷順序。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com">test</a>
var a = document.getElementById('test');
a.tabIndex // 0
```

### target 屬性

`target`屬性用來讀寫`<a>`元素的 HTML 屬性`target`。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com" target="_blank">test</a>
var a = document.getElementById('test');
a.target // "_blank"
```

### text 屬性

`text`屬性用來讀寫`<a>`元素的連結文字，等同於當前節點的`textContent`屬性。

```javascript
// HTML 程式碼如下
// <a id="test" href="https://example.com">test</a>
var a = document.getElementById('test');
a.text // "test"
```

### type 屬性

`type`屬性用來讀寫`<a>`元素的 HTML 屬性`type`，表示連結目標的 MIME 型別。

```javascript
// HTML 程式碼如下
// <a id="test" type="video/mp4" href="example.mp4">video</a>
var a = document.getElementById('test');
a.type // "video/mp4"
```

## 方法

`<a>`元素的方法都是繼承的，主要有以下三個。

- `blur()`：從當前元素移除鍵盤焦點，詳見`HTMLElement`介面的介紹。
- `focus()`：當前元素得到鍵盤焦點，詳見`HTMLElement`介面的介紹。
- `toString()`：返回當前`<a>`元素的 HTML 屬性`href`。
