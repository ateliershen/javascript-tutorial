# 瀏覽器環境概述

JavaScript 是瀏覽器的內建指令碼語言。也就是說，瀏覽器內建了 JavaScript 引擎，並且提供各種介面，讓 JavaScript 指令碼可以控制瀏覽器的各種功能。一旦網頁內嵌了 JavaScript 指令碼，瀏覽器載入網頁，就會去執行指令碼，從而達到操作瀏覽器的目的，實現網頁的各種動態效果。

本章開始介紹瀏覽器提供的各種 JavaScript 介面。首先，介紹 JavaScript 程式碼嵌入網頁的方法。

## 程式碼嵌入網頁的方法

網頁中嵌入 JavaScript 程式碼，主要有四種方法。

- `<script>`元素直接嵌入程式碼。
- `<script>`標籤載入外部指令碼
- 事件屬性
- URL 協議

### script 元素嵌入程式碼

`<script>`元素內部可以直接寫入 JavaScript 程式碼。

```html
<script>
  var x = 1 + 5;
  console.log(x);
</script>
```

`<script>`標籤有一個`type`屬性，用來指定指令碼型別。對 JavaScript 指令碼來說，`type`屬性可以設為兩種值。

- `text/javascript`：這是預設值，也是歷史上一貫設定的值。如果你省略`type`屬性，預設就是這個值。對於老式瀏覽器，設為這個值比較好。
- `application/javascript`：對於較新的瀏覽器，建議設為這個值。

```html
<script type="application/javascript">
  console.log('Hello World');
</script>
```

由於`<script>`標籤預設就是 JavaScript 程式碼。所以，嵌入 JavaScript 指令碼時，`type`屬性可以省略。

如果`type`屬性的值，瀏覽器不認識，那麼它不會執行其中的程式碼。利用這一點，可以在`<script>`標籤之中嵌入任意的文字內容，只要加上一個瀏覽器不認識的`type`屬性即可。

```html
<script id="mydata" type="x-custom-data">
  console.log('Hello World');
</script>
```

上面的程式碼，瀏覽器不會執行，也不會顯示它的內容，因為不認識它的`type`屬性。但是，這個`<script>`節點依然存在於 DOM 之中，可以使用`<script>`節點的`text`屬性讀出它的內容。

```javascript
document.getElementById('mydata').text
//   console.log('Hello World');
```

### script 元素載入外部指令碼

`<script>`標籤也可以指定載入外部的指令碼檔案。

```html
<script src="https://www.example.com/script.js"></script>
```

如果指令碼檔案使用了非英語字元，還應該註明字元的編碼。

```html
<script charset="utf-8" src="https://www.example.com/script.js"></script>
```

所載入的指令碼必須是純的 JavaScript 程式碼，不能有`HTML`程式碼和`<script>`標籤。

載入外部指令碼和直接新增程式碼塊，這兩種方法不能混用。下面程式碼的`console.log`語句直接被忽略。

```html
<script charset="utf-8" src="example.js">
  console.log('Hello World!');
</script>
```

為了防止攻擊者篡改外部指令碼，`script`標籤允許設定一個`integrity`屬性，寫入該外部指令碼的 Hash 簽名，用來驗證指令碼的一致性。

```html
<script src="/assets/application.js"
  integrity="sha256-TvVUHzSfftWg1rcfL6TIJ0XKEGrgLyEq6lEpcmrG9qs=">
</script>
```

上面程式碼中，`script`標籤有一個`integrity`屬性，指定了外部指令碼`/assets/application.js`的 SHA256 簽名。一旦有人改了這個指令碼，導致 SHA256 簽名不匹配，瀏覽器就會拒絕載入。

### 事件屬性

網頁元素的事件屬性（比如`onclick`和`onmouseover`），可以寫入 JavaScript 程式碼。當指定事件發生時，就會呼叫這些程式碼。

```html
<button id="myBtn" onclick="console.log(this.id)">點選</button>
```

上面的事件屬性程式碼只有一個語句。如果有多個語句，使用分號分隔即可。

### URL 協議

URL 支援`javascript:`協議，即在 URL 的位置寫入程式碼，使用這個 URL 的時候就會執行 JavaScript 程式碼。

```html
<a href="javascript:console.log('Hello')">點選</a>
```

瀏覽器的位址列也可以執行`javascript:`協議。將`javascript:console.log('Hello')`放入位址列，按回車鍵也會執行這段程式碼。

如果 JavaScript 程式碼返回一個字串，瀏覽器就會新建一個文件，展示這個字串的內容，原有文件的內容都會消失。

