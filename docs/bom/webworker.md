# Web Worker

## 概述

JavaScript 語言採用的是單執行緒模型，也就是說，所有任務只能在一個執行緒上完成，一次只能做一件事。前面的任務沒做完，後面的任務只能等著。隨著電腦計算能力的增強，尤其是多核 CPU 的出現，單執行緒帶來很大的不便，無法充分發揮計算機的計算能力。

Web Worker 的作用，就是為 JavaScript 創造多執行緒環境，允許主執行緒建立 Worker 執行緒，將一些任務分配給後者執行。在主執行緒執行的同時，Worker 執行緒在後臺執行，兩者互不干擾。等到 Worker 執行緒完成計算任務，再把結果返回給主執行緒。這樣的好處是，一些計算密集型或高延遲的任務可以交由 Worker 執行緒執行，主執行緒（通常負責 UI 互動）能夠保持流暢，不會被阻塞或拖慢。

Worker 執行緒一旦新建成功，就會始終執行，不會被主執行緒上的活動（比如使用者點選按鈕、提交表單）打斷。這樣有利於隨時響應主執行緒的通訊。但是，這也造成了 Worker 比較耗費資源，不應該過度使用，而且一旦使用完畢，就應該關閉。

Web Worker 有以下幾個使用注意點。

（1）**同源限制**

分配給 Worker 執行緒執行的指令碼檔案，必須與主執行緒的指令碼檔案同源。

（2）**DOM 限制**

Worker 執行緒所在的全域性物件，與主執行緒不一樣，無法讀取主執行緒所在網頁的 DOM 物件，也無法使用`document`、`window`、`parent`這些物件。但是，Worker 執行緒可以使用`navigator`物件和`location`物件。

（3）**全域性物件限制**

Worker 的全域性物件`WorkerGlobalScope`，不同於網頁的全域性物件`Window`，很多介面拿不到。比如，理論上 Worker 執行緒不能使用`console.log`，因為標準裡面沒有提到 Worker 的全域性物件存在`console`介面，只定義了`Navigator`介面和`Location`介面。不過，瀏覽器實際上支援 Worker 執行緒使用`console.log`，保險的做法還是不使用這個方法。

（4）**通訊聯絡**

Worker 執行緒和主執行緒不在同一個上下文環境，它們不能直接通訊，必須透過訊息完成。

（5）**指令碼限制**

Worker 執行緒不能執行`alert()`方法和`confirm()`方法，但可以使用 XMLHttpRequest 物件發出 AJAX 請求。

（6）**檔案限制**

Worker 執行緒無法讀取本地檔案，即不能開啟本機的檔案系統（`file://`），它所載入的指令碼，必須來自網路。

## 基本用法

### 主執行緒

主執行緒採用`new`命令，呼叫`Worker()`建構函式，新建一個 Worker 執行緒。

```javascript
var worker = new Worker('work.js');
```

`Worker()`建構函式的引數是一個指令碼檔案，該檔案就是 Worker 執行緒所要執行的任務。由於 Worker 不能讀取本地檔案，所以這個指令碼必須來自網路。如果下載沒有成功（比如404錯誤），Worker 就會默默地失敗。

然後，主執行緒呼叫`worker.postMessage()`方法，向 Worker 發訊息。

```javascript
worker.postMessage('Hello World');
worker.postMessage({method: 'echo', args: ['Work']});
```

`worker.postMessage()`方法的引數，就是主執行緒傳給 Worker 的資料。它可以是各種資料型別，包括二進位制資料。

接著，主執行緒透過`worker.onmessage`指定監聽函式，接收子執行緒發回來的訊息。

```javascript
worker.onmessage = function (event) {
  doSomething(event.data);
}

function doSomething() {
  // 執行任務
  worker.postMessage('Work done!');
}
```

上面程式碼中，事件物件的`data`屬性可以獲取 Worker 發來的資料。

Worker 完成任務以後，主執行緒就可以把它關掉。

```javascript
worker.terminate();
```

### Worker 執行緒

Worker 執行緒內部需要有一個監聽函式，監聽`message`事件。

```javascript
self.addEventListener('message', function (e) {
  self.postMessage('You said: ' + e.data);
}, false);
```

上面程式碼中，`self`代表子執行緒自身，即子執行緒的全域性物件。因此，等同於下面兩種寫法。

```javascript
// 寫法一
this.addEventListener('message', function (e) {
  this.postMessage('You said: ' + e.data);
}, false);

// 寫法二
addEventListener('message', function (e) {
  postMessage('You said: ' + e.data);
}, false);
```

除了使用`self.addEventListener()`指定監聽函式，也可以使用`self.onmessage`指定。監聽函式的引數是一個事件物件，它的`data`屬性包含主執行緒發來的資料。`self.postMessage()`方法用來向主執行緒傳送訊息。

