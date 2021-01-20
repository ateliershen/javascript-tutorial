# 函式

函式是一段可以反覆呼叫的程式碼塊。函式還能接受輸入的引數，不同的引數會返回不同的值。

## 概述

### 函式的宣告

JavaScript 有三種宣告函式的方法。

**（1）function 命令**

`function`命令宣告的程式碼區塊，就是一個函式。`function`命令後面是函式名，函式名後面是一對圓括號，裡面是傳入函式的引數。函式體放在大括號裡面。

```javascript
function print(s) {
  console.log(s);
}
```

上面的程式碼命名了一個`print`函式，以後使用`print()`這種形式，就可以呼叫相應的程式碼。這叫做函式的宣告（Function Declaration）。

**（2）函式表示式**

除了用`function`命令宣告函式，還可以採用變數賦值的寫法。

```javascript
var print = function(s) {
  console.log(s);
};
```

這種寫法將一個匿名函式賦值給變數。這時，這個匿名函式又稱函式表示式（Function Expression），因為賦值語句的等號右側只能放表示式。

採用函式表示式宣告函式時，`function`命令後面不帶有函式名。如果加上函式名，該函式名只在函式體內部有效，在函式體外部無效。

```javascript
var print = function x(){
  console.log(typeof x);
};

x
// ReferenceError: x is not defined

print()
// function
```

上面程式碼在函式表示式中，加入了函式名`x`。這個`x`只在函式體內部可用，指代函式表示式本身，其他地方都不可用。這種寫法的用處有兩個，一是可以在函式體內部呼叫自身，二是方便除錯（除錯工具顯示函式呼叫棧時，將顯示函式名，而不再顯示這裡是一個匿名函式）。因此，下面的形式宣告函式也非常常見。

```javascript
var f = function f() {};
```

需要注意的是，函式的表示式需要在語句的結尾加上分號，表示語句結束。而函式的宣告在結尾的大括號後面不用加分號。總的來說，這兩種宣告函式的方式，差別很細微，可以近似認為是等價的。

**（3）Function 建構函式**

第三種宣告函式的方式是`Function`建構函式。

```javascript
var add = new Function(
  'x',
  'y',
  'return x + y'
);

// 等同於
function add(x, y) {
  return x + y;
}
```

上面程式碼中，`Function`建構函式接受三個引數，除了最後一個引數是`add`函式的“函式體”，其他引數都是`add`函式的引數。

你可以傳遞任意數量的引數給`Function`建構函式，只有最後一個引數會被當做函式體，如果只有一個引數，該引數就是函式體。

```javascript
var foo = new Function(
  'return "hello world";'
);

// 等同於
function foo() {
  return 'hello world';
}
```

`Function`建構函式可以不使用`new`命令，返回結果完全一樣。

總的來說，這種宣告函式的方式非常不直觀，幾乎無人使用。

### 函式的重複宣告

如果同一個函式被多次宣告，後面的宣告就會覆蓋前面的宣告。

```javascript
function f() {
  console.log(1);
}
f() // 2

function f() {
  console.log(2);
}
f() // 2
```

上面程式碼中，後一次的函式宣告覆蓋了前面一次。而且，由於函式名的提升（參見下文），前一次宣告在任何時候都是無效的，這一點要特別注意。

### 圓括號運算子，return 語句和遞迴

呼叫函式時，要使用圓括號運算子。圓括號之中，可以加入函式的引數。

```javascript
function add(x, y) {
  return x + y;
}

add(1, 1) // 2
```

上面程式碼中，函式名後面緊跟一對圓括號，就會呼叫這個函式。

函式體內部的`return`語句，表示返回。JavaScript 引擎遇到`return`語句，就直接返回`return`後面的那個表示式的值，後面即使還有語句，也不會得到執行。也就是說，`return`語句所帶的那個表示式，就是函式的返回值。`return`語句不是必需的，如果沒有的話，該函式就不返回任何值，或者說返回`undefined`。

函式可以呼叫自身，這就是遞迴（recursion）。下面就是透過遞迴，計算斐波那契數列的程式碼。

```javascript
function fib(num) {
  if (num === 0) return 0;
  if (num === 1) return 1;
  return fib(num - 2) + fib(num - 1);
}

fib(6) // 8
```

