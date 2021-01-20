# 滑鼠事件

## 滑鼠事件的種類

滑鼠事件指與滑鼠相關的事件，繼承了`MouseEvent`介面。具體的事件主要有以下一些。

- `click`：按下滑鼠（通常是按下主按鈕）時觸發。
- `dblclick`：在同一個元素上雙擊滑鼠時觸發。
- `mousedown`：按下滑鼠鍵時觸發。
- `mouseup`：釋放按下的滑鼠鍵時觸發。
- `mousemove`：當滑鼠在一個節點內部移動時觸發。當滑鼠持續移動時，該事件會連續觸發。為了避免效能問題，建議對該事件的監聽函式做一些限定，比如限定一段時間內只能執行一次。
- `mouseenter`：滑鼠進入一個節點時觸發，進入子節點不會觸發這個事件（詳見後文）。
- `mouseover`：滑鼠進入一個節點時觸發，進入子節點會再一次觸發這個事件（詳見後文）。
- `mouseout`：滑鼠離開一個節點時觸發，離開父節點也會觸發這個事件（詳見後文）。
- `mouseleave`：滑鼠離開一個節點時觸發，離開父節點不會觸發這個事件（詳見後文）。
- `contextmenu`：按下滑鼠右鍵時（上下文選單出現前）觸發，或者按下“上下文選單鍵”時觸發。
- `wheel`：滾動滑鼠的滾輪時觸發，該事件繼承的是`WheelEvent`介面。

`click`事件指的是，使用者在同一個位置先完成`mousedown`動作，再完成`mouseup`動作。因此，觸發順序是，`mousedown`首先觸發，`mouseup`接著觸發，`click`最後觸發。

`dblclick`事件則會在`mousedown`、`mouseup`、`click`之後觸發。

`mouseover`事件和`mouseenter`事件，都是滑鼠進入一個節點時觸發。兩者的區別是，`mouseenter`事件只觸發一次，而只要滑鼠在節點內部移動，`mouseover`事件會在子節點上觸發多次。

```javascript
/* HTML 程式碼如下
 <ul>
   <li>item 1</li>
   <li>item 2</li>
  <li>item 3</li>
 </ul>
*/

var ul = document.querySelector('ul');

// 進入 ul 節點以後，mouseenter 事件只會觸發一次
// 以後只要滑鼠在節點內移動，都不會再觸發這個事件
// event.target 是 ul 節點
ul.addEventListener('mouseenter', function (event) {
  event.target.style.color = 'purple';
  setTimeout(function () {
    event.target.style.color = '';
  }, 500);
}, false);

// 進入 ul 節點以後，只要在子節點上移動，mouseover 事件會觸發多次
// event.target 是 li 節點
ul.addEventListener('mouseover', function (event) {
  event.target.style.color = 'orange';
  setTimeout(function () {
    event.target.style.color = '';
  }, 500);
}, false);
```

上面程式碼中，在父節點內部進入子節點，不會觸發`mouseenter`事件，但是會觸發`mouseover`事件。

`mouseout`事件和`mouseleave`事件，都是滑鼠離開一個節點時觸發。兩者的區別是，在父元素內部離開一個子元素時，`mouseleave`事件不會觸發，而`mouseout`事件會觸發。

```javascript
/* HTML 程式碼如下
 <ul>
   <li>item 1</li>
   <li>item 2</li>
  <li>item 3</li>
 </ul>
*/

var ul = document.querySelector('ul');

// 先進入 ul 節點，然後在節點內部移動，不會觸發 mouseleave 事件
// 只有離開 ul 節點時，觸發一次 mouseleave
// event.target 是 ul 節點
ul.addEventListener('mouseleave', function (event) {
  event.target.style.color = 'purple';
  setTimeout(function () {
    event.target.style.color = '';
  }, 500);
}, false);

// 先進入 ul 節點，然後在節點內部移動，mouseout 事件會觸發多次
// event.target 是 li 節點
ul.addEventListener('mouseout', function (event) {
  event.target.style.color = 'orange';
  setTimeout(function () {
    event.target.style.color = '';
  }, 500);
}, false);
```

上面程式碼中，在父節點內部離開子節點，不會觸發`mouseleave`事件，但是會觸發`mouseout`事件。

## MouseEvent 介面概述

`MouseEvent`介面代表了滑鼠相關的事件，單擊（click）、雙擊（dblclick）、鬆開滑鼠鍵（mouseup）、按下滑鼠鍵（mousedown）等動作，所產生的事件物件都是`MouseEvent`例項。此外，滾輪事件和拖拉事件也是`MouseEvent`例項。