根據主執行緒發來的資料，Worker 執行緒可以呼叫不同的方法，下面是一個例子。

```javascript
self.addEventListener('message', function (e) {
  var data = e.data;
  switch (data.cmd) {
    case 'start':
      self.postMessage('WORKER STARTED: ' + data.msg);
      break;
    case 'stop':
      self.postMessage('WORKER STOPPED: ' + data.msg);
      self.close(); // Terminates the worker.
      break;
    default:
      self.postMessage('Unknown command: ' + data.msg);
  };
}, false);
```

上面程式碼中，`self.close()`用於在 Worker 內部關閉自身。

### Worker 載入指令碼

Worker 內部如果要載入其他指令碼，有一個專門的方法`importScripts()`。

```javascript
importScripts('script1.js');
```

該方法可以同時載入多個指令碼。

```javascript
importScripts('script1.js', 'script2.js');
```

### 錯誤處理

主執行緒可以監聽 Worker 是否發生錯誤。如果發生錯誤，Worker 會觸發主執行緒的`error`事件。

```javascript
worker.onerror = function (event) {
  console.log(
    'ERROR: Line ', event.lineno, ' in ', event.filename, ': ', event.message
  );
};

// 或者
worker.addEventListener('error', function (event) {
  // ...
});
```

Worker 內部也可以監聽`error`事件。

### 關閉 Worker

使用完畢，為了節省系統資源，必須關閉 Worker。

```javascript
// 主執行緒
worker.terminate();

// Worker 執行緒
self.close();
```

## 資料通訊

前面說過，主執行緒與 Worker 之間的通訊內容，可以是文字，也可以是物件。需要注意的是，這種通訊是複製關係，即是傳值而不是傳址，Worker 對通訊內容的修改，不會影響到主執行緒。事實上，瀏覽器內部的執行機制是，先將通訊內容序列化，然後把序列化後的字串發給 Worker，後者再將它還原。

主執行緒與 Worker 之間也可以交換二進位制資料，比如 File、Blob、ArrayBuffer 等型別，也可以線上程之間傳送。下面是一個例子。

```javascript
// 主執行緒
var uInt8Array = new Uint8Array(new ArrayBuffer(10));
for (var i = 0; i < uInt8Array.length; ++i) {
  uInt8Array[i] = i * 2; // [0, 2, 4, 6, 8,...]
}
worker.postMessage(uInt8Array);

// Worker 執行緒
self.onmessage = function (e) {
  var uInt8Array = e.data;
  postMessage('Inside worker.js: uInt8Array.toString() = ' + uInt8Array.toString());
  postMessage('Inside worker.js: uInt8Array.byteLength = ' + uInt8Array.byteLength);
};
```

