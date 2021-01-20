# 定時器

JavaScript 提供定時執行程式碼的功能，叫做定時器（timer），主要由`setTimeout()`和`setInterval()`這兩個函式來完成。它們向任務佇列新增定時任務。

## setTimeout()

`setTimeout`函式用來指定某個函式或某段程式碼，在多少毫秒之後執行。它返回一個整數，表示定時器的編號，以後可以用來取消這個定時器。

```javascript
var timerId = setTimeout(func|code, delay);
```

上面程式碼中，`setTimeout`函式接受兩個引數，第一個引數`func|code`是將要推遲執行的函式名或者一段程式碼，第二個引數`delay`是推遲執行的毫秒數。

```javascript
console.log(1);
setTimeout('console.log(2)',1000);
console.log(3);
// 1
// 3
// 2
```

上面程式碼會先輸出1和3，然後等待1000毫秒再輸出2。注意，`console.log(2)`必須以字串的形式，作為`setTimeout`的引數。

如果推遲執行的是函式，就直接將函式名，作為`setTimeout`的引數。

```javascript
function f() {
  console.log(2);
}

setTimeout(f, 1000);
```

`setTimeout`的第二個引數如果省略，則預設為0。

```javascript
setTimeout(f)
// 等同於
setTimeout(f, 0)
```

除了前兩個引數，`setTimeout`還允許更多的引數。它們將依次傳入推遲執行的函式（回撥函式）。

```javascript
setTimeout(function (a,b) {
  console.log(a + b);
}, 1000, 1, 1);
```

上面程式碼中，`setTimeout`共有4個引數。最後那兩個引數，將在1000毫秒之後回撥函式執行時，作為回撥函式的引數。

還有一個需要注意的地方，如果回撥函式是物件的方法，那麼`setTimeout`使得方法內部的`this`關鍵字指向全域性環境，而不是定義時所在的那個物件。

```javascript
var x = 1;

var obj = {
  x: 2,
  y: function () {
    console.log(this.x);
  }
};

setTimeout(obj.y, 1000) // 1
```

上面程式碼輸出的是1，而不是2。因為當`obj.y`在1000毫秒後執行時，`this`所指向的已經不是`obj`了，而是全域性環境。

為了防止出現這個問題，一種解決方法是將`obj.y`放入一個函式。

```javascript
var x = 1;

var obj = {
  x: 2,
  y: function () {
    console.log(this.x);
  }
};

setTimeout(function () {
  obj.y();
}, 1000);
// 2
```

上面程式碼中，`obj.y`放在一個匿名函式之中，這使得`obj.y`在`obj`的作用域執行，而不是在全域性作用域內執行，所以能夠顯示正確的值。

另一種解決方法是，使用`bind`方法，將`obj.y`這個方法繫結在`obj`上面。

```javascript
var x = 1;

var obj = {
  x: 2,
  y: function () {
    console.log(this.x);
  }
};

setTimeout(obj.y.bind(obj), 1000)
// 2
```

## setInterval()

`setInterval`函式的用法與`setTimeout`完全一致，區別僅僅在於`setInterval`指定某個任務每隔一段時間就執行一次，也就是無限次的定時執行。

```javascript
var i = 1
var timer = setInterval(function() {
  console.log(2);
}, 1000)
```

上面程式碼中，每隔1000毫秒就輸出一個2，會無限執行下去，直到關閉當前視窗。

與`setTimeout`一樣，除了前兩個引數，`setInterval`方法還可以接受更多的引數，它們會傳入回撥函式。

下面是一個透過`setInterval`方法實現網頁動畫的例子。

```javascript
var div = document.getElementById('someDiv');
var opacity = 1;
var fader = setInterval(function() {
  opacity -= 0.1;
  if (opacity >= 0) {
    div.style.opacity = opacity;
  } else {
    clearInterval(fader);
  }
}, 100);
```

上面程式碼每隔100毫秒，設定一次`div`元素的透明度，直至其完全透明為止。

`setInterval`的一個常見用途是實現輪詢。下面是一個輪詢 URL 的 Hash 值是否發生變化的例子。

```javascript
var hash = window.location.hash;
var hashWatcher = setInterval(function() {
  if (window.location.hash != hash) {
    updatePage();
  }
}, 1000);
```

`setInterval`指定的是“開始執行”之間的間隔，並不考慮每次任務執行本身所消耗的時間。因此實際上，兩次執行之間的間隔會小於指定的時間。比如，`setInterval`指定每 100ms 執行一次，每次執行需要 5ms，那麼第一次執行結束後95毫秒，第二次執行就會開始。如果某次執行耗時特別長，比如需要105毫秒，那麼它結束後，下一次執行就會立即開始。

