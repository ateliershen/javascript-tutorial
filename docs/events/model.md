# 事件模型

## 監聽函式

瀏覽器的事件模型，就是透過監聽函式（listener）對事件做出反應。事件發生後，瀏覽器監聽到了這個事件，就會執行對應的監聽函式。這是事件驅動程式設計模式（event-driven）的主要程式設計方式。

JavaScript 有三種方法，可以為事件繫結監聽函式。

### HTML 的 on- 屬性

HTML 語言允許在元素的屬性中，直接定義某些事件的監聽程式碼。

```html
<body onload="doSomething()">
<div onclick="console.log('觸發事件')">
```

上面程式碼為`body`節點的`load`事件、`div`節點的`click`事件，指定了監聽程式碼。一旦事件發生，就會執行這段程式碼。

元素的事件監聽屬性，都是`on`加上事件名，比如`onload`就是`on + load`，表示`load`事件的監聽程式碼。

注意，這些屬性的值是將會執行的程式碼，而不是一個函式。

```html
<!-- 正確 -->
<body onload="doSomething()">

<!-- 錯誤 -->
<body onload="doSomething">
```

一旦指定的事件發生，`on-`屬性的值是原樣傳入 JavaScript 引擎執行。因此如果要執行函式，不要忘記加上一對圓括號。

使用這個方法指定的監聽程式碼，只會在冒泡階段觸發。

```html
<div onclick="console.log(2)">
  <button onclick="console.log(1)">點選</button>
</div>
```

上面程式碼中，`<button>`是`<div>`的子元素。`<button>`的`click`事件，也會觸發`<div>`的`click`事件。由於`on-`屬性的監聽程式碼，只在冒泡階段觸發，所以點選結果是先輸出`1`，再輸出`2`，即事件從子元素開始冒泡到父元素。

直接設定`on-`屬性，與透過元素節點的`setAttribute`方法設定`on-`屬性，效果是一樣的。

```javascript
el.setAttribute('onclick', 'doSomething()');
// 等同於
// <Element onclick="doSomething()">
```

### 元素節點的事件屬性

元素節點物件的事件屬性，同樣可以指定監聽函式。

```javascript
window.onload = doSomething;

div.onclick = function (event) {
  console.log('觸發事件');
};
```

使用這個方法指定的監聽函式，也是隻會在冒泡階段觸發。

注意，這種方法與 HTML 的`on-`屬性的差異是，它的值是函式名（`doSomething`），而不像後者，必須給出完整的監聽程式碼（`doSomething()`）。

### EventTarget.addEventListener()

所有 DOM 節點例項都有`addEventListener`方法，用來為該節點定義事件的監聽函式。

```javascript
window.addEventListener('load', doSomething, false);
```

`addEventListener`方法的詳細介紹，參見`EventTarget`章節。

### 小結

上面三種方法，第一種“HTML 的 on- 屬性”，違反了 HTML 與 JavaScript 程式碼相分離的原則，將兩者寫在一起，不利於程式碼分工，因此不推薦使用。

第二種“元素節點的事件屬性”的缺點在於，同一個事件只能定義一個監聽函式，也就是說，如果定義兩次`onclick`屬性，後一次定義會覆蓋前一次。因此，也不推薦使用。

第三種`EventTarget.addEventListener`是推薦的指定監聽函式的方法。它有如下優點：

- 同一個事件可以新增多個監聽函式。
- 能夠指定在哪個階段（捕獲階段還是冒泡階段）觸發監聽函式。
- 除了 DOM 節點，其他物件（比如`window`、`XMLHttpRequest`等）也有這個介面，它等於是整個 JavaScript 統一的監聽函式介面。

## this 的指向

監聽函式內部的`this`指向觸發事件的那個元素節點。

```html
<button id="btn" onclick="console.log(this.id)">點選</button>
```

執行上面程式碼，點選後會輸出`btn`。

其他兩種監聽函式的寫法，`this`的指向也是如此。

```javascript
// HTML 程式碼如下
// <button id="btn">點選</button>
var btn = document.getElementById('btn');

// 寫法一
btn.onclick = function () {
  console.log(this.id);
};

// 寫法二
btn.addEventListener(
  'click',
  function (e) {
    console.log(this.id);
  },
  false
);
```

上面兩種寫法，點選按鈕以後也是輸出`btn`。

## 事件的傳播

一個事件發生後，會在子元素和父元素之間傳播（propagation）。這種傳播分成三個階段。

- **第一階段**：從`window`物件傳導到目標節點（上層傳到底層），稱為“捕獲階段”（capture phase）。
- **第二階段**：在目標節點上觸發，稱為“目標階段”（target phase）。
- **第三階段**：從目標節點傳導回`window`物件（從底層傳回上層），稱為“冒泡階段”（bubbling phase）。

