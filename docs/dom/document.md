# Document 節點

## 概述

`document`節點物件代表整個文件，每張網頁都有自己的`document`物件。`window.document`屬性就指向這個物件。只要瀏覽器開始載入 HTML 文件，該物件就存在了，可以直接使用。

`document`物件有不同的辦法可以獲取。

- 正常的網頁，直接使用`document`或`window.document`。
- `iframe`框架裡面的網頁，使用`iframe`節點的`contentDocument`屬性。
- Ajax 操作返回的文件，使用`XMLHttpRequest`物件的`responseXML`屬性。
- 內部節點的`ownerDocument`屬性。

`document`物件繼承了`EventTarget`介面和`Node`介面，並且混入（mixin）了`ParentNode`介面。這意味著，這些介面的方法都可以在`document`物件上呼叫。除此之外，`document`物件還有很多自己的屬性和方法。

## 屬性

### 快捷方式屬性

以下屬性是指向文件內部的某個節點的快捷方式。

**（1）document.defaultView**

`document.defaultView`屬性返回`document`物件所屬的`window`物件。如果當前文件不屬於`window`物件，該屬性返回`null`。

```javascript
document.defaultView === window // true
```

**（2）document.doctype**

對於 HTML 文件來說，`document`物件一般有兩個子節點。第一個子節點是`document.doctype`，指向`<DOCTYPE>`節點，即文件型別（Document Type Declaration，簡寫DTD）節點。HTML 的文件型別節點，一般寫成`<!DOCTYPE html>`。如果網頁沒有宣告 DTD，該屬性返回`null`。

```javascript
var doctype = document.doctype;
doctype // "<!DOCTYPE html>"
doctype.name // "html"
```

`document.firstChild`通常就返回這個節點。

**（3）document.documentElement**

`document.documentElement`屬性返回當前文件的根元素節點（root）。它通常是`document`節點的第二個子節點，緊跟在`document.doctype`節點後面。HTML網頁的該屬性，一般是`<html>`節點。

**（4）document.body，document.head**

`document.body`屬性指向`<body>`節點，`document.head`屬性指向`<head>`節點。

這兩個屬性總是存在的，如果網頁原始碼裡面省略了`<head>`或`<body>`，瀏覽器會自動建立。另外，這兩個屬性是可寫的，如果改寫它們的值，相當於移除所有子節點。

**（5）document.scrollingElement**

`document.scrollingElement`屬性返回文件的滾動元素。也就是說，當文件整體滾動時，到底是哪個元素在滾動。

標準模式下，這個屬性返回的文件的根元素`document.documentElement`（即`<html>`）。相容（quirk）模式下，返回的是`<body>`元素，如果該元素不存在，返回`null`。

```javascript
// 頁面滾動到瀏覽器頂部
document.scrollingElement.scrollTop = 0;
```

**（6）document.activeElement**

`document.activeElement`屬性返回獲得當前焦點（focus）的 DOM 元素。通常，這個屬性返回的是`<input>`、`<textarea>`、`<select>`等表單元素，如果當前沒有焦點元素，返回`<body>`元素或`null`。

**（7）document.fullscreenElement**

`document.fullscreenElement`屬性返回當前以全屏狀態展示的 DOM 元素。如果不是全屏狀態，該屬性返回`null`。

```javascript
if (document.fullscreenElement.nodeName == 'VIDEO') {
  console.log('全屏播放影片');
}
```

上面程式碼中，透過`document.fullscreenElement`可以知道`<video>`元素有沒有處在全屏狀態，從而判斷使用者行為。

### 節點集合屬性

以下屬性返回一個`HTMLCollection`例項，表示文件內部特定元素的集合。這些集合都是動態的，原節點有任何變化，立刻會反映在集合中。

**（1）document.links**

`document.links`屬性返回當前文件所有設定了`href`屬性的`<a>`及`<area>`節點。

```javascript
// 列印文件所有的連結
var links = document.links;
for(var i = 0; i < links.length; i++) {
  console.log(links[i]);
}
```

**（2）document.forms**

`document.forms`屬性返回所有`<form>`表單節點。

```javascript
var selectForm = document.forms[0];
```

上面程式碼獲取文件第一個表單。

除了使用位置序號，`id`屬性和`name`屬性也可以用來引用表單。

```javascript
/* HTML 程式碼如下
  <form name="foo" id="bar"></form>
*/
document.forms[0] === document.forms.foo // true
document.forms.bar === document.forms.foo // true
```

**（3）document.images**

`document.images`屬性返回頁面所有`<img>`圖片節點。

