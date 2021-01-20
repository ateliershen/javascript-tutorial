# Location 物件，URL 物件，URLSearchParams 物件

URL 是網際網路的基礎設施之一。瀏覽器提供了一些原生物件，用來管理 URL。

## Location 物件

`Location`物件是瀏覽器提供的原生物件，提供 URL 相關的資訊和操作方法。透過`window.location`和`document.location`屬性，可以拿到這個物件。

### 屬性

`Location`物件提供以下屬性。

- `Location.href`：整個 URL。
- `Location.protocol`：當前 URL 的協議，包括冒號（`:`）。
- `Location.host`：主機。如果埠不是協議預設的`80`和`433`，則還會包括冒號（`:`）和埠。
- `Location.hostname`：主機名，不包括埠。
- `Location.port`：埠號。
- `Location.pathname`：URL 的路徑部分，從根路徑`/`開始。
- `Location.search`：查詢字串部分，從問號`?`開始。
- `Location.hash`：片段字串部分，從`#`開始。
- `Location.username`：域名前面的使用者名稱。
- `Location.password`：域名前面的密碼。
- `Location.origin`：URL 的協議、主機名和埠。

```javascript
// 當前網址為
// http://user:passwd@www.example.com:4097/path/a.html?x=111#part1
document.location.href
// "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
document.location.protocol
// "http:"
document.location.host
// "www.example.com:4097"
document.location.hostname
// "www.example.com"
document.location.port
// "4097"
document.location.pathname
// "/path/a.html"
document.location.search
// "?x=111"
document.location.hash
// "#part1"
document.location.username
// "user"
document.location.password
// "passwd"
document.location.origin
// "http://user:passwd@www.example.com:4097"
```

這些屬性裡面，只有`origin`屬性是隻讀的，其他屬性都可寫。

注意，如果對`Location.href`寫入新的 URL 地址，瀏覽器會立刻跳轉到這個新地址。

```javascript
// 跳轉到新網址
document.location.href = 'http://www.example.com';
```

這個特性常常用於讓網頁自動滾動到新的錨點。

```javascript
document.location.href = '#top';
// 等同於
document.location.hash = '#top';
```

直接改寫`location`，相當於寫入`href`屬性。

```javascript
document.location = 'http://www.example.com';
// 等同於
document.location.href = 'http://www.example.com';
```

另外，`Location.href`屬性是瀏覽器唯一允許跨域寫入的屬性，即非同源的視窗可以改寫另一個視窗（比如子視窗與父視窗）的`Location.href`屬性，導致後者的網址跳轉。`Location`的其他屬性都不允許跨域寫入。

### 方法

**（1）Location.assign()**

`assign`方法接受一個 URL 字串作為引數，使得瀏覽器立刻跳轉到新的 URL。如果引數不是有效的 URL 字串，則會報錯。

```javascript
// 跳轉到新的網址
document.location.assign('http://www.example.com')
```

**（2）Location.replace()**

`replace`方法接受一個 URL 字串作為引數，使得瀏覽器立刻跳轉到新的 URL。如果引數不是有效的 URL 字串，則會報錯。

它與`assign`方法的差異在於，`replace`會在瀏覽器的瀏覽歷史`History`裡面刪除當前網址，也就是說，一旦使用了該方法，後退按鈕就無法回到當前網頁了，相當於在瀏覽歷史裡面，使用新的 URL 替換了老的 URL。它的一個應用是，當指令碼發現當前是移動裝置時，就立刻跳轉到移動版網頁。

```javascript
// 跳轉到新的網址
document.location.replace('http://www.example.com')
```

**（3）Location.reload()**

`reload`方法使得瀏覽器重新載入當前網址，相當於按下瀏覽器的重新整理按鈕。

它接受一個布林值作為引數。如果引數為`true`，瀏覽器將向伺服器重新請求這個網頁，並且重新載入後，網頁將滾動到頭部（即`scrollTop === 0`）。如果引數是`false`或為空，瀏覽器將從本地快取重新載入該網頁，並且重新載入後，網頁的視口位置是重新載入前的位置。