上面程式碼中，`fib`函式內部又呼叫了`fib`，計算得到斐波那契數列的第6個元素是8。

### 第一等公民

JavaScript 語言將函式看作一種值，與其它值（數值、字串、布林值等等）地位相同。凡是可以使用值的地方，就能使用函式。比如，可以把函式賦值給變數和物件的屬性，也可以當作引數傳入其他函式，或者作為函式的結果返回。函式只是一個可以執行的值，此外並無特殊之處。

由於函式與其他資料型別地位平等，所以在 JavaScript 語言中又稱函式為第一等公民。

```javascript
function add(x, y) {
  return x + y;
}

// 將函式賦值給一個變數
var operator = add;

// 將函式作為引數和返回值
function a(op){
  return op;
}
a(add)(1, 1)
// 2
```

### 函式名的提升

JavaScript 引擎將函式名視同變數名，所以採用`function`命令宣告函式時，整個函式會像變數宣告一樣，被提升到程式碼頭部。所以，下面的程式碼不會報錯。

```javascript
f();

function f() {}
```

表面上，上面程式碼好像在宣告之前就呼叫了函式`f`。但是實際上，由於“變數提升”，函式`f`被提升到了程式碼頭部，也就是在呼叫之前已經聲明瞭。但是，如果採用賦值語句定義函式，JavaScript 就會報錯。

```javascript
f();
var f = function (){};
// TypeError: undefined is not a function
```

上面的程式碼等同於下面的形式。

```javascript
var f;
f();
f = function () {};
```

上面程式碼第二行，呼叫`f`的時候，`f`只是被聲明瞭，還沒有被賦值，等於`undefined`，所以會報錯。

注意，如果像下面例子那樣，採用`function`命令和`var`賦值語句宣告同一個函式，由於存在函式提升，最後會採用`var`賦值語句的定義。

```javascript
var f = function () {
  console.log('1');
}

function f() {
  console.log('2');
}

f() // 1
```

上面例子中，表面上後面宣告的函式`f`，應該覆蓋前面的`var`賦值語句，但是由於存在函式提升，實際上正好反過來。

## 函式的屬性和方法

### name 屬性

函式的`name`屬性返回函式的名字。

```javascript
function f1() {}
f1.name // "f1"
```

如果是透過變數賦值定義的函式，那麼`name`屬性返回變數名。

```javascript
var f2 = function () {};
f2.name // "f2"
```

但是，上面這種情況，只有在變數的值是一個匿名函式時才是如此。如果變數的值是一個具名函式，那麼`name`屬性返回`function`關鍵字之後的那個函式名。

```javascript
var f3 = function myName() {};
f3.name // 'myName'
```

上面程式碼中，`f3.name`返回函式表示式的名字。注意，真正的函式名還是`f3`，而`myName`這個名字只在函式體內部可用。

`name`屬性的一個用處，就是獲取引數函式的名字。

```javascript
var myFunc = function () {};

function test(f) {
  console.log(f.name);
}

test(myFunc) // myFunc
```

上面程式碼中，函式`test`內部透過`name`屬性，就可以知道傳入的引數是什麼函式。

### length 屬性

函式的`length`屬性返回函式預期傳入的引數個數，即函式定義之中的引數個數。

```javascript
function f(a, b) {}
f.length // 2
```

上面程式碼定義了空函式`f`，它的`length`屬性就是定義時的引數個數。不管呼叫時輸入了多少個引數，`length`屬性始終等於2。

`length`屬性提供了一種機制，判斷定義時和呼叫時引數的差異，以便實現面向物件程式設計的“方法過載”（overload）。

### toString()

函式的`toString()`方法返回一個字串，內容是函式的原始碼。

```javascript
function f() {
  a();
  b();
  c();
}

f.toString()
// function f() {
//  a();
//  b();
//  c();
// }
```

上面示例中，函式`f`的`toString()`方法返回了`f`的原始碼，包含換行符在內。

對於那些原生的函式，`toString()`方法返回`function (){[native code]}`。

```javascript
Math.sqrt.toString()
// "function sqrt() { [native code] }"
```

上面程式碼中，`Math.sqrt()`是 JavaScript 引擎提供的原生函式，`toString()`方法就返回原生程式碼的提示。

函式內部的註釋也可以返回。

```javascript
function f() {/*
  這是一個
  多行註釋
*/}

f.toString()
// "function f(){/*
//   這是一個
//   多行註釋
// */}"
```

