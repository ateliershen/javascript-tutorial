# 觸控事件

## 觸控操作概述

瀏覽器的觸控 API 由三個部分組成。

- Touch：一個觸控點
- TouchList：多個觸控點的集合
- TouchEvent：觸控引發的事件例項

`Touch`介面的例項物件用來表示觸控點（一根手指或者一根觸控筆），包括位置、大小、形狀、壓力、目標元素等屬性。有時，觸控動作由多個觸控點（多根手指）組成，多個觸控點的集合由`TouchList`介面的例項物件表示。`TouchEvent`介面的例項物件代表由觸控引發的事件，只有觸控式螢幕才會引發這一類事件。

很多時候，觸控事件和滑鼠事件同時觸發，即使這個時候並沒有用到滑鼠。這是為了讓那些只定義滑鼠事件、沒有定義觸控事件的程式碼，在觸控式螢幕的情況下仍然能用。如果想避免這種情況，可以用`event.preventDefault`方法阻止發出滑鼠事件。

## Touch 介面

### Touch 介面概述

Touch 介面代表單個觸控點。觸控點可能是一根手指，也可能是一根觸控筆。

瀏覽器原生提供`Touch`建構函式，用來生成`Touch`例項。

```javascript
var touch = new Touch(touchOptions);
```

`Touch`建構函式接受一個配置物件作為引數，它有以下屬性。

- `identifier`：必需，型別為整數，表示觸控點的唯一 ID。
- `target`：必需，型別為元素節點，表示觸控點開始時所在的網頁元素。
- `clientX`：可選，型別為數值，表示觸控點相對於瀏覽器視窗左上角的水平距離，預設為0。
- `clientY`：可選，型別為數值，表示觸控點相對於瀏覽器視窗左上角的垂直距離，預設為0。
- `screenX`：可選，型別為數值，表示觸控點相對於螢幕左上角的水平距離，預設為0。
- `screenY`：可選，型別為數值，表示觸控點相對於螢幕左上角的垂直距離，預設為0。
- `pageX`：可選，型別為數值，表示觸控點相對於網頁左上角的水平位置（即包括頁面的滾動距離），預設為0。
- `pageY`：可選，型別為數值，表示觸控點相對於網頁左上角的垂直位置（即包括頁面的滾動距離），預設為0。
- `radiusX`：可選，型別為數值，表示觸控點周圍受到影響的橢圓範圍的 X 軸半徑，預設為0。
- `radiusY`：可選：型別為數值，表示觸控點周圍受到影響的橢圓範圍的 Y 軸半徑，預設為0。
- `rotationAngle`：可選，型別為數值，表示觸控區域的橢圓的旋轉角度，單位為度數，在0到90度之間，預設值為0。
- `force`：可選，型別為數值，範圍在`0`到`1`之間，表示觸控壓力。`0`代表沒有壓力，`1`代表硬體所能識別的最大壓力，預設為`0`。

### Touch 介面的例項屬性

**（1）Touch.identifier**

`Touch.identifier`屬性返回一個整數，表示觸控點的唯一 ID。這個值在整個觸控過程保持不變，直到觸控事件結束。

```javascript
someElement.addEventListener('touchmove', function (e) {
  for (var i = 0; i < e.changedTouches.length; i++) {
    console.log(e.changedTouches[i].identifier);
  }
}, false);
```

**（2）Touch.screenX，Touch.screenY，Touch.clientX，Touch.clientY，pageX，pageY**

`Touch.screenX`屬性和`Touch.screenY`屬性，分別表示觸控點相對於螢幕左上角的橫座標和縱座標，與頁面是否滾動無關。

`Touch.clientX`屬性和`Touch.clientY`屬性，分別表示觸控點相對於瀏覽器視口左上角的橫座標和縱座標，與頁面是否滾動無關。

`Touch.pageX`屬性和`Touch.pageY`屬性，分別表示觸控點相對於當前頁面左上角的橫座標和縱座標，包含了頁面滾動帶來的位移。

**（3）Touch.radiusX，Touch.radiusY，Touch.rotationAngle**