`MouseEvent`介面繼承了`Event`介面，所以擁有`Event`的所有屬性和方法。它還有自己的屬性和方法。

瀏覽器原生提供一個`MouseEvent`建構函式，用於新建一個`MouseEvent`例項。

```javascript
var event = new MouseEvent(type, options);
```

`MouseEvent`建構函式接受兩個引數。第一個引數是字串，表示事件名稱；第二個引數是一個事件配置物件，該引數可選。除了`Event`介面的例項配置屬性，該物件可以配置以下屬性，所有屬性都是可選的。

- `screenX`：數值，滑鼠相對於螢幕的水平位置（單位畫素），預設值為0，設定該屬性不會移動滑鼠。
- `screenY`：數值，滑鼠相對於螢幕的垂直位置（單位畫素），其他與`screenX`相同。
- `clientX`：數值，滑鼠相對於程式視窗的水平位置（單位畫素），預設值為0，設定該屬性不會移動滑鼠。
- `clientY`：數值，滑鼠相對於程式視窗的垂直位置（單位畫素），其他與`clientX`相同。
- `ctrlKey`：布林值，是否同時按下了 Ctrl 鍵，預設值為`false`。
- `shiftKey`：布林值，是否同時按下了 Shift 鍵，預設值為`false`。
- `altKey`：布林值，是否同時按下 Alt 鍵，預設值為`false`。
- `metaKey`：布林值，是否同時按下 Meta 鍵，預設值為`false`。
- `button`：數值，表示按下了哪一個滑鼠按鍵，預設值為`0`，表示按下主鍵（通常是滑鼠的左鍵）或者當前事件沒有定義這個屬性；`1`表示按下輔助鍵（通常是滑鼠的中間鍵），`2`表示按下次要鍵（通常是滑鼠的右鍵）。
- `buttons`：數值，表示按下了滑鼠的哪些鍵，是一個三個位元位的二進位制值，預設為`0`（沒有按下任何鍵）。`1`（二進位制`001`）表示按下主鍵（通常是左鍵），`2`（二進位制`010`）表示按下次要鍵（通常是右鍵），`4`（二進位制`100`）表示按下輔助鍵（通常是中間鍵）。因此，如果返回`3`（二進位制`011`）就表示同時按下了左鍵和右鍵。
- `relatedTarget`：節點物件，表示事件的相關節點，預設為`null`。`mouseenter`和`mouseover`事件時，表示滑鼠剛剛離開的那個元素節點；`mouseout`和`mouseleave`事件時，表示滑鼠正在進入的那個元素節點。

下面是一個例子。

```javascript
function simulateClick() {
  var event = new MouseEvent('click', {
    'bubbles': true,
    'cancelable': true
  });
  var cb = document.getElementById('checkbox');
  cb.dispatchEvent(event);
}
```

上面程式碼生成一個滑鼠點選事件，並觸發該事件。

## MouseEvent 介面的例項屬性

### MouseEvent.altKey，MouseEvent.ctrlKey，MouseEvent.metaKey，MouseEvent.shiftKey

`MouseEvent.altKey`、`MouseEvent.ctrlKey`、`MouseEvent.metaKey`、`MouseEvent.shiftKey`這四個屬性都返回一個布林值，表示事件發生時，是否按下對應的鍵。它們都是隻讀屬性。

- `altKey`屬性：Alt 鍵
- `ctrlKey`屬性：Ctrl 鍵
- `metaKey`屬性：Meta 鍵（Mac 鍵盤是一個四瓣的小花，Windows 鍵盤是 Windows 鍵）
- `shiftKey`屬性：Shift 鍵

```javascript
// HTML 程式碼如下
// <body onclick="showKey(event)">
function showKey(e) {
  console.log('ALT key pressed: ' + e.altKey);
  console.log('CTRL key pressed: ' + e.ctrlKey);
  console.log('META key pressed: ' + e.metaKey);
  console.log('SHIFT key pressed: ' + e.shiftKey);
}
```

上面程式碼中，點選網頁會輸出是否同時按下對應的鍵。

### MouseEvent.button，MouseEvent.buttons

`MouseEvent.button`屬性返回一個數值，表示事件發生時按下了滑鼠的哪個鍵。該屬性只讀。

- 0：按下主鍵（通常是左鍵），或者該事件沒有初始化這個屬性（比如`mousemove`事件）。
- 1：按下輔助鍵（通常是中鍵或者滾輪鍵）。
- 2：按下次鍵（通常是右鍵）。