利用這一點，可以變相實現多行字串。

```javascript
var multiline = function (fn) {
  var arr = fn.toString().split('\n');
  return arr.slice(1, arr.length - 1).join('\n');
};

function f() {/*
  這是一個
  多行註釋
*/}

multiline(f);
// " 這是一個
//   多行註釋"
```

上面示例中，函式`f`內部有一個多行註釋，`toString()`方法拿到`f`的原始碼後，去掉首尾兩行，就得到了一個多行字串。

## 函式作用域

### 定義

作用域（scope）指的是變數存在的範圍。在 ES5 的規範中，JavaScript 只有兩種作用域：一種是全域性作用域，變數在整個程式中一直存在，所有地方都可以讀取；另一種是函式作用域，變數只在函式內部存在。ES6 又新增了塊級作用域，本教程不涉及。

對於頂層函式來說，函式外部宣告的變數就是全域性變數（global variable），它可以在函式內部讀取。

```javascript
var v = 1;

function f() {
  console.log(v);
}

f()
// 1
```

上面的程式碼表明，函式`f`內部可以讀取全域性變數`v`。

在函式內部定義的變數，外部無法讀取，稱為“區域性變數”（local variable）。

```javascript
function f(){
  var v = 1;
}

v // ReferenceError: v is not defined
```

上面程式碼中，變數`v`在函式內部定義，所以是一個區域性變數，函式之外就無法讀取。

函式內部定義的變數，會在該作用域內覆蓋同名全域性變數。

```javascript
var v = 1;

function f(){
  var v = 2;
  console.log(v);
}

f() // 2
v // 1
```

上面程式碼中，變數`v`同時在函式的外部和內部有定義。結果，在函式內部定義，區域性變數`v`覆蓋了全域性變數`v`。

注意，對於`var`命令來說，區域性變數只能在函式內部宣告，在其他區塊中宣告，一律都是全域性變數。

```javascript
if (true) {
  var x = 5;
}
console.log(x);  // 5
```

上面程式碼中，變數`x`在條件判斷區塊之中宣告，結果就是一個全域性變數，可以在區塊之外讀取。

### 函式內部的變數提升

與全域性作用域一樣，函式作用域內部也會產生“變數提升”現象。`var`命令宣告的變數，不管在什麼位置，變數宣告都會被提升到函式體的頭部。

```javascript
function foo(x) {
  if (x > 100) {
    var tmp = x - 100;
  }
}

// 等同於
function foo(x) {
  var tmp;
  if (x > 100) {
    tmp = x - 100;
  };
}
```

### 函式本身的作用域

函式本身也是一個值，也有自己的作用域。它的作用域與變數一樣，就是其宣告時所在的作用域，與其執行時所在的作用域無關。

```javascript
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f() // 1
```

上面程式碼中，函式`x`是在函式`f`的外部宣告的，所以它的作用域繫結外層，內部變數`a`不會到函式`f`體內取值，所以輸出`1`，而不是`2`。

總之，函式執行時所在的作用域，是定義時的作用域，而不是呼叫時所在的作用域。

很容易犯錯的一點是，如果函式`A`呼叫函式`B`，卻沒考慮到函式`B`不會引用函式`A`的內部變數。

```javascript
var x = function () {
  console.log(a);
};

function y(f) {
  var a = 2;
  f();
}

y(x)
// ReferenceError: a is not defined
```

上面程式碼將函式`x`作為引數，傳入函式`y`。但是，函式`x`是在函式`y`體外宣告的，作用域繫結外層，因此找不到函式`y`的內部變數`a`，導致報錯。

同樣的，函式體內部宣告的函式，作用域繫結函式體內部。

```javascript
function foo() {
  var x = 1;
  function bar() {
    console.log(x);
  }
  return bar;
}

var x = 2;
var f = foo();
f() // 1
```

上面程式碼中，函式`foo`內部聲明瞭一個函式`bar`，`bar`的作用域繫結`foo`。當我們在`foo`外部取出`bar`執行時，變數`x`指向的是`foo`內部的`x`，而不是`foo`外部的`x`。正是這種機制，構成了下文要講解的“閉包”現象。

## 引數

### 概述

函式執行的時候，有時需要提供外部資料，不同的外部資料會得到不同的結果，這種外部資料就叫引數。