為了確保兩次執行之間有固定的間隔，可以不用`setInterval`，而是每次執行結束後，使用`setTimeout`指定下一次執行的具體時間。

```javascript
var i = 1;
var timer = setTimeout(function f() {
  // ...
  timer = setTimeout(f, 2000);
}, 2000);
```

上面程式碼可以確保，下一次執行總是在本次執行結束之後的2000毫秒開始。

## clearTimeout()，clearInterval()

`setTimeout`和`setInterval`函式，都返回一個整數值，表示計數器編號。將該整數傳入`clearTimeout`和`clearInterval`函式，就可以取消對應的定時器。

```javascript
var id1 = setTimeout(f, 1000);
var id2 = setInterval(f, 1000);

clearTimeout(id1);
clearInterval(id2);
```

上面程式碼中，回撥函式`f`不會再執行了，因為兩個定時器都被取消了。

`setTimeout`和`setInterval`返回的整數值是連續的，也就是說，第二個`setTimeout`方法返回的整數值，將比第一個的整數值大1。

```javascript
function f() {}
setTimeout(f, 1000) // 10
setTimeout(f, 1000) // 11
setTimeout(f, 1000) // 12
```

上面程式碼中，連續呼叫三次`setTimeout`，返回值都比上一次大了1。

利用這一點，可以寫一個函式，取消當前所有的`setTimeout`定時器。

```javascript
(function() {
  // 每輪事件迴圈檢查一次
  var gid = setInterval(clearAllTimeouts, 0);

  function clearAllTimeouts() {
    var id = setTimeout(function() {}, 0);
    while (id > 0) {
      if (id !== gid) {
        clearTimeout(id);
      }
      id--;
    }
  }
})();
```

上面程式碼中，先呼叫`setTimeout`，得到一個計算器編號，然後把編號比它小的計數器全部取消。

## 例項：debounce 函式

有時，我們不希望回撥函式被頻繁呼叫。比如，使用者填入網頁輸入框的內容，希望透過 Ajax 方法傳回伺服器，jQuery 的寫法如下。

```javascript
$('textarea').on('keydown', ajaxAction);
```

這樣寫有一個很大的缺點，就是如果使用者連續擊鍵，就會連續觸發`keydown`事件，造成大量的 Ajax 通訊。這是不必要的，而且很可能產生效能問題。正確的做法應該是，設定一個門檻值，表示兩次 Ajax 通訊的最小間隔時間。如果在間隔時間內，發生新的`keydown`事件，則不觸發 Ajax 通訊，並且重新開始計時。如果過了指定時間，沒有發生新的`keydown`事件，再將資料傳送出去。

這種做法叫做 debounce（防抖動）。假定兩次 Ajax 通訊的間隔不得小於2500毫秒，上面的程式碼可以改寫成下面這樣。

```javascript
$('textarea').on('keydown', debounce(ajaxAction, 2500));

function debounce(fn, delay){
  var timer = null; // 宣告計時器
  return function() {
    var context = this;
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  };
}
```

上面程式碼中，只要在2500毫秒之內，使用者再次擊鍵，就會取消上一次的定時器，然後再新建一個定時器。這樣就保證了回撥函式之間的呼叫間隔，至少是2500毫秒。

## 執行機制

`setTimeout`和`setInterval`的執行機制，是將指定的程式碼移出本輪事件迴圈，等到下一輪事件迴圈，再檢查是否到了指定時間。如果到了，就執行對應的程式碼；如果不到，就繼續等待。

這意味著，`setTimeout`和`setInterval`指定的回撥函式，必須等到本輪事件迴圈的所有同步任務都執行完，才會開始執行。由於前面的任務到底需要多少時間執行完，是不確定的，所以沒有辦法保證，`setTimeout`和`setInterval`指定的任務，一定會按照預定時間執行。

```javascript
setTimeout(someTask, 100);
veryLongTask();
```

上面程式碼的`setTimeout`，指定100毫秒以後執行一個任務。但是，如果後面的`veryLongTask`函式（同步任務）執行時間非常長，過了100毫秒還無法結束，那麼被推遲執行的`someTask`就只有等著，等到`veryLongTask`執行結束，才輪到它執行。

再看一個`setInterval`的例子。

```javascript
setInterval(function () {
  console.log(2);
}, 1000);

sleep(3000);

function sleep(ms) {
  var start = Date.now();
  while ((Date.now() - start) < ms) {
  }
}
```

上面程式碼中，`setInterval`要求每隔1000毫秒，就輸出一個2。但是，緊接著的`sleep`語句需要3000毫秒才能完成，那麼`setInterval`就必須推遲到3000毫秒之後才開始生效。注意，生效後`setInterval`不會產生累積效應，即不會一下子輸出三個2，而是隻會輸出一個2。

