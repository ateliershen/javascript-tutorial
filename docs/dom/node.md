# Node 介面

所有 DOM 節點物件都繼承了 Node 介面，擁有一些共同的屬性和方法。這是 DOM 操作的基礎。

## 屬性

### Node.prototype.nodeType

`nodeType`屬性返回一個整數值，表示節點的型別。

```javascript
document.nodeType // 9
```

上面程式碼中，文件節點的型別值為9。

Node 物件定義了幾個常量，對應這些型別值。

```javascript
document.nodeType === Node.DOCUMENT_NODE // true
```

上面程式碼中，文件節點的`nodeType`屬性等於常量`Node.DOCUMENT_NODE`。

不同節點的`nodeType`屬性值和對應的常量如下。

- 文件節點（document）：9，對應常量`Node.DOCUMENT_NODE`
- 元素節點（element）：1，對應常量`Node.ELEMENT_NODE`
- 屬性節點（attr）：2，對應常量`Node.ATTRIBUTE_NODE`
- 文字節點（text）：3，對應常量`Node.TEXT_NODE`
- 文件片斷節點（DocumentFragment）：11，對應常量`Node.DOCUMENT_FRAGMENT_NODE`
- 文件型別節點（DocumentType）：10，對應常量`Node.DOCUMENT_TYPE_NODE`
- 註釋節點（Comment）：8，對應常量`Node.COMMENT_NODE`

確定節點型別時，使用`nodeType`屬性是常用方法。

```javascript
var node = document.documentElement.firstChild;
if (node.nodeType === Node.ELEMENT_NODE) {
  console.log('該節點是元素節點');
}
```

### Node.prototype.nodeName

`nodeName`屬性返回節點的名稱。

```javascript
// HTML 程式碼如下
// <div id="d1">hello world</div>
var div = document.getElementById('d1');
div.nodeName // "DIV"
```

上面程式碼中，元素節點`<div>`的`nodeName`屬性就是大寫的標籤名`DIV`。

不同節點的`nodeName`屬性值如下。

- 文件節點（document）：`#document`
- 元素節點（element）：大寫的標籤名
- 屬性節點（attr）：屬性的名稱
- 文字節點（text）：`#text`
- 文件片斷節點（DocumentFragment）：`#document-fragment`
- 文件型別節點（DocumentType）：文件的型別
- 註釋節點（Comment）：`#comment`

### Node.prototype.nodeValue

`nodeValue`屬性返回一個字串，表示當前節點本身的文字值，該屬性可讀寫。

只有文字節點（text）、註釋節點（comment）和屬性節點（attr）有文字值，因此這三類節點的`nodeValue`可以返回結果，其他型別的節點一律返回`null`。同樣的，也只有這三類節點可以設定`nodeValue`屬性的值，其他型別的節點設定無效。

```javascript
// HTML 程式碼如下
// <div id="d1">hello world</div>
var div = document.getElementById('d1');
div.nodeValue // null
div.firstChild.nodeValue // "hello world"
```

上面程式碼中，`div`是元素節點，`nodeValue`屬性返回`null`。`div.firstChild`是文字節點，所以可以返回文字值。

### Node.prototype.textContent

`textContent`屬性返回當前節點和它的所有後代節點的文字內容。

```javascript
// HTML 程式碼為
// <div id="divA">This is <span>some</span> text</div>

document.getElementById('divA').textContent
// This is some text
```

`textContent`屬性自動忽略當前節點內部的 HTML 標籤，返回所有文字內容。

該屬性是可讀寫的，設定該屬性的值，會用一個新的文字節點，替換所有原來的子節點。它還有一個好處，就是自動對 HTML 標籤轉義。這很適合用於使用者提供的內容。

```javascript
document.getElementById('foo').textContent = '<p>GoodBye!</p>';
```

上面程式碼在插入文字時，會將`<p>`標籤解釋為文字，而不會當作標籤處理。

對於文字節點（text）、註釋節點（comment）和屬性節點（attr），`textContent`屬性的值與`nodeValue`屬性相同。對於其他型別的節點，該屬性會將每個子節點（不包括註釋節點）的內容連線在一起返回。如果一個節點沒有子節點，則返回空字串。

文件節點（document）和文件型別節點（doctype）的`textContent`屬性為`null`。如果要讀取整個文件的內容，可以使用`document.documentElement.textContent`。

