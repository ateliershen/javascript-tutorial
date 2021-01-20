# JavaScript 的基本語法

## 語句

JavaScript 程式的執行單位為行（line），也就是一行一行地執行。一般情況下，每一行就是一個語句。

語句（statement）是為了完成某種任務而進行的操作，比如下面就是一行賦值語句。

```javascript
var a = 1 + 3;
```

這條語句先用`var`命令，聲明瞭變數`a`，然後將`1 + 3`的運算結果賦值給變數`a`。

`1 + 3`叫做表示式（expression），指一個為了得到返回值的計算式。語句和表示式的區別在於，前者主要為了進行某種操作，一般情況下不需要返回值；後者則是為了得到返回值，一定會返回一個值。凡是 JavaScript 語言中預期為值的地方，都可以使用表示式。比如，賦值語句的等號右邊，預期是一個值，因此可以放置各種表示式。

語句以分號結尾，一個分號就表示一個語句結束。多個語句可以寫在一行內。

```javascript
var a = 1 + 3 ; var b = 'abc';
```

分號前面可以沒有任何內容，JavaScript 引擎將其視為空語句。

```javascript
;;;
```

上面的程式碼就表示3個空語句。

表示式不需要分號結尾。一旦在表示式後面新增分號，則 JavaScript 引擎就將表示式視為語句，這樣會產生一些沒有任何意義的語句。

```javascript
1 + 3;
'abc';
```

上面兩行語句只是單純地產生一個值，並沒有任何實際的意義。

## 變數

### 概念

變數是對“值”的具名引用。變數就是為“值”起名，然後引用這個名字，就等同於引用這個值。變數的名字就是變數名。

```javascript
var a = 1;
```

上面的程式碼先宣告變數`a`，然後在變數`a`與數值1之間建立引用關係，稱為將數值1“賦值”給變數`a`。以後，引用變數名`a`就會得到數值1。最前面的`var`，是變數宣告命令。它表示通知解釋引擎，要建立一個變數`a`。

注意，JavaScript 的變數名區分大小寫，`A`和`a`是兩個不同的變數。

變數的宣告和賦值，是分開的兩個步驟，上面的程式碼將它們合在了一起，實際的步驟是下面這樣。

```javascript
var a;
a = 1;
```

如果只是宣告變數而沒有賦值，則該變數的值是`undefined`。`undefined`是一個特殊的值，表示“無定義”。

```javascript
var a;
a // undefined
```

如果變數賦值的時候，忘了寫`var`命令，這條語句也是有效的。

```javascript
var a = 1;
// 基本等同
a = 1;
```

但是，不寫`var`的做法，不利於表達意圖，而且容易不知不覺地建立全域性變數，所以建議總是使用`var`命令宣告變數。

如果一個變數沒有宣告就直接使用，JavaScript 會報錯，告訴你變數未定義。

```javascript
x
// ReferenceError: x is not defined
```

上面程式碼直接使用變數`x`，系統就報錯，告訴你變數`x`沒有宣告。

可以在同一條`var`命令中宣告多個變數。

```javascript
var a, b;
```

JavaScript 是一種動態型別語言，也就是說，變數的型別沒有限制，變數可以隨時更改型別。

```javascript
var a = 1;
a = 'hello';
```

上面程式碼中，變數`a`起先被賦值為一個數值，後來又被重新賦值為一個字串。第二次賦值的時候，因為變數`a`已經存在，所以不需要使用`var`命令。

如果使用`var`重新宣告一個已經存在的變數，是無效的。

```javascript
var x = 1;
var x;
x // 1
```

上面程式碼中，變數`x`聲明瞭兩次，第二次宣告是無效的。

但是，如果第二次宣告的時候還進行了賦值，則會覆蓋掉前面的值。

```javascript
var x = 1;
var x = 2;

// 等同於

var x = 1;
var x;
x = 2;
```

### 變數提升

JavaScript 引擎的工作方式是，先解析程式碼，獲取所有被宣告的變數，然後再一行一行地執行。這造成的結果，就是所有的變數的宣告語句，都會被提升到程式碼的頭部，這就叫做變數提升（hoisting）。