```javascript
function square(x) {
  return x * x;
}

square(2) // 4
square(3) // 9
```

上式的`x`就是`square`函式的引數。每次執行的時候，需要提供這個值，否則得不到結果。

### 引數的省略

函式引數不是必需的，JavaScript 允許省略引數。

```javascript
function f(a, b) {
  return a;
}

f(1, 2, 3) // 1
f(1) // 1
f() // undefined

f.length // 2
```

上面程式碼的函式`f`定義了兩個引數，但是執行時無論提供多少個引數（或者不提供引數），JavaScript 都不會報錯。省略的引數的值就變為`undefined`。需要注意的是，函式的`length`屬性與實際傳入的引數個數無關，只反映函式預期傳入的引數個數。

但是，沒有辦法只省略靠前的引數，而保留靠後的引數。如果一定要省略靠前的引數，只有顯式傳入`undefined`。

```javascript
function f(a, b) {
  return a;
}

f( , 1) // SyntaxError: Unexpected token ,(…)
f(undefined, 1) // undefined
```

上面程式碼中，如果省略第一個引數，就會報錯。

### 傳遞方式

函式引數如果是原始型別的值（數值、字串、布林值），傳遞方式是傳值傳遞（passes by value）。這意味著，在函式體內修改引數值，不會影響到函式外部。

```javascript
var p = 2;

function f(p) {
  p = 3;
}
f(p);

p // 2
```

上面程式碼中，變數`p`是一個原始型別的值，傳入函式`f`的方式是傳值傳遞。因此，在函式內部，`p`的值是原始值的複製，無論怎麼修改，都不會影響到原始值。

但是，如果函式引數是複合型別的值（陣列、物件、其他函式），傳遞方式是傳址傳遞（pass by reference）。也就是說，傳入函式的原始值的地址，因此在函式內部修改引數，將會影響到原始值。

```javascript
var obj = { p: 1 };

function f(o) {
  o.p = 2;
}
f(obj);

obj.p // 2
```

上面程式碼中，傳入函式`f`的是引數物件`obj`的地址。因此，在函式內部修改`obj`的屬性`p`，會影響到原始值。

注意，如果函式內部修改的，不是引數物件的某個屬性，而是替換掉整個引數，這時不會影響到原始值。

```javascript
var obj = [1, 2, 3];

function f(o) {
  o = [2, 3, 4];
}
f(obj);

obj // [1, 2, 3]
```

上面程式碼中，在函式`f()`內部，引數物件`obj`被整個替換成另一個值。這時不會影響到原始值。這是因為，形式引數（`o`）的值實際是引數`obj`的地址，重新對`o`賦值導致`o`指向另一個地址，儲存在原地址上的值當然不受影響。

### 同名引數

如果有同名的引數，則取最後出現的那個值。

```javascript
function f(a, a) {
  console.log(a);
}

f(1, 2) // 2
```

上面程式碼中，函式`f()`有兩個引數，且引數名都是`a`。取值的時候，以後面的`a`為準，即使後面的`a`沒有值或被省略，也是以其為準。

```javascript
function f(a, a) {
  console.log(a);
}

f(1) // undefined
```

呼叫函式`f()`的時候，沒有提供第二個引數，`a`的取值就變成了`undefined`。這時，如果要獲得第一個`a`的值，可以使用`arguments`物件。

```javascript
function f(a, a) {
  console.log(arguments[0]);
}

f(1) // 1
```

### arguments 物件

**（1）定義**

由於 JavaScript 允許函式有不定數目的引數，所以需要一種機制，可以在函式體內部讀取所有引數。這就是`arguments`物件的由來。

`arguments`物件包含了函式執行時的所有引數，`arguments[0]`就是第一個引數，`arguments[1]`就是第二個引數，以此類推。這個物件只有在函式體內部，才可以使用。

```javascript
var f = function (one) {
  console.log(arguments[0]);
  console.log(arguments[1]);
  console.log(arguments[2]);
}

f(1, 2, 3)
// 1
// 2
// 3
```

正常模式下，`arguments`物件可以在執行時修改。

```javascript
var f = function(a, b) {
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 5
```

上面程式碼中，函式`f()`呼叫時傳入的引數，在函式內部被修改成`3`和`2`。