```javascript
// 向伺服器重新請求當前網址
window.location.reload(true);
```

**（4）Location.toString()**

`toString`方法返回整個 URL 字串，相當於讀取`Location.href`屬性。

## URL 的編碼和解碼

網頁的 URL 只能包含合法的字元。合法字元分成兩類。

- URL 元字元：分號（`;`），逗號（`,`），斜槓（`/`），問號（`?`），冒號（`:`），at（`@`），`&`，等號（`=`），加號（`+`），美元符號（`$`），井號（`#`）
- 語義字元：`a-z`，`A-Z`，`0-9`，連詞號（`-`），下劃線（`_`），點（`.`），感嘆號（`!`），波浪線（`~`），星號（`*`），單引號（`'`），圓括號（`()`）

除了以上字元，其他字元出現在 URL 之中都必須轉義，規則是根據作業系統的預設編碼，將每個位元組轉為百分號（`%`）加上兩個大寫的十六進位制字母。

比如，UTF-8 的作業系統上，`http://www.example.com/q=春節`這個 URL 之中，漢字“春節”不是 URL 的合法字元，所以被瀏覽器自動轉成`http://www.example.com/q=%E6%98%A5%E8%8A%82`。其中，“春”轉成了`%E6%98%A5`，“節”轉成了`%E8%8A%82`。這是因為“春”和“節”的 UTF-8 編碼分別是`E6 98 A5`和`E8 8A 82`，將每個位元組前面加上百分號，就構成了 URL 編碼。

JavaScript 提供四個 URL 的編碼/解碼方法。

- `encodeURI()`
- `encodeURIComponent()`
- `decodeURI()`
- `decodeURIComponent()`

### encodeURI()

`encodeURI()`方法用於轉碼整個 URL。它的引數是一個字串，代表整個 URL。它會將元字元和語義字元之外的字元，都進行轉義。

```javascript
encodeURI('http://www.example.com/q=春節')
// "http://www.example.com/q=%E6%98%A5%E8%8A%82"
```

### encodeURIComponent()

`encodeURIComponent()`方法用於轉碼 URL 的組成部分，會轉碼除了語義字元之外的所有字元，即元字元也會被轉碼。所以，它不能用於轉碼整個 URL。它接受一個引數，就是 URL 的片段。

```javascript
encodeURIComponent('春節')
// "%E6%98%A5%E8%8A%82"
encodeURIComponent('http://www.example.com/q=春節')
// "http%3A%2F%2Fwww.example.com%2Fq%3D%E6%98%A5%E8%8A%82"
```

上面程式碼中，`encodeURIComponent()`會連 URL 元字元一起轉義，所以如果轉碼整個 URL 就會出錯。

### decodeURI()

`decodeURI()`方法用於整個 URL 的解碼。它是`encodeURI()`方法的逆運算。它接受一個引數，就是轉碼後的 URL。

```javascript
decodeURI('http://www.example.com/q=%E6%98%A5%E8%8A%82')
// "http://www.example.com/q=春節"
```

### decodeURIComponent()

`decodeURIComponent()`用於URL 片段的解碼。它是`encodeURIComponent()`方法的逆運算。它接受一個引數，就是轉碼後的 URL 片段。

```javascript
decodeURIComponent('%E6%98%A5%E8%8A%82')
// "春節"
```

## URL 介面

瀏覽器原生提供`URL()`介面，它是一個建構函式，用來構造、解析和編碼 URL。一般情況下，透過`window.URL`可以拿到這個建構函式。

### 建構函式

`URL()`作為建構函式，可以生成 URL 例項。它接受一個表示 URL 的字串作為引數。如果引數不是合法的 URL，會報錯。

```javascript
var url = new URL('http://www.example.com/index.html');
url.href
// "http://www.example.com/index.html"
```

上面示例生成了一個 URL 例項，用來代表指定的網址。

除了字串，`URL()`的引數也可以是另一個 URL 例項。這時，`URL()`會自動讀取該例項的`href`屬性，作為實際引數。

