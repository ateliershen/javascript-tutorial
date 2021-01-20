# Text 節點和 DocumentFragment 節點

## Text 節點的概念

文字節點（`Text`）代表元素節點（`Element`）和屬性節點（`Attribute`）的文字內容。如果一個節點只包含一段文字，那麼它就有一個文字子節點，代表該節點的文字內容。

通常我們使用父節點的`firstChild`、`nextSibling`等屬性獲取文字節點，或者使用`Document`節點的`createTextNode`方法創造一個文字節點。

```javascript
// 獲取文字節點
var textNode = document.querySelector('p').firstChild;

// 創造文字節點
var textNode = document.createTextNode('Hi');
document.querySelector('div').appendChild(textNode);
```

瀏覽器原生提供一個`Text`建構函式。它返回一個文字節點例項。它的引數就是該文字節點的文字內容。

```javascript
// 空字串
var text1 = new Text();

// 非空字串
var text2 = new Text('This is a text node');
```

注意，由於空格也是一個字元，所以哪怕只有一個空格，也會形成文字節點。比如，`<p> </p>`包含一個空格，它的子節點就是一個文字節點。

文字節點除了繼承`Node`介面，還繼承了`CharacterData`介面。`Node`介面的屬性和方法請參考《Node 介面》一章，這裡不再重複介紹了，以下的屬性和方法大部分來自`CharacterData`介面。

## Text 節點的屬性

### data

`data`屬性等同於`nodeValue`屬性，用來設定或讀取文字節點的內容。

```javascript
// 讀取文字內容
document.querySelector('p').firstChild.data
// 等同於
document.querySelector('p').firstChild.nodeValue

// 設定文字內容
document.querySelector('p').firstChild.data = 'Hello World';
```

### wholeText

`wholeText`屬性將當前文字節點與毗鄰的文字節點，作為一個整體返回。大多數情況下，`wholeText`屬性的返回值，與`data`屬性和`textContent`屬性相同。但是，某些特殊情況會有差異。

舉例來說，HTML 程式碼如下。

```html
<p id="para">A <em>B</em> C</p>
```

這時，文字節點的`wholeText`屬性和`data`屬性，返回值相同。

```javascript
var el = document.getElementById('para');
el.firstChild.wholeText // "A "
el.firstChild.data // "A "
```

但是，一旦移除`<em>`節點，`wholeText`屬性與`data`屬性就會有差異，因為這時其實`<p>`節點下面包含了兩個毗鄰的文字節點。

```javascript
el.removeChild(para.childNodes[1]);
el.firstChild.wholeText // "A C"
el.firstChild.data // "A "
```

### length

`length`屬性返回當前文字節點的文字長度。

```javascript
(new Text('Hello')).length // 5
```

### nextElementSibling，previousElementSibling

`nextElementSibling`屬性返回緊跟在當前文字節點後面的那個同級元素節點。如果取不到元素節點，則返回`null`。

```javascript
// HTML 為
// <div>Hello <em>World</em></div>
var tn = document.querySelector('div').firstChild;
tn.nextElementSibling
// <em>World</em>
```

`previousElementSibling`屬性返回當前文字節點前面最近的同級元素節點。如果取不到元素節點，則返回`null：`。

## Text 節點的方法

### appendData()，deleteData()，insertData()，replaceData()，subStringData()

以下5個方法都是編輯`Text`節點文字內容的方法。

- `appendData()`：在`Text`節點尾部追加字串。
- `deleteData()`：刪除`Text`節點內部的子字串，第一個引數為子字串開始位置，第二個引數為子字串長度。
- `insertData()`：在`Text`節點插入字串，第一個引數為插入位置，第二個引數為插入的子字串。
- `replaceData()`：用於替換文字，第一個引數為替換開始位置，第二個引數為需要被替換掉的長度，第三個引數為新加入的字串。
- `subStringData()`：用於獲取子字串，第一個引數為子字串在`Text`節點中的開始位置，第二個引數為子字串長度。