```javascript
var imglist = document.images;

for(var i = 0; i < imglist.length; i++) {
  if (imglist[i].src === 'banner.gif') {
    // ...
  }
}
```

上面程式碼在所有`img`標籤中，尋找某張圖片。

**（4）document.embeds，document.plugins**

`document.embeds`屬性和`document.plugins`屬性，都返回所有`<embed>`節點。

**（5）document.scripts**

`document.scripts`屬性返回所有`<script>`節點。

```javascript
var scripts = document.scripts;
if (scripts.length !== 0 ) {
  console.log('當前網頁有指令碼');
}
```

**（6）document.styleSheets**

`document.styleSheets`屬性返回文件內嵌或引入的樣式表集合，詳細介紹請看《CSS 物件模型》一章。

**（7）小結**

除了`document.styleSheets`，以上的集合屬性返回的都是`HTMLCollection`例項。

```javascript
document.links instanceof HTMLCollection // true
document.images instanceof HTMLCollection // true
document.forms instanceof HTMLCollection // true
document.embeds instanceof HTMLCollection // true
document.scripts instanceof HTMLCollection // true
```

`HTMLCollection`例項是類似陣列的物件，所以這些屬性都有`length`屬性，都可以使用方括號運算子引用成員。如果成員有`id`或`name`屬性，還可以用這兩個屬性的值，在`HTMLCollection`例項上引用到這個成員。

```javascript
// HTML 程式碼如下
// <form name="myForm">
document.myForm === document.forms.myForm // true
```

### 文件靜態資訊屬性

以下屬性返回文件資訊。

**（1）document.documentURI，document.URL**

`document.documentURI`屬性和`document.URL`屬性都返回一個字串，表示當前文件的網址。不同之處是它們繼承自不同的介面，`documentURI`繼承自`Document`介面，可用於所有文件；`URL`繼承自`HTMLDocument`介面，只能用於 HTML 文件。

```javascript
document.URL
// http://www.example.com/about

document.documentURI === document.URL
// true
```

如果文件的錨點（`#anchor`）變化，這兩個屬性都會跟著變化。

**（2）document.domain**

`document.domain`屬性返回當前文件的域名，不包含協議和埠。比如，網頁的網址是`http://www.example.com:80/hello.html`，那麼`document.domain`屬性就等於`www.example.com`。如果無法獲取域名，該屬性返回`null`。

`document.domain`基本上是一個只讀屬性，只有一種情況除外。次級域名的網頁，可以把`document.domain`設為對應的上級域名。比如，當前域名是`a.sub.example.com`，則`document.domain`屬性可以設定為`sub.example.com`，也可以設為`example.com`。修改後，`document.domain`相同的兩個網頁，可以讀取對方的資源，比如設定的 Cookie。

另外，設定`document.domain`會導致埠被改成`null`。因此，如果透過設定`document.domain`來進行通訊，雙方網頁都必須設定這個值，才能保證埠相同。

**（3）document.location**

`Location`物件是瀏覽器提供的原生物件，提供 URL 相關的資訊和操作方法。透過`window.location`和`document.location`屬性，可以拿到這個物件。

關於這個物件的詳細介紹，請看《瀏覽器模型》部分的《Location 物件》章節。

**（4）document.lastModified**

`document.lastModified`屬性返回一個字串，表示當前文件最後修改的時間。不同瀏覽器的返回值，日期格式是不一樣的。

```javascript
document.lastModified
// "03/07/2018 11:18:27"
```

注意，`document.lastModified`屬性的值是字串，所以不能直接用來比較。`Date.parse`方法將其轉為`Date`例項，才能比較兩個網頁。

```javascript
var lastVisitedDate = Date.parse('01/01/2018');
if (Date.parse(document.lastModified) > lastVisitedDate) {
  console.log('網頁已經變更');
}
```

如果頁面上有 JavaScript 生成的內容，`document.lastModified`屬性返回的總是當前時間。

**（5）document.title**

`document.title`屬性返回當前文件的標題。預設情況下，返回`<title>`節點的值。但是該屬性是可寫的，一旦被修改，就返回修改後的值。

```javascript
document.title = '新標題';
document.title // "新標題"
```

**（6）document.characterSet**

`document.characterSet`屬性返回當前文件的編碼，比如`UTF-8`、`ISO-8859-1`等等。

**（7）document.referrer**

`document.referrer`屬性返回一個字串，表示當前文件的訪問者來自哪裡。

```javascript
document.referrer
// "https://example.com/path"
```

如果無法獲取來源，或者使用者直接鍵入網址而不是從其他網頁點選進入，`document.referrer`返回一個空字串。

