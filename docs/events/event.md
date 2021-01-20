# Event 物件

## 概述

事件發生以後，會產生一個事件物件，作為引數傳給監聽函式。瀏覽器原生提供一個`Event`物件，所有的事件都是這個物件的例項，或者說繼承了`Event.prototype`物件。

`Event`物件本身就是一個建構函式，可以用來生成新的例項。

```javascript
event = new Event(type, options);
```

`Event`建構函式接受兩個引數。第一個引數`type`是字串，表示事件的名稱；第二個引數`options`是一個物件，表示事件物件的配置。該物件主要有下面兩個屬性。

- `bubbles`：布林值，可選，預設為`false`，表示事件物件是否冒泡。
- `cancelable`：布林值，可選，預設為`false`，表示事件是否可以被取消，即能否用`Event.preventDefault()`取消這個事件。一旦事件被取消，就好像從來沒有發生過，不會觸發瀏覽器對該事件的預設行為。

```javascript
var ev = new Event(
  'look',
  {
    'bubbles': true,
    'cancelable': false
  }
);
document.dispatchEvent(ev);
```

上面程式碼新建一個`look`事件例項，然後使用`dispatchEvent`方法觸發該事件。

注意，如果不是顯式指定`bubbles`屬性為`true`，生成的事件就只能在“捕獲階段”觸發監聽函式。

```javascript
// HTML 程式碼為
// <div><p>Hello</p></div>
var div = document.querySelector('div');
var p = document.querySelector('p');

function callback(event) {
  var tag = event.currentTarget.tagName;
  console.log('Tag: ' + tag); // 沒有任何輸出
}

div.addEventListener('click', callback, false);

var click = new Event('click');
p.dispatchEvent(click);
```

上面程式碼中，`p`元素髮出一個`click`事件，該事件預設不會冒泡。`div.addEventListener`方法指定在冒泡階段監聽，因此監聽函式不會觸發。如果寫成`div.addEventListener('click', callback, true)`，那麼在“捕獲階段”可以監聽到這個事件。

另一方面，如果這個事件在`div`元素上觸發。

```javascript
div.dispatchEvent(click);
```

那麼，不管`div`元素是在冒泡階段監聽，還是在捕獲階段監聽，都會觸發監聽函式。因為這時`div`元素是事件的目標，不存在是否冒泡的問題，`div`元素總是會接收到事件，因此導致監聽函式生效。

## 例項屬性

### Event.bubbles，Event.eventPhase

`Event.bubbles`屬性返回一個布林值，表示當前事件是否會冒泡。該屬性為只讀屬性，一般用來了解 Event 例項是否可以冒泡。前面說過，除非顯式宣告，`Event`建構函式生成的事件，預設是不冒泡的。

`Event.eventPhase`屬性返回一個整數常量，表示事件目前所處的階段。該屬性只讀。

```javascript
var phase = event.eventPhase;
```

`Event.eventPhase`的返回值有四種可能。

- 0，事件目前沒有發生。
- 1，事件目前處於捕獲階段，即處於從祖先節點向目標節點的傳播過程中。
- 2，事件到達目標節點，即`Event.target`屬性指向的那個節點。
- 3，事件處於冒泡階段，即處於從目標節點向祖先節點的反向傳播過程中。

### Event.cancelable，Event.cancelBubble，event.defaultPrevented

`Event.cancelable`屬性返回一個布林值，表示事件是否可以取消。該屬性為只讀屬性，一般用來了解 Event 例項的特性。

大多數瀏覽器的原生事件是可以取消的。比如，取消`click`事件，點選連結將無效。但是除非顯式宣告，`Event`建構函式生成的事件，預設是不可以取消的。

```javascript
var evt = new Event('foo');
evt.cancelable  // false
```

當`Event.cancelable`屬性為`true`時，呼叫`Event.preventDefault()`就可以取消這個事件，阻止瀏覽器對該事件的預設行為。

如果事件不能取消，呼叫`Event.preventDefault()`會沒有任何效果。所以使用這個方法之前，最好用`Event.cancelable`屬性判斷一下是否可以取消。