```html
<a href="javascript: new Date().toLocaleTimeString();">點選</a>
```

上面程式碼中，使用者點選連結以後，會開啟一個新文件，裡面有當前時間。

如果返回的不是字串，那麼瀏覽器不會新建文件，也不會跳轉。

```javascript
<a href="javascript: console.log(new Date().toLocaleTimeString())">點選</a>
```

上面程式碼中，使用者點選連結後，網頁不會跳轉，只會在控制檯顯示當前時間。

`javascript:`協議的常見用途是書籤指令碼 Bookmarklet。由於瀏覽器的書籤儲存的是一個網址，所以`javascript:`網址也可以儲存在裡面，使用者選擇這個書籤的時候，就會在當前頁面執行這個指令碼。為了防止書籤替換掉當前文件，可以在指令碼前加上`void`，或者在指令碼最後加上`void 0`。

```html
<a href="javascript: void new Date().toLocaleTimeString();">點選</a>
<a href="javascript: new Date().toLocaleTimeString();void 0;">點選</a>
```

上面這兩種寫法，點選連結後，執行程式碼都不會網頁跳轉。

## script 元素

### 工作原理

瀏覽器載入 JavaScript 指令碼，主要透過`<script>`元素完成。正常的網頁載入流程是這樣的。

1. 瀏覽器一邊下載 HTML 網頁，一邊開始解析。也就是說，不等到下載完，就開始解析。
2. 解析過程中，瀏覽器發現`<script>`元素，就暫停解析，把網頁渲染的控制權轉交給 JavaScript 引擎。
3. 如果`<script>`元素引用了外部指令碼，就下載該指令碼再執行，否則就直接執行程式碼。
4. JavaScript 引擎執行完畢，控制權交還渲染引擎，恢復往下解析 HTML 網頁。

載入外部指令碼時，瀏覽器會暫停頁面渲染，等待指令碼下載並執行完成後，再繼續渲染。原因是 JavaScript 程式碼可以修改 DOM，所以必須把控制權讓給它，否則會導致複雜的執行緒競賽的問題。

如果外部指令碼載入時間很長（一直無法完成下載），那麼瀏覽器就會一直等待指令碼下載完成，造成網頁長時間失去響應，瀏覽器就會呈現“假死”狀態，這被稱為“阻塞效應”。

為了避免這種情況，較好的做法是將`<script>`標籤都放在頁面底部，而不是頭部。這樣即使遇到指令碼失去響應，網頁主體的渲染也已經完成了，使用者至少可以看到內容，而不是面對一張空白的頁面。如果某些指令碼程式碼非常重要，一定要放在頁面頭部的話，最好直接將程式碼寫入頁面，而不是連線外部指令碼檔案，這樣能縮短載入時間。

指令碼檔案都放在網頁尾部載入，還有一個好處。因為在 DOM 結構生成之前就呼叫 DOM 節點，JavaScript 會報錯，如果指令碼都在網頁尾部載入，就不存在這個問題，因為這時 DOM 肯定已經生成了。

```html
<head>
  <script>
    console.log(document.body.innerHTML);
  </script>
</head>
<body>
</body>
```

上面程式碼執行時會報錯，因為此時`document.body`元素還未生成。

一種解決方法是設定`DOMContentLoaded`事件的回撥函式。

```html
<head>
  <script>
    document.addEventListener(
      'DOMContentLoaded',
      function (event) {
        console.log(document.body.innerHTML);
      }
    );
  </script>
</head>
```

上面程式碼中，指定`DOMContentLoaded`事件發生後，才開始執行相關程式碼。`DOMContentLoaded`事件只有在 DOM 結構生成之後才會觸發。

另一種解決方法是，使用`<script>`標籤的`onload`屬性。當`<script>`標籤指定的外部指令碼檔案下載和解析完成，會觸發一個`load`事件，可以把所需執行的程式碼，放在這個事件的回撥函式裡面。

```html
<script src="jquery.min.js" onload="console.log(document.body.innerHTML)">
</script>
```

但是，如果將指令碼放在頁面底部，就可以完全按照正常的方式寫，上面兩種方式都不需要。

```html
<body>
  <!-- 其他程式碼  -->
  <script>
    console.log(document.body.innerHTML);
  </script>
</body>
```

如果有多個`script`標籤，比如下面這樣。

```html
<script src="a.js"></script>
<script src="b.js"></script>
```

