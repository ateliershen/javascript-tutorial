# Element 節點

## 簡介

`Element`節點物件對應網頁的 HTML 元素。每一個 HTML 元素，在 DOM 樹上都會轉化成一個`Element`節點物件（以下簡稱元素節點）。

元素節點的`nodeType`屬性都是`1`。

```javascript
var p = document.querySelector('p');
p.nodeName // "P"
p.nodeType // 1
```

`Element`物件繼承了`Node`介面，因此`Node`的屬性和方法在`Element`物件都存在。

此外，不同的 HTML 元素對應的元素節點是不一樣的，瀏覽器使用不同的建構函式，生成不同的元素節點，比如`<a>`元素的建構函式是`HTMLAnchorElement()`，`<button>`是`HTMLButtonElement()`。因此，元素節點不是一種物件，而是許多種物件，這些物件除了繼承`Element`物件的屬性和方法，還有各自獨有的屬性和方法。

## 例項屬性

### 元素特性的相關屬性

**（1）Element.id**

`Element.id`屬性返回指定元素的`id`屬性，該屬性可讀寫。

```javascript
// HTML 程式碼為 <p id="foo">
var p = document.querySelector('p');
p.id // "foo"
```

注意，`id`屬性的值是大小寫敏感，即瀏覽器能正確識別`<p id="foo">`和`<p id="FOO">`這兩個元素的`id`屬性，但是最好不要這樣命名。

**（2）Element.tagName**

`Element.tagName`屬性返回指定元素的大寫標籤名，與`nodeName`屬性的值相等。

```javascript
// HTML程式碼為
// <span id="myspan">Hello</span>
var span = document.getElementById('myspan');
span.id // "myspan"
span.tagName // "SPAN"
```

**（3）Element.dir**

`Element.dir`屬性用於讀寫當前元素的文字方向，可能是從左到右（`"ltr"`），也可能是從右到左（`"rtl"`）。

**（4）Element.accessKey**

`Element.accessKey`屬性用於讀寫分配給當前元素的快捷鍵。

```javascript
// HTML 程式碼如下
// <button accesskey="h" id="btn">點選</button>
var btn = document.getElementById('btn');
btn.accessKey // "h"
```

上面程式碼中，`btn`元素的快捷鍵是`h`，按下`Alt + h`就能將焦點轉移到它上面。

**（5）Element.draggable**

`Element.draggable`屬性返回一個布林值，表示當前元素是否可拖動。該屬性可讀寫。

**（6）Element.lang**

`Element.lang`屬性返回當前元素的語言設定。該屬性可讀寫。

```javascript
// HTML 程式碼如下
// <html lang="en">
document.documentElement.lang // "en"
```

**（7）Element.tabIndex**

`Element.tabIndex`屬性返回一個整數，表示當前元素在 Tab 鍵遍歷時的順序。該屬性可讀寫。

`tabIndex`屬性值如果是負值（通常是`-1`），則 Tab 鍵不會遍歷到該元素。如果是正整數，則按照順序，從小到大遍歷。如果兩個元素的`tabIndex`屬性的正整數值相同，則按照出現的順序遍歷。遍歷完所有`tabIndex`為正整數的元素以後，再遍歷所有`tabIndex`等於`0`、或者屬性值是非法值、或者沒有`tabIndex`屬性的元素，順序為它們在網頁中出現的順序。

**（8）Element.title**

`Element.title`屬性用來讀寫當前元素的 HTML 屬性`title`。該屬性通常用來指定，滑鼠懸浮時彈出的文字提示框。

### 元素狀態的相關屬性

**（1）Element.hidden**

`Element.hidden`屬性返回一個布林值，表示當前元素的`hidden`屬性，用來控制當前元素是否可見。該屬性可讀寫。

```javascript
var btn = document.getElementById('btn');
var mydiv = document.getElementById('mydiv');

btn.addEventListener('click', function () {
  mydiv.hidden = !mydiv.hidden;
}, false);
```

注意，該屬性與 CSS 設定是互相獨立的。CSS 對這個元素可見性的設定，`Element.hidden`並不能反映出來。也就是說，這個屬性並不能用來判斷當前元素的實際可見性。