但是，複製方式傳送二進位制資料，會造成效能問題。比如，主執行緒向 Worker 傳送一個 500MB 檔案，預設情況下瀏覽器會生成一個原檔案的複製。為了解決這個問題，JavaScript 允許主執行緒把二進位制資料直接轉移給子執行緒，但是一旦轉移，主執行緒就無法再使用這些二進位制資料了，這是為了防止出現多個執行緒同時修改資料的麻煩局面。這種轉移資料的方法，叫做[Transferable Objects](http://www.w3.org/html/wg/drafts/html/master/infrastructure.html#transferable-objects)。這使得主執行緒可以快速把資料交給 Worker，對於影像處理、聲音處理、3D 運算等就非常方便了，不會產生效能負擔。

如果要直接轉移資料的控制權，就要使用下面的寫法。

```javascript
// Transferable Objects 格式
worker.postMessage(arrayBuffer, [arrayBuffer]);

// 例子
var ab = new ArrayBuffer(1);
worker.postMessage(ab, [ab]);
```

## 同頁面的 Web Worker

通常情況下，Worker 載入的是一個單獨的 JavaScript 指令碼檔案，但是也可以載入與主執行緒在同一個網頁的程式碼。

```html
<!DOCTYPE html>
  <body>
    <script id="worker" type="app/worker">
      addEventListener('message', function () {
        postMessage('some message');
      }, false);
    </script>
  </body>
</html>
```

上面是一段嵌入網頁的指令碼，注意必須指定`<script>`標籤的`type`屬性是一個瀏覽器不認識的值，上例是`app/worker`。

然後，讀取這一段嵌入頁面的指令碼，用 Worker 來處理。

```javascript
var blob = new Blob([document.querySelector('#worker').textContent]);
var url = window.URL.createObjectURL(blob);
var worker = new Worker(url);

worker.onmessage = function (e) {
  // e.data === 'some message'
};
```

上面程式碼中，先將嵌入網頁的指令碼程式碼，轉成一個二進位制物件，然後為這個二進位制物件生成 URL，再讓 Worker 載入這個 URL。這樣就做到了，主執行緒和 Worker 的程式碼都在同一個網頁上面。

## 例項：Worker 執行緒完成輪詢

有時，瀏覽器需要輪詢伺服器狀態，以便第一時間得知狀態改變。這個工作可以放在 Worker 裡面。

```javascript
function createWorker(f) {
  var blob = new Blob(['(' + f.toString() + ')()']);
  var url = window.URL.createObjectURL(blob);
  var worker = new Worker(url);
  return worker;
}

var pollingWorker = createWorker(function (e) {
  var cache;

  function compare(new, old) { ... };

  setInterval(function () {
    fetch('/my-api-endpoint').then(function (res) {
      var data = res.json();

      if (!compare(data, cache)) {
        cache = data;
        self.postMessage(data);
      }
    })
  }, 1000)
});

pollingWorker.onmessage = function () {
  // render data
}

pollingWorker.postMessage('init');
```

上面程式碼中，Worker 每秒鐘輪詢一次資料，然後跟快取做比較。如果不一致，就說明服務端有了新的變化，因此就要通知主執行緒。

## 例項： Worker 新建 Worker

Worker 執行緒內部還能再新建 Worker 執行緒（目前只有 Firefox 瀏覽器支援）。下面的例子是將一個計算密集的任務，分配到10個 Worker。

主執行緒程式碼如下。

```javascript
var worker = new Worker('worker.js');
worker.onmessage = function (event) {
  document.getElementById('result').textContent = event.data;
};
```

Worker 執行緒程式碼如下。

```javascript
// worker.js

// settings
var num_workers = 10;
var items_per_worker = 1000000;

// start the workers
var result = 0;
var pending_workers = num_workers;
for (var i = 0; i < num_workers; i += 1) {
  var worker = new Worker('core.js');
  worker.postMessage(i * items_per_worker);
  worker.postMessage((i + 1) * items_per_worker);
  worker.onmessage = storeResult;
}

// handle the results
function storeResult(event) {
  result += event.data;
  pending_workers -= 1;
  if (pending_workers <= 0)
    postMessage(result); // finished!
}
```

上面程式碼中，Worker 執行緒內部新建了10個 Worker 執行緒，並且依次向這10個 Worker 傳送訊息，告知了計算的起點和終點。計算任務指令碼的程式碼如下。

```javascript
// core.js
var start;
onmessage = getStart;
function getStart(event) {
  start = event.data;
  onmessage = getEnd;
}

var end;
function getEnd(event) {
  end = event.data;
  onmessage = null;
  work();
}

function work() {
  var result = 0;
  for (var i = start; i < end; i += 1) {
    // perform some complex calculation here
    result += 1;
  }
  postMessage(result);
  close();
}
```

## API

### 主執行緒

瀏覽器原生提供`Worker()`建構函式，用來供主執行緒生成 Worker 執行緒。

```javascript
var myWorker = new Worker(jsUrl, options);
```

`Worker()`建構函式，可以接受兩個引數。第一個引數是指令碼的網址（必須遵守同源政策），該引數是必需的，且只能載入 JS 指令碼，否則會報錯。第二個引數是配置物件，該物件可選。它的一個作用就是指定 Worker 的名稱，用來區分多個 Worker 執行緒。

```javascript
// 主執行緒
var myWorker = new Worker('worker.js', { name : 'myWorker' });

// Worker 執行緒
self.name // myWorker
```

`Worker()`建構函式返回一個 Worker 執行緒物件，用來供主執行緒操作 Worker。Worker 執行緒物件的屬性和方法如下。

- Worker.onerror：指定 error 事件的監聽函式。
- Worker.onmessage：指定 message 事件的監聽函式，傳送過來的資料在`Event.data`屬性中。
- Worker.onmessageerror：指定 messageerror 事件的監聽函式。傳送的資料無法序列化成字串時，會觸發這個事件。
- Worker.postMessage()：向 Worker 執行緒傳送訊息。
- Worker.terminate()：立即終止 Worker 執行緒。

### Worker 執行緒

Web Worker 有自己的全域性物件，不是主執行緒的`window`，而是一個專門為 Worker 定製的全域性物件。因此定義在`window`上面的物件和方法不是全部都可以使用。

Worker 執行緒有一些自己的全域性屬性和方法。

- self.name： Worker 的名字。該屬性只讀，由建構函式指定。
- self.onmessage：指定`message`事件的監聽函式。
- self.onmessageerror：指定 messageerror 事件的監聽函式。傳送的資料無法序列化成字串時，會觸發這個事件。
- self.close()：關閉 Worker 執行緒。
- self.postMessage()：向產生這個 Worker 的執行緒傳送訊息。
- self.importScripts()：載入 JS 指令碼。

（完）