```javascript
function preventEvent(event) {
  if (event.cancelable) {
    event.preventDefault();
  } else {
    console.warn('This event couldn\'t be canceled.');
    console.dir(event);
  }
}
```

`Event.cancelBubble`屬性是一個布林值，如果設為`true`，相當於執行`Event.stopPropagation()`，可以阻止事件的傳播。

`Event.defaultPrevented`屬性返回一個布林值，表示該事件是否呼叫過`Event.preventDefault`方法。該屬性只讀。

```javascript
if (event.defaultPrevented) {
  console.log('該事件已經取消了');
}
```

### Event.currentTarget，Event.target

事件發生以後，會經過捕獲和冒泡兩個階段，依次透過多個 DOM 節點。因此，任意事件都有兩個與事件相關的節點，一個是事件的原始觸發節點（`Event.target`），另一個是事件當前正在透過的節點（`Event.currentTarget`）。前者通常是後者的後代節點。

`Event.currentTarget`屬性返回事件當前所在的節點，即事件當前正在透過的節點，也就是當前正在執行的監聽函式所在的那個節點。隨著事件的傳播，這個屬性的值會變。

`Event.target`屬性返回原始觸發事件的那個節點，即事件最初發生的節點。這個屬性不會隨著事件的傳播而改變。

事件傳播過程中，不同節點的監聽函式內部的`Event.target`與`Event.currentTarget`屬性的值是不一樣的。

```javascript
// HTML 程式碼為
// <p id="para">Hello <em>World</em></p>
function hide(e) {
  // 不管點選 Hello 或 World，總是返回 true
  console.log(this === e.currentTarget);

  // 點選 Hello，返回 true
  // 點選 World，返回 false
  console.log(this === e.target);
}

document.getElementById('para').addEventListener('click', hide, false);
```

上面程式碼中，`<em>`是`<p>`的子節點，點選`<em>`或者點選`<p>`，都會導致監聽函式執行。這時，`e.target`總是指向原始點選位置的那個節點，而`e.currentTarget`指向事件傳播過程中正在經過的那個節點。由於監聽函式只有事件經過時才會觸發，所以`e.currentTarget`總是等同於監聽函式內部的`this`。

### Event.type

`Event.type`屬性返回一個字串，表示事件型別。事件的型別是在生成事件的時候指定的。該屬性只讀。

```javascript
var evt = new Event('foo');
evt.type // "foo"
```

### Event.timeStamp

`Event.timeStamp`屬性返回一個毫秒時間戳，表示事件發生的時間。它是相對於網頁載入成功開始計算的。

```javascript
var evt = new Event('foo');
evt.timeStamp // 3683.6999999995896
```

它的返回值有可能是整數，也有可能是小數（高精度時間戳），取決於瀏覽器的設定。

下面是一個計算滑鼠移動速度的例子，顯示每秒移動的畫素數量。

```javascript
var previousX;
var previousY;
var previousT;

window.addEventListener('mousemove', function(event) {
  if (
    previousX !== undefined &&
    previousY !== undefined &&
    previousT !== undefined
  ) {
    var deltaX = event.screenX - previousX;
    var deltaY = event.screenY - previousY;
    var deltaD = Math.sqrt(Math.pow(deltaX, 2) + Math.pow(deltaY, 2));

    var deltaT = event.timeStamp - previousT;
    console.log(deltaD / deltaT * 1000);
  }

  previousX = event.screenX;
  previousY = event.screenY;
  previousT = event.timeStamp;
});
```

### Event.isTrusted

`Event.isTrusted`屬性返回一個布林值，表示該事件是否由真實的使用者行為產生。比如，使用者點選連結會產生一個`click`事件，該事件是使用者產生的；`Event`建構函式生成的事件，則是指令碼產生的。

```javascript
var evt = new Event('foo');
evt.isTrusted // false
```

上面程式碼中，`evt`物件是指令碼產生的，所以`isTrusted`屬性返回`false`。

### Event.detail