### Node.prototype.baseURI

`baseURI`屬性返回一個字串，表示當前網頁的絕對路徑。瀏覽器根據這個屬性，計算網頁上的相對路徑的 URL。該屬性為只讀。

```javascript
// 當前網頁的網址為
// http://www.example.com/index.html
document.baseURI
// "http://www.example.com/index.html"
```

如果無法讀到網頁的 URL，`baseURI`屬性返回`null`。

該屬性的值一般由當前網址的 URL（即`window.location`屬性）決定，但是可以使用 HTML 的`<base>`標籤，改變該屬性的值。

```html
<base href="http://www.example.com/page.html">
```

設定了以後，`baseURI`屬性就返回`<base>`標籤設定的值。

### Node.prototype.ownerDocument

`Node.ownerDocument`屬性返回當前節點所在的頂層文件物件，即`document`物件。

```javascript
var d = p.ownerDocument;
d === document // true
```

`document`物件本身的`ownerDocument`屬性，返回`null`。

### Node.prototype.nextSibling

`Node.nextSibling`屬性返回緊跟在當前節點後面的第一個同級節點。如果當前節點後面沒有同級節點，則返回`null`。

```javascript
// HTML 程式碼如下
// <div id="d1">hello</div><div id="d2">world</div>
var d1 = document.getElementById('d1');
var d2 = document.getElementById('d2');

d1.nextSibling === d2 // true
```

上面程式碼中，`d1.nextSibling`就是緊跟在`d1`後面的同級節點`d2`。

注意，該屬性還包括文字節點和註釋節點（`<!-- comment -->`）。因此如果當前節點後面有空格，該屬性會返回一個文字節點，內容為空格。

`nextSibling`屬性可以用來遍歷所有子節點。

```javascript
var el = document.getElementById('div1').firstChild;

while (el !== null) {
  console.log(el.nodeName);
  el = el.nextSibling;
}
```

上面程式碼遍歷`div1`節點的所有子節點。

### Node.prototype.previousSibling

`previousSibling`屬性返回當前節點前面的、距離最近的一個同級節點。如果當前節點前面沒有同級節點，則返回`null`。

```javascript
// HTML 程式碼如下
// <div id="d1">hello</div><div id="d2">world</div>
var d1 = document.getElementById('d1');
var d2 = document.getElementById('d2');

d2.previousSibling === d1 // true
```

上面程式碼中，`d2.previousSibling`就是`d2`前面的同級節點`d1`。

注意，該屬性還包括文字節點和註釋節點。因此如果當前節點前面有空格，該屬性會返回一個文字節點，內容為空格。

### Node.prototype.parentNode

`parentNode`屬性返回當前節點的父節點。對於一個節點來說，它的父節點只可能是三種類型：元素節點（element）、文件節點（document）和文件片段節點（documentfragment）。

```javascript
if (node.parentNode) {
  node.parentNode.removeChild(node);
}
```

上面程式碼中，透過`node.parentNode`屬性將`node`節點從文件裡面移除。

文件節點（document）和文件片段節點（documentfragment）的父節點都是`null`。另外，對於那些生成後還沒插入 DOM 樹的節點，父節點也是`null`。

### Node.prototype.parentElement

`parentElement`屬性返回當前節點的父元素節點。如果當前節點沒有父節點，或者父節點型別不是元素節點，則返回`null`。

```javascript
if (node.parentElement) {
  node.parentElement.style.color = 'red';
}
```

上面程式碼中，父元素節點的樣式設定了紅色。

由於父節點只可能是三種類型：元素節點、文件節點（document）和文件片段節點（documentfragment）。`parentElement`屬性相當於把後兩種父節點都排除了。

### Node.prototype.firstChild，Node.prototype.lastChild

`firstChild`屬性返回當前節點的第一個子節點，如果當前節點沒有子節點，則返回`null`。

```javascript
// HTML 程式碼如下
// <p id="p1"><span>First span</span></p>
var p1 = document.getElementById('p1');
p1.firstChild.nodeName // "SPAN"
```

上面程式碼中，`p`元素的第一個子節點是`span`元素。

注意，`firstChild`返回的除了元素節點，還可能是文字節點或註釋節點。