CSS 的設定高於`Element.hidden`。如果 CSS 指定了該元素不可見（`display: none`）或可見（`display: hidden`），那麼`Element.hidden`並不能改變該元素實際的可見性。換言之，這個屬性只在 CSS 沒有明確設定當前元素的可見性時才有效。

**（2）Element.contentEditable，Element.isContentEditable**

HTML 元素可以設定`contentEditable`屬性，使得元素的內容可以編輯。

```html
<div contenteditable>123</div>
```

上面程式碼中，`<div>`元素有`contenteditable`屬性，因此使用者可以在網頁上編輯這個區塊的內容。

`Element.contentEditable`屬性返回一個字串，表示是否設定了`contenteditable`屬性，有三種可能的值。該屬性可寫。

- `"true"`：元素內容可編輯
- `"false"`：元素內容不可編輯
- `"inherit"`：元素是否可編輯，繼承了父元素的設定

`Element.isContentEditable`屬性返回一個布林值，同樣表示是否設定了`contenteditable`屬性。該屬性只讀。

### Element.attributes

`Element.attributes`屬性返回一個類似陣列的物件，成員是當前元素節點的所有屬性節點，詳見《屬性的操作》一章。

```javascript
var p = document.querySelector('p');
var attrs = p.attributes;

for (var i = attrs.length - 1; i >= 0; i--) {
  console.log(attrs[i].name + '->' + attrs[i].value);
}
```

上面程式碼遍歷`p`元素的所有屬性。

### Element.className，Element.classList

`className`屬性用來讀寫當前元素節點的`class`屬性。它的值是一個字串，每個`class`之間用空格分割。

`classList`屬性返回一個類似陣列的物件，當前元素節點的每個`class`就是這個物件的一個成員。

```javascript
// HTML 程式碼 <div class="one two three" id="myDiv"></div>
var div = document.getElementById('myDiv');

div.className
// "one two three"

div.classList
// {
//   0: "one"
//   1: "two"
//   2: "three"
//   length: 3
// }
```

上面程式碼中，`className`屬性返回一個空格分隔的字串，而`classList`屬性指向一個類似陣列的物件，該物件的`length`屬性（只讀）返回當前元素的`class`數量。

`classList`物件有下列方法。

- `add()`：增加一個 class。
- `remove()`：移除一個 class。
- `contains()`：檢查當前元素是否包含某個 class。
- `toggle()`：將某個 class 移入或移出當前元素。
- `item()`：返回指定索引位置的 class。
- `toString()`：將 class 的列表轉為字串。

```javascript
var div = document.getElementById('myDiv');

div.classList.add('myCssClass');
div.classList.add('foo', 'bar');
div.classList.remove('myCssClass');
div.classList.toggle('myCssClass'); // 如果 myCssClass 不存在就加入，否則移除
div.classList.contains('myCssClass'); // 返回 true 或者 false
div.classList.item(0); // 返回第一個 Class
div.classList.toString();
```

下面比較一下，`className`和`classList`在新增和刪除某個 class 時的寫法。

```javascript
var foo = document.getElementById('foo');

// 新增class
foo.className += 'bold';
foo.classList.add('bold');

// 刪除class
foo.classList.remove('bold');
foo.className = foo.className.replace(/^bold$/, '');
```

`toggle`方法可以接受一個布林值，作為第二個引數。如果為`true`，則新增該屬性；如果為`false`，則去除該屬性。

```javascript
el.classList.toggle('abc', boolValue);

// 等同於
if (boolValue) {
  el.classList.add('abc');
} else {
  el.classList.remove('abc');
}
```

### Element.dataset

網頁元素可以自定義`data-`屬性，用來新增資料。

```html
<div data-timestamp="1522907809292"></div>
```

上面程式碼中，`<div>`元素有一個自定義的`data-timestamp`屬性，用來為該元素新增一個時間戳。

`Element.dataset`屬性返回一個物件，可以從這個物件讀寫`data-`屬性。

```javascript
// <article
//   id="foo"
//   data-columns="3"
//   data-index-number="12314"
//   data-parent="cars">
//   ...
// </article>
var article = document.getElementById('foo');
article.dataset.columns // "3"
article.dataset.indexNumber // "12314"
article.dataset.parent // "cars"
```

