# Promise 物件

## 概述

Promise 物件是 JavaScript 的非同步操作解決方案，為非同步操作提供統一介面。它起到代理作用（proxy），充當非同步操作與回撥函式之間的中介，使得非同步操作具備同步操作的介面。Promise 可以讓非同步操作寫起來，就像在寫同步操作的流程，而不必一層層地巢狀回撥函式。

注意，本章只是 Promise 物件的簡單介紹。為了避免與後續教程的重複，更完整的介紹請看[《ES6 標準入門》](http://es6.ruanyifeng.com/)的[《Promise 物件》](http://es6.ruanyifeng.com/#docs/promise)一章。

首先，Promise 是一個物件，也是一個建構函式。

```javascript
function f1(resolve, reject) {
  // 非同步程式碼...
}

var p1 = new Promise(f1);
```

上面程式碼中，`Promise`建構函式接受一個回撥函式`f1`作為引數，`f1`裡面是非同步操作的程式碼。然後，返回的`p1`就是一個 Promise 例項。

Promise 的設計思想是，所有非同步任務都返回一個 Promise 例項。Promise 例項有一個`then`方法，用來指定下一步的回撥函式。

```javascript
var p1 = new Promise(f1);
p1.then(f2);
```

上面程式碼中，`f1`的非同步操作執行完成，就會執行`f2`。

傳統的寫法可能需要把`f2`作為回撥函式傳入`f1`，比如寫成`f1(f2)`，非同步操作完成後，在`f1`內部呼叫`f2`。Promise 使得`f1`和`f2`變成了鏈式寫法。不僅改善了可讀性，而且對於多層巢狀的回撥函式尤其方便。

```javascript
// 傳統寫法
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // ...
      });
    });
  });
});

// Promise 的寫法
(new Promise(step1))
  .then(step2)
  .then(step3)
  .then(step4);
```

從上面程式碼可以看到，採用 Promises 以後，程式流程變得非常清楚，十分易讀。注意，為了便於理解，上面程式碼的`Promise`例項的生成格式，做了簡化，真正的語法請參照下文。

總的來說，傳統的回撥函式寫法使得程式碼混成一團，變得橫向發展而不是向下發展。Promise 就是解決這個問題，使得非同步流程可以寫成同步流程。

Promise 原本只是社群提出的一個構想，一些函式庫率先實現了這個功能。ECMAScript 6 將其寫入語言標準，目前 JavaScript 原生支援 Promise 物件。

## Promise 物件的狀態

Promise 物件透過自身的狀態，來控制非同步操作。Promise 例項具有三種狀態。

- 非同步操作未完成（pending）
- 非同步操作成功（fulfilled）
- 非同步操作失敗（rejected）

上面三種狀態裡面，`fulfilled`和`rejected`合在一起稱為`resolved`（已定型）。

這三種的狀態的變化途徑只有兩種。

- 從“未完成”到“成功”
- 從“未完成”到“失敗”

一旦狀態發生變化，就凝固了，不會再有新的狀態變化。這也是 Promise 這個名字的由來，它的英語意思是“承諾”，一旦承諾成效，就不得再改變了。這也意味著，Promise 例項的狀態變化只可能發生一次。

因此，Promise 的最終結果只有兩種。

- 非同步操作成功，Promise 例項傳回一個值（value），狀態變為`fulfilled`。
- 非同步操作失敗，Promise 例項丟擲一個錯誤（error），狀態變為`rejected`。

## Promise 建構函式

JavaScript 提供原生的`Promise`建構函式，用來生成 Promise 例項。

```javascript
var promise = new Promise(function (resolve, reject) {
  // ...

  if (/* 非同步操作成功 */){
    resolve(value);
  } else { /* 非同步操作失敗 */
    reject(new Error());
  }
});
```

上面程式碼中，`Promise`建構函式接受一個函式作為引數，該函式的兩個引數分別是`resolve`和`reject`。它們是兩個函式，由 JavaScript 引擎提供，不用自己實現。

`resolve`函式的作用是，將`Promise`例項的狀態從“未完成”變為“成功”（即從`pending`變為`fulfilled`），在非同步操作成功時呼叫，並將非同步操作的結果，作為引數傳遞出去。`reject`函式的作用是，將`Promise`例項的狀態從“未完成”變為“失敗”（即從`pending`變為`rejected`），在非同步操作失敗時呼叫，並將非同步操作報出的錯誤，作為引數傳遞出去。

下面是一個例子。

```javascript
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms, 'done');
  });
}

timeout(100)
```

上面程式碼中，`timeout(100)`返回一個 Promise 例項。100毫秒以後，該例項的狀態會變為`fulfilled`。

## Promise.prototype.then()

Promise 例項的`then`方法，用來添加回調函式。

`then`方法可以接受兩個回撥函式，第一個是非同步操作成功時（變為`fulfilled`狀態）的回撥函式，第二個是非同步操作失敗（變為`rejected`）時的回撥函式（該引數可以省略）。一旦狀態改變，就呼叫相應的回撥函式。

```javascript
var p1 = new Promise(function (resolve, reject) {
  resolve('成功');
});
p1.then(console.log, console.error);
// "成功"

var p2 = new Promise(function (resolve, reject) {
  reject(new Error('失敗'));
});
p2.then(console.log, console.error);
// Error: 失敗
```

上面程式碼中，`p1`和`p2`都是Promise 例項，它們的`then`方法繫結兩個回撥函式：成功時的回撥函式`console.log`，失敗時的回撥函式`console.error`（可以省略）。`p1`的狀態變為成功，`p2`的狀態變為失敗，對應的回撥函式會收到非同步操作傳回的值，然後在控制檯輸出。

`then`方法可以鏈式使用。

```javascript
p1
  .then(step1)
  .then(step2)
  .then(step3)
  .then(
    console.log,
    console.error
  );
```

上面程式碼中，`p1`後面有四個`then`，意味依次有四個回撥函式。只要前一步的狀態變為`fulfilled`，就會依次執行緊跟在後面的回撥函式。

最後一個`then`方法，回撥函式是`console.log`和`console.error`，用法上有一點重要的區別。`console.log`只顯示`step3`的返回值，而`console.error`可以顯示`p1`、`step1`、`step2`、`step3`之中任意一個發生的錯誤。舉例來說，如果`step1`的狀態變為`rejected`，那麼`step2`和`step3`都不會執行了（因為它們是`resolved`的回撥函式）。Promise 開始尋找，接下來第一個為`rejected`的回撥函式，在上面程式碼中是`console.error`。這就是說，Promise 物件的報錯具有傳遞性。

## then() 用法辨析

Promise 的用法，簡單說就是一句話：使用`then`方法添加回調函式。但是，不同的寫法有一些細微的差別，請看下面四種寫法，它們的差別在哪裡？

```javascript
// 寫法一
f1().then(function () {
  return f2();
});

// 寫法二
f1().then(function () {
  f2();
});

// 寫法三
f1().then(f2());

// 寫法四
f1().then(f2);
```

為了便於講解，下面這四種寫法都再用`then`方法接一個回撥函式`f3`。寫法一的`f3`回撥函式的引數，是`f2`函式的執行結果。

```javascript
f1().then(function () {
  return f2();
}).then(f3);
```

寫法二的`f3`回撥函式的引數是`undefined`。

```javascript
f1().then(function () {
  f2();
  return;
}).then(f3);
```

寫法三的`f3`回撥函式的引數，是`f2`函式返回的函式的執行結果。

```javascript
f1().then(f2())
  .then(f3);
```

寫法四與寫法一隻有一個差別，那就是`f2`會接收到`f1()`返回的結果。

```javascript
f1().then(f2)
  .then(f3);
```

## 例項：圖片載入

下面是使用 Promise 完成圖片的載入。

```javascript
var preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    var image = new Image();
    image.onload  = resolve;
    image.onerror = reject;
    image.src = path;
  });
};
```

上面程式碼中，`image`是一個圖片物件的例項。它有兩個事件監聽屬性，`onload`屬性在圖片載入成功後呼叫，`onerror`屬性在載入失敗呼叫。

上面的`preloadImage()`函式用法如下。

```javascript
preloadImage('https://example.com/my.jpg')
  .then(function (e) { document.body.append(e.target) })
  .then(function () { console.log('載入成功') })
```

上面程式碼中，圖片載入成功以後，`onload`屬性會返回一個事件物件，因此第一個`then()`方法的回撥函式，會接收到這個事件物件。該物件的`target`屬性就是圖片載入後生成的 DOM 節點。

## 小結

Promise 的優點在於，讓回撥函式變成了規範的鏈式寫法，程式流程可以看得很清楚。它有一整套介面，可以實現許多強大的功能，比如同時執行多個非同步操作，等到它們的狀態都改變以後，再執行一個回撥函式；再比如，為多個回撥函式中丟擲的錯誤，統一指定處理方法等等。

而且，Promise 還有一個傳統寫法沒有的好處：它的狀態一旦改變，無論何時查詢，都能得到這個狀態。這意味著，無論何時為 Promise 例項添加回調函式，該函式都能正確執行。所以，你不用擔心是否錯過了某個事件或訊號。如果是傳統寫法，透過監聽事件來執行回撥函式，一旦錯過了事件，再添加回調函式是不會執行的。

Promise 的缺點是，編寫的難度比傳統寫法高，而且閱讀程式碼也不是一眼可以看懂。你只會看到一堆`then`，必須自己在`then`的回撥函式裡面理清邏輯。

## 微任務

Promise 的回撥函式屬於非同步任務，會在同步任務之後執行。

```javascript
new Promise(function (resolve, reject) {
  resolve(1);
}).then(console.log);

console.log(2);
// 2
// 1
```

上面程式碼會先輸出2，再輸出1。因為`console.log(2)`是同步任務，而`then`的回撥函式屬於非同步任務，一定晚於同步任務執行。

但是，Promise 的回撥函式不是正常的非同步任務，而是微任務（microtask）。它們的區別在於，正常任務追加到下一輪事件迴圈，微任務追加到本輪事件迴圈。這意味著，微任務的執行時間一定早於正常任務。

```javascript
setTimeout(function() {
  console.log(1);
}, 0);

new Promise(function (resolve, reject) {
  resolve(2);
}).then(console.log);

console.log(3);
// 3
// 2
// 1
```

上面程式碼的輸出結果是`321`。這說明`then`的回撥函式的執行時間，早於`setTimeout(fn, 0)`。因為`then`是本輪事件迴圈執行，`setTimeout(fn, 0)`在下一輪事件迴圈開始時執行。

## 參考連結

- Sebastian Porto, [Asynchronous JS: Callbacks, Listeners, Control Flow Libs and Promises](http://sporto.github.com/blog/2012/12/09/callbacks-listeners-promises/)
- Rhys Brett-Bowen, [Promises/A+ - understanding the spec through implementation](http://modernjavascript.blogspot.com/2013/08/promisesa-understanding-by-doing.html)
- Matt Podwysocki, Amanda Silver, [Asynchronous Programming in JavaScript with “Promises”](http://blogs.msdn.com/b/ie/archive/2011/09/11/asynchronous-programming-in-javascript-with-promises.aspx)
- Marc Harter, [Promise A+ Implementation](https://gist.github.com//wavded/5692344)
- Bryan Klimt, [What’s so great about JavaScript Promises?](http://blog.parse.com/2013/01/29/whats-so-great-about-javascript-promises/)
- Jake Archibald, [JavaScript Promises There and back again](http://www.html5rocks.com/en/tutorials/es6/promises/)
- Mikito Takada, [7. Control flow, Mixu's Node book](http://book.mixu.net/node/ch7.html)
