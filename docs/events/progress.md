# 進度事件

## 進度事件的種類

進度事件用來描述資源載入的進度，主要由 AJAX 請求、`<img>`、`<audio>`、`<video>`、`<style>`、`<link>`等外部資源的載入觸發，繼承了`ProgressEvent`介面。它主要包含以下幾種事件。

- `abort`：外部資源中止載入時（比如使用者取消）觸發。如果發生錯誤導致中止，不會觸發該事件。
- `error`：由於錯誤導致外部資源無法載入時觸發。
- `load`：外部資源載入成功時觸發。
- `loadstart`：外部資源開始載入時觸發。
- `loadend`：外部資源停止載入時觸發，發生順序排在`error`、`abort`、`load`等事件的後面。
- `progress`：外部資源載入過程中不斷觸發。
- `timeout`：載入超時時觸發。

注意，除了資源下載，檔案上傳也存在這些事件。

下面是一個例子。

```javascript
image.addEventListener('load', function (event) {
  image.classList.add('finished');
});

image.addEventListener('error', function (event) {
  image.style.display = 'none';
});
```

上面程式碼在圖片元素載入完成後，為圖片元素新增一個`finished`的 Class。如果載入失敗，就把圖片元素的樣式設定為不顯示。

有時候，圖片載入會在指令碼執行之前就完成，尤其是當指令碼放置在網頁底部的時候，因此有可能`load`和`error`事件的監聽函式根本不會執行。所以，比較可靠的方式，是用`complete`屬性先判斷一下是否載入完成。

```javascript
function loaded() {
  // ...
}

if (image.complete) {
  loaded();
} else {
  image.addEventListener('load', loaded);
}
```

由於 DOM 的元素節點沒有提供是否載入錯誤的屬性，所以`error`事件的監聽函式最好放在`<img>`元素的 HTML 程式碼中，這樣才能保證發生載入錯誤時百分之百會執行。

```html
<img src="/wrong/url" onerror="this.style.display='none';" />
```

`loadend`事件的監聽函式，可以用來取代`abort`事件、`load`事件、`error`事件的監聽函式，因為它總是在這些事件之後發生。

```javascript
req.addEventListener('loadend', loadEnd, false);

function loadEnd(e) {
  console.log('傳輸結束，成功失敗未知');
}
```

`loadend`事件本身不提供關於進度結束的原因，但可以用它來做所有載入結束場景都需要做的一些操作。

另外，`error`事件有一個特殊的性質，就是不會冒泡。所以，子元素的`error`事件，不會觸發父元素的`error`事件監聽函式。

## ProgressEvent 介面

`ProgressEvent`介面主要用來描述外部資源載入的進度，比如 AJAX 載入、`<img>`、`<video>`、`<style>`、`<link>`等外部資源載入。進度相關的事件都繼承了這個介面。

瀏覽器原生提供了`ProgressEvent()`建構函式，用來生成事件例項。

```javascript
new ProgressEvent(type, options)
```

`ProgressEvent()`建構函式接受兩個引數。第一個引數是字串，表示事件的型別，這個引數是必須的。第二個引數是一個配置物件，表示事件的屬性，該引數可選。配置物件除了可以使用`Event`介面的配置屬性，還可以使用下面的屬性，所有這些屬性都是可選的。

- `lengthComputable`：布林值，表示載入的總量是否可以計算，預設是`false`。
- `loaded`：整數，表示已經載入的量，預設是`0`。
- `total`：整數，表示需要載入的總量，預設是`0`。

`ProgressEvent`具有對應的例項屬性。

- `ProgressEvent.lengthComputable`
- `ProgressEvent.loaded`
- `ProgressEvent.total`

如果`ProgressEvent.lengthComputable`為`false`，`ProgressEvent.total`實際上是沒有意義的。

下面是一個例子。

```javascript
var p = new ProgressEvent('load', {
  lengthComputable: true,
  loaded: 30,
  total: 100,
});

document.body.addEventListener('load', function (e) {
  console.log('已經載入：' + (e.loaded / e.total) * 100 + '%');
});

document.body.dispatchEvent(p);
// 已經載入：30%
```

上面程式碼先構造一個`load`事件，丟擲後被監聽函式捕捉到。

下面是一個實際的例子。

```javascript
var xhr = new XMLHttpRequest();

xhr.addEventListener('progress', updateProgress, false);
xhr.addEventListener('load', transferComplete, false);
xhr.addEventListener('error', transferFailed, false);
xhr.addEventListener('abort', transferCanceled, false);

xhr.open();

function updateProgress(e) {
  if (e.lengthComputable) {
    var percentComplete = e.loaded / e.total;
  } else {
    console.log('不能計算進度');
  }
}

function transferComplete(e) {
  console.log('傳輸結束');
}

function transferFailed(evt) {
  console.log('傳輸過程中發生錯誤');
}

function transferCanceled(evt) {
  console.log('使用者取消了傳輸');
}
```

上面是下載過程的進度事件，還存在上傳過程的進度事件。這時所有監聽函式都要放在`XMLHttpRequest.upload`物件上面。

```javascript
var xhr = new XMLHttpRequest();

xhr.upload.addEventListener('progress', updateProgress, false);
xhr.upload.addEventListener('load', transferComplete, false);
xhr.upload.addEventListener('error', transferFailed, false);
xhr.upload.addEventListener('abort', transferCanceled, false);

xhr.open();
```