```javascript
// HTML 程式碼如下
// <p id="p1">
//   <span>First span</span>
//  </p>
var p1 = document.getElementById('p1');
p1.firstChild.nodeName // "#text"
```

上面程式碼中，`p`元素與`span`元素之間有空白字元，這導致`firstChild`返回的是文字節點。

`lastChild`屬性返回當前節點的最後一個子節點，如果當前節點沒有子節點，則返回`null`。用法與`firstChild`屬性相同。

### Node.prototype.childNodes

`childNodes`屬性返回一個類似陣列的物件（`NodeList`集合），成員包括當前節點的所有子節點。

```javascript
var children = document.querySelector('ul').childNodes;
```

上面程式碼中，`children`就是`ul`元素的所有子節點。

使用該屬性，可以遍歷某個節點的所有子節點。

```javascript
var div = document.getElementById('div1');
var children = div.childNodes;

for (var i = 0; i < children.length; i++) {
  // ...
}
```

文件節點（document）就有兩個子節點：文件型別節點（docType）和 HTML 根元素節點。

```javascript
var children = document.childNodes;
for (var i = 0; i < children.length; i++) {
  console.log(children[i].nodeType);
}
// 10
// 1
```

上面程式碼中，文件節點的第一個子節點的型別是10（即文件型別節點），第二個子節點的型別是1（即元素節點）。

注意，除了元素節點，`childNodes`屬性的返回值還包括文字節點和註釋節點。如果當前節點不包括任何子節點，則返回一個空的`NodeList`集合。由於`NodeList`物件是一個動態集合，一旦子節點發生變化，立刻會反映在返回結果之中。

### Node.prototype.isConnected

`isConnected`屬性返回一個布林值，表示當前節點是否在文件之中。

```javascript
var test = document.createElement('p');
test.isConnected // false

document.body.appendChild(test);
test.isConnected // true
```

上面程式碼中，`test`節點是指令碼生成的節點，沒有插入文件之前，`isConnected`屬性返回`false`，插入之後返回`true`。

## 方法

### Node.prototype.appendChild()

`appendChild()`方法接受一個節點物件作為引數，將其作為最後一個子節點，插入當前節點。該方法的返回值就是插入文件的子節點。

```javascript
var p = document.createElement('p');
document.body.appendChild(p);
```

上面程式碼新建一個`<p>`節點，將其插入`document.body`的尾部。

如果引數節點是 DOM 已經存在的節點，`appendChild()`方法會將其從原來的位置，移動到新位置。

```javascript
var div = document.getElementById('myDiv');
document.body.appendChild(div);
```

上面程式碼中，插入的是一個已經存在的節點`myDiv`，結果就是該節點會從原來的位置，移動到`document.body`的尾部。

如果`appendChild()`方法的引數是`DocumentFragment`節點，那麼插入的是`DocumentFragment`的所有子節點，而不是`DocumentFragment`節點本身。返回值是一個空的`DocumentFragment`節點。

### Node.prototype.hasChildNodes()

`hasChildNodes`方法返回一個布林值，表示當前節點是否有子節點。

```javascript
var foo = document.getElementById('foo');

if (foo.hasChildNodes()) {
  foo.removeChild(foo.childNodes[0]);
}
```

上面程式碼表示，如果`foo`節點有子節點，就移除第一個子節點。

注意，子節點包括所有型別的節點，並不僅僅是元素節點。哪怕節點只包含一個空格，`hasChildNodes`方法也會返回`true`。

判斷一個節點有沒有子節點，有許多種方法，下面是其中的三種。

- `node.hasChildNodes()`
- `node.firstChild !== null`
- `node.childNodes && node.childNodes.length > 0`

`hasChildNodes`方法結合`firstChild`屬性和`nextSibling`屬性，可以遍歷當前節點的所有後代節點。

```javascript
function DOMComb(parent, callback) {
  if (parent.hasChildNodes()) {
    for (var node = parent.firstChild; node; node = node.nextSibling) {
      DOMComb(node, callback);
    }
  }
  callback(parent);
}

// 用法
DOMComb(document.body, console.log)
```

上面程式碼中，`DOMComb`函式的第一個引數是某個指定的節點，第二個引數是回撥函式。這個回撥函式會依次作用於指定節點，以及指定節點的所有後代節點。

### Node.prototype.cloneNode()