`Event.detail`屬性只有瀏覽器的 UI （使用者介面）事件才具有。該屬性返回一個數值，表示事件的某種資訊。具體含義與事件型別相關。比如，對於`click`和`dblclick`事件，`Event.detail`是滑鼠按下的次數（`1`表示單擊，`2`表示雙擊，`3`表示三擊）；對於滑鼠滾輪事件，`Event.detail`是滾輪正向滾動的距離，負值就是負向滾動的距離，返回值總是3的倍數。

```javascript
// HTML 程式碼如下
// <p>Hello</p>
function giveDetails(e) {
  console.log(e.detail);
}

document.querySelector('p').onclick = giveDetails;
```

## 例項方法

### Event.preventDefault()

`Event.preventDefault`方法取消瀏覽器對當前事件的預設行為。比如點選連結後，瀏覽器預設會跳轉到另一個頁面，使用這個方法以後，就不會跳轉了；再比如，按一下空格鍵，頁面向下滾動一段距離，使用這個方法以後也不會滾動了。該方法生效的前提是，事件物件的`cancelable`屬性為`true`，如果為`false`，呼叫該方法沒有任何效果。

注意，該方法只是取消事件對當前元素的預設影響，不會阻止事件的傳播。如果要阻止傳播，可以使用`stopPropagation()`或`stopImmediatePropagation()`方法。

```javascript
// HTML 程式碼為
// <input type="checkbox" id="my-checkbox" />
var cb = document.getElementById('my-checkbox');

cb.addEventListener(
  'click',
  function (e){ e.preventDefault(); },
  false
);
```

上面程式碼中，瀏覽器的預設行為是單擊會選中單選框，取消這個行為，就導致無法選中單選框。

利用這個方法，可以為文字輸入框設定校驗條件。如果使用者的輸入不符合條件，就無法將字元輸入文字框。

```javascript
// HTML 程式碼為
// <input type="text" id="my-input" />
var input = document.getElementById('my-input');
input.addEventListener('keypress', checkName, false);

function checkName(e) {
  if (e.charCode < 97 || e.charCode > 122) {
    e.preventDefault();
  }
}
```

上面程式碼為文字框的`keypress`事件設定監聽函式後，將只能輸入小寫字母，否則輸入事件的預設行為（寫入文字框）將被取消，導致不能向文字框輸入內容。

### Event.stopPropagation()

`stopPropagation`方法阻止事件在 DOM 中繼續傳播，防止再觸發定義在別的節點上的監聽函式，但是不包括在當前節點上其他的事件監聽函式。

```javascript
function stopEvent(e) {
  e.stopPropagation();
}

el.addEventListener('click', stopEvent, false);
```

上面程式碼中，`click`事件將不會進一步冒泡到`el`節點的父節點。

### Event.stopImmediatePropagation()

`Event.stopImmediatePropagation`方法阻止同一個事件的其他監聽函式被呼叫，不管監聽函式定義在當前節點還是其他節點。也就是說，該方法阻止事件的傳播，比`Event.stopPropagation()`更徹底。

如果同一個節點對於同一個事件指定了多個監聽函式，這些函式會根據新增的順序依次呼叫。只要其中有一個監聽函式呼叫了`Event.stopImmediatePropagation`方法，其他的監聽函式就不會再執行了。

```javascript
function l1(e){
  e.stopImmediatePropagation();
}

function l2(e){
  console.log('hello world');
}

el.addEventListener('click', l1, false);
el.addEventListener('click', l2, false);
```

上面程式碼在`el`節點上，為`click`事件添加了兩個監聽函式`l1`和`l2`。由於`l1`呼叫了`event.stopImmediatePropagation`方法，所以`l2`不會被呼叫。

### Event.composedPath()

`Event.composedPath()`返回一個數組，成員是事件的最底層節點和依次冒泡經過的所有上層節點。

```javascript
// HTML 程式碼如下
// <div>
//   <p>Hello</p>
// </div>
var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', function (e) {
  console.log(e.composedPath());
}, false);
// [p, div, body, html, document, Window]
```

上面程式碼中，`click`事件的最底層節點是`p`，向上依次是`div`、`body`、`html`、`document`、`Window`。
