# console 物件與控制檯

## console 物件

`console`物件是 JavaScript 的原生物件，它有點像 Unix 系統的標準輸出`stdout`和標準錯誤`stderr`，可以輸出各種資訊到控制檯，並且還提供了很多有用的輔助方法。

`console`的常見用途有兩個。

- 除錯程式，顯示網頁程式碼執行時的錯誤資訊。
- 提供了一個命令列介面，用來與網頁程式碼互動。

`console`物件的瀏覽器實現，包含在瀏覽器自帶的開發工具之中。以 Chrome 瀏覽器的“開發者工具”（Developer Tools）為例，可以使用下面三種方法的開啟它。

1. 按 F12 或者`Control + Shift + i`（PC）/ `Command + Option + i`（Mac）。
2. 瀏覽器選單選擇“工具/開發者工具”。
3. 在一個頁面元素上，開啟右鍵選單，選擇其中的“Inspect Element”。

開啟開發者工具以後，頂端有多個面板。

- **Elements**：檢視網頁的 HTML 原始碼和 CSS 程式碼。
- **Resources**：檢視網頁載入的各種資原始檔（比如程式碼檔案、字型檔案 CSS 檔案等），以及在硬碟上建立的各種內容（比如本地快取、Cookie、Local Storage等）。
- **Network**：檢視網頁的 HTTP 通訊情況。
- **Sources**：檢視網頁載入的指令碼原始碼。
- **Timeline**：檢視各種網頁行為隨時間變化的情況。
- **Performance**：檢視網頁的效能情況，比如 CPU 和記憶體消耗。
- **Console**：用來執行 JavaScript 命令。

這些面板都有各自的用途，以下只介紹`Console`面板（又稱為控制檯）。

`Console`面板基本上就是一個命令列視窗，你可以在提示符下，鍵入各種命令。

## console 物件的靜態方法

`console`物件提供的各種靜態方法，用來與控制檯視窗互動。

### console.log()，console.info()，console.debug()

`console.log`方法用於在控制檯輸出資訊。它可以接受一個或多個引數，將它們連線起來輸出。

```javascript
console.log('Hello World')
// Hello World
console.log('a', 'b', 'c')
// a b c
```

`console.log`方法會自動在每次輸出的結尾，新增換行符。

```javascript
console.log(1);
console.log(2);
console.log(3);
// 1
// 2
// 3
```

如果第一個引數是格式字串（使用了格式佔位符），`console.log`方法將依次用後面的引數替換佔位符，然後再進行輸出。

```javascript
console.log(' %s + %s = %s', 1, 1, 2)
//  1 + 1 = 2
```

上面程式碼中，`console.log`方法的第一個引數有三個佔位符（`%s`），第二、三、四個引數會在顯示時，依次替換掉這個三個佔位符。

`console.log`方法支援以下佔位符，不同型別的資料必須使用對應的佔位符。

- `%s` 字串
- `%d` 整數
- `%i` 整數
- `%f` 浮點數
- `%o` 物件的連結
- `%c` CSS 格式字串

```javascript
var number = 11 * 9;
var color = 'red';

console.log('%d %s balloons', number, color);
// 99 red balloons
```

上面程式碼中，第二個引數是數值，對應的佔位符是`%d`，第三個引數是字串，對應的佔位符是`%s`。

使用`%c`佔位符時，對應的引數必須是 CSS 程式碼，用來對輸出內容進行 CSS 渲染。

```javascript
console.log(
  '%cThis text is styled!',
  'color: red; background: yellow; font-size: 24px;'
)
```

上面程式碼執行後，輸出的內容將顯示為黃底紅字。

`console.log`方法的兩種引數格式，可以結合在一起使用。

```javascript
console.log(' %s + %s ', 1, 1, '= 2')
// 1 + 1  = 2
```

如果引數是一個物件，`console.log`會顯示該物件的值。

```javascript
console.log({foo: 'bar'})
// Object {foo: "bar"}
console.log(Date)
// function Date() { [native code] }
```

上面程式碼輸出`Date`物件的值，結果為一個建構函式。

`console.info`是`console.log`方法的別名，用法完全一樣。只不過`console.info`方法會在輸出資訊的前面，加上一個藍色圖示。