```javascript
// HTML 程式碼為
// <button onmouseup="whichButton(event)">點選</button>
var whichButton = function (e) {
  switch (e.button) {
    case 0:
      console.log('Left button clicked.');
      break;
    case 1:
      console.log('Middle button clicked.');
      break;
    case 2:
      console.log('Right button clicked.');
      break;
    default:
      console.log('Unexpected code: ' + e.button);
  }
}
```

`MouseEvent.buttons`屬性返回一個三個位元位的值，表示同時按下了哪些鍵。它用來處理同時按下多個滑鼠鍵的情況。該屬性只讀。

- 1：二進位制為`001`（十進位制的1），表示按下左鍵。
- 2：二進位制為`010`（十進位制的2），表示按下右鍵。
- 4：二進位制為`100`（十進位制的4），表示按下中鍵或滾輪鍵。

同時按下多個鍵的時候，每個按下的鍵對應的位元位都會有值。比如，同時按下左鍵和右鍵，會返回3（二進位制為011）。

### MouseEvent.clientX，MouseEvent.clientY

`MouseEvent.clientX`屬性返回滑鼠位置相對於瀏覽器視窗左上角的水平座標（單位畫素），`MouseEvent.clientY`屬性返回垂直座標。這兩個屬性都是隻讀屬性。

```javascript
// HTML 程式碼為
// <body onmousedown="showCoords(event)">
function showCoords(evt){
  console.log(
    'clientX value: ' + evt.clientX + '\n' +
    'clientY value: ' + evt.clientY + '\n'
  );
}
```

這兩個屬性還分別有一個別名`MouseEvent.x`和`MouseEvent.y`。

### MouseEvent.movementX，MouseEvent.movementY

`MouseEvent.movementX`屬性返回當前位置與上一個`mousemove`事件之間的水平距離（單位畫素）。數值上，它等於下面的計算公式。

```javascript
currentEvent.movementX = currentEvent.screenX - previousEvent.screenX
```

`MouseEvent.movementY`屬性返回當前位置與上一個`mousemove`事件之間的垂直距離（單位畫素）。數值上，它等於下面的計算公式。

```javascript
currentEvent.movementY = currentEvent.screenY - previousEvent.screenY。
```

這兩個屬性都是隻讀屬性。

### MouseEvent.screenX，MouseEvent.screenY

`MouseEvent.screenX`屬性返回滑鼠位置相對於螢幕左上角的水平座標（單位畫素），`MouseEvent.screenY`屬性返回垂直座標。這兩個屬性都是隻讀屬性。

```javascript
// HTML 程式碼如下
// <body onmousedown="showCoords(event)">
function showCoords(evt) {
  console.log(
    'screenX value: ' + evt.screenX + '\n',
    'screenY value: ' + evt.screenY + '\n'
  );
}
```

### MouseEvent.offsetX，MouseEvent.offsetY

`MouseEvent.offsetX`屬性返回滑鼠位置與目標節點左側的`padding`邊緣的水平距離（單位畫素），`MouseEvent.offsetY`屬性返回與目標節點上方的`padding`邊緣的垂直距離。這兩個屬性都是隻讀屬性。

```javascript
/* HTML 程式碼如下
  <style>
    p {
      width: 100px;
      height: 100px;
      padding: 100px;
    }
  </style>
  <p>Hello</p>
*/
var p = document.querySelector('p');
p.addEventListener(
  'click',
  function (e) {
    console.log(e.offsetX);
    console.log(e.offsetY);
  },
  false
);
```

上面程式碼中，滑鼠如果在`p`元素的中心位置點選，會返回`150 150`。因此中心位置距離左側和上方的`padding`邊緣，等於`padding`的寬度（100畫素）加上元素內容區域一半的寬度（50畫素）。

### MouseEvent.pageX，MouseEvent.pageY

`MouseEvent.pageX`屬性返回滑鼠位置與文件左側邊緣的距離（單位畫素），`MouseEvent.pageY`屬性返回與文件上側邊緣的距離（單位畫素）。它們的返回值都包括文件不可見的部分。這兩個屬性都是隻讀。

```javascript
/* HTML 程式碼如下
  <style>
    body {
      height: 2000px;
    }
  </style>
*/
document.body.addEventListener(
  'click',
  function (e) {
    console.log(e.pageX);
    console.log(e.pageY);
  },
  false
);
```