注意，`dataset`上面的各個屬性返回都是字串。

HTML 程式碼中，`data-`屬性的屬性名，只能包含英文字母、數字、連詞線（`-`）、點（`.`）、冒號（`:`）和下劃線（`_`）。它們轉成 JavaScript 對應的`dataset`屬性名，規則如下。

- 開頭的`data-`會省略。
- 如果連詞線後面跟了一個英文字母，那麼連詞線會取消，該字母變成大寫。
- 其他字元不變。

因此，`data-abc-def`對應`dataset.abcDef`，`data-abc-1`對應`dataset["abc-1"]`。

除了使用`dataset`讀寫`data-`屬性，也可以使用`Element.getAttribute()`和`Element.setAttribute()`，透過完整的屬性名讀寫這些屬性。

```javascript
var mydiv = document.getElementById('mydiv');

mydiv.dataset.foo = 'bar';
mydiv.getAttribute('data-foo') // "bar"
```

### Element.innerHTML

`Element.innerHTML`屬性返回一個字串，等同於該元素包含的所有 HTML 程式碼。該屬性可讀寫，常用來設定某個節點的內容。它能改寫所有元素節點的內容，包括`<HTML>`和`<body>`元素。

如果將`innerHTML`屬性設為空，等於刪除所有它包含的所有節點。

```javascript
el.innerHTML = '';
```

上面程式碼等於將`el`節點變成了一個空節點，`el`原來包含的節點被全部刪除。

注意，讀取屬性值的時候，如果文字節點包含`&`、小於號（`<`）和大於號（`>`），`innerHTML`屬性會將它們轉為實體形式`&amp;`、`&lt;`、`&gt;`。如果想得到原文，建議使用`element.textContent`屬性。

```javascript
// HTML程式碼如下 <p id="para"> 5 > 3 </p>
document.getElementById('para').innerHTML
// 5 &gt; 3
```

寫入的時候，如果插入的文字包含 HTML 標籤，會被解析成為節點物件插入 DOM。注意，如果文字之中含有`<script>`標籤，雖然可以生成`script`節點，但是插入的程式碼不會執行。

```javascript
var name = "<script>alert('haha')</script>";
el.innerHTML = name;
```

上面程式碼將指令碼插入內容，指令碼並不會執行。但是，`innerHTML`還是有安全風險的。

```javascript
var name = "<img src=x onerror=alert(1)>";
el.innerHTML = name;
```

上面程式碼中，`alert`方法是會執行的。因此為了安全考慮，如果插入的是文字，最好用`textContent`屬性代替`innerHTML`。

### Element.outerHTML

`Element.outerHTML`屬性返回一個字串，表示當前元素節點的所有 HTML 程式碼，包括該元素本身和所有子元素。

```javascript
// HTML 程式碼如下
// <div id="d"><p>Hello</p></div>
var d = document.getElementById('d');
d.outerHTML
// '<div id="d"><p>Hello</p></div>'
```

`outerHTML`屬性是可讀寫的，對它進行賦值，等於替換掉當前元素。

```javascript
// HTML 程式碼如下
// <div id="container"><div id="d">Hello</div></div>
var container = document.getElementById('container');
var d = document.getElementById('d');
container.firstChild.nodeName // "DIV"
d.nodeName // "DIV"

d.outerHTML = '<p>Hello</p>';
container.firstChild.nodeName // "P"
d.nodeName // "DIV"
```

上面程式碼中，變數`d`代表子節點，它的`outerHTML`屬性重新賦值以後，內層的`div`元素就不存在了，被`p`元素替換了。但是，變數`d`依然指向原來的`div`元素，這表示被替換的`DIV`元素還存在於記憶體中。

注意，如果一個節點沒有父節點，設定`outerHTML`屬性會報錯。

```javascript
var div = document.createElement('div');
div.outerHTML = '<p>test</p>';
// DOMException: This element has no parent node.
```

上面程式碼中，`div`元素沒有父節點，設定`outerHTML`屬性會報錯。

### Element.clientHeight，Element.clientWidth

`Element.clientHeight`屬性返回一個整數值，表示元素節點的 CSS 高度（單位畫素），只對塊級元素生效，對於行內元素返回`0`。如果塊級元素沒有設定 CSS 高度，則返回實際高度。