`document.referrer`的值，總是與 HTTP 頭資訊的`Referer`欄位保持一致。但是，`document.referrer`的拼寫有兩個`r`，而頭資訊的`Referer`欄位只有一個`r`。

**（8）document.dir**

`document.dir`返回一個字串，表示文字方向。它只有兩個可能的值：`rtl`表示文字從右到左，阿拉伯文是這種方式；`ltr`表示文字從左到右，包括英語和漢語在內的大多數文字採用這種方式。

**（9）document.compatMode**

`compatMode`屬性返回瀏覽器處理文件的模式，可能的值為`BackCompat`（向後相容模式）和`CSS1Compat`（嚴格模式）。

一般來說，如果網頁程式碼的第一行設定了明確的`DOCTYPE`（比如`<!doctype html>`），`document.compatMode`的值都為`CSS1Compat`。

### 文件狀態屬性

**（1）document.hidden**

`document.hidden`屬性返回一個布林值，表示當前頁面是否可見。如果視窗最小化、瀏覽器切換了 Tab，都會導致導致頁面不可見，使得`document.hidden`返回`true`。

這個屬性是 Page Visibility API 引入的，一般都是配合這個 API 使用。

**（2）document.visibilityState**

`document.visibilityState`返回文件的可見狀態。

它的值有四種可能。

> - `visible`：頁面可見。注意，頁面可能是部分可見，即不是焦點視窗，前面被其他視窗部分擋住了。
> - `hidden`：頁面不可見，有可能視窗最小化，或者瀏覽器切換到了另一個 Tab。
> - `prerender`：頁面處於正在渲染狀態，對於使用者來說，該頁面不可見。
> - `unloaded`：頁面從記憶體裡面解除安裝了。

這個屬性可以用在頁面載入時，防止載入某些資源；或者頁面不可見時，停掉一些頁面功能。

**（3）document.readyState**

`document.readyState`屬性返回當前文件的狀態，共有三種可能的值。

- `loading`：載入 HTML 程式碼階段（尚未完成解析）
- `interactive`：載入外部資源階段
- `complete`：載入完成

這個屬性變化的過程如下。

1. 瀏覽器開始解析 HTML 文件，`document.readyState`屬性等於`loading`。
1. 瀏覽器遇到 HTML 文件中的`<script>`元素，並且沒有`async`或`defer`屬性，就暫停解析，開始執行指令碼，這時`document.readyState`屬性還是等於`loading`。
1. HTML 文件解析完成，`document.readyState`屬性變成`interactive`。
1. 瀏覽器等待圖片、樣式表、字型檔案等外部資源載入完成，一旦全部載入完成，`document.readyState`屬性變成`complete`。

下面的程式碼用來檢查網頁是否載入成功。

```javascript
// 基本檢查
if (document.readyState === 'complete') {
  // ...
}

// 輪詢檢查
var interval = setInterval(function() {
  if (document.readyState === 'complete') {
    clearInterval(interval);
    // ...
  }
}, 100);
```

另外，每次狀態變化都會觸發一個`readystatechange`事件。

### document.cookie

`document.cookie`屬性用來操作瀏覽器 Cookie，詳見《瀏覽器模型》部分的《Cookie》章節。

### document.designMode

`document.designMode`屬性控制當前文件是否可編輯。該屬性只有兩個值`on`和`off`，預設值為`off`。一旦設為`on`，使用者就可以編輯整個文件的內容。

下面程式碼開啟`iframe`元素內部文件的`designMode`屬性，就能將其變為一個所見即所得的編輯器。

```javascript
// HTML 程式碼如下
// <iframe id="editor" src="about:blank"></iframe>
var editor = document.getElementById('editor');
editor.contentDocument.designMode = 'on';
```

### document.currentScript

`document.currentScript`屬性只用在`<script>`元素的內嵌指令碼或載入的外部指令碼之中，返回當前指令碼所在的那個 DOM 節點，即`<script>`元素的 DOM 節點。

```html
<script id="foo">
  console.log(
    document.currentScript === document.getElementById('foo')
  ); // true
</script>
```

上面程式碼中，`document.currentScript`就是`<script>`元素節點。

### document.implementation

`document.implementation`屬性返回一個`DOMImplementation`物件。該物件有三個方法，主要用於建立獨立於當前文件的新的 Document 物件。

- `DOMImplementation.createDocument()`：建立一個 XML 文件。
- `DOMImplementation.createHTMLDocument()`：建立一個 HTML 文件。
- `DOMImplementation.createDocumentType()`：建立一個 DocumentType 物件。