`console.debug`方法與`console.log`方法類似，會在控制檯輸出除錯資訊。但是，預設情況下，`console.debug`輸出的資訊不會顯示，只有在開啟顯示級別在`verbose`的情況下，才會顯示。

`console`物件的所有方法，都可以被覆蓋。因此，可以按照自己的需要，定義`console.log`方法。

```javascript
['log', 'info', 'warn', 'error'].forEach(function(method) {
  console[method] = console[method].bind(
    console,
    new Date().toISOString()
  );
});

console.log("出錯了！");
// 2014-05-18T09:00.000Z 出錯了！
```

上面程式碼表示，使用自定義的`console.log`方法，可以在顯示結果添加當前時間。

### console.warn()，console.error()

`warn`方法和`error`方法也是在控制檯輸出資訊，它們與`log`方法的不同之處在於，`warn`方法輸出資訊時，在最前面加一個黃色三角，表示警告；`error`方法輸出資訊時，在最前面加一個紅色的叉，表示出錯。同時，還會高亮顯示輸出文字和錯誤發生的堆疊。其他方面都一樣。

```javascript
console.error('Error: %s (%i)', 'Server is not responding', 500)
// Error: Server is not responding (500)
console.warn('Warning! Too few nodes (%d)', document.childNodes.length)
// Warning! Too few nodes (1)
```

可以這樣理解，`log`方法是寫入標準輸出（`stdout`），`warn`方法和`error`方法是寫入標準錯誤（`stderr`）。

### console.table()

對於某些複合型別的資料，`console.table`方法可以將其轉為表格顯示。

```javascript
var languages = [
  { name: "JavaScript", fileExtension: ".js" },
  { name: "TypeScript", fileExtension: ".ts" },
  { name: "CoffeeScript", fileExtension: ".coffee" }
];

console.table(languages);
```

上面程式碼的`language`變數，轉為表格顯示如下。

(index)|name|fileExtension
-------|----|-------------
0|"JavaScript"|".js"
1|"TypeScript"|".ts"
2|"CoffeeScript"|".coffee"

下面是顯示錶格內容的例子。

```javascript
var languages = {
  csharp: { name: "C#", paradigm: "object-oriented" },
  fsharp: { name: "F#", paradigm: "functional" }
};

console.table(languages);
```

上面程式碼的`language`，轉為表格顯示如下。

(index)|name|paradigm
-------|----|--------
csharp|"C#"|"object-oriented"
fsharp|"F#"|"functional"

### console.count()

`count`方法用於計數，輸出它被呼叫了多少次。

```javascript
function greet(user) {
  console.count();
  return 'hi ' + user;
}

greet('bob')
//  : 1
// "hi bob"

greet('alice')
//  : 2
// "hi alice"

greet('bob')
//  : 3
// "hi bob"
```

上面程式碼每次呼叫`greet`函式，內部的`console.count`方法就輸出執行次數。

該方法可以接受一個字串作為引數，作為標籤，對執行次數進行分類。

```javascript
function greet(user) {
  console.count(user);
  return "hi " + user;
}

greet('bob')
// bob: 1
// "hi bob"

greet('alice')
// alice: 1
// "hi alice"

greet('bob')
// bob: 2
// "hi bob"
```

上面程式碼根據引數的不同，顯示`bob`執行了兩次，`alice`執行了一次。

### console.dir()，console.dirxml()

`dir`方法用來對一個物件進行檢查（inspect），並以易於閱讀和列印的格式顯示。

```javascript
console.log({f1: 'foo', f2: 'bar'})
// Object {f1: "foo", f2: "bar"}

console.dir({f1: 'foo', f2: 'bar'})
// Object
//   f1: "foo"
//   f2: "bar"
//   __proto__: Object
```

上面程式碼顯示`dir`方法的輸出結果，比`log`方法更易讀，資訊也更豐富。

該方法對於輸出 DOM 物件非常有用，因為會顯示 DOM 物件的所有屬性。

```javascript
console.dir(document.body)
```

Node 環境之中，還可以指定以程式碼高亮的形式輸出。

```javascript
console.dir(obj, {colors: true})
```

`dirxml`方法主要用於以目錄樹的形式，顯示 DOM 節點。

```javascript
console.dirxml(document.body)
```