除了元素本身的高度，它還包括`padding`部分，但是不包括`border`、`margin`。如果有水平捲軸，還要減去水平捲軸的高度。注意，這個值始終是整數，如果是小數會被四捨五入。

`Element.clientWidth`屬性返回元素節點的 CSS 寬度，同樣只對塊級元素有效，也是隻包括元素本身的寬度和`padding`，如果有垂直捲軸，還要減去垂直捲軸的寬度。

`document.documentElement`的`clientHeight`屬性，返回當前視口的高度（即瀏覽器視窗的高度），等同於`window.innerHeight`屬性減去水平捲軸的高度（如果有的話）。`document.body`的高度則是網頁的實際高度。一般來說，`document.body.clientHeight`大於`document.documentElement.clientHeight`。

```javascript
// 視口高度
document.documentElement.clientHeight

// 網頁總高度
document.body.clientHeight
```

### Element.clientLeft，Element.clientTop

`Element.clientLeft`屬性等於元素節點左邊框（left border）的寬度（單位畫素），不包括左側的`padding`和`margin`。如果沒有設定左邊框，或者是行內元素（`display: inline`），該屬性返回`0`。該屬性總是返回整數值，如果是小數，會四捨五入。

`Element.clientTop`屬性等於網頁元素頂部邊框的寬度（單位畫素），其他特點都與`clientLeft`相同。

### Element.scrollHeight，Element.scrollWidth

`Element.scrollHeight`屬性返回一個整數值（小數會四捨五入），表示當前元素的總高度（單位畫素），包括溢位容器、當前不可見的部分。它包括`padding`，但是不包括`border`、`margin`以及水平捲軸的高度（如果有水平捲軸的話），還包括偽元素（`::before`或`::after`）的高度。

`Element.scrollWidth`屬性表示當前元素的總寬度（單位畫素），其他地方都與`scrollHeight`屬性類似。這兩個屬性只讀。

整張網頁的總高度可以從`document.documentElement`或`document.body`上讀取。

```javascript
// 返回網頁的總高度
document.documentElement.scrollHeight
document.body.scrollHeight
```

注意，如果元素節點的內容出現溢位，即使溢位的內容是隱藏的，`scrollHeight`屬性仍然返回元素的總高度。

```javascript
// HTML 程式碼如下
// <div id="myDiv" style="height: 200px; overflow: hidden;">...<div>
document.getElementById('myDiv').scrollHeight // 356
```

上面程式碼中，即使`myDiv`元素的 CSS 高度只有200畫素，且溢位部分不可見，但是`scrollHeight`仍然會返回該元素的原始高度。

### Element.scrollLeft，Element.scrollTop

`Element.scrollLeft`屬性表示當前元素的水平捲軸向右側滾動的畫素數量，`Element.scrollTop`屬性表示當前元素的垂直捲軸向下滾動的畫素數量。對於那些沒有捲軸的網頁元素，這兩個屬性總是等於0。

如果要檢視整張網頁的水平的和垂直的滾動距離，要從`document.documentElement`元素上讀取。

```javascript
document.documentElement.scrollLeft
document.documentElement.scrollTop
```

這兩個屬性都可讀寫，設定該屬性的值，會導致瀏覽器將當前元素自動滾動到相應的位置。

### Element.offsetParent

`Element.offsetParent`屬性返回最靠近當前元素的、並且 CSS 的`position`屬性不等於`static`的上層元素。

```html
<div style="position: absolute;">
  <p>
    <span>Hello</span>
  </p>
</div>
```

上面程式碼中，`span`元素的`offsetParent`屬性就是`div`元素。

該屬性主要用於確定子元素位置偏移的計算基準，`Element.offsetTop`和`Element.offsetLeft`就是`offsetParent`元素計算的。

如果該元素是不可見的（`display`屬性為`none`），或者位置是固定的（`position`屬性為`fixed`），則`offsetParent`屬性返回`null`。

```html
<div style="position: absolute;">
  <p>
    <span style="display: none;">Hello</span>
  </p>
</div>
```

上面程式碼中，`span`元素的`offsetParent`屬性是`null`。

如果某個元素的所有上層節點的`position`屬性都是`static`，則`Element.offsetParent`屬性指向`<body>`元素。