這種三階段的傳播模型，使得同一個事件會在多個節點上觸發。

```html
<div>
  <p>點選</p>
</div>
```

上面程式碼中，`<div>`節點之中有一個`<p>`節點。

如果對這兩個節點，都設定`click`事件的監聽函式（每個節點的捕獲階段和冒泡階段，各設定一個監聽函式），共計設定四個監聽函式。然後，對`<p>`點選，`click`事件會觸發四次。

```javascript
var phases = {
  1: 'capture',
  2: 'target',
  3: 'bubble'
};

var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', callback, true);
p.addEventListener('click', callback, true);
div.addEventListener('click', callback, false);
p.addEventListener('click', callback, false);

function callback(event) {
  var tag = event.currentTarget.tagName;
  var phase = phases[event.eventPhase];
  console.log("Tag: '" + tag + "'. EventPhase: '" + phase + "'");
}

// 點選以後的結果
// Tag: 'DIV'. EventPhase: 'capture'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'DIV'. EventPhase: 'bubble'
```

上面程式碼表示，`click`事件被觸發了四次：`<div>`節點的捕獲階段和冒泡階段各1次，`<p>`節點的目標階段觸發了2次。

1. 捕獲階段：事件從`<div>`向`<p>`傳播時，觸發`<div>`的`click`事件；
2. 目標階段：事件從`<div>`到達`<p>`時，觸發`<p>`的`click`事件；
3. 冒泡階段：事件從`<p>`傳回`<div>`時，再次觸發`<div>`的`click`事件。

其中，`<p>`節點有兩個監聽函式（`addEventListener`方法第三個引數的不同，會導致繫結兩個監聽函式），因此它們都會因為`click`事件觸發一次。所以，`<p>`會在`target`階段有兩次輸出。

注意，瀏覽器總是假定`click`事件的目標節點，就是點選位置巢狀最深的那個節點（本例是`<div>`節點裡面的`<p>`節點）。所以，`<p>`節點的捕獲階段和冒泡階段，都會顯示為`target`階段。

事件傳播的最上層物件是`window`，接著依次是`document`，`html`（`document.documentElement`）和`body`（`document.body`）。也就是說，上例的事件傳播順序，在捕獲階段依次為`window`、`document`、`html`、`body`、`div`、`p`，在冒泡階段依次為`p`、`div`、`body`、`html`、`document`、`window`。

## 事件的代理

由於事件會在冒泡階段向上傳播到父節點，因此可以把子節點的監聽函式定義在父節點上，由父節點的監聽函式統一處理多個子元素的事件。這種方法叫做事件的代理（delegation）。

```javascript
var ul = document.querySelector('ul');

ul.addEventListener('click', function (event) {
  if (event.target.tagName.toLowerCase() === 'li') {
    // some code
  }
});
```

上面程式碼中，`click`事件的監聽函式定義在`<ul>`節點，但是實際上，它處理的是子節點`<li>`的`click`事件。這樣做的好處是，只要定義一個監聽函式，就能處理多個子節點的事件，而不用在每個`<li>`節點上定義監聽函式。而且以後再新增子節點，監聽函式依然有效。

如果希望事件到某個節點為止，不再傳播，可以使用事件物件的`stopPropagation`方法。

```javascript
// 事件傳播到 p 元素後，就不再向下傳播了
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, true);

// 事件冒泡到 p 元素後，就不再向上冒泡了
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, false);
```

上面程式碼中，`stopPropagation`方法分別在捕獲階段和冒泡階段，阻止了事件的傳播。

但是，`stopPropagation`方法只會阻止事件的傳播，不會阻止該事件觸發`<p>`節點的其他`click`事件的監聽函式。也就是說，不是徹底取消`click`事件。

```javascript
p.addEventListener('click', function (event) {
  event.stopPropagation();
  console.log(1);
});

p.addEventListener('click', function(event) {
  // 會觸發
  console.log(2);
});
```

上面程式碼中，`p`元素綁定了兩個`click`事件的監聽函式。`stopPropagation`方法只能阻止這個事件的傳播，不能取消這個事件，因此，第二個監聽函式會觸發。輸出結果會先是1，然後是2。

如果想要徹底取消該事件，不再觸發後面所有`click`的監聽函式，可以使用`stopImmediatePropagation`方法。

```javascript
p.addEventListener('click', function (event) {
  event.stopImmediatePropagation();
  console.log(1);
});

p.addEventListener('click', function(event) {
  // 不會被觸發
  console.log(2);
});
```

上面程式碼中，`stopImmediatePropagation`方法可以徹底取消這個事件，使得後面繫結的所有`click`監聽函式都不再觸發。所以，只會輸出1，不會輸出2。