如果引數不是 DOM 節點，而是普通的 JavaScript 物件，`console.dirxml`等同於`console.dir`。

```javascript
console.dirxml([1, 2, 3])
// 等同於
console.dir([1, 2, 3])
```

### console.assert()

`console.assert`方法主要用於程式執行過程中，進行條件判斷，如果不滿足條件，就顯示一個錯誤，但不會中斷程式執行。這樣就相當於提示使用者，內部狀態不正確。

它接受兩個引數，第一個引數是表示式，第二個引數是字串。只有當第一個引數為`false`，才會提示有錯誤，在控制檯輸出第二個引數，否則不會有任何結果。

```javascript
console.assert(false, '判斷條件不成立')
// Assertion failed: 判斷條件不成立

// 相當於
try {
  if (!false) {
    throw new Error('判斷條件不成立');
  }
} catch(e) {
  console.error(e);
}
```

下面是一個例子，判斷子節點的個數是否大於等於500。

```javascript
console.assert(list.childNodes.length < 500, '節點個數大於等於500')
```

上面程式碼中，如果符合條件的節點小於500個，不會有任何輸出；只有大於等於500時，才會在控制檯提示錯誤，並且顯示指定文字。

### console.time()，console.timeEnd()

這兩個方法用於計時，可以算出一個操作所花費的準確時間。

```javascript
console.time('Array initialize');

var array= new Array(1000000);
for (var i = array.length - 1; i >= 0; i--) {
  array[i] = new Object();
};

console.timeEnd('Array initialize');
// Array initialize: 1914.481ms
```

`time`方法表示計時開始，`timeEnd`方法表示計時結束。它們的引數是計時器的名稱。呼叫`timeEnd`方法之後，控制檯會顯示“計時器名稱: 所耗費的時間”。

### console.group()，console.groupEnd()，console.groupCollapsed()

`console.group`和`console.groupEnd`這兩個方法用於將顯示的資訊分組。它只在輸出大量資訊時有用，分在一組的資訊，可以用滑鼠摺疊/展開。

```javascript
console.group('一級分組');
console.log('一級分組的內容');

console.group('二級分組');
console.log('二級分組的內容');

console.groupEnd(); // 二級分組結束
console.groupEnd(); // 一級分組結束
```

上面程式碼會將“二級分組”顯示在“一級分組”內部，並且“一級分組”和“二級分組”前面都有一個摺疊符號，可以用來摺疊本級的內容。

`console.groupCollapsed`方法與`console.group`方法很類似，唯一的區別是該組的內容，在第一次顯示時是收起的（collapsed），而不是展開的。

```javascript
console.groupCollapsed('Fetching Data');

console.log('Request Sent');
console.error('Error: Server not responding (500)');

console.groupEnd();
```

上面程式碼只顯示一行”Fetching Data“，點選後才會展開，顯示其中包含的兩行。

### console.trace()，console.clear()

`console.trace`方法顯示當前執行的程式碼在堆疊中的呼叫路徑。

```javascript
console.trace()
// console.trace()
//   (anonymous function)
//   InjectedScript._evaluateOn
//   InjectedScript._evaluateAndWrap
//   InjectedScript.evaluate
```

`console.clear`方法用於清除當前控制檯的所有輸出，將游標回置到第一行。如果使用者選中了控制檯的“Preserve log”選項，`console.clear`方法將不起作用。

## 控制檯命令列 API

瀏覽器控制檯中，除了使用`console`物件，還可以使用一些控制檯自帶的命令列方法。

（1）`$_`

`$_`屬性返回上一個表示式的值。

```javascript
2 + 2
// 4
$_
// 4
```

（2）`$0` - `$4`

控制檯儲存了最近5個在 Elements 面板選中的 DOM 元素，`$0`代表倒數第一個（最近一個），`$1`代表倒數第二個，以此類推直到`$4`。

（3）`$(selector)`

`$(selector)`返回第一個匹配的元素，等同於`document.querySelector()`。注意，如果頁面指令碼對`$`有定義，則會覆蓋原始的定義。比如，頁面裡面有 jQuery，控制檯執行`$(selector)`就會採用 jQuery 的實現，返回一個數組。

（4）`$$(selector)`