## setTimeout(f, 0)

### 含義

`setTimeout`的作用是將程式碼推遲到指定時間執行，如果指定時間為`0`，即`setTimeout(f, 0)`，那麼會立刻執行嗎？

答案是不會。因為上一節說過，必須要等到當前指令碼的同步任務，全部處理完以後，才會執行`setTimeout`指定的回撥函式`f`。也就是說，`setTimeout(f, 0)`會在下一輪事件迴圈一開始就執行。

```javascript
setTimeout(function () {
  console.log(1);
}, 0);
console.log(2);
// 2
// 1
```

上面程式碼先輸出`2`，再輸出`1`。因為`2`是同步任務，在本輪事件迴圈執行，而`1`是下一輪事件迴圈執行。

總之，`setTimeout(f, 0)`這種寫法的目的是，儘可能早地執行`f`，但是並不能保證立刻就執行`f`。

實際上，`setTimeout(f, 0)`不會真的在0毫秒之後執行，不同的瀏覽器有不同的實現。以 Edge 瀏覽器為例，會等到4毫秒之後執行。如果電腦正在使用電池供電，會等到16毫秒之後執行；如果網頁不在當前 Tab 頁，會推遲到1000毫秒（1秒）之後執行。這樣是為了節省系統資源。

### 應用

`setTimeout(f, 0)`有幾個非常重要的用途。它的一大應用是，可以調整事件的發生順序。比如，網頁開發中，某個事件先發生在子元素，然後冒泡到父元素，即子元素的事件回撥函式，會早於父元素的事件回撥函式觸發。如果，想讓父元素的事件回撥函式先發生，就要用到`setTimeout(f, 0)`。

```javascript
// HTML 程式碼如下
// <input type="button" id="myButton" value="click">

var input = document.getElementById('myButton');

input.onclick = function A() {
  setTimeout(function B() {
    input.value +=' input';
  }, 0)
};

document.body.onclick = function C() {
  input.value += ' body'
};
```

上面程式碼在點選按鈕後，先觸發回撥函式`A`，然後觸發函式`C`。函式`A`中，`setTimeout`將函式`B`推遲到下一輪事件迴圈執行，這樣就起到了，先觸發父元素的回撥函式`C`的目的了。

另一個應用是，使用者自定義的回撥函式，通常在瀏覽器的預設動作之前觸發。比如，使用者在輸入框輸入文字，`keypress`事件會在瀏覽器接收文字之前觸發。因此，下面的回撥函式是達不到目的的。

```javascript
// HTML 程式碼如下
// <input type="text" id="input-box">

document.getElementById('input-box').onkeypress = function (event) {
  this.value = this.value.toUpperCase();
}
```

上面程式碼想在使用者每次輸入文字後，立即將字元轉為大寫。但是實際上，它只能將本次輸入前的字元轉為大寫，因為瀏覽器此時還沒接收到新的文字，所以`this.value`取不到最新輸入的那個字元。只有用`setTimeout`改寫，上面的程式碼才能發揮作用。

```javascript
document.getElementById('input-box').onkeypress = function() {
  var self = this;
  setTimeout(function() {
    self.value = self.value.toUpperCase();
  }, 0);
}
```

上面程式碼將程式碼放入`setTimeout`之中，就能使得它在瀏覽器接收到文字之後觸發。

由於`setTimeout(f, 0)`實際上意味著，將任務放到瀏覽器最早可得的空閒時段執行，所以那些計算量大、耗時長的任務，常常會被放到幾個小部分，分別放到`setTimeout(f, 0)`裡面執行。

```javascript
var div = document.getElementsByTagName('div')[0];

// 寫法一
for (var i = 0xA00000; i < 0xFFFFFF; i++) {
  div.style.backgroundColor = '#' + i.toString(16);
}

// 寫法二
var timer;
var i=0x100000;

function func() {
  timer = setTimeout(func, 0);
  div.style.backgroundColor = '#' + i.toString(16);
  if (i++ == 0xFFFFFF) clearTimeout(timer);
}

timer = setTimeout(func, 0);
```

上面程式碼有兩種寫法，都是改變一個網頁元素的背景色。寫法一會造成瀏覽器“堵塞”，因為 JavaScript 執行速度遠高於 DOM，會造成大量 DOM 操作“堆積”，而寫法二就不會，這就是`setTimeout(f, 0)`的好處。

另一個使用這種技巧的例子是程式碼高亮的處理。如果程式碼塊很大，一次性處理，可能會對效能造成很大的壓力，那麼將其分成一個個小塊，一次處理一塊，比如寫成`setTimeout(highlightNext, 50)`的樣子，效能壓力就會減輕。
