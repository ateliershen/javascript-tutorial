# EventTarget 介面

事件的本質是程式各個組成部分之間的一種通訊方式，也是非同步程式設計的一種實現。DOM 支援大量的事件，本章開始介紹 DOM 的事件程式設計。

## 概述

DOM 的事件操作（監聽和觸發），都定義在`EventTarget`介面。所有節點物件都部署了這個介面，其他一些需要事件通訊的瀏覽器內建物件（比如，`XMLHttpRequest`、`AudioNode`、`AudioContext`）也部署了這個介面。

該介面主要提供三個例項方法。

- `addEventListener`：繫結事件的監聽函式
- `removeEventListener`：移除事件的監聽函式
- `dispatchEvent`：觸發事件

## EventTarget.addEventListener()

`EventTarget.addEventListener()`用於在當前節點或物件上，定義一個特定事件的監聽函式。一旦這個事件發生，就會執行監聽函式。該方法沒有返回值。

```javascript
target.addEventListener(type, listener[, useCapture]);
```

該方法接受三個引數。

- `type`：事件名稱，大小寫敏感。
- `listener`：監聽函式。事件發生時，會呼叫該監聽函式。
- `useCapture`：布林值，表示監聽函式是否在捕獲階段（capture）觸發（參見後文《事件的傳播》部分），預設為`false`（監聽函式只在冒泡階段被觸發）。該引數可選。

下面是一個例子。

```javascript
function hello() {
  console.log('Hello world');
}

var button = document.getElementById('btn');
button.addEventListener('click', hello, false);
```

上面程式碼中，`button`節點的`addEventListener`方法繫結`click`事件的監聽函式`hello`，該函式只在冒泡階段觸發。

關於引數，有兩個地方需要注意。

首先，第二個引數除了監聽函式，還可以是一個具有`handleEvent`方法的物件。

```javascript
buttonElement.addEventListener('click', {
  handleEvent: function (event) {
    console.log('click');
  }
});
```

上面程式碼中，`addEventListener`方法的第二個引數，就是一個具有`handleEvent`方法的物件。

其次，第三個引數除了布林值`useCapture`，還可以是一個屬性配置物件。該物件有以下屬性。

> - `capture`：布林值，表示該事件是否在`捕獲階段`觸發監聽函式。
> - `once`：布林值，表示監聽函式是否只觸發一次，然後就自動移除。
> - `passive`：布林值，表示監聽函式不會呼叫事件的`preventDefault`方法。如果監聽函式呼叫了，瀏覽器將忽略這個要求，並在監控臺輸出一行警告。

如果希望事件監聽函式只執行一次，可以開啟屬性配置物件的`once`屬性。

```javascript
element.addEventListener('click', function (event) {
  // 只執行一次的程式碼
}, {once: true});
```

`addEventListener`方法可以為針對當前物件的同一個事件，新增多個不同的監聽函式。這些函式按照新增順序觸發，即先新增先觸發。如果為同一個事件多次新增同一個監聽函式，該函式只會執行一次，多餘的新增將自動被去除（不必使用`removeEventListener`方法手動去除）。

```javascript
function hello() {
  console.log('Hello world');
}

document.addEventListener('click', hello, false);
document.addEventListener('click', hello, false);
```

執行上面程式碼，點選文件只會輸出一行`Hello world`。

如果希望向監聽函式傳遞引數，可以用匿名函式包裝一下監聽函式。

```javascript
function print(x) {
  console.log(x);
}

var el = document.getElementById('div1');
el.addEventListener('click', function () { print('Hello'); }, false);
```

上面程式碼透過匿名函式，向監聽函式`print`傳遞了一個引數。

監聽函式內部的`this`，指向當前事件所在的那個物件。

```javascript
// HTML 程式碼如下
// <p id="para">Hello</p>
var para = document.getElementById('para');
para.addEventListener('click', function (e) {
  console.log(this.nodeName); // "P"
}, false);
```

上面程式碼中，監聽函式內部的`this`指向事件所在的物件`para`。

## EventTarget.removeEventListener()

`EventTarget.removeEventListener`方法用來移除`addEventListener`方法新增的事件監聽函式。該方法沒有返回值。

```javascript
div.addEventListener('click', listener, false);
div.removeEventListener('click', listener, false);
```

`removeEventListener`方法的引數，與`addEventListener`方法完全一致。它的第一個引數“事件型別”，大小寫敏感。

注意，`removeEventListener`方法移除的監聽函式，必須是`addEventListener`方法新增的那個監聽函式，而且必須在同一個元素節點，否則無效。

```javascript
div.addEventListener('click', function (e) {}, false);
div.removeEventListener('click', function (e) {}, false);
```

上面程式碼中，`removeEventListener`方法無效，因為監聽函式不是同一個匿名函式。

```javascript
element.addEventListener('mousedown', handleMouseDown, true);
element.removeEventListener("mousedown", handleMouseDown, false);
```

上面程式碼中，`removeEventListener`方法也是無效的，因為第三個引數不一樣。

## EventTarget.dispatchEvent()

`EventTarget.dispatchEvent`方法在當前節點上觸發指定事件，從而觸發監聽函式的執行。該方法返回一個布林值，只要有一個監聽函式呼叫了`Event.preventDefault()`，則返回值為`false`，否則為`true`。

```javascript
target.dispatchEvent(event)
```

`dispatchEvent`方法的引數是一個`Event`物件的例項（詳見《Event 物件》章節）。

```javascript
para.addEventListener('click', hello, false);
var event = new Event('click');
para.dispatchEvent(event);
```

上面程式碼在當前節點觸發了`click`事件。

如果`dispatchEvent`方法的引數為空，或者不是一個有效的事件物件，將報錯。

下面程式碼根據`dispatchEvent`方法的返回值，判斷事件是否被取消了。

```javascript
var canceled = !cb.dispatchEvent(event);
if (canceled) {
  console.log('事件取消');
} else {
  console.log('事件未取消');
}
```