嚴格模式下，`arguments`物件與函式引數不具有聯動關係。也就是說，修改`arguments`物件不會影響到實際的函式引數。

```javascript
var f = function(a, b) {
  'use strict'; // 開啟嚴格模式
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 2
```

上面程式碼中，函式體內是嚴格模式，這時修改`arguments`物件，不會影響到真實引數`a`和`b`。

透過`arguments`物件的`length`屬性，可以判斷函式呼叫時到底帶幾個引數。

```javascript
function f() {
  return arguments.length;
}

f(1, 2, 3) // 3
f(1) // 1
f() // 0
```

**（2）與陣列的關係**

需要注意的是，雖然`arguments`很像陣列，但它是一個物件。陣列專有的方法（比如`slice`和`forEach`），不能在`arguments`物件上直接使用。

如果要讓`arguments`物件使用陣列方法，真正的解決方法是將`arguments`轉為真正的陣列。下面是兩種常用的轉換方法：`slice`方法和逐一填入新陣列。

```javascript
var args = Array.prototype.slice.call(arguments);

// 或者
var args = [];
for (var i = 0; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```

**（3）callee 屬性**

`arguments`物件帶有一個`callee`屬性，返回它所對應的原函式。

```javascript
var f = function () {
  console.log(arguments.callee === f);
}

f() // true
```

可以透過`arguments.callee`，達到呼叫函式自身的目的。這個屬性在嚴格模式裡面是禁用的，因此不建議使用。

## 函式的其他知識點

### 閉包

閉包（closure）是 JavaScript 語言的一個難點，也是它的特色，很多高階應用都要依靠閉包實現。

理解閉包，首先必須理解變數作用域。前面提到，JavaScript 有兩種作用域：全域性作用域和函式作用域。函式內部可以直接讀取全域性變數。

```javascript
var n = 999;

function f1() {
  console.log(n);
}
f1() // 999
```

上面程式碼中，函式`f1`可以讀取全域性變數`n`。

但是，正常情況下，函式外部無法讀取函式內部宣告的變數。

```javascript
function f1() {
  var n = 999;
}

console.log(n)
// Uncaught ReferenceError: n is not defined(
```

上面程式碼中，函式`f1`內部宣告的變數`n`，函式外是無法讀取的。

如果出於種種原因，需要得到函式內的區域性變數。正常情況下，這是辦不到的，只有透過變通方法才能實現。那就是在函式的內部，再定義一個函式。

```javascript
function f1() {
  var n = 999;
  function f2() {
　　console.log(n); // 999
  }
}
```

上面程式碼中，函式`f2`就在函式`f1`內部，這時`f1`內部的所有區域性變數，對`f2`都是可見的。但是反過來就不行，`f2`內部的區域性變數，對`f1`就是不可見的。這就是 JavaScript 語言特有的"鏈式作用域"結構（chain scope），子物件會一級一級地向上尋找所有父物件的變數。所以，父物件的所有變數，對子物件都是可見的，反之則不成立。

既然`f2`可以讀取`f1`的區域性變數，那麼只要把`f2`作為返回值，我們不就可以在`f1`外部讀取它的內部變量了嗎！

```javascript
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

上面程式碼中，函式`f1`的返回值就是函式`f2`，由於`f2`可以讀取`f1`的內部變數，所以就可以在外部獲得`f1`的內部變量了。

閉包就是函式`f2`，即能夠讀取其他函式內部變數的函式。由於在 JavaScript 語言中，只有函式內部的子函式才能讀取內部變數，因此可以把閉包簡單理解成“定義在一個函式內部的函式”。閉包最大的特點，就是它可以“記住”誕生的環境，比如`f2`記住了它誕生的環境`f1`，所以從`f2`可以得到`f1`的內部變數。在本質上，閉包就是將函式內部和函式外部連線起來的一座橋樑。

閉包的最大用處有兩個，一個是可以讀取外層函式內部的變數，另一個就是讓這些變數始終保持在記憶體中，即閉包可以使得它誕生環境一直存在。請看下面的例子，閉包使得內部變數記住上一次呼叫時的運算結果。

```javascript
function createIncrementor(start) {
  return function () {
    return start++;
  };
}

var inc = createIncrementor(5);