下面是建立 HTML 文件的例子。

```javascript
var doc = document.implementation.createHTMLDocument('Title');
var p = doc.createElement('p');
p.innerHTML = 'hello world';
doc.body.appendChild(p);

document.replaceChild(
  doc.documentElement,
  document.documentElement
);
```

上面程式碼中，第一步生成一個新的 HTML 文件`doc`，然後用它的根元素`document.documentElement`替換掉`document.documentElement`。這會使得當前文件的內容全部消失，變成`hello world`。

## 方法

### document.open()，document.close()

`document.open`方法清除當前文件所有內容，使得文件處於可寫狀態，供`document.write`方法寫入內容。

`document.close`方法用來關閉`document.open()`開啟的文件。

```javascript
document.open();
document.write('hello world');
document.close();
```

### document.write()，document.writeln()

`document.write`方法用於向當前文件寫入內容。

在網頁的首次渲染階段，只要頁面沒有關閉寫入（即沒有執行`document.close()`），`document.write`寫入的內容就會追加在已有內容的後面。

```javascript
// 頁面顯示“helloworld”
document.open();
document.write('hello');
document.write('world');
document.close();
```

注意，`document.write`會當作 HTML 程式碼解析，不會轉義。

```javascript
document.write('<p>hello world</p>');
```

上面程式碼中，`document.write`會將`<p>`當作 HTML 標籤解釋。

如果頁面已經解析完成（`DOMContentLoaded`事件發生之後），再呼叫`write`方法，它會先呼叫`open`方法，擦除當前文件所有內容，然後再寫入。

```javascript
document.addEventListener('DOMContentLoaded', function (event) {
  document.write('<p>Hello World!</p>');
});

// 等同於
document.addEventListener('DOMContentLoaded', function (event) {
  document.open();
  document.write('<p>Hello World!</p>');
  document.close();
});
```

如果在頁面渲染過程中呼叫`write`方法，並不會自動呼叫`open`方法。（可以理解成，`open`方法已呼叫，但`close`方法還未呼叫。）

```html
<html>
<body>
hello
<script type="text/javascript">
  document.write("world")
</script>
</body>
</html>
```

在瀏覽器開啟上面網頁，將會顯示`hello world`。

`document.write`是 JavaScript 語言標準化之前就存在的方法，現在完全有更符合標準的方法向文件寫入內容（比如對`innerHTML`屬性賦值）。所以，除了某些特殊情況，應該儘量避免使用`document.write`這個方法。

`document.writeln`方法與`write`方法完全一致，除了會在輸出內容的尾部新增換行符。

```javascript
document.write(1);
document.write(2);
// 12

document.writeln(1);
document.writeln(2);
// 1
// 2
//
```

注意，`writeln`方法新增的是 ASCII 碼的換行符，渲染成 HTML 網頁時不起作用，即在網頁上顯示不出換行。網頁上的換行，必須顯式寫入`<br>`。

### document.querySelector()，document.querySelectorAll()

`document.querySelector`方法接受一個 CSS 選擇器作為引數，返回匹配該選擇器的元素節點。如果有多個節點滿足匹配條件，則返回第一個匹配的節點。如果沒有發現匹配的節點，則返回`null`。

```javascript
var el1 = document.querySelector('.myclass');
var el2 = document.querySelector('#myParent > [ng-click]');
```

`document.querySelectorAll`方法與`querySelector`用法類似，區別是返回一個`NodeList`物件，包含所有匹配給定選擇器的節點。

```javascript
elementList = document.querySelectorAll('.myclass');
```

這兩個方法的引數，可以是逗號分隔的多個 CSS 選擇器，返回匹配其中一個選擇器的元素節點，這與 CSS 選擇器的規則是一致的。

```javascript
var matches = document.querySelectorAll('div.note, div.alert');
```

上面程式碼返回`class`屬性是`note`或`alert`的`div`元素。

這兩個方法都支援複雜的 CSS 選擇器。

```javascript
// 選中 data-foo-bar 屬性等於 someval 的元素
document.querySelectorAll('[data-foo-bar="someval"]');

// 選中 myForm 表單中所有不透過驗證的元素
document.querySelectorAll('#myForm :invalid');

// 選中div元素，那些 class 含 ignore 的除外
document.querySelectorAll('DIV:not(.ignore)');

// 同時選中 div，a，script 三類元素
document.querySelectorAll('DIV, A, SCRIPT');
```

但是，它們不支援 CSS 偽元素的選擇器（比如`:first-line`和`:first-letter`）和偽類的選擇器（比如`:link`和`:visited`），即無法選中偽元素和偽類。

