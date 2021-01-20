# 嚴格模式

除了正常的執行模式，JavaScript 還有第二種執行模式：嚴格模式（strict mode）。顧名思義，這種模式採用更加嚴格的 JavaScript 語法。

同樣的程式碼，在正常模式和嚴格模式中，可能會有不一樣的執行結果。一些在正常模式下可以執行的語句，在嚴格模式下將不能執行。

## 設計目的

早期的 JavaScript 語言有很多設計不合理的地方，但是為了相容以前的程式碼，又不能改變老的語法，只能不斷新增新的語法，載入程式員使用新語法。

嚴格模式是從 ES5 進入標準的，主要目的有以下幾個。

- 明確禁止一些不合理、不嚴謹的語法，減少 JavaScript 語言的一些怪異行為。
- 增加更多報錯的場合，消除程式碼執行的一些不安全之處，保證程式碼執行的安全。
- 提高編譯器效率，增加執行速度。
- 為未來新版本的 JavaScript 語法做好鋪墊。

總之，嚴格模式體現了 JavaScript 更合理、更安全、更嚴謹的發展方向。

## 啟用方法

進入嚴格模式的標誌，是一行字串`use strict`。

```javascript
'use strict';
```

老版本的引擎會把它當作一行普通字串，加以忽略。新版本的引擎就會進入嚴格模式。

嚴格模式可以用於整個指令碼，也可以只用於單個函式。

**（1） 整個指令碼檔案**

`use strict`放在指令碼檔案的第一行，整個指令碼都將以嚴格模式執行。如果這行語句不在第一行就無效，整個指令碼會以正常模式執行。(嚴格地說，只要前面不是產生實際執行結果的語句，`use strict`可以不在第一行，比如直接跟在一個空的分號後面，或者跟在註釋後面。)

```html
<script>
  'use strict';
  console.log('這是嚴格模式');
</script>

<script>
  console.log('這是正常模式');
</script>
```

上面程式碼中，一個網頁檔案依次有兩段 JavaScript 程式碼。前一個`<script>`標籤是嚴格模式，後一個不是。

如果`use strict`寫成下面這樣，則不起作用，嚴格模式必須從程式碼一開始就生效。

```html
<script>
  console.log('這是正常模式');
  'use strict';
</script>
```

**（2）單個函式**

`use strict`放在函式體的第一行，則整個函式以嚴格模式執行。

```javascript
function strict() {
  'use strict';
  return '這是嚴格模式';
}

function strict2() {
  'use strict';
  function f() {
    return '這也是嚴格模式';
  }
  return f();
}

function notStrict() {
  return '這是正常模式';
}
```

有時，需要把不同的指令碼合併在一個檔案裡面。如果一個指令碼是嚴格模式，另一個指令碼不是，它們的合併就可能出錯。嚴格模式的指令碼在前，則合併後的指令碼都是嚴格模式；如果正常模式的指令碼在前，則合併後的指令碼都是正常模式。這兩種情況下，合併後的結果都是不正確的。這時可以考慮把整個指令碼檔案放在一個立即執行的匿名函式之中。

```javascript
(function () {
  'use strict';
  // some code here
})();
```

## 顯式報錯

嚴格模式使得 JavaScript 的語法變得更嚴格，更多的操作會顯式報錯。其中有些操作，在正常模式下只會默默地失敗，不會報錯。

### 只讀屬性不可寫

嚴格模式下，設定字串的`length`屬性，會報錯。

```javascript
'use strict';
'abc'.length = 5;
// TypeError: Cannot assign to read only property 'length' of string 'abc'
```

上面程式碼報錯，因為`length`是隻讀屬性，嚴格模式下不可寫。正常模式下，改變`length`屬性是無效的，但不會報錯。

嚴格模式下，對只讀屬性賦值，或者刪除不可配置（non-configurable）屬性都會報錯。

```javascript
// 對只讀屬性賦值會報錯
'use strict';
Object.defineProperty({}, 'a', {
  value: 37,
  writable: false
});
obj.a = 123;
// TypeError: Cannot assign to read only property 'a' of object #<Object>

// 刪除不可配置的屬性會報錯
'use strict';
var obj = Object.defineProperty({}, 'p', {
  value: 1,
  configurable: false
});
delete obj.p
// TypeError: Cannot delete property 'p' of #<Object>
```

### 只設置了取值器的屬性不可寫

嚴格模式下，對一個只有取值器（getter）、沒有存值器（setter）的屬性賦值，會報錯。

```javascript
'use strict';
var obj = {
  get v() { return 1; }
};
obj.v = 2;
// Uncaught TypeError: Cannot set property v of #<Object> which has only a getter
```

上面程式碼中，`obj.v`只有取值器，沒有存值器，對它進行賦值就會報錯。

