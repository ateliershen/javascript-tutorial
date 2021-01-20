# History 物件

## 概述

`window.history`屬性指向 History 物件，它表示當前視窗的瀏覽歷史。

History 物件儲存了當前視窗訪問過的所有頁面網址。下面程式碼表示當前視窗一共訪問過3個網址。

```javascript
window.history.length // 3
```

由於安全原因，瀏覽器不允許指令碼讀取這些地址，但是允許在地址之間導航。

```javascript
// 後退到前一個網址
history.back()

// 等同於
history.go(-1)
```

瀏覽器工具欄的“前進”和“後退”按鈕，其實就是對 History 物件進行操作。

## 屬性

History 物件主要有兩個屬性。

- `History.length`：當前視窗訪問過的網址數量（包括當前網頁）
- `History.state`：History 堆疊最上層的狀態值（詳見下文）

```javascript
// 當前視窗訪問過多少個網頁
window.history.length // 1

// History 物件的當前狀態
// 通常是 undefined，即未設定
window.history.state // undefined
```

## 方法

### History.back()、History.forward()、History.go()

這三個方法用於在歷史之中移動。

- `History.back()`：移動到上一個網址，等同於點選瀏覽器的後退鍵。對於第一個訪問的網址，該方法無效果。
- `History.forward()`：移動到下一個網址，等同於點選瀏覽器的前進鍵。對於最後一個訪問的網址，該方法無效果。
- `History.go()`：接受一個整數作為引數，以當前網址為基準，移動到引數指定的網址，比如`go(1)`相當於`forward()`，`go(-1)`相當於`back()`。如果引數超過實際存在的網址範圍，該方法無效果；如果不指定引數，預設引數為`0`，相當於重新整理當前頁面。

```javascript
history.back();
history.forward();
history.go(-2);
```

`history.go(0)`相當於重新整理當前頁面。

```javascript
history.go(0); // 重新整理當前頁面
```

注意，移動到以前訪問過的頁面時，頁面通常是從瀏覽器快取之中載入，而不是重新要求伺服器傳送新的網頁。

### History.pushState()，

`History.pushState()`方法用於在歷史中新增一條記錄。

```javascript
window.history.pushState(state, title, url)
```

該方法接受三個引數，依次為：

- `state`：一個與新增的記錄相關聯的狀態物件，主要用於`popstate`事件。該事件觸發時，該物件會傳入回撥函式。也就是說，瀏覽器會將這個物件序列化以後保留在本地，重新載入這個頁面的時候，可以拿到這個物件。如果不需要這個物件，此處可以填`null`。
- `title`：新頁面的標題。但是，現在所有瀏覽器都忽視這個引數，所以這裡可以填空字串。
- `url`：新的網址，必須與當前頁面處在同一個域。瀏覽器的位址列將顯示這個網址。

假定當前網址是`example.com/1.html`，使用`pushState()`方法在瀏覽記錄（History 物件）中新增一個新記錄。

```javascript
var stateObj = { foo: 'bar' };
history.pushState(stateObj, 'page 2', '2.html');
```

新增新記錄後，瀏覽器位址列立刻顯示`example.com/2.html`，但並不會跳轉到`2.html`，甚至也不會檢查`2.html`是否存在，它只是成為瀏覽歷史中的最新記錄。這時，在位址列輸入一個新的地址(比如訪問`google.com`)，然後點選了倒退按鈕，頁面的 URL 將顯示`2.html`；你再點選一次倒退按鈕，URL 將顯示`1.html`。

總之，`pushState()`方法不會觸發頁面重新整理，只是導致 History 物件發生變化，位址列會有反應。

使用該方法之後，就可以用`History.state`屬性讀出狀態物件。

```javascript
var stateObj = { foo: 'bar' };
history.pushState(stateObj, 'page 2', '2.html');
history.state // {foo: "bar"}
```

如果`pushState`的 URL 引數設定了一個新的錨點值（即`hash`），並不會觸發`hashchange`事件。反過來，如果 URL 的錨點值變了，則會在 History 物件建立一條瀏覽記錄。

如果`pushState()`方法設定了一個跨域網址，則會報錯。

```javascript
// 報錯
// 當前網址為 http://example.com
history.pushState(null, '', 'https://twitter.com/hello');
```

上面程式碼中，`pushState`想要插入一個跨域的網址，導致報錯。這樣設計的目的是，防止惡意程式碼讓使用者以為他們是在另一個網站上，因為這個方法不會導致頁面跳轉。

### History.replaceState()

`History.replaceState()`方法用來修改 History 物件的當前記錄，其他都與`pushState()`方法一模一樣。

假定當前網頁是`example.com/example.html`。

```javascript
history.pushState({page: 1}, 'title 1', '?page=1')
// URL 顯示為 http://example.com/example.html?page=1

history.pushState({page: 2}, 'title 2', '?page=2');
// URL 顯示為 http://example.com/example.html?page=2

history.replaceState({page: 3}, 'title 3', '?page=3');
// URL 顯示為 http://example.com/example.html?page=3

history.back()
// URL 顯示為 http://example.com/example.html?page=1

history.back()
// URL 顯示為 http://example.com/example.html

history.go(2)
// URL 顯示為 http://example.com/example.html?page=3
```

## popstate 事件

每當同一個文件的瀏覽歷史（即`history`物件）出現變化時，就會觸發`popstate`事件。

注意，僅僅呼叫`pushState()`方法或`replaceState()`方法 ，並不會觸發該事件，只有使用者點選瀏覽器倒退按鈕和前進按鈕，或者使用 JavaScript 呼叫`History.back()`、`History.forward()`、`History.go()`方法時才會觸發。另外，該事件只針對同一個文件，如果瀏覽歷史的切換，導致載入不同的文件，該事件也不會觸發。

使用的時候，可以為`popstate`事件指定回撥函式。

```javascript
window.onpopstate = function (event) {
  console.log('location: ' + document.location);
  console.log('state: ' + JSON.stringify(event.state));
};

// 或者
window.addEventListener('popstate', function(event) {
  console.log('location: ' + document.location);
  console.log('state: ' + JSON.stringify(event.state));
});
```

回撥函式的引數是一個`event`事件物件，它的`state`屬性指向`pushState`和`replaceState`方法為當前 URL 所提供的狀態物件（即這兩個方法的第一個引數）。上面程式碼中的`event.state`，就是透過`pushState`和`replaceState`方法，為當前 URL 繫結的`state`物件。

這個`state`物件也可以直接透過`history`物件讀取。

```javascript
var currentState = history.state;
```

注意，頁面第一次載入的時候，瀏覽器不會觸發`popstate`事件。
