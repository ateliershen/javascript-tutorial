# GlobalEventHandlers 介面

指定事件的回撥函式，推薦使用的方法是元素的`addEventListener`方法。

```javascript
div.addEventListener('click', clickHandler, false);
```

除了之外，還有一種方法可以直接指定事件的回撥函式。

```javascript
div.onclick = clickHandler;
```

這個介面是由`GlobalEventHandlers`介面提供的。它的優點是使用比較方便，缺點是隻能為每個事件指定一個回撥函式，並且無法指定事件觸發的階段（捕獲階段還是冒泡階段）。

`HTMLElement`、`Document`和`Window`都繼承了這個介面，也就是說，各種 HTML 元素、`document`物件、`window`物件上面都可以使用`GlobalEventHandlers`介面提供的屬性。下面就列出這個介面提供的主要的事件屬性。

## GlobalEventHandlers.onabort

某個物件的`abort`事件（停止載入）發生時，就會呼叫`onabort`屬性指定的回撥函式。

各種元素的停止載入事件，到底如何觸發，目前並沒有統一的規定。因此實際上，這個屬性現在一般只用在`<img>`元素上面。

```javascript
// HTML 程式碼如下
// <img src="example.jpg" id="img">
var img = document.getElementById('img');
img.onabort = function () {
  console.log('image load aborted.');
}
```

## GlobalEventHandlers.onerror

`error`事件發生時，就會呼叫`onerror`屬性指定的回撥函式。

`error`事件分成兩種。

一種是 JavaScript 的執行時錯誤，這會傳到`window`物件，導致`window.onerror()`。

```javascript
window.onerror = function (message, source, lineno, colno, error) {
  // ...
}
```

`window.onerror`的處理函式共接受五個引數，含義如下。

- message：錯誤資訊字串
- source：報錯指令碼的 URL
- lineno：報錯的行號，是一個整數
- colno：報錯的列號，是一個整數
- error： 錯誤物件

另一種是資源載入錯誤，比如`<img>`或`<script>`載入的資源出現載入錯誤。這時，Error 物件會傳到對應的元素，導致該元素的`onerror`屬性開始執行。

```javascript
element.onerror = function (event) {
  // ...
}
```

注意，一般來說，資源的載入錯誤不會觸發`window.onerror`。

## GlobalEventHandlers.onload、GlobalEventHandlers.onloadstart

元素完成載入時，會觸發`load`事件，執行`onload()`。它的典型使用場景是`window`物件和`<img>`元素。對於`window`物件來說，只有頁面的所有資源載入完成（包括圖片、指令碼、樣式表、字型等所有外部資源），才會觸發`load`事件。

對於`<img>`和`<video>`等元素，載入開始時還會觸發`loadstart`事件，導致執行`onloadstart`。

## GlobalEventHandlers.onfocus，GlobalEventHandlers.onblur

當前元素獲得焦點時，會觸發`element.onfocus`；失去焦點時，會觸發`element.onblur`。

```javascript
element.onfocus = function () {
  console.log("onfocus event detected!");
};
element.onblur = function () {
  console.log("onblur event detected!");
};
```

注意，如果不是可以接受使用者輸入的元素，要觸發`onfocus`，該元素必須有`tabindex`屬性。

## GlobalEventHandlers.onscroll

頁面或元素滾動時，會觸發`scroll`事件，導致執行`onscroll()`。

## GlobalEventHandlers.oncontextmenu，GlobalEventHandlers.onshow

使用者在頁面上按下滑鼠的右鍵，會觸發`contextmenu`事件，導致執行`oncontextmenu()`。如果該屬性執行後返回`false`，就等於禁止了右鍵選單。`document.oncontextmenu`與`window.oncontextmenu`效果一樣。

```javascript
document.oncontextmenu = function () {
  return false;
};
```

上面程式碼中，`oncontextmenu`屬性執行後返回`false`，右鍵選單就不會出現。

元素的右鍵選單顯示時，會觸發該元素的`onshow`監聽函式。

## 其他的事件屬性

滑鼠的事件屬性。

- onclick
- ondblclick
- onmousedown
- onmouseenter
- onmouseleave
- onmousemove
- onmouseout
- onmouseover
- onmouseup
- onwheel

鍵盤的事件屬性。

- onkeydown
- onkeypress
- onkeyup

焦點的事件屬性。

- onblur
- onfocus

表單的事件屬性。

- oninput
- onchange
- onsubmit
- onreset
- oninvalid
- onselect

觸控的事件屬性。

- ontouchcancel
- ontouchend
- ontouchmove
- ontouchstart

拖動的事件屬性分成兩類：一類與被拖動元素相關，另一類與接收被拖動元素的容器元素相關。

被拖動元素的事件屬性。

- ondragstart：拖動開始
- ondrag：拖動過程中，每隔幾百毫秒觸發一次
- ondragend：拖動結束

接收被拖動元素的容器元素的事件屬性。

- ondragenter：被拖動元素進入容器元素。
- ondragleave：被拖動元素離開容器元素。
- ondragover：被拖動元素在容器元素上方，每隔幾百毫秒觸發一次。
- ondrop：鬆開滑鼠後，被拖動元素放入容器元素。

`<dialog>`對話方塊元素的事件屬性。

- oncancel
- onclose