`$$(selector)`返回選中的 DOM 物件，等同於`document.querySelectorAll`。

（5）`$x(path)`

`$x(path)`方法返回一個數組，包含匹配特定 XPath 表示式的所有 DOM 元素。

```javascript
$x("//p[a]")
```

上面程式碼返回所有包含`a`元素的`p`元素。

（6）`inspect(object)`

`inspect(object)`方法開啟相關面板，並選中相應的元素，顯示它的細節。DOM 元素在`Elements`面板中顯示，比如`inspect(document)`會在 Elements 面板顯示`document`元素。JavaScript 物件在控制檯面板`Profiles`面板中顯示，比如`inspect(window)`。

（7）`getEventListeners(object)`

`getEventListeners(object)`方法返回一個物件，該物件的成員為`object`登記了回撥函式的各種事件（比如`click`或`keydown`），每個事件對應一個數組，陣列的成員為該事件的回撥函式。

（8）`keys(object)`，`values(object)`

`keys(object)`方法返回一個數組，包含`object`的所有鍵名。

`values(object)`方法返回一個數組，包含`object`的所有鍵值。

```javascript
var o = {'p1': 'a', 'p2': 'b'};

keys(o)
// ["p1", "p2"]
values(o)
// ["a", "b"]
```

（9）`monitorEvents(object[, events]) ，unmonitorEvents(object[, events])`

`monitorEvents(object[, events])`方法監聽特定物件上發生的特定事件。事件發生時，會返回一個`Event`物件，包含該事件的相關資訊。`unmonitorEvents`方法用於停止監聽。

```javascript
monitorEvents(window, "resize");
monitorEvents(window, ["resize", "scroll"])
```

上面程式碼分別表示單個事件和多個事件的監聽方法。

```javascript
monitorEvents($0, 'mouse');
unmonitorEvents($0, 'mousemove');
```

上面程式碼表示如何停止監聽。

`monitorEvents`允許監聽同一大類的事件。所有事件可以分成四個大類。

- mouse："mousedown", "mouseup", "click", "dblclick", "mousemove", "mouseover", "mouseout", "mousewheel"
- key："keydown", "keyup", "keypress", "textInput"
- touch："touchstart", "touchmove", "touchend", "touchcancel"
- control："resize", "scroll", "zoom", "focus", "blur", "select", "change", "submit", "reset"

```javascript
monitorEvents($("#msg"), "key");
```

上面程式碼表示監聽所有`key`大類的事件。

（10）其他方法

命令列 API 還提供以下方法。

- `clear()`：清除控制檯的歷史。
- `copy(object)`：複製特定 DOM 元素到剪貼簿。
- `dir(object)`：顯示特定物件的所有屬性，是`console.dir`方法的別名。
- `dirxml(object)`：顯示特定物件的 XML 形式，是`console.dirxml`方法的別名。

## debugger 語句

`debugger`語句主要用於除錯，作用是設定斷點。如果有正在執行的除錯工具，程式執行到`debugger`語句時會自動停下。如果沒有除錯工具，`debugger`語句不會產生任何結果，JavaScript 引擎自動跳過這一句。

Chrome 瀏覽器中，當代碼執行到`debugger`語句時，就會暫停執行，自動開啟指令碼原始碼介面。

```javascript
for(var i = 0; i < 5; i++){
  console.log(i);
  if (i === 2) debugger;
}
```

上面程式碼打印出0，1，2以後，就會暫停，自動開啟原始碼介面，等待進一步處理。

## 參考連結

- Chrome Developer Tools, [Using the Console](https://developers.google.com/chrome-developer-tools/docs/console)
- Matt West, [Mastering The Developer Tools Console](http://blog.teamtreehouse.com/mastering-developer-tools-console)
- Firebug Wiki, [Console API](https://getfirebug.com/wiki/index.php/Console_API)
- Axel Rauschmayer, [The JavaScript console API](http://www.2ality.com/2013/10/console-api.html)
- Marius Schulz, [Advanced JavaScript Debugging with console.table()](http://blog.mariusschulz.com/2013/11/13/advanced-javascript-debugging-with-consoletable)
- Google Developer, [Command Line API Reference](https://developers.google.com/chrome-developer-tools/docs/commandline-api)