瀏覽器會同時並行下載`a.js`和`b.js`，但是，執行時會保證先執行`a.js`，然後再執行`b.js`，即使後者先下載完成，也是如此。也就是說，指令碼的執行順序由它們在頁面中的出現順序決定，這是為了保證指令碼之間的依賴關係不受到破壞。當然，載入這兩個指令碼都會產生“阻塞效應”，必須等到它們都載入完成，瀏覽器才會繼續頁面渲染。

解析和執行 CSS，也會產生阻塞。Firefox 瀏覽器會等到指令碼前面的所有樣式表，都下載並解析完，再執行指令碼；Webkit則是一旦發現指令碼引用了樣式，就會暫停執行指令碼，等到樣式表下載並解析完，再恢復執行。

此外，對於來自同一個域名的資源，比如指令碼檔案、樣式表文件、圖片檔案等，瀏覽器一般有限制，同時最多下載6～20個資源，即最多同時開啟的 TCP 連線有限制，這是為了防止對伺服器造成太大壓力。如果是來自不同域名的資源，就沒有這個限制。所以，通常把靜態檔案放在不同的域名之下，以加快下載速度。

### defer 屬性

為了解決指令碼檔案下載阻塞網頁渲染的問題，一個方法是對`<script>`元素加入`defer`屬性。它的作用是延遲指令碼的執行，等到 DOM 載入生成後，再執行指令碼。

```html
<script src="a.js" defer></script>
<script src="b.js" defer></script>
```

上面程式碼中，只有等到 DOM 載入完成後，才會執行`a.js`和`b.js`。

`defer`屬性的執行流程如下。

1. 瀏覽器開始解析 HTML 網頁。
2. 解析過程中，發現帶有`defer`屬性的`<script>`元素。
3. 瀏覽器繼續往下解析 HTML 網頁，同時並行下載`<script>`元素載入的外部指令碼。
4. 瀏覽器完成解析 HTML 網頁，此時再回過頭執行已經下載完成的指令碼。

有了`defer`屬性，瀏覽器下載指令碼檔案的時候，不會阻塞頁面渲染。下載的指令碼檔案在`DOMContentLoaded`事件觸發前執行（即剛剛讀取完`</html>`標籤），而且可以保證執行順序就是它們在頁面上出現的順序。

對於內建而不是載入外部指令碼的`script`標籤，以及動態生成的`script`標籤，`defer`屬性不起作用。另外，使用`defer`載入的外部指令碼不應該使用`document.write`方法。

### async 屬性

解決“阻塞效應”的另一個方法是對`<script>`元素加入`async`屬性。

```html
<script src="a.js" async></script>
<script src="b.js" async></script>
```

`async`屬性的作用是，使用另一個程序下載指令碼，下載時不會阻塞渲染。

1. 瀏覽器開始解析 HTML 網頁。
2. 解析過程中，發現帶有`async`屬性的`script`標籤。
3. 瀏覽器繼續往下解析 HTML 網頁，同時並行下載`<script>`標籤中的外部指令碼。
4. 指令碼下載完成，瀏覽器暫停解析 HTML 網頁，開始執行下載的指令碼。
5. 指令碼執行完畢，瀏覽器恢復解析 HTML 網頁。

`async`屬性可以保證指令碼下載的同時，瀏覽器繼續渲染。需要注意的是，一旦採用這個屬性，就無法保證指令碼的執行順序。哪個指令碼先下載結束，就先執行那個指令碼。另外，使用`async`屬性的指令碼檔案裡面的程式碼，不應該使用`document.write`方法。

`defer`屬性和`async`屬性到底應該使用哪一個？

一般來說，如果指令碼之間沒有依賴關係，就使用`async`屬性，如果指令碼之間有依賴關係，就使用`defer`屬性。如果同時使用`async`和`defer`屬性，後者不起作用，瀏覽器行為由`async`屬性決定。

### 指令碼的動態載入

`<script>`元素還可以動態生成，生成後再插入頁面，從而實現指令碼的動態載入。

```javascript
['a.js', 'b.js'].forEach(function(src) {
  var script = document.createElement('script');
  script.src = src;
  document.head.appendChild(script);
});
```

這種方法的好處是，動態生成的`script`標籤不會阻塞頁面渲染，也就不會造成瀏覽器假死。但是問題在於，這種方法無法保證指令碼的執行順序，哪個指令碼檔案先下載完成，就先執行哪個。

如果想避免這個問題，可以設定async屬性為`false`。

```javascript
['a.js', 'b.js'].forEach(function(src) {
  var script = document.createElement('script');
  script.src = src;
  script.async = false;
  document.head.appendChild(script);
});
```