如果`querySelectorAll`方法的引數是字串`*`，則會返回文件中的所有元素節點。另外，`querySelectorAll`的返回結果不是動態集合，不會實時反映元素節點的變化。

最後，這兩個方法除了定義在`document`物件上，還定義在元素節點上，即在元素節點上也可以呼叫。

### document.getElementsByTagName()

`document.getElementsByTagName()`方法搜尋 HTML 標籤名，返回符合條件的元素。它的返回值是一個類似陣列物件（`HTMLCollection`例項），可以實時反映 HTML 文件的變化。如果沒有任何匹配的元素，就返回一個空集。

```javascript
var paras = document.getElementsByTagName('p');
paras instanceof HTMLCollection // true
```

上面程式碼返回當前文件的所有`p`元素節點。

HTML 標籤名是大小寫不敏感的，因此`getElementsByTagName()`方法的引數也是大小寫不敏感的。另外，返回結果中，各個成員的順序就是它們在文件中出現的順序。

如果傳入`*`，就可以返回文件中所有 HTML 元素。

```javascript
var allElements = document.getElementsByTagName('*');
```

注意，元素節點本身也定義了`getElementsByTagName`方法，返回該元素的後代元素中符合條件的元素。也就是說，這個方法不僅可以在`document`物件上呼叫，也可以在任何元素節點上呼叫。

```javascript
var firstPara = document.getElementsByTagName('p')[0];
var spans = firstPara.getElementsByTagName('span');
```

上面程式碼選中第一個`p`元素內部的所有`span`元素。

### document.getElementsByClassName()

`document.getElementsByClassName()`方法返回一個類似陣列的物件（`HTMLCollection`例項），包括了所有`class`名字符合指定條件的元素，元素的變化實時反映在返回結果中。

```javascript
var elements = document.getElementsByClassName(names);
```

由於`class`是保留字，所以 JavaScript 一律使用`className`表示 CSS 的`class`。

引數可以是多個`class`，它們之間使用空格分隔。

```javascript
var elements = document.getElementsByClassName('foo bar');
```

上面程式碼返回同時具有`foo`和`bar`兩個`class`的元素，`foo`和`bar`的順序不重要。

注意，正常模式下，CSS 的`class`是大小寫敏感的。（`quirks mode`下，大小寫不敏感。）

與`getElementsByTagName()`方法一樣，`getElementsByClassName()`方法不僅可以在`document`物件上呼叫，也可以在任何元素節點上呼叫。

```javascript
// 非document物件上呼叫
var elements = rootElement.getElementsByClassName(names);
```

### document.getElementsByName()

`document.getElementsByName()`方法用於選擇擁有`name`屬性的 HTML 元素（比如`<form>`、`<radio>`、`<img>`、`<frame>`、`<embed>`和`<object>`等），返回一個類似陣列的的物件（`NodeList`例項），因為`name`屬性相同的元素可能不止一個。

```javascript
// 表單為 <form name="x"></form>
var forms = document.getElementsByName('x');
forms[0].tagName // "FORM"
```

### document.getElementById()

`document.getElementById()`方法返回匹配指定`id`屬性的元素節點。如果沒有發現匹配的節點，則返回`null`。

```javascript
var elem = document.getElementById('para1');
```

注意，該方法的引數是大小寫敏感的。比如，如果某個節點的`id`屬性是`main`，那麼`document.getElementById('Main')`將返回`null`。

`document.getElementById()`方法與`document.querySelector()`方法都能獲取元素節點，不同之處是`document.querySelector()`方法的引數使用 CSS 選擇器語法，`document.getElementById()`方法的引數是元素的`id`屬性。

```javascript
document.getElementById('myElement')
document.querySelector('#myElement')
```

上面程式碼中，兩個方法都能選中`id`為`myElement`的元素，但是`document.getElementById()`比`document.querySelector()`效率高得多。

另外，這個方法只能在`document`物件上使用，不能在其他元素節點上使用。

### document.elementFromPoint()，document.elementsFromPoint()

`document.elementFromPoint()`方法返回位於頁面指定位置最上層的元素節點。

```javascript
var element = document.elementFromPoint(50, 50);
```

上面程式碼選中在`(50, 50)`這個座標位置的最上層的那個 HTML 元素。

`elementFromPoint`方法的兩個引數，依次是相對於當前視口左上角的橫座標和縱座標，單位是畫素。如果位於該位置的 HTML 元素不可返回（比如文字框的捲軸），則返回它的父元素（比如文字框）。如果座標值無意義（比如負值或超過視口大小），則返回`null`。

