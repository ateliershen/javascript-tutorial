# NodeList 介面，HTMLCollection 介面

節點都是單個物件，有時需要一種資料結構，能夠容納多個節點。DOM 提供兩種節點集合，用於容納多個節點：`NodeList`和`HTMLCollection`。

這兩種集合都屬於介面規範。許多 DOM 屬性和方法，返回的結果是`NodeList`例項或`HTMLCollection`例項。主要區別是，`NodeList`可以包含各種型別的節點，`HTMLCollection`只能包含 HTML 元素節點。

## NodeList 介面

### 概述

`NodeList`例項是一個類似陣列的物件，它的成員是節點物件。透過以下方法可以得到`NodeList`例項。

- `Node.childNodes`
- `document.querySelectorAll()`等節點搜尋方法

```javascript
document.body.childNodes instanceof NodeList // true
```

`NodeList`例項很像陣列，可以使用`length`屬性和`forEach`方法。但是，它不是陣列，不能使用`pop`或`push`之類陣列特有的方法。

```javascript
var children = document.body.childNodes;

Array.isArray(children) // false

children.length // 34
children.forEach(console.log)
```

上面程式碼中，NodeList 例項`children`不是陣列，但是具有`length`屬性和`forEach`方法。

如果`NodeList`例項要使用陣列方法，可以將其轉為真正的陣列。

```javascript
var children = document.body.childNodes;
var nodeArr = Array.prototype.slice.call(children);
```

除了使用`forEach`方法遍歷 NodeList 例項，還可以使用`for`迴圈。

```javascript
var children = document.body.childNodes;

for (var i = 0; i < children.length; i++) {
  var item = children[i];
}
```

注意，NodeList 例項可能是動態集合，也可能是靜態集合。所謂動態集合就是一個活的集合，DOM 刪除或新增一個相關節點，都會立刻反映在 NodeList 例項。目前，只有`Node.childNodes`返回的是一個動態集合，其他的 NodeList 都是靜態集合。

```javascript
var children = document.body.childNodes;
children.length // 18
document.body.appendChild(document.createElement('p'));
children.length // 19
```

上面程式碼中，文件增加一個子節點，NodeList 例項`children`的`length`屬性就增加了1。

### NodeList.prototype.length

`length`屬性返回 NodeList 例項包含的節點數量。

```javascript
document.querySelectorAll('xxx').length
// 0
```

上面程式碼中，`document.querySelectorAll`返回一個 NodeList 集合。對於那些不存在的 HTML 標籤，`length`屬性返回`0`。

### NodeList.prototype.forEach()

`forEach`方法用於遍歷 NodeList 的所有成員。它接受一個回撥函式作為引數，每一輪遍歷就執行一次這個回撥函式，用法與陣列例項的`forEach`方法完全一致。

```javascript
var children = document.body.childNodes;
children.forEach(function f(item, i, list) {
  // ...
}, this);
```

上面程式碼中，回撥函式`f`的三個引數依次是當前成員、位置和當前 NodeList 例項。`forEach`方法的第二個引數，用於繫結回撥函式內部的`this`，該引數可省略。

### NodeList.prototype.item()

`item`方法接受一個整數值作為引數，表示成員的位置，返回該位置上的成員。

```javascript
document.body.childNodes.item(0)
```

上面程式碼中，`item(0)`返回第一個成員。

如果引數值大於實際長度，或者索引不合法（比如負數），`item`方法返回`null`。如果省略引數，`item`方法會報錯。

所有類似陣列的物件，都可以使用方括號運算子取出成員。一般情況下，都是使用方括號運算子，而不使用`item`方法。

```javascript
document.body.childNodes[0]
```

### NodeList.prototype.keys()，NodeList.prototype.values()，NodeList.prototype.entries()

這三個方法都返回一個 ES6 的遍歷器物件，可以透過`for...of`迴圈遍歷獲取每一個成員的資訊。區別在於，`keys()`返回鍵名的遍歷器，`values()`返回鍵值的遍歷器，`entries()`返回的遍歷器同時包含鍵名和鍵值的資訊。

```javascript
var children = document.body.childNodes;

for (var key of children.keys()) {
  console.log(key);
}
// 0
// 1
// 2
// ...

for (var value of children.values()) {
  console.log(value);
}
// #text
// <script>
// ...

for (var entry of children.entries()) {
  console.log(entry);
}
// Array [ 0, #text ]
// Array [ 1, <script> ]
// ...
```

## HTMLCollection 介面

### 概述

`HTMLCollection`是一個節點物件的集合，只能包含元素節點（element），不能包含其他型別的節點。它的返回值是一個類似陣列的物件，但是與`NodeList`介面不同，`HTMLCollection`沒有`forEach`方法，只能使用`for`迴圈遍歷。

返回`HTMLCollection`例項的，主要是一些`Document`物件的集合屬性，比如`document.links`、`document.forms`、`document.images`等。

```javascript
document.links instanceof HTMLCollection // true
```

`HTMLCollection`例項都是動態集合，節點的變化會實時反映在集合中。

如果元素節點有`id`或`name`屬性，那麼`HTMLCollection`例項上面，可以使用`id`屬性或`name`屬性引用該節點元素。如果沒有對應的節點，則返回`null`。

```javascript
// HTML 程式碼如下
// <img id="pic" src="http://example.com/foo.jpg">

var pic = document.getElementById('pic');
document.images.pic === pic // true
```

上面程式碼中，`document.images`是一個`HTMLCollection`例項，可以透過`<img>`元素的`id`屬性值，從`HTMLCollection`例項上取到這個元素。

### HTMLCollection.prototype.length

`length`屬性返回`HTMLCollection`例項包含的成員數量。

```javascript
document.links.length // 18
```

### HTMLCollection.prototype.item()

`item`方法接受一個整數值作為引數，表示成員的位置，返回該位置上的成員。

```javascript
var c = document.images;
var img0 = c.item(0);
```

上面程式碼中，`item(0)`表示返回0號位置的成員。由於方括號運算子也具有同樣作用，而且使用更方便，所以一般情況下，總是使用方括號運算子。

如果引數值超出成員數量或者不合法（比如小於0），那麼`item`方法返回`null`。

### HTMLCollection.prototype.namedItem()

`namedItem`方法的引數是一個字串，表示`id`屬性或`name`屬性的值，返回對應的元素節點。如果沒有對應的節點，則返回`null`。

```javascript
// HTML 程式碼如下
// <img id="pic" src="http://example.com/foo.jpg">

var pic = document.getElementById('pic');
document.images.namedItem('pic') === pic // true
```