上面的程式碼不會阻塞頁面渲染，而且可以保證`b.js`在`a.js`後面執行。不過需要注意的是，在這段程式碼後面載入的指令碼檔案，會因此都等待`b.js`執行完成後再執行。

如果想為動態載入的指令碼指定回撥函式，可以使用下面的寫法。

```javascript
function loadScript(src, done) {
  var js = document.createElement('script');
  js.src = src;
  js.onload = function() {
    done();
  };
  js.onerror = function() {
    done(new Error('Failed to load script ' + src));
  };
  document.head.appendChild(js);
}
```

### 載入使用的協議

如果不指定協議，瀏覽器預設採用 HTTP 協議下載。

```html
<script src="example.js"></script>
```

上面的`example.js`預設就是採用 HTTP 協議下載，如果要採用 HTTPS 協議下載，必需寫明。

```html
<script src="https://example.js"></script>
```

但是有時我們會希望，根據頁面本身的協議來決定載入協議，這時可以採用下面的寫法。

```html
<script src="//example.js"></script>
```

## 瀏覽器的組成

瀏覽器的核心是兩部分：渲染引擎和 JavaScript 直譯器（又稱 JavaScript 引擎）。

### 渲染引擎

渲染引擎的主要作用是，將網頁程式碼渲染為使用者視覺可以感知的平面文件。

不同的瀏覽器有不同的渲染引擎。

- Firefox：Gecko 引擎
- Safari：WebKit 引擎
- Chrome：Blink 引擎
- IE: Trident 引擎
- Edge: EdgeHTML 引擎

渲染引擎處理網頁，通常分成四個階段。

1. 解析程式碼：HTML 程式碼解析為 DOM，CSS 程式碼解析為 CSSOM（CSS Object Model）。
2. 物件合成：將 DOM 和 CSSOM 合成一棵渲染樹（render tree）。
3. 佈局：計算出渲染樹的佈局（layout）。
4. 繪製：將渲染樹繪製到螢幕。

以上四步並非嚴格按順序執行，往往第一步還沒完成，第二步和第三步就已經開始了。所以，會看到這種情況：網頁的 HTML 程式碼還沒下載完，但瀏覽器已經顯示出內容了。

### 重流和重繪

渲染樹轉換為網頁佈局，稱為“佈局流”（flow）；佈局顯示到頁面的這個過程，稱為“繪製”（paint）。它們都具有阻塞效應，並且會耗費很多時間和計算資源。

頁面生成以後，指令碼操作和樣式表操作，都會觸發“重流”（reflow）和“重繪”（repaint）。使用者的互動也會觸發重流和重繪，比如設定了滑鼠懸停（`a:hover`）效果、頁面滾動、在輸入框中輸入文字、改變視窗大小等等。

重流和重繪並不一定一起發生，重流必然導致重繪，重繪不一定需要重流。比如改變元素顏色，只會導致重繪，而不會導致重流；改變元素的佈局，則會導致重繪和重流。

大多數情況下，瀏覽器會智慧判斷，將重流和重繪只限制到相關的子樹上面，最小化所耗費的代價，而不會全域性重新生成網頁。

作為開發者，應該儘量設法降低重繪的次數和成本。比如，儘量不要變動高層的 DOM 元素，而以底層 DOM 元素的變動代替；再比如，重繪`table`佈局和`flex`佈局，開銷都會比較大。

```javascript
var foo = document.getElementById('foobar');

foo.style.color = 'blue';
foo.style.marginTop = '30px';
```

上面的程式碼只會導致一次重繪，因為瀏覽器會累積 DOM 變動，然後一次性執行。

下面是一些最佳化技巧。

- 讀取 DOM 或者寫入 DOM，儘量寫在一起，不要混雜。不要讀取一個 DOM 節點，然後立刻寫入，接著再讀取一個 DOM 節點。
- 快取 DOM 資訊。
- 不要一項一項地改變樣式，而是使用 CSS class 一次性改變樣式。
- 使用`documentFragment`操作 DOM
- 動畫使用`absolute`定位或`fixed`定位，這樣可以減少對其他元素的影響。
- 只在必要時才顯示隱藏元素。
- 使用`window.requestAnimationFrame()`，因為它可以把程式碼推遲到下一次重繪之前執行，而不是立即要求頁面重繪。
- 使用虛擬 DOM（virtual DOM）庫。

下面是一個`window.requestAnimationFrame()`對比效果的例子。