`document.elementsFromPoint()`返回一個數組，成員是位於指定座標（相對於視口）的所有元素。

```javascript
var elements = document.elementsFromPoint(x, y);
```

### document.createElement()

`document.createElement`方法用來生成元素節點，並返回該節點。

```javascript
var newDiv = document.createElement('div');
```

`createElement`方法的引數為元素的標籤名，即元素節點的`tagName`屬性，對於 HTML 網頁大小寫不敏感，即引數為`div`或`DIV`返回的是同一種節點。如果引數裡面包含尖括號（即`<`和`>`）會報錯。

```javascript
document.createElement('<div>');
// DOMException: The tag name provided ('<div>') is not a valid name
```

注意，`document.createElement`的引數可以是自定義的標籤名。

```javascript
document.createElement('foo');
```

### document.createTextNode()

`document.createTextNode`方法用來生成文字節點（`Text`例項），並返回該節點。它的引數是文字節點的內容。

```javascript
var newDiv = document.createElement('div');
var newContent = document.createTextNode('Hello');
newDiv.appendChild(newContent);
```

上面程式碼新建一個`div`節點和一個文字節點，然後將文字節點插入`div`節點。

這個方法可以確保返回的節點，被瀏覽器當作文字渲染，而不是當作 HTML 程式碼渲染。因此，可以用來展示使用者的輸入，避免 XSS 攻擊。

```javascript
var div = document.createElement('div');
div.appendChild(document.createTextNode('<span>Foo & bar</span>'));
console.log(div.innerHTML)
// &lt;span&gt;Foo &amp; bar&lt;/span&gt;
```

上面程式碼中，`createTextNode`方法對大於號和小於號進行轉義，從而保證即使使用者輸入的內容包含惡意程式碼，也能正確顯示。

需要注意的是，該方法不對單引號和雙引號轉義，所以不能用來對 HTML 屬性賦值。

```html
function escapeHtml(str) {
  var div = document.createElement('div');
  div.appendChild(document.createTextNode(str));
  return div.innerHTML;
};

var userWebsite = '" onmouseover="alert(\'derp\')" "';
var profileLink = '<a href="' + escapeHtml(userWebsite) + '">Bob</a>';
var div = document.getElementById('target');
div.innerHTML = profileLink;
// <a href="" onmouseover="alert('derp')" "">Bob</a>
```

上面程式碼中，由於`createTextNode`方法不轉義雙引號，導致`onmouseover`方法被注入了程式碼。

### document.createAttribute()

`document.createAttribute`方法生成一個新的屬性節點（`Attr`例項），並返回它。

```javascript
var attribute = document.createAttribute(name);
```

`document.createAttribute`方法的引數`name`，是屬性的名稱。

```javascript
var node = document.getElementById('div1');

var a = document.createAttribute('my_attrib');
a.value = 'newVal';

node.setAttributeNode(a);
// 或者
node.setAttribute('my_attrib', 'newVal');
```

上面程式碼為`div1`節點，插入一個值為`newVal`的`my_attrib`屬性。

### document.createComment()

`document.createComment`方法生成一個新的註釋節點，並返回該節點。

```javascript
var CommentNode = document.createComment(data);
```

`document.createComment`方法的引數是一個字串，會成為註釋節點的內容。

### document.createDocumentFragment()

`document.createDocumentFragment`方法生成一個空的文件片段物件（`DocumentFragment`例項）。

```javascript
var docFragment = document.createDocumentFragment();
```

`DocumentFragment`是一個存在於記憶體的 DOM 片段，不屬於當前文件，常常用來生成一段較複雜的 DOM 結構，然後再插入當前文件。這樣做的好處在於，因為`DocumentFragment`不屬於當前文件，對它的任何改動，都不會引發網頁的重新渲染，比直接修改當前文件的 DOM 有更好的效能表現。

```javascript
var docfrag = document.createDocumentFragment();

[1, 2, 3, 4].forEach(function (e) {
  var li = document.createElement('li');
  li.textContent = e;
  docfrag.appendChild(li);
});

var element  = document.getElementById('ul');
element.appendChild(docfrag);
```

上面程式碼中，文件片斷`docfrag`包含四個`<li>`節點，這些子節點被一次性插入了當前文件。

### document.createEvent()

`document.createEvent`方法生成一個事件物件（`Event`例項），該物件可以被`element.dispatchEvent`方法使用，觸發指定事件。

```javascript
var event = document.createEvent(type);
```

`document.createEvent`方法的引數是事件型別，比如`UIEvents`、`MouseEvents`、`MutationEvents`、`HTMLEvents`。