### 禁止擴充套件的物件不可擴充套件

嚴格模式下，對禁止擴充套件的物件新增新屬性，會報錯。

```javascript
'use strict';
var obj = {};
Object.preventExtensions(obj);
obj.v = 1;
// Uncaught TypeError: Cannot add property v, object is not extensible
```

上面程式碼中，`obj`物件禁止擴充套件，新增屬性就會報錯。

### eval、arguments 不可用作標識名

嚴格模式下，使用`eval`或者`arguments`作為標識名，將會報錯。下面的語句都會報錯。

```javascript
'use strict';
var eval = 17;
var arguments = 17;
var obj = { set p(arguments) { } };
try { } catch (arguments) { }
function x(eval) { }
function arguments() { }
var y = function eval() { };
var f = new Function('arguments', "'use strict'; return 17;");
// SyntaxError: Unexpected eval or arguments in strict mode
```

### 函式不能有重名的引數

正常模式下，如果函式有多個重名的引數，可以用`arguments[i]`讀取。嚴格模式下，這屬於語法錯誤。

```javascript
function f(a, a, b) {
  'use strict';
  return a + b;
}
// Uncaught SyntaxError: Duplicate parameter name not allowed in this context
```

### 禁止八進位制的字首0表示法

正常模式下，整數的第一位如果是`0`，表示這是八進位制數，比如`0100`等於十進位制的64。嚴格模式禁止這種表示法，整數第一位為`0`，將報錯。

```javascript
'use strict';
var n = 0100;
// Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```

## 增強的安全措施

嚴格模式增強了安全保護，從語法上防止了一些不小心會出現的錯誤。

### 全域性變數顯式宣告

正常模式中，如果一個變數沒有宣告就賦值，預設是全域性變數。嚴格模式禁止這種用法，全域性變數必須顯式宣告。

```javascript
'use strict';

v = 1; // 報錯，v未宣告

for (i = 0; i < 2; i++) { // 報錯，i 未宣告
  // ...
}

function f() {
  x = 123;
}
f() // 報錯，未宣告就建立一個全域性變數
```

因此，嚴格模式下，變數都必須先宣告，然後再使用。

### 禁止 this 關鍵字指向全域性物件

正常模式下，函式內部的`this`可能會指向全域性物件，嚴格模式禁止這種用法，避免無意間創造全域性變數。

```javascript
// 正常模式
function f() {
  console.log(this === window);
}
f() // true

// 嚴格模式
function f() {
  'use strict';
  console.log(this === undefined);
}
f() // true
```

上面程式碼中，嚴格模式的函式體內部`this`是`undefined`。

這種限制對於建構函式尤其有用。使用建構函式時，有時忘了加`new`，這時`this`不再指向全域性物件，而是報錯。

```javascript
function f() {
  'use strict';
  this.a = 1;
};

f();// 報錯，this 未定義
```

嚴格模式下，函式直接呼叫時（不使用`new`呼叫），函式內部的`this`表示`undefined`（未定義），因此可以用`call`、`apply`和`bind`方法，將任意值繫結在`this`上面。正常模式下，`this`指向全域性物件，如果繫結的值是非物件，將被自動轉為物件再繫結上去，而`null`和`undefined`這兩個無法轉成物件的值，將被忽略。

```javascript
// 正常模式
function fun() {
  return this;
}

fun() // window
fun.call(2) // Number {2}
fun.call(true) // Boolean {true}
fun.call(null) // window
fun.call(undefined) // window

// 嚴格模式
'use strict';
function fun() {
  return this;
}

fun() //undefined
fun.call(2) // 2
fun.call(true) // true
fun.call(null) // null
fun.call(undefined) // undefined
```

上面程式碼中，可以把任意型別的值，繫結在`this`上面。

### 禁止使用 fn.callee、fn.caller

函式內部不得使用`fn.caller`、`fn.arguments`，否則會報錯。這意味著不能在函式內部得到呼叫棧了。

```javascript
function f1() {
  'use strict';
  f1.caller;    // 報錯
  f1.arguments; // 報錯
}

f1();
```

### 禁止使用 arguments.callee、arguments.caller

`arguments.callee`和`arguments.caller`是兩個歷史遺留的變數，從來沒有標準化過，現在已經取消了。正常模式下呼叫它們沒有什麼作用，但是不會報錯。嚴格模式明確規定，函式內部使用`arguments.callee`、`arguments.caller`將會報錯。

```javascript
'use strict';
var f = function () {
  return arguments.callee;
};

f(); // 報錯
```

### 禁止刪除變數

嚴格模式下無法刪除變數，如果使用`delete`命令刪除一個變數，會報錯。只有物件的屬性，且屬性的描述物件的`configurable`屬性設定為`true`，才能被`delete`命令刪除。