```javascript
console.log(a);
var a = 1;
```

上面程式碼首先使用`console.log`方法，在控制檯（console）顯示變數`a`的值。這時變數`a`還沒有宣告和賦值，所以這是一種錯誤的做法，但是實際上不會報錯。因為存在變數提升，真正執行的是下面的程式碼。

```javascript
var a;
console.log(a);
a = 1;
```

最後的結果是顯示`undefined`，表示變數`a`已宣告，但還未賦值。

## 識別符號

識別符號（identifier）指的是用來識別各種值的合法名稱。最常見的識別符號就是變數名，以及後面要提到的函式名。JavaScript 語言的識別符號對大小寫敏感，所以`a`和`A`是兩個不同的識別符號。

識別符號有一套命名規則，不符合規則的就是非法識別符號。JavaScript 引擎遇到非法識別符號，就會報錯。

簡單說，識別符號命名規則如下。

- 第一個字元，可以是任意 Unicode 字母（包括英文字母和其他語言的字母），以及美元符號（`$`）和下劃線（`_`）。
- 第二個字元及後面的字元，除了 Unicode 字母、美元符號和下劃線，還可以用數字`0-9`。

下面這些都是合法的識別符號。

```javascript
arg0
_tmp
$elem
π
```

下面這些則是不合法的識別符號。

```javascript
1a  // 第一個字元不能是數字
23  // 同上
***  // 識別符號不能包含星號
a+b  // 識別符號不能包含加號
-d  // 識別符號不能包含減號或連詞線
```

中文是合法的識別符號，可以用作變數名。

```javascript
var 臨時變數 = 1;
```

> JavaScript 有一些保留字，不能用作識別符號：arguments、break、case、catch、class、const、continue、debugger、default、delete、do、else、enum、eval、export、extends、false、finally、for、function、if、implements、import、in、instanceof、interface、let、new、null、package、private、protected、public、return、static、super、switch、this、throw、true、try、typeof、var、void、while、with、yield。

## 註釋

原始碼中被 JavaScript 引擎忽略的部分就叫做註釋，它的作用是對程式碼進行解釋。JavaScript 提供兩種註釋的寫法：一種是單行註釋，用`//`起頭；另一種是多行註釋，放在`/*`和`*/`之間。

```javascript
// 這是單行註釋

/*
 這是
 多行
 註釋
*/
```

此外，由於歷史上 JavaScript 可以相容 HTML 程式碼的註釋，所以`<!--`和`-->`也被視為合法的單行註釋。

```javascript
x = 1; <!-- x = 2;
--> x = 3;
```

上面程式碼中，只有`x = 1`會執行，其他的部分都被註釋掉了。

需要注意的是，`-->`只有在行首，才會被當成單行註釋，否則會當作正常的運算。

```javascript
function countdown(n) {
  while (n --> 0) console.log(n);
}
countdown(3)
// 2
// 1
// 0
```

上面程式碼中，`n --> 0`實際上會當作`n-- > 0`，因此輸出2、1、0。

## 區塊

JavaScript 使用大括號，將多個相關的語句組合在一起，稱為“區塊”（block）。

對於`var`命令來說，JavaScript 的區塊不構成單獨的作用域（scope）。

```javascript
{
  var a = 1;
}

a // 1
```

上面程式碼在區塊內部，使用`var`命令宣告並賦值了變數`a`，然後在區塊外部，變數`a`依然有效，區塊對於`var`命令不構成單獨的作用域，與不使用區塊的情況沒有任何區別。在 JavaScript 語言中，單獨使用區塊並不常見，區塊往往用來構成其他更復雜的語法結構，比如`for`、`if`、`while`、`function`等。

## 條件語句

JavaScript 提供`if`結構和`switch`結構，完成條件判斷，即只有滿足預設的條件，才會執行相應的語句。

### if 結構

`if`結構先判斷一個表示式的布林值，然後根據布林值的真偽，執行不同的語句。所謂布林值，指的是 JavaScript 的兩個特殊值，`true`表示真，`false`表示`偽`。