```javascript
var event = document.createEvent('Event');
event.initEvent('build', true, true);
document.addEventListener('build', function (e) {
  console.log(e.type); // "build"
}, false);
document.dispatchEvent(event);
```

上面程式碼新建了一個名為`build`的事件例項，然後觸發該事件。

### document.addEventListener()，document.removeEventListener()，document.dispatchEvent()

這三個方法用於處理`document`節點的事件。它們都繼承自`EventTarget`介面，詳細介紹參見《EventTarget 介面》一章。

```javascript
// 新增事件監聽函式
document.addEventListener('click', listener, false);

// 移除事件監聽函式
document.removeEventListener('click', listener, false);

// 觸發事件
var event = new Event('click');
document.dispatchEvent(event);
```

### document.hasFocus()

`document.hasFocus`方法返回一個布林值，表示當前文件之中是否有元素被啟用或獲得焦點。

```javascript
var focused = document.hasFocus();
```

注意，有焦點的文件必定被啟用（active），反之不成立，啟用的文件未必有焦點。比如，使用者點選按鈕，從當前視窗跳出一個新視窗，該新視窗就是啟用的，但是不擁有焦點。

### document.adoptNode()，document.importNode()

`document.adoptNode`方法將某個節點及其子節點，從原來所在的文件或`DocumentFragment`裡面移除，歸屬當前`document`物件，返回插入後的新節點。插入的節點物件的`ownerDocument`屬性，會變成當前的`document`物件，而`parentNode`屬性是`null`。

```javascript
var node = document.adoptNode(externalNode);
document.appendChild(node);
```

注意，`document.adoptNode`方法只是改變了節點的歸屬，並沒有將這個節點插入新的文件樹。所以，還要再用`appendChild`方法或`insertBefore`方法，將新節點插入當前文件樹。

`document.importNode`方法則是從原來所在的文件或`DocumentFragment`裡面，複製某個節點及其子節點，讓它們歸屬當前`document`物件。複製的節點物件的`ownerDocument`屬性，會變成當前的`document`物件，而`parentNode`屬性是`null`。

```javascript
var node = document.importNode(externalNode, deep);
```

`document.importNode`方法的第一個引數是外部節點，第二個引數是一個布林值，表示對外部節點是深複製還是淺複製，預設是淺複製（false）。雖然第二個引數是可選的，但是建議總是保留這個引數，並設為`true`。

注意，`document.importNode`方法只是複製外部節點，這時該節點的父節點是`null`。下一步還必須將這個節點插入當前文件樹。

```javascript
var iframe = document.getElementsByTagName('iframe')[0];
var oldNode = iframe.contentWindow.document.getElementById('myNode');
var newNode = document.importNode(oldNode, true);
document.getElementById("container").appendChild(newNode);
```

上面程式碼從`iframe`視窗，複製一個指定節點`myNode`，插入當前文件。

### document.createNodeIterator()

`document.createNodeIterator`方法返回一個子節點遍歷器。

```javascript
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);
```

上面程式碼返回`<body>`元素子節點的遍歷器。

`document.createNodeIterator`方法第一個引數為所要遍歷的根節點，第二個引數為所要遍歷的節點型別，這裡指定為元素節點（`NodeFilter.SHOW_ELEMENT`）。幾種主要的節點型別寫法如下。

- 所有節點：NodeFilter.SHOW_ALL
- 元素節點：NodeFilter.SHOW_ELEMENT
- 文字節點：NodeFilter.SHOW_TEXT
- 評論節點：NodeFilter.SHOW_COMMENT

`document.createNodeIterator`方法返回一個“遍歷器”物件（`NodeFilter`例項）。該例項的`nextNode()`方法和`previousNode()`方法，可以用來遍歷所有子節點。

```javascript
var nodeIterator = document.createNodeIterator(document.body);
var pars = [];
var currentNode;

while (currentNode = nodeIterator.nextNode()) {
  pars.push(currentNode);
}
```

上面程式碼中，使用遍歷器的`nextNode`方法，將根節點的所有子節點，依次讀入一個數組。`nextNode`方法先返回遍歷器的內部指標所在的節點，然後會將指標移向下一個節點。所有成員遍歷完成後，返回`null`。`previousNode`方法則是先將指標移向上一個節點，然後返回該節點。

```javascript
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var currentNode = nodeIterator.nextNode();
var previousNode = nodeIterator.previousNode();

currentNode === previousNode // true
```

上面程式碼中，`currentNode`和`previousNode`都指向同一個的節點。

注意，遍歷器返回的第一個節點，總是根節點。