```javascript
'use strict';
var x;
delete x; // 語法錯誤

var obj = Object.create(null, {
  x: {
    value: 1,
    configurable: true
  }
});
delete obj.x; // 刪除成功
```

## 靜態繫結

JavaScript 語言的一個特點，就是允許“動態繫結”，即某些屬性和方法到底屬於哪一個物件，不是在編譯時確定的，而是在執行時（runtime）確定的。

嚴格模式對動態繫結做了一些限制。某些情況下，只允許靜態繫結。也就是說，屬性和方法到底歸屬哪個物件，必須在編譯階段就確定。這樣做有利於編譯效率的提高，也使得程式碼更容易閱讀，更少出現意外。

具體來說，涉及以下幾個方面。

### 禁止使用 with 語句

嚴格模式下，使用`with`語句將報錯。因為`with`語句無法在編譯時就確定，某個屬性到底歸屬哪個物件，從而影響了編譯效果。

```javascript
'use strict';
var v  = 1;
var obj = {};

with (obj) {
  v = 2;
}
// Uncaught SyntaxError: Strict mode code may not include a with statement
```

### 創設 eval 作用域

正常模式下，JavaScript 語言有兩種變數作用域（scope）：全域性作用域和函式作用域。嚴格模式創設了第三種作用域：`eval`作用域。

正常模式下，`eval`語句的作用域，取決於它處於全域性作用域，還是函式作用域。嚴格模式下，`eval`語句本身就是一個作用域，不再能夠在其所執行的作用域創設新的變量了，也就是說，`eval`所生成的變數只能用於`eval`內部。

```javascript
(function () {
  'use strict';
  var x = 2;
  console.log(eval('var x = 5; x')) // 5
  console.log(x) // 2
})()
```

上面程式碼中，由於`eval`語句內部是一個獨立作用域，所以內部的變數`x`不會洩露到外部。

注意，如果希望`eval`語句也使用嚴格模式，有兩種方式。

```javascript
// 方式一
function f1(str){
  'use strict';
  return eval(str);
}
f1('undeclared_variable = 1'); // 報錯

// 方式二
function f2(str){
  return eval(str);
}
f2('"use strict";undeclared_variable = 1')  // 報錯
```

上面兩種寫法，`eval`內部使用的都是嚴格模式。

### arguments 不再追蹤引數的變化

變數`arguments`代表函式的引數。嚴格模式下，函式內部改變引數與`arguments`的聯絡被切斷了，兩者不再存在聯動關係。

```javascript
function f(a) {
  a = 2;
  return [a, arguments[0]];
}
f(1); // 正常模式為[2, 2]

function f(a) {
  'use strict';
  a = 2;
  return [a, arguments[0]];
}
f(1); // 嚴格模式為[2, 1]
```

上面程式碼中，改變函式的引數，不會反應到`arguments`物件上來。

## 向下一個版本的 JavaScript 過渡

JavaScript 語言的下一個版本是 ECMAScript 6，為了平穩過渡，嚴格模式引入了一些 ES6 語法。

### 非函式程式碼塊不得宣告函式

ES6 會引入塊級作用域。為了與新版本接軌，ES5 的嚴格模式只允許在全域性作用域或函式作用域宣告函式。也就是說，不允許在非函式的程式碼塊內宣告函式。

```javascript
'use strict';
if (true) {
  function f1() { } // 語法錯誤
}

for (var i = 0; i < 5; i++) {
  function f2() { } // 語法錯誤
}
```

上面程式碼在`if`程式碼塊和`for`程式碼塊中聲明瞭函式，ES5 環境會報錯。

注意，如果是 ES6 環境，上面的程式碼不會報錯，因為 ES6 允許在程式碼塊之中宣告函式。

### 保留字

為了向將來 JavaScript 的新版本過渡，嚴格模式新增了一些保留字（implements、interface、let、package、private、protected、public、static、yield等）。使用這些詞作為變數名將會報錯。

```javascript
function package(protected) { // 語法錯誤
  'use strict';
  var implements; // 語法錯誤
}
```

## 參考連結

- MDN, [Strict mode](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Functions_and_function_scope/Strict_mode)
- Dr. Axel Rauschmayer, [JavaScript: Why the hatred for strict mode?](http://www.2ality.com/2011/10/strict-mode-hatred.html)
- Dr. Axel Rauschmayer，[JavaScript’s strict mode: a summary](http://www.2ality.com/2011/01/javascripts-strict-mode-summary.html)
- Douglas Crockford, [Strict Mode Is Coming To Town](http://www.yuiblog.com/blog/2010/12/14/strict-mode-is-coming-to-town/)
- [JavaScript Strict Mode Support](http://java-script.limewebs.com/strictMode/test_hosted.html)