`Touch.radiusX`屬性和`Touch.radiusY`屬性，分別返回觸控點周圍受到影響的橢圓範圍的 X 軸半徑和 Y 軸半徑，單位為畫素。乘以 2 就可以得到觸控範圍的寬度和高度。

`Touch.rotationAngle`屬性表示觸控區域的橢圓的旋轉角度，單位為度數，在`0`到`90`度之間。

上面這三個屬性共同定義了使用者與螢幕接觸的區域，對於描述手指這一類非精確的觸控，很有幫助。指尖接觸螢幕，觸控範圍會形成一個橢圓，這三個屬性就用來描述這個橢圓區域。

下面是一個示例。

```javascript
div.addEventListener('touchstart', rotate);
div.addEventListener('touchmove', rotate);
div.addEventListener('touchend', rotate);

function rotate(e) {
  var touch = e.changedTouches.item(0);
  e.preventDefault();

  src.style.width = touch.radiusX * 2 + 'px';
  src.style.height = touch.radiusY * 2 + 'px';
  src.style.transform = 'rotate(' + touch.rotationAngle + 'deg)';
};
```

**（4）Touch.force**

`Touch.force`屬性返回一個`0`到`1`之間的數值，表示觸控壓力。`0`代表沒有壓力，`1`代表硬體所能識別的最大壓力。

**（5）Touch.target**

`Touch.target`屬性返回一個元素節點，代表觸控發生時所在的那個元素節點。即使觸控點已經離開了這個節點，該屬性依然不變。

## TouchList 介面

`TouchList`介面表示一組觸控點的集合。它的例項是一個類似陣列的物件，成員是`Touch`的例項物件，表示所有觸控點。使用者用三根手指觸控，產生的`TouchList`例項就會包含三個成員，每根手指的觸控點對應一個`Touch`例項物件。

它的例項主要透過觸控事件的`TouchEvent.touches`、`TouchEvent.changedTouches`、`TouchEvent.targetTouches`這幾個屬性獲取。

它的例項屬性和例項方法只有兩個。

- `TouchList.length`：數值，表示成員數量（即觸控點的數量）。
- `TouchList.item()`：返回指定位置的成員，它的引數是該成員的位置編號（從零開始）。

## TouchEvent 介面

### 概述

TouchEvent 介面繼承了 Event 介面，表示由觸控引發的事件例項，通常來自觸控式螢幕或軌跡板。除了被繼承的屬性以外，它還有一些自己的屬性。

瀏覽器原生提供`TouchEvent()`建構函式，用來生成觸控事件的例項。

```javascript
new TouchEvent(type, options)
```

`TouchEvent()`建構函式可以接受兩個引數，第一個引數是字串，表示事件型別；第二個引數是事件的配置物件，該引數是可選的，物件的所有屬性也是可選的。除了`Event`介面的配置屬性，該介面還有一些自己的配置屬性。

- `touches`：`TouchList`例項，代表所有的當前處於活躍狀態的觸控點，預設值是一個空陣列`[]`。
- `targetTouches`：`TouchList`例項，代表所有處在觸控的目標元素節點內部、且仍然處於活動狀態的觸控點，預設值是一個空陣列`[]`。
- `changedTouches`：`TouchList`例項，代表本次觸控事件的相關觸控點，預設值是一個空陣列`[]`。
- `ctrlKey`：布林值，表示 Ctrl 鍵是否同時按下，預設值為`false`。
- `shiftKey`：布林值，表示 Shift 鍵是否同時按下，預設值為`false`。
- `altKey`：布林值，表示 Alt 鍵是否同時按下，預設值為`false`。
- `metaKey`：布林值，表示 Meta 鍵（或 Windows 鍵）是否同時按下，預設值為`false`。

### 例項屬性

TouchEvent 介面的例項具有`Event`例項的所有屬性和方法，此外還有一些它自己的例項屬性，這些屬性全部都是隻讀。

**（1）TouchEvent.altKey，TouchEvent.ctrlKey，TouchEvent.shiftKey，TouchEvent.metaKey**

