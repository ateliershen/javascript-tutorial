# 其他常見事件

## 資源事件

### beforeunload 事件

`beforeunload`事件在視窗、文件、各種資源將要解除安裝前觸發。它可以用來防止使用者不小心解除安裝資源。

如果該事件物件的`returnValue`屬性是一個非空字串，那麼瀏覽器就會彈出一個對話方塊，詢問使用者是否要解除安裝該資源。但是，使用者指定的字串可能無法顯示，瀏覽器會展示預定義的字串。如果使用者點選“取消”按鈕，資源就不會解除安裝。

```javascript
window.addEventListener('beforeunload', function (event) {
  event.returnValue = '你確定離開嗎？';
});
```

上面程式碼中，使用者如果關閉視窗，瀏覽器會彈出一個視窗，要求使用者確認。

瀏覽器對這個事件的行為很不一致，有的瀏覽器呼叫`event.preventDefault()`，也會彈出對話方塊。IE 瀏覽器需要顯式返回一個非空的字串，才會彈出對話方塊。而且，大多數瀏覽器在對話方塊中不顯示指定文字，只顯示預設文字。因此，可以採用下面的寫法，取得最大的相容性。

```javascript
window.addEventListener('beforeunload', function (e) {
  var confirmationMessage = '確認關閉視窗？';

  e.returnValue = confirmationMessage;
  return confirmationMessage;
});
```

注意，許多手機瀏覽器（比如 Safari）預設忽略這個事件，桌面瀏覽器也有辦法忽略這個事件。所以，它可能根本不會生效，不能依賴它來阻止使用者關閉瀏覽器視窗，最好不要使用這個事件。

另外，一旦使用了`beforeunload`事件，瀏覽器就不會快取當前網頁，使用“回退”按鈕將重新向伺服器請求網頁。這是因為監聽這個事件的目的，一般是為了網頁狀態，這時快取頁面的初始狀態就沒意義了。

### unload 事件

`unload`事件在視窗關閉或者`document`物件將要解除安裝時觸發。它的觸發順序排在`beforeunload`、`pagehide`事件後面。

`unload`事件發生時，文件處於一個特殊狀態。所有資源依然存在，但是對使用者來說都不可見，UI 互動全部無效。這個事件是無法取消的，即使在監聽函式裡面丟擲錯誤，也不能停止文件的解除安裝。

```javascript
window.addEventListener('unload', function(event) {
  console.log('文件將要解除安裝');
});
```

手機上，瀏覽器或系統可能會直接丟棄網頁，這時該事件根本不會發生。而且跟`beforeunload`事件一樣，一旦使用了`unload`事件，瀏覽器就不會快取當前網頁，理由同上。因此，任何情況下都不應該依賴這個事件，指定網頁解除安裝時要執行的程式碼，可以考慮完全不使用這個事件。

該事件可以用`pagehide`代替。

### load 事件，error 事件

`load`事件在頁面或某個資源載入成功時觸發。注意，頁面或資源從瀏覽器快取載入，並不會觸發`load`事件。

```javascript
window.addEventListener('load', function(event) {
  console.log('所有資源都載入完成');
});
```

`error`事件是在頁面或資源載入失敗時觸發。`abort`事件在使用者取消載入時觸發。

這三個事件實際上屬於進度事件，不僅發生在`document`物件，還發生在各種外部資源上面。瀏覽網頁就是一個載入各種資源的過程，影象（image）、樣式表（style sheet）、指令碼（script）、影片（video）、音訊（audio）、Ajax請求（XMLHttpRequest）等等。這些資源和`document`物件、`window`物件、XMLHttpRequestUpload 物件，都會觸發`load`事件和`error`事件。

最後，頁面的`load`事件也可以用`pageshow`事件代替。

## session 歷史事件

### pageshow 事件，pagehide 事件

預設情況下，瀏覽器會在當前會話（session）快取頁面，當用戶點選“前進/後退”按鈕時，瀏覽器就會從快取中載入頁面。

`pageshow`事件在頁面載入時觸發，包括第一次載入和從快取載入兩種情況。如果要指定頁面每次載入（不管是不是從瀏覽器快取）時都執行的程式碼，可以放在這個事件的監聽函式。

第一次載入時，它的觸發順序排在`load`事件後面。從快取載入時，`load`事件不會觸發，因為網頁在快取中的樣子通常是`load`事件的監聽函式執行後的樣子，所以不必重複執行。同理，如果是從快取中載入頁面，網頁內初始化的 JavaScript 指令碼（比如 DOMContentLoaded 事件的監聽函式）也不會執行。