```javascript
if (布林值)
  語句;

// 或者
if (布林值) 語句;
```

上面是`if`結構的基本形式。需要注意的是，“布林值”往往由一個條件表示式產生的，必須放在圓括號中，表示對錶達式求值。如果表示式的求值結果為`true`，就執行緊跟在後面的語句；如果結果為`false`，則跳過緊跟在後面的語句。

```javascript
if (m === 3)
  m = m + 1;
```

上面程式碼表示，只有在`m`等於3時，才會將其值加上1。

這種寫法要求條件表示式後面只能有一個語句。如果想執行多個語句，必須在`if`的條件判斷之後，加上大括號，表示程式碼塊（多個語句合併成一個語句）。

```javascript
if (m === 3) {
  m += 1;
}
```

建議總是在`if`語句中使用大括號，因為這樣方便插入語句。

注意，`if`後面的表示式之中，不要混淆賦值表示式（`=`）、嚴格相等運算子（`===`）和相等運算子（`==`）。尤其是賦值表示式不具有比較作用。

```javascript
var x = 1;
var y = 2;
if (x = y) {
  console.log(x);
}
// "2"
```

上面程式碼的原意是，當`x`等於`y`的時候，才執行相關語句。但是，不小心將嚴格相等運算子寫成賦值表示式，結果變成了將`y`賦值給變數`x`，再判斷變數`x`的值（等於2）的布林值（結果為`true`）。

這種錯誤可以正常生成一個布林值，因而不會報錯。為了避免這種情況，有些開發者習慣將常量寫在運算子的左邊，這樣的話，一旦不小心將相等運算子寫成賦值運算子，就會報錯，因為常量不能被賦值。

```javascript
if (x = 2) { // 不報錯
if (2 = x) { // 報錯
```

至於為什麼優先採用“嚴格相等運算子”（`===`），而不是“相等運算子”（`==`），請參考《運算子》章節。

### if...else 結構

`if`程式碼塊後面，還可以跟一個`else`程式碼塊，表示不滿足條件時，所要執行的程式碼。

```javascript
if (m === 3) {
  // 滿足條件時，執行的語句
} else {
  // 不滿足條件時，執行的語句
}
```

上面程式碼判斷變數`m`是否等於3，如果等於就執行`if`程式碼塊，否則執行`else`程式碼塊。

對同一個變數進行多次判斷時，多個`if...else`語句可以連寫在一起。

```javascript
if (m === 0) {
  // ...
} else if (m === 1) {
  // ...
} else if (m === 2) {
  // ...
} else {
  // ...
}
```

`else`程式碼塊總是與離自己最近的那個`if`語句配對。

```javascript
var m = 1;
var n = 2;

if (m !== 1)
if (n === 2) console.log('hello');
else console.log('world');
```

上面程式碼不會有任何輸出，`else`程式碼塊不會得到執行，因為它跟著的是最近的那個`if`語句，相當於下面這樣。

```javascript
if (m !== 1) {
  if (n === 2) {
    console.log('hello');
  } else {
    console.log('world');
  }
}
```

如果想讓`else`程式碼塊跟隨最上面的那個`if`語句，就要改變大括號的位置。

```javascript
if (m !== 1) {
  if (n === 2) {
    console.log('hello');
  }
} else {
  console.log('world');
}
// world
```

### switch 結構

多個`if...else`連在一起使用的時候，可以轉為使用更方便的`switch`結構。

```javascript
switch (fruit) {
  case "banana":
    // ...
    break;
  case "apple":
    // ...
    break;
  default:
    // ...
}
```

上面程式碼根據變數`fruit`的值，選擇執行相應的`case`。如果所有`case`都不符合，則執行最後的`default`部分。需要注意的是，每個`case`程式碼塊內部的`break`語句不能少，否則會接下去執行下一個`case`程式碼塊，而不是跳出`switch`結構。

```javascript
var x = 1;

switch (x) {
  case 1:
    console.log('x 等於1');
  case 2:
    console.log('x 等於2');
  default:
    console.log('x 等於其他值');
}
// x等於1
// x等於2
// x等於其他值
```