如果 URL 字串是一個相對路徑，那麼需要表示絕對路徑的第二個引數，作為計算基準。

```javascript
var url1 = new URL('index.html', 'http://example.com');
url1.href
// "http://example.com/index.html"

var url2 = new URL('page2.html', 'http://example.com/page1.html');
url2.href
// "http://example.com/page2.html"

var url3 = new URL('..', 'http://example.com/a/b.html')
url3.href
// "http://example.com/"
```

上面程式碼中，返回的 URL 例項的路徑都是在第二個引數的基礎上，切換到第一個引數得到的。最後一個例子裡面，第一個引數是`..`，表示上層路徑。

### 例項屬性

URL 例項的屬性與`Location`物件的屬性基本一致，返回當前 URL 的資訊。

- URL.href：返回整個 URL
- URL.protocol：返回協議，以冒號`:`結尾
- URL.hostname：返回域名
- URL.host：返回域名與埠，包含`:`號，預設的80和443埠會省略
- URL.port：返回埠
- URL.origin：返回協議、域名和埠
- URL.pathname：返回路徑，以斜槓`/`開頭
- URL.search：返回查詢字串，以問號`?`開頭
- URL.searchParams：返回一個`URLSearchParams`例項，該屬性是`Location`物件沒有的
- URL.hash：返回片段識別符，以井號`#`開頭
- URL.password：返回域名前面的密碼
- URL.username：返回域名前面的使用者名稱

```javascript
var url = new URL('http://user:passwd@www.example.com:4097/path/a.html?x=111#part1');

url.href
// "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
url.protocol
// "http:"
url.hostname
// "www.example.com"
url.host
// "www.example.com:4097"
url.port
// "4097"
url.origin
// "http://www.example.com:4097"
url.pathname
// "/path/a.html"
url.search
// "?x=111"
url.searchParams
// URLSearchParams {}
url.hash
// "#part1"
url.password
// "passwd"
url.username
// "user"
```

這些屬性裡面，只有`origin`屬性是隻讀的，其他屬性都可寫，並且會立即生效。

```javascript
var url = new URL('http://example.com/index.html#part1');

url.pathname = 'index2.html';
url.href // "http://example.com/index2.html#part1"

url.hash = '#part2';
url.href // "http://example.com/index2.html#part2"
```

上面程式碼中，改變 URL 例項的`pathname`屬性和`hash`屬性，都會實時反映在 URL 例項當中。

### 靜態方法

**（1）URL.createObjectURL()**

`URL.createObjectURL()`方法用來為上傳/下載的檔案、流媒體檔案生成一個 URL 字串。這個字串代表了`File`物件或`Blob`物件的 URL。

```javascript
// HTML 程式碼如下
// <div id="display"/>
// <input
//   type="file"
//   id="fileElem"
//   multiple
//   accept="image/*"
//   onchange="handleFiles(this.files)"
//  >
var div = document.getElementById('display');

function handleFiles(files) {
  for (var i = 0; i < files.length; i++) {
    var img = document.createElement('img');
    img.src = window.URL.createObjectURL(files[i]);
    div.appendChild(img);
  }
}
```

上面程式碼中，`URL.createObjectURL()`方法用來為上傳的檔案生成一個 URL 字串，作為`<img>`元素的圖片來源。

該方法生成的 URL 就像下面的樣子。

```javascript
blob:http://localhost/c745ef73-ece9-46da-8f66-ebes574789b1
```

注意，每次使用`URL.createObjectURL()`方法，都會在記憶體裡面生成一個 URL 例項。如果不再需要該方法生成的 URL 字串，為了節省記憶體，可以使用`URL.revokeObjectURL()`方法釋放這個例項。

**（2）URL.revokeObjectURL()**

`URL.revokeObjectURL()`方法用來釋放`URL.createObjectURL()`方法生成的 URL 例項。它的引數就是`URL.createObjectURL()`方法返回的 URL 字串。

下面為上一段的示例加上`URL.revokeObjectURL()`。