`cloneNode`方法用於克隆一個節點。它接受一個布林值作為引數，表示是否同時克隆子節點。它的返回值是一個克隆出來的新節點。

```javascript
var cloneUL = document.querySelector('ul').cloneNode(true);
```

該方法有一些使用注意點。

（1）克隆一個節點，會複製該節點的所有屬性，但是會喪失`addEventListener`方法和`on-`屬性（即`node.onclick = fn`），新增在這個節點上的事件回撥函式。

（2）該方法返回的節點不在文件之中，即沒有任何父節點，必須使用諸如`Node.appendChild`這樣的方法新增到文件之中。

（3）克隆一個節點之後，DOM 有可能出現兩個有相同`id`屬性（即`id="xxx"`）的網頁元素，這時應該修改其中一個元素的`id`屬性。如果原節點有`name`屬性，可能也需要修改。

### Node.prototype.insertBefore()

`insertBefore`方法用於將某個節點插入父節點內部的指定位置。

```javascript
var insertedNode = parentNode.insertBefore(newNode, referenceNode);
```

`insertBefore`方法接受兩個引數，第一個引數是所要插入的節點`newNode`，第二個引數是父節點`parentNode`內部的一個子節點`referenceNode`。`newNode`將插在`referenceNode`這個子節點的前面。返回值是插入的新節點`newNode`。

```javascript
var p = document.createElement('p');
document.body.insertBefore(p, document.body.firstChild);
```

上面程式碼中，新建一個`<p>`節點，插在`document.body.firstChild`的前面，也就是成為`document.body`的第一個子節點。

如果`insertBefore`方法的第二個引數為`null`，則新節點將插在當前節點內部的最後位置，即變成最後一個子節點。

```javascript
var p = document.createElement('p');
document.body.insertBefore(p, null);
```

上面程式碼中，`p`將成為`document.body`的最後一個子節點。這也說明`insertBefore`的第二個引數不能省略。

注意，如果所要插入的節點是當前 DOM 現有的節點，則該節點將從原有的位置移除，插入新的位置。

由於不存在`insertAfter`方法，如果新節點要插在父節點的某個子節點後面，可以用`insertBefore`方法結合`nextSibling`屬性模擬。

```javascript
parent.insertBefore(s1, s2.nextSibling);
```

上面程式碼中，`parent`是父節點，`s1`是一個全新的節點，`s2`是可以將`s1`節點，插在`s2`節點的後面。如果`s2`是當前節點的最後一個子節點，則`s2.nextSibling`返回`null`，這時`s1`節點會插在當前節點的最後，變成當前節點的最後一個子節點，等於緊跟在`s2`的後面。

如果要插入的節點是`DocumentFragment`型別，那麼插入的將是`DocumentFragment`的所有子節點，而不是`DocumentFragment`節點本身。返回值將是一個空的`DocumentFragment`節點。

### Node.prototype.removeChild()

`removeChild`方法接受一個子節點作為引數，用於從當前節點移除該子節點。返回值是移除的子節點。

```javascript
var divA = document.getElementById('A');
divA.parentNode.removeChild(divA);
```

上面程式碼移除了`divA`節點。注意，這個方法是在`divA`的父節點上呼叫的，不是在`divA`上呼叫的。

下面是如何移除當前節點的所有子節點。

```javascript
var element = document.getElementById('top');
while (element.firstChild) {
  element.removeChild(element.firstChild);
}
```

被移除的節點依然存在於記憶體之中，但不再是 DOM 的一部分。所以，一個節點移除以後，依然可以使用它，比如插入到另一個節點下面。

如果引數節點不是當前節點的子節點，`removeChild`方法將報錯。

### Node.prototype.replaceChild()

`replaceChild`方法用於將一個新的節點，替換當前節點的某一個子節點。

```javascript
var replacedNode = parentNode.replaceChild(newChild, oldChild);
```

上面程式碼中，`replaceChild`方法接受兩個引數，第一個引數`newChild`是用來替換的新節點，第二個引數`oldChild`是將要替換走的子節點。返回值是替換走的那個節點`oldChild`。

```javascript
var divA = document.getElementById('divA');
var newSpan = document.createElement('span');
newSpan.textContent = 'Hello World!';
divA.parentNode.replaceChild(newSpan, divA);
```