```javascript
// 重流代價高
function doubleHeight(element) {
  var currentHeight = element.clientHeight;
  element.style.height = (currentHeight * 2) + 'px';
}

all_my_elements.forEach(doubleHeight);

// 重繪代價低
function doubleHeight(element) {
  var currentHeight = element.clientHeight;

  window.requestAnimationFrame(function () {
    element.style.height = (currentHeight * 2) + 'px';
  });
}

all_my_elements.forEach(doubleHeight);
```

上面的第一段程式碼，每讀一次 DOM，就寫入新的值，會造成不停的重排和重流。第二段程式碼把所有的寫操作，都累積在一起，從而 DOM 程式碼變動的代價就最小化了。

### JavaScript 引擎

JavaScript 引擎的主要作用是，讀取網頁中的 JavaScript 程式碼，對其處理後執行。

JavaScript 是一種解釋型語言，也就是說，它不需要編譯，由直譯器實時執行。這樣的好處是執行和修改都比較方便，重新整理頁面就可以重新解釋；缺點是每次執行都要呼叫直譯器，系統開銷較大，執行速度慢於編譯型語言。

為了提高執行速度，目前的瀏覽器都將 JavaScript 進行一定程度的編譯，生成類似位元組碼（bytecode）的中間程式碼，以提高執行速度。

早期，瀏覽器內部對 JavaScript 的處理過程如下：

1. 讀取程式碼，進行詞法分析（Lexical analysis），將程式碼分解成詞元（token）。
2. 對詞元進行語法分析（parsing），將程式碼整理成“語法樹”（syntax tree）。
3. 使用“翻譯器”（translator），將程式碼轉為位元組碼（bytecode）。
4. 使用“位元組碼直譯器”（bytecode interpreter），將位元組碼轉為機器碼。

逐行解釋將位元組碼轉為機器碼，是很低效的。為了提高執行速度，現代瀏覽器改為採用“即時編譯”（Just In Time compiler，縮寫 JIT），即位元組碼只在執行時編譯，用到哪一行就編譯哪一行，並且把編譯結果快取（inline cache）。通常，一個程式被經常用到的，只是其中一小部分程式碼，有了快取的編譯結果，整個程式的執行速度就會顯著提升。

位元組碼不能直接執行，而是執行在一個虛擬機器（Virtual Machine）之上，一般也把虛擬機器稱為 JavaScript 引擎。並非所有的 JavaScript 虛擬機器執行時都有位元組碼，有的 JavaScript 虛擬機器基於原始碼，即只要有可能，就透過 JIT（just in time）編譯器直接把原始碼編譯成機器碼執行，省略位元組碼步驟。這一點與其他採用虛擬機器（比如 Java）的語言不盡相同。這樣做的目的，是為了儘可能地最佳化程式碼、提高效能。下面是目前最常見的一些 JavaScript 虛擬機器：

- [Chakra](https://en.wikipedia.org/wiki/Chakra_(JScript_engine)) (Microsoft Internet Explorer)
- [Nitro/JavaScript Core](http://en.wikipedia.org/wiki/WebKit#JavaScriptCore) (Safari)
- [Carakan](http://dev.opera.com/articles/view/labs-carakan/) (Opera)
- [SpiderMonkey](https://developer.mozilla.org/en-US/docs/SpiderMonkey) (Firefox)
- [V8](https://en.wikipedia.org/wiki/Chrome_V8) (Chrome, Chromium)

## 參考連結

- John Dalziel, [The race for speed part 2: How JavaScript compilers work](http://creativejs.com/2013/06/the-race-for-speed-part-2-how-javascript-compilers-work/)
- Jake Archibald, [Deep dive into the murky waters of script loading](http://www.html5rocks.com/en/tutorials/speed/script-loading/)
- Mozilla Developer Network, [window.setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/window.setTimeout)
- Remy Sharp, [Throttling function calls](http://remysharp.com/2010/07/21/throttling-function-calls/)
- Ayman Farhat, [An alternative to JavaScript's evil setInterval](http://www.thecodeship.com/web-development/alternative-to-javascript-evil-setinterval/)
- Ilya Grigorik, [Script-injected "async scripts" considered harmful](https://www.igvita.com/2014/05/20/script-injected-async-scripts-considered-harmful/)
- Axel Rauschmayer, [ECMAScript 6 promises (1/2): foundations](http://www.2ality.com/2014/09/es6-promises-foundations.html)
- Daniel Imms, [async vs defer attributes](http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html)
- Craig Buckler, [Load Non-blocking JavaScript with HTML5 Async and Defer](http://www.sitepoint.com/non-blocking-async-defer/)
- Domenico De Felice, [How browsers work](http://domenicodefelice.blogspot.sg/2015/08/how-browsers-work.html?t=2)