```javascript
window.addEventListener('pageshow', function(event) {
  console.log('pageshow: ', event);
});
```

`pageshow`事件有一個`persisted`屬性，返回一個布林值。頁面第一次載入時，這個屬性是`false`；當頁面從快取載入時，這個屬性是`true`。

```javascript
window.addEventListener('pageshow', function(event){
  if (event.persisted) {
    // ...
  }
});
```

`pagehide`事件與`pageshow`事件類似，當用戶透過“前進/後退”按鈕，離開當前頁面時觸發。它與 unload 事件的區別在於，如果在 window 物件上定義`unload`事件的監聽函式之後，頁面不會儲存在快取中，而使用`pagehide`事件，頁面會儲存在快取中。

`pagehide`事件例項也有一個`persisted`屬性，將這個屬性設為`true`，就表示頁面要儲存在快取中；設為`false`，表示網頁不儲存在快取中，這時如果設定了unload 事件的監聽函式，該函式將在 pagehide 事件後立即執行。

如果頁面包含`<frame>`或`<iframe>`元素，則`<frame>`頁面的`pageshow`事件和`pagehide`事件，都會在主頁面之前觸發。

注意，這兩個事件只在瀏覽器的`history`物件發生變化時觸發，跟網頁是否可見沒有關係。

### popstate 事件

`popstate`事件在瀏覽器的`history`物件的當前記錄發生顯式切換時觸發。注意，呼叫`history.pushState()`或`history.replaceState()`，並不會觸發`popstate`事件。該事件只在使用者在`history`記錄之間顯式切換時觸發，比如滑鼠點選“後退/前進”按鈕，或者在指令碼中呼叫`history.back()`、`history.forward()`、`history.go()`時觸發。

該事件物件有一個`state`屬性，儲存`history.pushState`方法和`history.replaceState`方法為當前記錄新增的`state`物件。

```javascript
window.onpopstate = function (event) {
  console.log('state: ' + event.state);
};
history.pushState({page: 1}, 'title 1', '?page=1');
history.pushState({page: 2}, 'title 2', '?page=2');
history.replaceState({page: 3}, 'title 3', '?page=3');
history.back(); // state: {"page":1}
history.back(); // state: null
history.go(2);  // state: {"page":3}
```

上面程式碼中，`pushState`方法向`history`添加了兩條記錄，然後`replaceState`方法替換掉當前記錄。因此，連續兩次`back`方法，會讓當前條目退回到原始網址，它沒有附帶`state`物件，所以事件的`state`屬性為`null`，然後前進兩條記錄，又回到`replaceState`方法新增的記錄。

瀏覽器對於頁面首次載入，是否觸發`popstate`事件，處理不一樣，Firefox 不觸發該事件。

### hashchange 事件

`hashchange`事件在 URL 的 hash 部分（即`#`號後面的部分，包括`#`號）發生變化時觸發。該事件一般在`window`物件上監聽。

`hashchange`的事件例項具有兩個特有屬性：`oldURL`屬性和`newURL`屬性，分別表示變化前後的完整 URL。

```javascript
// URL 是 http://www.example.com/
window.addEventListener('hashchange', myFunction);

function myFunction(e) {
  console.log(e.oldURL);
  console.log(e.newURL);
}

location.hash = 'part2';
// http://www.example.com/
// http://www.example.com/#part2
```

## 網頁狀態事件

### DOMContentLoaded 事件

網頁下載並解析完成以後，瀏覽器就會在`document`物件上觸發 DOMContentLoaded 事件。這時，僅僅完成了網頁的解析（整張頁面的 DOM 生成了），所有外部資源（樣式表、指令碼、iframe 等等）可能還沒有下載結束。也就是說，這個事件比`load`事件，發生時間早得多。

```javascript
document.addEventListener('DOMContentLoaded', function (event) {
  console.log('DOM生成');
});
```

注意，網頁的 JavaScript 指令碼是同步執行的，指令碼一旦發生堵塞，將推遲觸發`DOMContentLoaded`事件。

```javascript
document.addEventListener('DOMContentLoaded', function (event) {
  console.log('DOM 生成');
});

// 這段程式碼會推遲觸發 DOMContentLoaded 事件
for(var i = 0; i < 1000000000; i++) {
  // ...
}
```