上面程式碼是如何將指定節點`divA`替換走。

### Node.prototype.contains()

`contains`方法返回一個布林值，表示引數節點是否滿足以下三個條件之一。

- 引數節點為當前節點。
- 引數節點為當前節點的子節點。
- 引數節點為當前節點的後代節點。

```javascript
document.body.contains(node)
```

上面程式碼檢查引數節點`node`，是否包含在當前文件之中。

注意，當前節點傳入`contains`方法，返回`true`。

```javascript
nodeA.contains(nodeA) // true
```

### Node.prototype.compareDocumentPosition()

`compareDocumentPosition`方法的用法，與`contains`方法完全一致，返回一個六個位元位的二進位制值，表示引數節點與當前節點的關係。

二進位制值 | 十進位制值 | 含義
---------|------|-----
000000 | 0 | 兩個節點相同
000001 | 1 | 兩個節點不在同一個文件（即有一個節點不在當前文件）
000010 | 2 | 引數節點在當前節點的前面
000100 | 4 | 引數節點在當前節點的後面
001000 | 8 | 引數節點包含當前節點
010000 | 16 | 當前節點包含引數節點
100000 | 32 | 瀏覽器內部使用

```javascript
// HTML 程式碼如下
// <div id="mydiv">
//   <form><input id="test" /></form>
// </div>

var div = document.getElementById('mydiv');
var input = document.getElementById('test');

div.compareDocumentPosition(input) // 20
input.compareDocumentPosition(div) // 10
```

上面程式碼中，節點`div`包含節點`input`（二進位制`010000`），而且節點`input`在節點`div`的後面（二進位制`000100`），所以第一個`compareDocumentPosition`方法返回`20`（二進位制`010100`，即`010000 + 000100`），第二個`compareDocumentPosition`方法返回`10`（二進位制`001010`）。

由於`compareDocumentPosition`返回值的含義，定義在每一個位元位上，所以如果要檢查某一種特定的含義，就需要使用位元位運算子。

```javascript
var head = document.head;
var body = document.body;
if (head.compareDocumentPosition(body) & 4) {
  console.log('文件結構正確');
} else {
  console.log('<body> 不能在 <head> 前面');
}
```

上面程式碼中，`compareDocumentPosition`的返回值與`4`（又稱掩碼）進行與運算（`&`），得到一個布林值，表示`<head>`是否在`<body>`前面。

### Node.prototype.isEqualNode()，Node.prototype.isSameNode()

`isEqualNode`方法返回一個布林值，用於檢查兩個節點是否相等。所謂相等的節點，指的是兩個節點的型別相同、屬性相同、子節點相同。

```javascript
var p1 = document.createElement('p');
var p2 = document.createElement('p');

p1.isEqualNode(p2) // true
```

`isSameNode`方法返回一個布林值，表示兩個節點是否為同一個節點。

```javascript
var p1 = document.createElement('p');
var p2 = document.createElement('p');

p1.isSameNode(p2) // false
p1.isSameNode(p1) // true
```

### Node.prototype.normalize()

`normalize`方法用於清理當前節點內部的所有文字節點（text）。它會去除空的文字節點，並且將毗鄰的文字節點合併成一個，也就是說不存在空的文字節點，以及毗鄰的文字節點。

```javascript
var wrapper = document.createElement('div');

wrapper.appendChild(document.createTextNode('Part 1 '));
wrapper.appendChild(document.createTextNode('Part 2 '));

wrapper.childNodes.length // 2
wrapper.normalize();
wrapper.childNodes.length // 1
```

上面程式碼使用`normalize`方法之前，`wrapper`節點有兩個毗鄰的文字子節點。使用`normalize`方法之後，兩個文字子節點被合併成一個。

該方法是`Text.splitText`的逆方法，可以檢視《Text 節點物件》一章，瞭解更多內容。

### Node.prototype.getRootNode()

`getRootNode()`方法返回當前節點所在文件的根節點`document`，與`ownerDocument`屬性的作用相同。

```javascript
document.body.firstChild.getRootNode() === document
// true
document.body.firstChild.getRootNode() === document.body.firstChild.ownerDocument
// true
```

該方法可用於`document`節點自身，這一點與`document.ownerDocument`不同。

```javascript
document.getRootNode() // document
document.ownerDocument // null
```