```javascript
var div = document.getElementById('display');

function handleFiles(files) {
  for (var i = 0; i < files.length; i++) {
    var img = document.createElement('img');
    img.src = window.URL.createObjectURL(files[i]);
    div.appendChild(img);
    img.onload = function() {
      window.URL.revokeObjectURL(this.src);
    }
  }
}
```

上面程式碼中，一旦圖片載入成功以後，為本地檔案生成的 URL 字串就沒用了，於是可以在`img.onload`回撥函式裡面，透過`URL.revokeObjectURL()`方法解除安裝這個 URL 例項。

## URLSearchParams 物件

### 概述

`URLSearchParams`物件是瀏覽器的原生物件，用來構造、解析和處理 URL 的查詢字串（即 URL 問號後面的部分）。

它本身也是一個建構函式，可以生成例項。引數可以為查詢字串，起首的問號`?`有沒有都行，也可以是對應查詢字串的陣列或物件。

```javascript
// 方法一：傳入字串
var params = new URLSearchParams('?foo=1&bar=2');
// 等同於
var params = new URLSearchParams(document.location.search);

// 方法二：傳入陣列
var params = new URLSearchParams([['foo', 1], ['bar', 2]]);

// 方法三：傳入物件
var params = new URLSearchParams({'foo' : 1 , 'bar' : 2});
```

`URLSearchParams`會對查詢字串自動編碼。

```javascript
var params = new URLSearchParams({'foo': '你好'});
params.toString() // "foo=%E4%BD%A0%E5%A5%BD"
```

上面程式碼中，`foo`的值是漢字，`URLSearchParams`對其自動進行 URL 編碼。

瀏覽器向伺服器傳送表單資料時，可以直接使用`URLSearchParams`例項作為表單資料。

```javascript
const params = new URLSearchParams({foo: 1, bar: 2});
fetch('https://example.com/api', {
  method: 'POST',
  body: params
}).then(...)
```

上面程式碼中，`fetch`命令向伺服器傳送命令時，可以直接使用`URLSearchParams`例項。

`URLSearchParams`可以與`URL()`介面結合使用。

```javascript
var url = new URL(window.location);
var foo = url.searchParams.get('foo') || 'somedefault';
```

上面程式碼中，URL 例項的`searchParams`屬性就是一個`URLSearchParams`例項，所以可以使用`URLSearchParams`介面的`get`方法。

`URLSearchParams`例項有遍歷器介面，可以用`for...of`迴圈遍歷（詳見《ES6 標準入門》的《Iterator》一章）。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});

for (var p of params) {
  console.log(p[0] + ': ' + p[1]);
}
// foo: 1
// bar: 2
```

`URLSearchParams`沒有例項屬性，只有例項方法。

### URLSearchParams.toString()

`toString`方法返回例項的字串形式。

```javascript
var url = new URL('https://example.com?foo=1&bar=2');
var params = new URLSearchParams(url.search);

params.toString() // "foo=1&bar=2'
```

那麼需要字串的場合，會自動呼叫`toString`方法。

```javascript
var params = new URLSearchParams({version: 2.0});
window.location.href = location.pathname + '?' + params;
```

上面程式碼中，`location.href`賦值時，可以直接使用`params`物件。這時就會自動呼叫`toString`方法。

### URLSearchParams.append()

`append()`方法用來追加一個查詢引數。它接受兩個引數，第一個為鍵名，第二個為鍵值，沒有返回值。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.append('baz', 3);
params.toString() // "foo=1&bar=2&baz=3"
```

`append()`方法不會識別是否鍵名已經存在。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.append('foo', 3);
params.toString() // "foo=1&bar=2&foo=3"
```

上面程式碼中，查詢字串裡面`foo`已經存在了，但是`append`依然會追加一個同名鍵。

### URLSearchParams.delete()

`delete()`方法用來刪除指定的查詢引數。它接受鍵名作為引數。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.delete('bar');
params.toString() // "foo=1"
```

### URLSearchParams.has()