上面程式碼中，`case`程式碼塊之中沒有`break`語句，導致不會跳出`switch`結構，而會一直執行下去。正確的寫法是像下面這樣。

```javascript
switch (x) {
  case 1:
    console.log('x 等於1');
    break;
  case 2:
    console.log('x 等於2');
    break;
  default:
    console.log('x 等於其他值');
}
```

`switch`語句部分和`case`語句部分，都可以使用表示式。

```javascript
switch (1 + 3) {
  case 2 + 2:
    f();
    break;
  default:
    neverHappens();
}
```

上面程式碼的`default`部分，是永遠不會執行到的。

需要注意的是，`switch`語句後面的表示式，與`case`語句後面的表示式比較執行結果時，採用的是嚴格相等運算子（`===`），而不是相等運算子（`==`），這意味著比較時不會發生型別轉換。

```javascript
var x = 1;

switch (x) {
  case true:
    console.log('x 發生型別轉換');
    break;
  default:
    console.log('x 沒有發生型別轉換');
}
// x 沒有發生型別轉換
```

上面程式碼中，由於變數`x`沒有發生型別轉換，所以不會執行`case true`的情況。這表明，`switch`語句內部採用的是“嚴格相等運算子”，詳細解釋請參考《運算子》一節。

### 三元運算子 ?:

JavaScript 還有一個三元運算子（即該運算子需要三個運運算元）`?:`，也可以用於邏輯判斷。

```javascript
(條件) ? 表示式1 : 表示式2
```

上面程式碼中，如果“條件”為`true`，則返回“表示式1”的值，否則返回“表示式2”的值。

```javascript
var even = (n % 2 === 0) ? true : false;
```

上面程式碼中，如果`n`可以被2整除，則`even`等於`true`，否則等於`false`。它等同於下面的形式。

```javascript
var even;
if (n % 2 === 0) {
  even = true;
} else {
  even = false;
}
```

這個三元運算子可以被視為`if...else...`的簡寫形式，因此可以用於多種場合。

```javascript
var myVar;
console.log(
  myVar ?
  'myVar has a value' :
  'myVar does not have a value'
)
// myVar does not have a value
```

上面程式碼利用三元運算子，輸出相應的提示。

```javascript
var msg = '數字' + n + '是' + (n % 2 === 0 ? '偶數' : '奇數');
```

上面程式碼利用三元運算子，在字串之中插入不同的值。

## 迴圈語句

迴圈語句用於重複執行某個操作，它有多種形式。

### while 迴圈

`While`語句包括一個迴圈條件和一段程式碼塊，只要條件為真，就不斷迴圈執行程式碼塊。

```javascript
while (條件)
  語句;

// 或者
while (條件) 語句;
```

`while`語句的迴圈條件是一個表示式，必須放在圓括號中。程式碼塊部分，如果只有一條語句，可以省略大括號，否則就必須加上大括號。

```javascript
while (條件) {
  語句;
}
```

下面是`while`語句的一個例子。

```javascript
var i = 0;

while (i < 100) {
  console.log('i 當前為：' + i);
  i = i + 1;
}
```

上面的程式碼將迴圈100次，直到`i`等於100為止。

下面的例子是一個無限迴圈，因為迴圈條件總是為真。

```javascript
while (true) {
  console.log('Hello, world');
}
```

### for 迴圈

`for`語句是迴圈命令的另一種形式，可以指定迴圈的起點、終點和終止條件。它的格式如下。

```javascript
for (初始化表示式; 條件; 遞增表示式)
  語句

// 或者

for (初始化表示式; 條件; 遞增表示式) {
  語句
}
```

`for`語句後面的括號裡面，有三個表示式。

- 初始化表示式（initialize）：確定迴圈變數的初始值，只在迴圈開始時執行一次。
- 條件表示式（test）：每輪迴圈開始時，都要執行這個條件表示式，只有值為真，才繼續進行迴圈。
- 遞增表示式（increment）：每輪迴圈的最後一個操作，通常用來遞增迴圈變數。