### readystatechange 事件

`readystatechange`事件當 Document 物件和 XMLHttpRequest 物件的`readyState`屬性發生變化時觸發。`document.readyState`有三個可能的值：`loading`（網頁正在載入）、`interactive`（網頁已經解析完成，但是外部資源仍然處在載入狀態）和`complete`（網頁和所有外部資源已經結束載入，`load`事件即將觸發）。

```javascript
document.onreadystatechange = function () {
  if (document.readyState === 'interactive') {
    // ...
  }
}
```

這個事件可以看作`DOMContentLoaded`事件的另一種實現方法。

## 視窗事件

### scroll 事件

`scroll`事件在文件或文件元素滾動時觸發，主要出現在使用者拖動捲軸。

```javascript
window.addEventListener('scroll', callback);
```

該事件會連續地大量觸發，所以它的監聽函式之中不應該有非常耗費計算的操作。推薦的做法是使用`requestAnimationFrame`或`setTimeout`控制該事件的觸發頻率，然後可以結合`customEvent`丟擲一個新事件。

```javascript
(function () {
  var throttle = function (type, name, obj) {
    var obj = obj || window;
    var running = false;
    var func = function () {
      if (running) { return; }
      running = true;
      requestAnimationFrame(function() {
        obj.dispatchEvent(new CustomEvent(name));
        running = false;
      });
    };
    obj.addEventListener(type, func);
  };

  // 將 scroll 事件轉為 optimizedScroll 事件
  throttle('scroll', 'optimizedScroll');
})();

window.addEventListener('optimizedScroll', function() {
  console.log('Resource conscious scroll callback!');
});
```

上面程式碼中，`throttle()`函式用於控制事件觸發頻率，它有一個內部函式`func()`，每次`scroll`事件實際上觸發的是這個函式。`func()`函式內部使用`requestAnimationFrame()`方法，保證只有每次頁面重繪時（每秒60次），才可能會觸發`optimizedScroll`事件，從而實際上將`scroll`事件轉換為`optimizedScroll`事件，觸發頻率被控制在每秒最多60次。

改用`setTimeout()`方法，可以放置更大的時間間隔。

```javascript
(function() {
  window.addEventListener('scroll', scrollThrottler, false);

  var scrollTimeout;
  function scrollThrottler() {
    if (!scrollTimeout) {
      scrollTimeout = setTimeout(function () {
        scrollTimeout = null;
        actualScrollHandler();
      }, 66);
    }
  }

  function actualScrollHandler() {
    // ...
  }
}());
```

上面程式碼中，每次`scroll`事件都會執行`scrollThrottler`函式。該函式裡面有一個定時器`setTimeout`，每66毫秒觸發一次（每秒15次）真正執行的任務`actualScrollHandler`。

下面是一個更一般的`throttle`函式的寫法。

```javascript
function throttle(fn, wait) {
  var time = Date.now();
  return function() {
    if ((time + wait - Date.now()) < 0) {
      fn();
      time = Date.now();
    }
  }
}

window.addEventListener('scroll', throttle(callback, 1000));
```

上面的程式碼將`scroll`事件的觸發頻率，限制在一秒一次。

`lodash`函式庫提供了現成的`throttle`函式，可以直接使用。

```javascript
window.addEventListener('scroll', _.throttle(callback, 1000));
```

本書前面介紹過`debounce`的概念，`throttle`與它區別在於，`throttle`是“節流”，確保一段時間內只執行一次，而`debounce`是“防抖”，要連續操作結束後再執行。以網頁滾動為例，`debounce`要等到使用者停止滾動後才執行，`throttle`則是如果使用者一直在滾動網頁，那麼在滾動過程中還是會執行。

### resize 事件

`resize`事件在改變瀏覽器視窗大小時觸發，主要發生在`window`物件上面。

```javascript
var resizeMethod = function () {
  if (document.body.clientWidth < 768) {
    console.log('移動裝置的視口');
  }
};

window.addEventListener('resize', resizeMethod, true);
```

該事件也會連續地大量觸發，所以最好像上面的`scroll`事件一樣，透過`throttle`函式控制事件觸發頻率。

### fullscreenchange 事件，fullscreenerror 事件

`fullscreenchange`事件在進入或退出全屏狀態時觸發，該事件發生在`document`物件上面。

```javascript
document.addEventListener('fullscreenchange', function (event) {
  console.log(document.fullscreenElement);
});
```