`has()`方法返回一個布林值，表示查詢字串是否包含指定的鍵名。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.has('bar') // true
params.has('baz') // false
```

### URLSearchParams.set()

`set()`方法用來設定查詢字串的鍵值。

它接受兩個引數，第一個是鍵名，第二個是鍵值。如果是已經存在的鍵，鍵值會被改寫，否則會被追加。

```javascript
var params = new URLSearchParams('?foo=1');
params.set('foo', 2);
params.toString() // "foo=2"
params.set('bar', 3);
params.toString() // "foo=2&bar=3"
```

上面程式碼中，`foo`是已經存在的鍵，`bar`是還不存在的鍵。

如果有多個的同名鍵，`set`會移除現存所有的鍵。

```javascript
var params = new URLSearchParams('?foo=1&foo=2');
params.set('foo', 3);
params.toString() // "foo=3"
```

下面是一個替換當前 URL 的例子。

```javascript
// URL: https://example.com?version=1.0
var params = new URLSearchParams(location.search.slice(1));
params.set('version', 2.0);

window.history.replaceState({}, '', location.pathname + `?` + params);
// URL: https://example.com?version=2.0
```

### URLSearchParams.get()，URLSearchParams.getAll()

`get()`方法用來讀取查詢字串裡面的指定鍵。它接受鍵名作為引數。

```javascript
var params = new URLSearchParams('?foo=1');
params.get('foo') // "1"
params.get('bar') // null
```

兩個地方需要注意。第一，它返回的是字串，如果原始值是數值，需要轉一下型別；第二，如果指定的鍵名不存在，返回值是`null`。

如果有多個的同名鍵，`get`返回位置最前面的那個鍵值。

```javascript
var params = new URLSearchParams('?foo=3&foo=2&foo=1');
params.get('foo') // "3"
```

上面程式碼中，查詢字串有三個`foo`鍵，`get`方法返回最前面的鍵值`3`。

`getAll()`方法返回一個數組，成員是指定鍵的所有鍵值。它接受鍵名作為引數。

```javascript
var params = new URLSearchParams('?foo=1&foo=2');
params.getAll('foo') // ["1", "2"]
```

上面程式碼中，查詢字串有兩個`foo`鍵，`getAll`返回的陣列就有兩個成員。

### URLSearchParams.sort()

`sort()`方法對查詢字串裡面的鍵進行排序，規則是按照 Unicode 碼點從小到大排列。

該方法沒有返回值，或者說返回值是`undefined`。

```javascript
var params = new URLSearchParams('c=4&a=2&b=3&a=1');
params.sort();
params.toString() // "a=2&a=1&b=3&c=4"
```

上面程式碼中，如果有兩個同名的鍵`a`，它們之間不會排序，而是保留原始的順序。

### URLSearchParams.keys()，URLSearchParams.values()，URLSearchParams.entries()

這三個方法都返回一個遍歷器物件，供`for...of`迴圈遍歷。它們的區別在於，`keys`方法返回的是鍵名的遍歷器，`values`方法返回的是鍵值的遍歷器，`entries`返回的是鍵值對的遍歷器。

```javascript
var params = new URLSearchParams('a=1&b=2');

for(var p of params.keys()) {
  console.log(p);
}
// a
// b

for(var p of params.values()) {
  console.log(p);
}
// 1
// 2

for(var p of params.entries()) {
  console.log(p);
}
// ["a", "1"]
// ["b", "2"]
```

如果直接對`URLSearchParams`進行遍歷，其實內部呼叫的就是`entries`介面。

```javascript
for (var p of params) {}
// 等同於
for (var p of params.entries()) {}
```

## 參考連結

- [Location](https://developer.mozilla.org/en-US/docs/Web/API/Location), by MDN
- [URL](https://developer.mozilla.org/en-US/docs/Web/API/URL), by MDN
- [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams), by MDN
- [Easy URL Manipulation with URLSearchParams](https://developers.google.com/web/updates/2016/01/urlsearchparams?hl=en), by Eric Bidelman