### Element.offsetHeight，Element.offsetWidth

`Element.offsetHeight`屬性返回一個整數，表示元素的 CSS 垂直高度（單位畫素），包括元素本身的高度、padding 和 border，以及水平捲軸的高度（如果存在捲軸）。

`Element.offsetWidth`屬性表示元素的 CSS 水平寬度（單位畫素），其他都與`Element.offsetHeight`一致。

這兩個屬性都是隻讀屬性，只比`Element.clientHeight`和`Element.clientWidth`多了邊框的高度或寬度。如果元素的 CSS 設為不可見（比如`display: none;`），則返回`0`。

### Element.offsetLeft，Element.offsetTop

`Element.offsetLeft`返回當前元素左上角相對於`Element.offsetParent`節點的水平位移，`Element.offsetTop`返回垂直位移，單位為畫素。通常，這兩個值是指相對於父節點的位移。

下面的程式碼可以算出元素左上角相對於整張網頁的座標。

```javascript
function getElementPosition(e) {
  var x = 0;
  var y = 0;
  while (e !== null)  {
    x += e.offsetLeft;
    y += e.offsetTop;
    e = e.offsetParent;
  }
  return {x: x, y: y};
}
```

### Element.style

每個元素節點都有`style`用來讀寫該元素的行內樣式資訊，具體介紹參見《CSS 操作》一章。

### Element.children，Element.childElementCount

`Element.children`屬性返回一個類似陣列的物件（`HTMLCollection`例項），包括當前元素節點的所有子元素。如果當前元素沒有子元素，則返回的物件包含零個成員。

```javascript
if (para.children.length) {
  var children = para.children;
    for (var i = 0; i < children.length; i++) {
      // ...
    }
}
```

上面程式碼遍歷了`para`元素的所有子元素。

這個屬性與`Node.childNodes`屬性的區別是，它只包括元素型別的子節點，不包括其他型別的子節點。

`Element.childElementCount`屬性返回當前元素節點包含的子元素節點的個數，與`Element.children.length`的值相同。

### Element.firstElementChild，Element.lastElementChild

`Element.firstElementChild`屬性返回當前元素的第一個元素子節點，`Element.lastElementChild`返回最後一個元素子節點。

如果沒有元素子節點，這兩個屬性返回`null`。

### Element.nextElementSibling，Element.previousElementSibling

`Element.nextElementSibling`屬性返回當前元素節點的後一個同級元素節點，如果沒有則返回`null`。

```javascript
// HTML 程式碼如下
// <div id="div-01">Here is div-01</div>
// <div id="div-02">Here is div-02</div>
var el = document.getElementById('div-01');
el.nextElementSibling
// <div id="div-02">Here is div-02</div>
```

`Element.previousElementSibling`屬性返回當前元素節點的前一個同級元素節點，如果沒有則返回`null`。

## 例項方法

### 屬性相關方法

元素節點提供六個方法，用來操作屬性。

- `getAttribute()`：讀取某個屬性的值
- `getAttributeNames()`：返回當前元素的所有屬性名
- `setAttribute()`：寫入屬性值
- `hasAttribute()`：某個屬性是否存在
- `hasAttributes()`：當前元素是否有屬性
- `removeAttribute()`：刪除屬性

這些方法的介紹請看《屬性的操作》一章。

### Element.querySelector()

`Element.querySelector`方法接受 CSS 選擇器作為引數，返回父元素的第一個匹配的子元素。如果沒有找到匹配的子元素，就返回`null`。

```javascript
var content = document.getElementById('content');
var el = content.querySelector('p');
```

上面程式碼返回`content`節點的第一個`p`元素。

`Element.querySelector`方法可以接受任何複雜的 CSS 選擇器。

```javascript
document.body.querySelector("style[type='text/css'], style:not([type])");
```

注意，這個方法無法選中偽元素。

它可以接受多個選擇器，它們之間使用逗號分隔。

```javascript
element.querySelector('div, p')
```

上面程式碼返回`element`的第一個`div`或`p`子元素。

需要注意的是，瀏覽器執行`querySelector`方法時，是先在全域性範圍內搜尋給定的 CSS 選擇器，然後過濾出哪些屬於當前元素的子元素。因此，會有一些違反直覺的結果，下面是一段 HTML 程式碼。