inc() // 5
inc() // 6
inc() // 7
```

上面程式碼中，`start`是函式`createIncrementor`的內部變數。透過閉包，`start`的狀態被保留了，每一次呼叫都是在上一次呼叫的基礎上進行計算。從中可以看到，閉包`inc`使得函式`createIncrementor`的內部環境，一直存在。所以，閉包可以看作是函式內部作用域的一個介面。

為什麼閉包能夠返回外層函式的內部變數？原因是閉包（上例的`inc`）用到了外層變數（`start`），導致外層函式（`createIncrementor`）不能從記憶體釋放。只要閉包沒有被垃圾回收機制清除，外層函式提供的執行環境也不會被清除，它的內部變數就始終儲存著當前值，供閉包讀取。

閉包的另一個用處，是封裝物件的私有屬性和私有方法。

```javascript
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge
  };
}

var p1 = Person('張三');
p1.setAge(25);
p1.getAge() // 25
```

上面程式碼中，函式`Person`的內部變數`_age`，透過閉包`getAge`和`setAge`，變成了返回物件`p1`的私有變數。

注意，外層函式每次執行，都會生成一個新的閉包，而這個閉包又會保留外層函式的內部變數，所以記憶體消耗很大。因此不能濫用閉包，否則會造成網頁的效能問題。

### 立即呼叫的函式表示式（IIFE）

根據 JavaScript 的語法，圓括號`()`跟在函式名之後，表示呼叫該函式。比如，`print()`就表示呼叫`print`函式。

有時，我們需要在定義函式之後，立即呼叫該函式。這時，你不能在函式的定義之後加上圓括號，這會產生語法錯誤。

```javascript
function(){ /* code */ }();
// SyntaxError: Unexpected token (
```

產生這個錯誤的原因是，`function`這個關鍵字即可以當作語句，也可以當作表示式。

```javascript
// 語句
function f() {}

// 表示式
var f = function f() {}
```

當作表示式時，函式可以定義後直接加圓括號呼叫。

```javascript
var f = function f(){ return 1}();
f // 1
```

上面的程式碼中，函式定義後直接加圓括號呼叫，沒有報錯。原因就是`function`作為表示式，引擎就把函式定義當作一個值。這種情況下，就不會報錯。

為了避免解析的歧義，JavaScript 規定，如果`function`關鍵字出現在行首，一律解釋成語句。因此，引擎看到行首是`function`關鍵字之後，認為這一段都是函式的定義，不應該以圓括號結尾，所以就報錯了。

函式定義後立即呼叫的解決方法，就是不要讓`function`出現在行首，讓引擎將其理解成一個表示式。最簡單的處理，就是將其放在一個圓括號裡面。

```javascript
(function(){ /* code */ }());
// 或者
(function(){ /* code */ })();
```

上面兩種寫法都是以圓括號開頭，引擎就會認為後面跟的是一個表示式，而不是函式定義語句，所以就避免了錯誤。這就叫做“立即呼叫的函式表示式”（Immediately-Invoked Function Expression），簡稱 IIFE。

注意，上面兩種寫法最後的分號都是必須的。如果省略分號，遇到連著兩個 IIFE，可能就會報錯。

```javascript
// 報錯
(function(){ /* code */ }())
(function(){ /* code */ }())
```

上面程式碼的兩行之間沒有分號，JavaScript 會將它們連在一起解釋，將第二行解釋為第一行的引數。

推而廣之，任何讓直譯器以表示式來處理函式定義的方法，都能產生同樣的效果，比如下面三種寫法。

```javascript
var i = function(){ return 10; }();
true && function(){ /* code */ }();
0, function(){ /* code */ }();
```

甚至像下面這樣寫，也是可以的。

```javascript
!function () { /* code */ }();
~function () { /* code */ }();
-function () { /* code */ }();
+function () { /* code */ }();
```

通常情況下，只對匿名函式使用這種“立即執行的函式表示式”。它的目的有兩個：一是不必為函式命名，避免了汙染全域性變數；二是 IIFE 內部形成了一個單獨的作用域，可以封裝一些外部無法讀取的私有變數。

```javascript
// 寫法一
var tmp = newData;
processData(tmp);
storeData(tmp);