`fullscreenerror`事件在瀏覽器無法切換到全屏狀態時觸發。

## 剪貼簿事件

以下三個事件屬於剪貼簿操作的相關事件。

- `cut`：將選中的內容從文件中移除，加入剪貼簿時觸發。
- `copy`：進行復制動作時觸發。
- `paste`：剪貼簿內容貼上到文件後觸發。

舉例來說，如果希望禁止輸入框的貼上事件，可以使用下面的程式碼。

```javascript
inputElement.addEventListener('paste', e => e.preventDefault());
```

上面的程式碼使得使用者無法在`<input>`輸入框裡面貼上內容。

`cut`、`copy`、`paste`這三個事件的事件物件都是`ClipboardEvent`介面的例項。`ClipboardEvent`有一個例項屬性`clipboardData`，是一個 DataTransfer 物件，存放剪貼的資料。具體的 API 介面和操作方法，請參見《拖拉事件》的 DataTransfer 物件部分。

```javascript
document.addEventListener('copy', function (e) {
  e.clipboardData.setData('text/plain', 'Hello, world!');
  e.clipboardData.setData('text/html', '<b>Hello, world!</b>');
  e.preventDefault();
});
```

上面的程式碼使得複製進入剪貼簿的，都是開發者指定的資料，而不是使用者想要複製的資料。

## 焦點事件

焦點事件發生在元素節點和`document`物件上面，與獲得或失去焦點相關。它主要包括以下四個事件。

- `focus`：元素節點獲得焦點後觸發，該事件不會冒泡。
- `blur`：元素節點失去焦點後觸發，該事件不會冒泡。
- `focusin`：元素節點將要獲得焦點時觸發，發生在`focus`事件之前。該事件會冒泡。
- `focusout`：元素節點將要失去焦點時觸發，發生在`blur`事件之前。該事件會冒泡。

這四個事件的事件物件都繼承了`FocusEvent`介面。`FocusEvent`例項具有以下屬性。

- `FocusEvent.target`：事件的目標節點。
- `FocusEvent.relatedTarget`：對於`focusin`事件，返回失去焦點的節點；對於`focusout`事件，返回將要接受焦點的節點；對於`focus`和`blur`事件，返回`null`。

由於`focus`和`blur`事件不會冒泡，只能在捕獲階段觸發，所以`addEventListener`方法的第三個引數需要設為`true`。

```javascript
form.addEventListener('focus', function (event) {
  event.target.style.background = 'pink';
}, true);

form.addEventListener('blur', function (event) {
  event.target.style.background = '';
}, true);
```

上面程式碼針對表單的文字輸入框，接受焦點時設定背景色，失去焦點時去除背景色。

## CustomEvent 介面

CustomEvent 介面用於生成自定義的事件例項。那些瀏覽器預定義的事件，雖然可以手動生成，但是往往不能在事件上繫結資料。如果需要在觸發事件的同時，傳入指定的資料，就可以使用 CustomEvent 介面生成的自定義事件物件。

瀏覽器原生提供`CustomEvent()`建構函式，用來生成 CustomEvent 事件例項。

```javascript
new CustomEvent(type, options)
```

`CustomEvent()`建構函式接受兩個引數。第一個引數是字串，表示事件的名字，這是必須的。第二個引數是事件的配置物件，這個引數是可選的。`CustomEvent`的配置物件除了接受 Event 事件的配置屬性，只有一個自己的屬性。

- `detail`：表示事件的附帶資料，預設為`null`。

下面是一個例子。

```javascript
var event = new CustomEvent('build', { 'detail': 'hello' });

function eventHandler(e) {
  console.log(e.detail);
}

document.body.addEventListener('build', function (e) {
  console.log(e.detail);
});

document.body.dispatchEvent(event);
```

上面程式碼中，我們手動定義了`build`事件。該事件觸發後，會被監聽到，從而輸出該事件例項的`detail`屬性（即字串`hello`）。

下面是另一個例子。

```javascript
var myEvent = new CustomEvent('myevent', {
  detail: {
    foo: 'bar'
  },
  bubbles: true,
  cancelable: false
});

el.addEventListener('myevent', function (event) {
  console.log('Hello ' + event.detail.foo);
});

el.dispatchEvent(myEvent);
```

上面程式碼也說明，CustomEvent 的事件例項，除了具有 Event 介面的例項屬性，還具有`detail`屬性。