```javascript
pars[0] === document.body // true
```

### document.createTreeWalker()

`document.createTreeWalker`方法返回一個 DOM 的子樹遍歷器。它與`document.createNodeIterator`方法基本是類似的，區別在於它返回的是`TreeWalker`例項，後者返回的是`NodeIterator`例項。另外，它的第一個節點不是根節點。

`document.createTreeWalker`方法的第一個引數是所要遍歷的根節點，第二個引數指定所要遍歷的節點型別（與`document.createNodeIterator`方法的第二個引數相同）。

```javascript
var treeWalker = document.createTreeWalker(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var nodeList = [];

while(treeWalker.nextNode()) {
  nodeList.push(treeWalker.currentNode);
}
```

上面程式碼遍歷`<body>`節點下屬的所有元素節點，將它們插入`nodeList`陣列。

### document.execCommand()，document.queryCommandSupported()，document.queryCommandEnabled()

**（1）document.execCommand()**

如果`document.designMode`屬性設為`on`，那麼整個文件使用者可編輯；如果元素的`contenteditable`屬性設為`true`，那麼該元素可編輯。這兩種情況下，可以使用`document.execCommand()`方法，改變內容的樣式，比如`document.execCommand('bold')`會使得字型加粗。

```javascript
document.execCommand(command, showDefaultUI, input)
```

該方法接受三個引數。

- `command`：字串，表示所要實施的樣式。
- `showDefaultUI`：布林值，表示是否要使用預設的使用者介面，建議總是設為`false`。
- `input`：字串，表示該樣式的輔助內容，比如生成超級連結時，這個引數就是所要連結的網址。如果第二個引數設為`true`，那麼瀏覽器會彈出提示框，要求使用者在提示框輸入該引數。但是，不是所有瀏覽器都支援這樣做，為了相容性，還是需要自己部署獲取這個引數的方式。

```javascript
var url = window.prompt('請輸入網址');

if (url) {
  document.execCommand('createlink', false, url);
}
```

上面程式碼中，先提示使用者輸入所要連結的網址，然後手動生成超級連結。注意，第二個引數是`false`，表示此時不需要自動彈出提示框。

`document.execCommand()`的返回值是一個布林值。如果為`false`，表示這個方法無法生效。

這個方法大部分情況下，只對選中的內容生效。如果有多個內容可編輯區域，那麼只對當前焦點所在的元素生效。

`document.execCommand()`方法可以執行的樣式改變有很多種，下面是其中的一些：bold、insertLineBreak、selectAll、createLink、insertOrderedList、subscript、delete、insertUnorderedList、superscript、formatBlock、insertParagraph、undo、forwardDelete、insertText、unlink、insertImage、italic、unselect、insertHTML、redo。這些值都可以用作第一個引數，它們的含義不難從字面上看出來。

**（2）document.queryCommandSupported()**

`document.queryCommandSupported()`方法返回一個布林值，表示瀏覽器是否支援`document.execCommand()`的某個命令。

```javascript
if (document.queryCommandSupported('SelectAll')) {
  console.log('瀏覽器支援選中可編輯區域的所有內容');
}
```

**（3）document.queryCommandEnabled()**

`document.queryCommandEnabled()`方法返回一個布林值，表示當前是否可用`document.execCommand()`的某個命令。比如，`bold`（加粗）命令只有存在文字選中時才可用，如果沒有選中文字，就不可用。

```javascript
// HTML 程式碼為
// <input type="button" value="Copy" onclick="doCopy()">

function doCopy(){
  // 瀏覽器是否支援 copy 命令（選中內容複製到剪貼簿）
  if (document.queryCommandSupported('copy')) {
    copyText('你好');
  }else{
    console.log('瀏覽器不支援');
  }
}

function copyText(text) {
  var input = document.createElement('textarea');
  document.body.appendChild(input);
  input.value = text;
  input.focus();
  input.select();

  // 當前是否有選中文字
  if (document.queryCommandEnabled('copy')) {
    var success = document.execCommand('copy');
    input.remove();
    console.log('Copy Ok');
  } else {
    console.log('queryCommandEnabled is false');
  }
}
```

上面程式碼中，先判斷瀏覽器是否支援`copy`命令（允許可編輯區域的選中內容，複製到剪貼簿），如果支援，就新建一個臨時文字框，裡面寫入內容“你好”，並將其選中。然後，判斷是否選中成功，如果成功，就將“你好”複製到剪貼簿，再刪除那個臨時文字框。

### document.getSelection()

這個方法指向`window.getSelection()`，參見`window`物件一節的介紹。