```javascript
// HTML 程式碼為
// <p>Hello World</p>
var pElementText = document.querySelector('p').firstChild;

pElementText.appendData('!');
// 頁面顯示 Hello World!
pElementText.deleteData(7, 5);
// 頁面顯示 Hello W
pElementText.insertData(7, 'Hello ');
// 頁面顯示 Hello WHello
pElementText.replaceData(7, 5, 'World');
// 頁面顯示 Hello WWorld
pElementText.substringData(7, 10);
// 頁面顯示不變，返回"World "
```

### remove()

`remove`方法用於移除當前`Text`節點。

```javascript
// HTML 程式碼為
// <p>Hello World</p>
document.querySelector('p').firstChild.remove()
// 現在 HTML 程式碼為
// <p></p>
```

### splitText()

`splitText`方法將`Text`節點一分為二，變成兩個毗鄰的`Text`節點。它的引數就是分割位置（從零開始），分割到該位置的字元前結束。如果分割位置不存在，將報錯。

分割後，該方法返回分割位置後方的字串，而原`Text`節點變成只包含分割位置前方的字串。

```javascript
// html 程式碼為 <p id="p">foobar</p>
var p = document.getElementById('p');
var textnode = p.firstChild;

var newText = textnode.splitText(3);
newText // "bar"
textnode // "foo"
```

父元素節點的`normalize`方法可以將毗鄰的兩個`Text`節點合併。

接上面的例子，文字節點的`splitText`方法將一個`Text`節點分割成兩個，父元素的`normalize`方法可以實現逆操作，將它們合併。

```javascript
p.childNodes.length // 2

// 將毗鄰的兩個 Text 節點合併
p.normalize();
p.childNodes.length // 1
```

## DocumentFragment 節點

`DocumentFragment`節點代表一個文件的片段，本身就是一個完整的 DOM 樹形結構。它沒有父節點，`parentNode`返回`null`，但是可以插入任意數量的子節點。它不屬於當前文件，操作`DocumentFragment`節點，要比直接操作 DOM 樹快得多。

它一般用於構建一個 DOM 結構，然後插入當前文件。`document.createDocumentFragment`方法，以及瀏覽器原生的`DocumentFragment`建構函式，可以建立一個空的`DocumentFragment`節點。然後再使用其他 DOM 方法，向其新增子節點。

```javascript
var docFrag = document.createDocumentFragment();
// 等同於
var docFrag = new DocumentFragment();

var li = document.createElement('li');
li.textContent = 'Hello World';
docFrag.appendChild(li);

document.querySelector('ul').appendChild(docFrag);
```

上面程式碼建立了一個`DocumentFragment`節點，然後將一個`li`節點新增在它裡面，最後將`DocumentFragment`節點移動到原文件。

注意，`DocumentFragment`節點本身不能被插入當前文件。當它作為`appendChild()`、`insertBefore()`、`replaceChild()`等方法的引數時，是它的所有子節點插入當前文件，而不是它自身。一旦`DocumentFragment`節點被新增進當前文件，它自身就變成了空節點（`textContent`屬性為空字串），可以被再次使用。如果想要儲存`DocumentFragment`節點的內容，可以使用`cloneNode`方法。

```javascript
document
  .querySelector('ul')
  .appendChild(docFrag.cloneNode(true));
```

上面這樣新增`DocumentFragment`節點進入當前文件，不會清空`DocumentFragment`節點。

下面是一個例子，使用`DocumentFragment`反轉一個指定節點的所有子節點的順序。

```javascript
function reverse(n) {
  var f = document.createDocumentFragment();
  while(n.lastChild) f.appendChild(n.lastChild);
  n.appendChild(f);
}
```

`DocumentFragment`節點物件沒有自己的屬性和方法，全部繼承自`Node`節點和`ParentNode`介面。也就是說，`DocumentFragment`節點比`Node`節點多出以下四個屬性。

- `children`：返回一個動態的`HTMLCollection`集合物件，包括當前`DocumentFragment`物件的所有子元素節點。
- `firstElementChild`：返回當前`DocumentFragment`物件的第一個子元素節點，如果沒有則返回`null`。
- `lastElementChild`：返回當前`DocumentFragment`物件的最後一個子元素節點，如果沒有則返回`null`。
- `childElementCount`：返回當前`DocumentFragment`物件的所有子元素數量。