```html
<div>
<blockquote id="outer">
  <p>Hello</p>
  <div id="inner">
    <p>World</p>
  </div>
</blockquote>
</div>
```

那麼，像下面這樣查詢的話，實際上返回的是第一個`p`元素，而不是第二個。

```javascript
var outer = document.getElementById('outer');
outer.querySelector('div p')
// <p>Hello</p>
```

### Element.querySelectorAll()

`Element.querySelectorAll`方法接受 CSS 選擇器作為引數，返回一個`NodeList`例項，包含所有匹配的子元素。

```javascript
var el = document.querySelector('#test');
var matches = el.querySelectorAll('div.highlighted > p');
```

該方法的執行機制與`querySelector`方法相同，也是先在全域性範圍內查詢，再過濾出當前元素的子元素。因此，選擇器實際上針對整個文件的。

它也可以接受多個 CSS 選擇器，它們之間使用逗號分隔。如果選擇器裡面有偽元素的選擇器，則總是返回一個空的`NodeList`例項。

### Element.getElementsByClassName()

`Element.getElementsByClassName`方法返回一個`HTMLCollection`例項，成員是當前元素節點的所有具有指定 class 的子元素節點。該方法與`document.getElementsByClassName`方法的用法類似，只是搜尋範圍不是整個文件，而是當前元素節點。

```javascript
element.getElementsByClassName('red test');
```

注意，該方法的引數大小寫敏感。

由於`HTMLCollection`例項是一個活的集合，`document`物件的任何變化會立刻反應到例項，下面的程式碼不會生效。

```javascript
// HTML 程式碼如下
// <div id="example">
//   <p class="foo"></p>
//   <p class="foo"></p>
// </div>
var element = document.getElementById('example');
var matches = element.getElementsByClassName('foo');

for (var i = 0; i< matches.length; i++) {
  matches[i].classList.remove('foo');
  matches.item(i).classList.add('bar');
}
// 執行後，HTML 程式碼如下
// <div id="example">
//   <p></p>
//   <p class="foo bar"></p>
// </div>
```

上面程式碼中，`matches`集合的第一個成員，一旦被拿掉 class 裡面的`foo`，就會立刻從`matches`裡面消失，導致出現上面的結果。

### Element.getElementsByTagName()

`Element.getElementsByTagName()`方法返回一個`HTMLCollection`例項，成員是當前節點的所有匹配指定標籤名的子元素節點。該方法與`document.getElementsByClassName()`方法的用法類似，只是搜尋範圍不是整個文件，而是當前元素節點。

```javascript
var table = document.getElementById('forecast-table');
var cells = table.getElementsByTagName('td');
```

注意，該方法的引數是大小寫不敏感的，因為 HTML 標籤名也是大小寫不敏感。

### Element.closest()

`Element.closest`方法接受一個 CSS 選擇器作為引數，返回匹配該選擇器的、最接近當前節點的一個祖先節點（包括當前節點本身）。如果沒有任何節點匹配 CSS 選擇器，則返回`null`。

```javascript
// HTML 程式碼如下
// <article>
//   <div id="div-01">Here is div-01
//     <div id="div-02">Here is div-02
//       <div id="div-03">Here is div-03</div>
//     </div>
//   </div>
// </article>

var div03 = document.getElementById('div-03');

// div-03 最近的祖先節點
div03.closest("#div-02") // div-02
div03.closest("div div") // div-03
div03.closest("article > div") //div-01
div03.closest(":not(div)") // article
```

上面程式碼中，由於`closest`方法將當前節點也考慮在內，所以第二個`closest`方法返回`div-03`。

### Element.matches()

`Element.matches`方法返回一個布林值，表示當前元素是否匹配給定的 CSS 選擇器。

```javascript
if (el.matches('.someClass')) {
  console.log('Match!');
}
```

### 事件相關方法

以下三個方法與`Element`節點的事件相關。這些方法都繼承自`EventTarget`介面，詳見相關章節。

- `Element.addEventListener()`：新增事件的回撥函式
- `Element.removeEventListener()`：移除事件監聽函式
- `Element.dispatchEvent()`：觸發事件