上面程式碼中，頁面高度為2000畫素，會產生垂直捲軸。滾動到頁面底部，點選滑鼠輸出的`pageY`值會接近2000。

### MouseEvent.relatedTarget

`MouseEvent.relatedTarget`屬性返回事件的相關節點。對於那些沒有相關節點的事件，該屬性返回`null`。該屬性只讀。

下表列出不同事件的`target`屬性值和`relatedTarget`屬性值義。

|事件名稱 |target 屬性 |relatedTarget 屬性 |
|---------|-----------|------------------|
|focusin |接受焦點的節點 |喪失焦點的節點 |
|focusout |喪失焦點的節點 |接受焦點的節點 |
|mouseenter |將要進入的節點 |將要離開的節點 |
|mouseleave |將要離開的節點 |將要進入的節點 |
|mouseout |將要離開的節點 |將要進入的節點 |
|mouseover |將要進入的節點 |將要離開的節點 |
|dragenter |將要進入的節點 |將要離開的節點 |
|dragexit |將要離開的節點 |將要進入的節點 |

下面是一個例子。

```javascript
/*
  HTML 程式碼如下
  <div id="outer" style="height:50px;width:50px;border:1px solid black;">
    <div id="inner" style="height:25px;width:25px;border:1px solid black;"></div>
  </div>
*/

var inner = document.getElementById('inner');
inner.addEventListener('mouseover', function (event) {
  console.log('進入' + event.target.id + ' 離開' + event.relatedTarget.id);
}, false);
inner.addEventListener('mouseenter', function (event) {
  console.log('進入' + event.target.id + ' 離開' + event.relatedTarget.id);
});
inner.addEventListener('mouseout', function () {
  console.log('離開' + event.target.id + ' 進入' + event.relatedTarget.id);
});
inner.addEventListener("mouseleave", function (){
  console.log('離開' + event.target.id + ' 進入' + event.relatedTarget.id);
});

// 滑鼠從 outer 進入inner，輸出
// 進入inner 離開outer
// 進入inner 離開outer

// 滑鼠從 inner進入 outer，輸出
// 離開inner 進入outer
// 離開inner 進入outer
```

## MouseEvent 介面的例項方法

### MouseEvent.getModifierState()

`MouseEvent.getModifierState`方法返回一個布林值，表示有沒有按下特定的功能鍵。它的引數是一個表示[功能鍵](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState#Modifier_keys_on_Gecko)的字串。

```javascript
document.addEventListener('click', function (e) {
  console.log(e.getModifierState('CapsLock'));
}, false);
```

上面的程式碼可以瞭解使用者是否按下了大寫鍵。

## WheelEvent 介面

### 概述

WheelEvent 介面繼承了 MouseEvent 例項，代表滑鼠滾輪事件的例項物件。目前，滑鼠滾輪相關的事件只有一個`wheel`事件，使用者滾動滑鼠的滾輪，就生成這個事件的例項。

瀏覽器原生提供`WheelEvent()`建構函式，用來生成`WheelEvent`例項。

```javascript
var wheelEvent = new WheelEvent(type, options);
```

`WheelEvent()`建構函式可以接受兩個引數，第一個是字串，表示事件型別，對於滾輪事件來說，這個值目前只能是`wheel`。第二個引數是事件的配置物件。該物件的屬性除了`Event`、`UIEvent`的配置屬性以外，還可以接受以下幾個屬性，所有屬性都是可選的。

- `deltaX`：數值，表示滾輪的水平滾動量，預設值是 0.0。
- `deltaY`：數值，表示滾輪的垂直滾動量，預設值是 0.0。
- `deltaZ`：數值，表示滾輪的 Z 軸滾動量，預設值是 0.0。
- `deltaMode`：數值，表示相關的滾動事件的單位，適用於上面三個屬性。`0`表示滾動單位為畫素，`1`表示單位為行，`2`表示單位為頁，預設為`0`。

### 例項屬性

`WheelEvent`事件例項除了具有`Event`和`MouseEvent`的例項屬性和例項方法，還有一些自己的例項屬性，但是沒有自己的例項方法。

下面的屬性都是隻讀屬性。

- `WheelEvent.deltaX`：數值，表示滾輪的水平滾動量。
- `WheelEvent.deltaY`：數值，表示滾輪的垂直滾動量。
- `WheelEvent.deltaZ`：數值，表示滾輪的 Z 軸滾動量。
- `WheelEvent.deltaMode`：數值，表示上面三個屬性的單位，`0`是畫素，`1`是行，`2`是頁。