- `TouchEvent.altKey`：布林值，表示觸控時是否按下了 Alt 鍵。
- `TouchEvent.ctrlKey`：布林值，表示觸控時是否按下了 Ctrl 鍵。
- `TouchEvent.shiftKey`：布林值：表示觸控時是否按下了 Shift 鍵。
- `TouchEvent.metaKey`：布林值，表示觸控時是否按下了 Meta 鍵（或 Windows 鍵）。

下面是一個示例。

```javascript
someElement.addEventListener('touchstart', function (e) {
  console.log('altKey = ' + e.altKey);
  console.log('ctrlKey = ' + e.ctrlKey);
  console.log('metaKey = ' + e.metaKey);
  console.log('shiftKey = ' + e.shiftKey);
}, false);
```

**（2）TouchEvent.changedTouches**

`TouchEvent.changedTouches`屬性返回一個`TouchList`例項，成員是一組`Touch`例項物件，表示本次觸控事件的相關觸控點。

對於不同的時間，該屬性的含義有所不同。

- `touchstart`事件：被啟用的觸控點
- `touchmove`事件：發生變化的觸控點
- `touchend`事件：消失的觸控點（即不再被觸碰的點）

下面是一個示例。

```javascript
someElement.addEventListener('touchmove', function (e) {
  for (var i = 0; i < e.changedTouches.length; i++) {
    console.log(e.changedTouches[i].identifier);
  }
}, false);
```

**（3）TouchEvent.touches**

`TouchEvent.touches`屬性返回一個`TouchList`例項，成員是所有仍然處於活動狀態（即觸控中）的觸控點。一般來說，一個手指就是一個觸控點。

下面是一個示例。

```javascript
someElement.addEventListener('touchstart', function (e) {
  switch (e.touches.length) {
    // 一根手指觸控
    case 1: handle_one_touch(e); break;
    // 兩根手指觸控
    case 2: handle_two_touches(e); break;
    // 三根手指觸控
    case 3: handle_three_touches(e); break;
    // 其他情況
    default: console.log('Not supported'); break;
  }
}, false);
```

**（4）TouchEvent.targetTouches**

`TouchEvent.targetTouches`屬性返回一個`TouchList`例項，成員是觸控事件的目標元素節點內部、所有仍然處於活動狀態（即觸控中）的觸控點。

```javascript
function touches_in_target(ev) {
  return (ev.touches.length === ev.targetTouches.length ? true : false);
}
```

上面程式碼用來判斷，是否所有觸控點都在目標元素內。

## 觸控事件的種類

觸控引發的事件，有以下幾種。可以透過`TouchEvent.type`屬性，檢視到底發生的是哪一種事件。

- `touchstart`：使用者開始觸控時觸發，它的`target`屬性返回發生觸控的元素節點。
- `touchend`：使用者不再接觸觸控式螢幕時（或者移出螢幕邊緣時）觸發，它的`target`屬性與`touchstart`事件一致的，就是開始觸控時所在的元素節點。它的`changedTouches`屬性返回一個`TouchList`例項，包含所有不再觸控的觸控點（即`Touch`例項物件）。
- `touchmove`：使用者移動觸控點時觸發，它的`target`屬性與`touchstart`事件一致。如果觸控的半徑、角度、力度發生變化，也會觸發該事件。
- `touchcancel`：觸控點取消時觸發，比如在觸控區域跳出一個模態視窗（modal window）、觸控點離開了文件區域（進入瀏覽器選單欄）、使用者的觸控點太多，超過了支援的上限（自動取消早先的觸控點）。

下面是一個例子。

```javascript
var el = document.getElementsByTagName('canvas')[0];
el.addEventListener('touchstart', handleStart, false);
el.addEventListener('touchmove', handleMove, false);

function handleStart(evt) {
  evt.preventDefault();
  var touches = evt.changedTouches;
  for (var i = 0; i < touches.length; i++) {
    console.log(touches[i].pageX, touches[i].pageY);
  }
}

function handleMove(evt) {
  evt.preventDefault();
  var touches = evt.changedTouches;
  for (var i = 0; i < touches.length; i++) {
    var touch = touches[i];
    console.log(touch.pageX, touch.pageY);
  }
}
```