```javascript
element.addEventListener('click', listener, false);
element.removeEventListener('click', listener, false);

var event = new Event('click');
element.dispatchEvent(event);
```

### Element.scrollIntoView()

`Element.scrollIntoView`方法滾動當前元素，進入瀏覽器的可見區域，類似於設定`window.location.hash`的效果。

```javascript
el.scrollIntoView(); // 等同於el.scrollIntoView(true)
el.scrollIntoView(false);
```

該方法可以接受一個布林值作為引數。如果為`true`，表示元素的頂部與當前區域的可見部分的頂部對齊（前提是當前區域可滾動）；如果為`false`，表示元素的底部與當前區域的可見部分的尾部對齊（前提是當前區域可滾動）。如果沒有提供該引數，預設為`true`。

### Element.getBoundingClientRect()

`Element.getBoundingClientRect`方法返回一個物件，提供當前元素節點的大小、位置等資訊，基本上就是 CSS 盒狀模型的所有資訊。

```javascript
var rect = obj.getBoundingClientRect();
```

上面程式碼中，`getBoundingClientRect`方法返回的`rect`物件，具有以下屬性（全部為只讀）。

- `x`：元素左上角相對於視口的橫座標
- `y`：元素左上角相對於視口的縱座標
- `height`：元素高度
- `width`：元素寬度
- `left`：元素左上角相對於視口的橫座標，與`x`屬性相等
- `right`：元素右邊界相對於視口的橫座標（等於`x + width`）
- `top`：元素頂部相對於視口的縱座標，與`y`屬性相等
- `bottom`：元素底部相對於視口的縱座標（等於`y + height`）

由於元素相對於視口（viewport）的位置，會隨著頁面滾動變化，因此表示位置的四個屬性值，都不是固定不變的。如果想得到絕對位置，可以將`left`屬性加上`window.scrollX`，`top`屬性加上`window.scrollY`。

注意，`getBoundingClientRect`方法的所有屬性，都把邊框（`border`屬性）算作元素的一部分。也就是說，都是從邊框外緣的各個點來計算。因此，`width`和`height`包括了元素本身 + `padding` + `border`。

另外，上面的這些屬性，都是繼承自原型的屬性，`Object.keys`會返回一個空陣列，這一點也需要注意。

```javascript
var rect = document.body.getBoundingClientRect();
Object.keys(rect) // []
```

上面程式碼中，`rect`物件沒有自身屬性，而`Object.keys`方法只返回物件自身的屬性，所以返回了一個空陣列。

### Element.getClientRects()

`Element.getClientRects`方法返回一個類似陣列的物件，裡面是當前元素在頁面上形成的所有矩形（所以方法名中的`Rect`用的是複數）。每個矩形都有`bottom`、`height`、`left`、`right`、`top`和`width`六個屬性，表示它們相對於視口的四個座標，以及本身的高度和寬度。

對於盒狀元素（比如`<div>`和`<p>`），該方法返回的物件中只有該元素一個成員。對於行內元素（比如`<span>`、`<a>`、`<em>`），該方法返回的物件有多少個成員，取決於該元素在頁面上佔據多少行。這是它和`Element.getBoundingClientRect()`方法的主要區別，後者對於行內元素總是返回一個矩形。

```html
<span id="inline">Hello World Hello World Hello World</span>
```

上面程式碼是一個行內元素`<span>`，如果它在頁面上佔據三行，`getClientRects`方法返回的物件就有三個成員，如果它在頁面上佔據一行，`getClientRects`方法返回的物件就只有一個成員。

```javascript
var el = document.getElementById('inline');
el.getClientRects().length // 3
el.getClientRects()[0].left // 8
el.getClientRects()[0].right // 113.908203125
el.getClientRects()[0].bottom // 31.200000762939453
el.getClientRects()[0].height // 23.200000762939453
el.getClientRects()[0].width // 105.908203125
```

這個方法主要用於判斷行內元素是否換行，以及行內元素的每一行的位置偏移。

注意，如果行內元素包括換行符，那麼該方法會把換行符考慮在內。

```html
<span id="inline">
  Hello World
  Hello World
  Hello World
</span>
```