// 寫法二
(function () {
  var tmp = newData;
  processData(tmp);
  storeData(tmp);
}());
```

上面程式碼中，寫法二比寫法一更好，因為完全避免了汙染全域性變數。

## eval 命令

### 基本用法

`eval`命令接受一個字串作為引數，並將這個字串當作語句執行。

```javascript
eval('var a = 1;');
a // 1
```

上面程式碼將字串當作語句執行，生成了變數`a`。

如果引數字串無法當作語句執行，那麼就會報錯。

```javascript
eval('3x') // Uncaught SyntaxError: Invalid or unexpected token
```

放在`eval`中的字串，應該有獨自存在的意義，不能用來與`eval`以外的命令配合使用。舉例來說，下面的程式碼將會報錯。

```javascript
eval('return;'); // Uncaught SyntaxError: Illegal return statement
```

上面程式碼會報錯，因為`return`不能單獨使用，必須在函式中使用。

如果`eval`的引數不是字串，那麼會原樣返回。

```javascript
eval(123) // 123
```

`eval`沒有自己的作用域，都在當前作用域內執行，因此可能會修改當前作用域的變數的值，造成安全問題。

```javascript
var a = 1;
eval('a = 2');

a // 2
```

上面程式碼中，`eval`命令修改了外部變數`a`的值。由於這個原因，`eval`有安全風險。

為了防止這種風險，JavaScript 規定，如果使用嚴格模式，`eval`內部宣告的變數，不會影響到外部作用域。

```javascript
(function f() {
  'use strict';
  eval('var foo = 123');
  console.log(foo);  // ReferenceError: foo is not defined
})()
```

上面程式碼中，函式`f`內部是嚴格模式，這時`eval`內部宣告的`foo`變數，就不會影響到外部。

不過，即使在嚴格模式下，`eval`依然可以讀寫當前作用域的變數。

```javascript
(function f() {
  'use strict';
  var foo = 1;
  eval('foo = 2');
  console.log(foo);  // 2
})()
```

上面程式碼中，嚴格模式下，`eval`內部還是改寫了外部變數，可見安全風險依然存在。

總之，`eval`的本質是在當前作用域之中，注入程式碼。由於安全風險和不利於 JavaScript 引擎最佳化執行速度，所以一般不推薦使用。通常情況下，`eval`最常見的場合是解析 JSON 資料的字串，不過正確的做法應該是使用原生的`JSON.parse`方法。

### eval 的別名呼叫

前面說過`eval`不利於引擎最佳化執行速度。更麻煩的是，還有下面這種情況，引擎在靜態程式碼分析的階段，根本無法分辨執行的是`eval`。

```javascript
var m = eval;
m('var x = 1');
x // 1
```

上面程式碼中，變數`m`是`eval`的別名。靜態程式碼分析階段，引擎分辨不出`m('var x = 1')`執行的是`eval`命令。

為了保證`eval`的別名不影響程式碼最佳化，JavaScript 的標準規定，凡是使用別名執行`eval`，`eval`內部一律是全域性作用域。

```javascript
var a = 1;

function f() {
  var a = 2;
  var e = eval;
  e('console.log(a)');
}

f() // 1
```

上面程式碼中，`eval`是別名呼叫，所以即使它是在函式中，它的作用域還是全域性作用域，因此輸出的`a`為全域性變數。這樣的話，引擎就能確認`e()`不會對當前的函式作用域產生影響，最佳化的時候就可以把這一行排除掉。

`eval`的別名呼叫的形式五花八門，只要不是直接呼叫，都屬於別名呼叫，因為引擎只能分辨`eval()`這一種形式是直接呼叫。

```javascript
eval.call(null, '...')
window.eval('...')
(1, eval)('...')
(eval, eval)('...')
```

上面這些形式都是`eval`的別名呼叫，作用域都是全域性作用域。

## 參考連結

- Ben Alman, [Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
- Mark Daggett, [Functions Explained](http://markdaggett.com/blog/2013/02/15/functions-explained/)
- Juriy Zaytsev, [Named function expressions demystified](http://kangax.github.com/nfe/)
- Marco Rogers polotek, [What is the arguments object?](http://docs.nodejitsu.com/articles/javascript-conventions/what-is-the-arguments-object)
- Juriy Zaytsev, [Global eval. What are the options?](http://perfectionkills.com/global-eval-what-are-the-options/)
- Axel Rauschmayer, [Evaluating JavaScript code via eval() and new Function()](http://www.2ality.com/2014/01/eval.html)