下面是一個例子。

```javascript
var x = 3;
for (var i = 0; i < x; i++) {
  console.log(i);
}
// 0
// 1
// 2
```

上面程式碼中，初始化表示式是`var i = 0`，即初始化一個變數`i`；測試表達式是`i < x`，即只要`i`小於`x`，就會執行迴圈；遞增表示式是`i++`，即每次迴圈結束後，`i`增大1。

所有`for`迴圈，都可以改寫成`while`迴圈。上面的例子改為`while`迴圈，程式碼如下。

```javascript
var x = 3;
var i = 0;

while (i < x) {
  console.log(i);
  i++;
}
```

`for`語句的三個部分（initialize、test、increment），可以省略任何一個，也可以全部省略。

```javascript
for ( ; ; ){
  console.log('Hello World');
}
```

上面程式碼省略了`for`語句表示式的三個部分，結果就導致了一個無限迴圈。

### do...while 迴圈

`do...while`迴圈與`while`迴圈類似，唯一的區別就是先執行一次迴圈體，然後判斷迴圈條件。

```javascript
do
  語句
while (條件);

// 或者
do {
  語句
} while (條件);
```

不管條件是否為真，`do...while`迴圈至少執行一次，這是這種結構最大的特點。另外，`while`語句後面的分號注意不要省略。

下面是一個例子。

```javascript
var x = 3;
var i = 0;

do {
  console.log(i);
  i++;
} while(i < x);
```

### break 語句和 continue 語句

`break`語句和`continue`語句都具有跳轉作用，可以讓程式碼不按既有的順序執行。

`break`語句用於跳出程式碼塊或迴圈。

```javascript
var i = 0;

while(i < 100) {
  console.log('i 當前為：' + i);
  i++;
  if (i === 10) break;
}
```

上面程式碼只會執行10次迴圈，一旦`i`等於10，就會跳出迴圈。

`for`迴圈也可以使用`break`語句跳出迴圈。

```javascript
for (var i = 0; i < 5; i++) {
  console.log(i);
  if (i === 3)
    break;
}
// 0
// 1
// 2
// 3
```

上面程式碼執行到`i`等於3，就會跳出迴圈。

`continue`語句用於立即終止本輪迴圈，返回迴圈結構的頭部，開始下一輪迴圈。

```javascript
var i = 0;

while (i < 100){
  i++;
  if (i % 2 === 0) continue;
  console.log('i 當前為：' + i);
}
```

上面程式碼只有在`i`為奇數時，才會輸出`i`的值。如果`i`為偶數，則直接進入下一輪迴圈。

如果存在多重迴圈，不帶引數的`break`語句和`continue`語句都只針對最內層迴圈。

### 標籤（label）

JavaScript 語言允許，語句的前面有標籤（label），相當於定位符，用於跳轉到程式的任意位置，標籤的格式如下。

```javascript
label:
  語句
```

標籤可以是任意的識別符號，但不能是保留字，語句部分可以是任意語句。

標籤通常與`break`語句和`continue`語句配合使用，跳出特定的迴圈。

```javascript
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) break top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
```

上面程式碼為一個雙重迴圈區塊，`break`命令後面加上了`top`標籤（注意，`top`不用加引號），滿足條件時，直接跳出雙層迴圈。如果`break`語句後面不使用標籤，則只能跳出內層迴圈，進入下一次的外層迴圈。

標籤也可以用於跳出程式碼塊。

```javascript
foo: {
  console.log(1);
  break foo;
  console.log('本行不會輸出');
}
console.log(2);
// 1
// 2
```

上面程式碼執行到`break foo`，就會跳出區塊。

`continue`語句也可以與標籤配合使用。

```javascript
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) continue top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```

上面程式碼中，`continue`命令後面有一個標籤名，滿足條件時，會跳過當前迴圈，直接進入下一輪外層迴圈。如果`continue`語句後面不使用標籤，則只能進入下一輪的內層迴圈。

## 參考連結

- Axel Rauschmayer, [A quick overview of JavaScript](http://www.2ality.com/2011/10/javascript-overview.html)