上面程式碼中，`<span>`節點內部有三個換行符，即使 HTML 語言忽略換行符，將它們顯示為一行，`getClientRects()`方法依然會返回三個成員。如果行寬設定得特別窄，上面的`<span>`元素顯示為6行，那麼就會返回六個成員。

### Element.insertAdjacentElement()

`Element.insertAdjacentElement`方法在相對於當前元素的指定位置，插入一個新的節點。該方法返回被插入的節點，如果插入失敗，返回`null`。

```javascript
element.insertAdjacentElement(position, element);
```

`Element.insertAdjacentElement`方法一共可以接受兩個引數，第一個引數是一個字串，表示插入的位置，第二個引數是將要插入的節點。第一個引數只可以取如下的值。

- `beforebegin`：當前元素之前
- `afterbegin`：當前元素內部的第一個子節點前面
- `beforeend`：當前元素內部的最後一個子節點後面
- `afterend`：當前元素之後

注意，`beforebegin`和`afterend`這兩個值，只在當前節點有父節點時才會生效。如果當前節點是由指令碼建立的，沒有父節點，那麼插入會失敗。

```javascript
var p1 = document.createElement('p')
var p2 = document.createElement('p')
p1.insertAdjacentElement('afterend', p2) // null
```

上面程式碼中，`p1`沒有父節點，所以插入`p2`到它後面就失敗了。

如果插入的節點是一個文件裡現有的節點，它會從原有位置刪除，放置到新的位置。

### Element.insertAdjacentHTML()，Element.insertAdjacentText()

`Element.insertAdjacentHTML`方法用於將一個 HTML 字串，解析生成 DOM 結構，插入相對於當前節點的指定位置。

```javascript
element.insertAdjacentHTML(position, text);
```

該方法接受兩個引數，第一個是一個表示指定位置的字串，第二個是待解析的 HTML 字串。第一個引數只能設定下面四個值之一。

- `beforebegin`：當前元素之前
- `afterbegin`：當前元素內部的第一個子節點前面
- `beforeend`：當前元素內部的最後一個子節點後面
- `afterend`：當前元素之後

```javascript
// HTML 程式碼：<div id="one">one</div>
var d1 = document.getElementById('one');
d1.insertAdjacentHTML('afterend', '<div id="two">two</div>');
// 執行後的 HTML 程式碼：
// <div id="one">one</div><div id="two">two</div>
```

該方法只是在現有的 DOM 結構裡面插入節點，這使得它的執行速度比`innerHTML`方法快得多。

注意，該方法不會轉義 HTML 字串，這導致它不能用來插入使用者輸入的內容，否則會有安全風險。

`Element.insertAdjacentText`方法在相對於當前節點的指定位置，插入一個文字節點，用法與`Element.insertAdjacentHTML`方法完全一致。

```javascript
// HTML 程式碼：<div id="one">one</div>
var d1 = document.getElementById('one');
d1.insertAdjacentText('afterend', 'two');
// 執行後的 HTML 程式碼：
// <div id="one">one</div>two
```

### Element.remove()

`Element.remove`方法繼承自 ChildNode 介面，用於將當前元素節點從它的父節點移除。

```javascript
var el = document.getElementById('mydiv');
el.remove();
```

上面程式碼將`el`節點從 DOM 樹裡面移除。

### Element.focus()，Element.blur()

`Element.focus`方法用於將當前頁面的焦點，轉移到指定元素上。

```javascript
document.getElementById('my-span').focus();
```

該方法可以接受一個物件作為引數。引數物件的`preventScroll`屬性是一個布林值，指定是否將當前元素停留在原始位置，而不是滾動到可見區域。

```javascript
function getFocus() {
  document.getElementById('btn').focus({preventScroll:false});
}
```

上面程式碼會讓`btn`元素獲得焦點，並滾動到可見區域。

最後，從`document.activeElement`屬性可以得到當前獲得焦點的元素。

`Element.blur`方法用於將焦點從當前元素移除。

### Element.click()

`Element.click`方法用於在當前元素上模擬一次滑鼠點選，相當於觸發了`click`事件。

## 參考連結

- Craig Buckler，[How to Translate from DOM to SVG Coordinates and Back Again](https://www.sitepoint.com/how-to-translate-from-dom-to-svg-coordinates-and-back-again/)
