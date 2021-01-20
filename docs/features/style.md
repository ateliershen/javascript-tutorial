# 程式設計風格

## 概述

“程式設計風格”（programming style）指的是編寫程式碼的樣式規則。不同的程式設計師，往往有不同的程式設計風格。

有人說，編譯器的規範叫做“語法規則”（grammar），這是程式設計師必須遵守的；而編譯器忽略的部分，就叫“程式設計風格”（programming style），這是程式設計師可以自由選擇的。這種說法不完全正確，程式設計師固然可以自由選擇程式設計風格，但是好的程式設計風格有助於寫出質量更高、錯誤更少、更易於維護的程式。

所以，程式設計風格的選擇不應該基於個人愛好、熟悉程度、打字量等因素，而要考慮如何儘量使程式碼清晰易讀、減少出錯。你選擇的，不是你喜歡的風格，而是一種能夠清晰表達你的意圖的風格。這一點，對於 JavaScript 這種語法自由度很高的語言尤其重要。

必須牢記的一點是，如果你選定了一種“程式設計風格”，就應該堅持遵守，切忌多種風格混用。如果你加入他人的專案，就應該遵守現有的風格。

## 縮排

行首的空格和 Tab 鍵，都可以產生程式碼縮排效果（indent）。

Tab 鍵可以節省擊鍵次數，但不同的文字編輯器對 Tab 的顯示不盡相同，有的顯示四個空格，有的顯示兩個空格，所以有人覺得，空格鍵可以使得顯示效果更統一。

無論你選擇哪一種方法，都是可以接受的，要做的就是始終堅持這一種選擇。不要一會使用 Tab 鍵，一會使用空格鍵。

## 區塊

如果迴圈和判斷的程式碼體只有一行，JavaScript 允許該區塊（block）省略大括號。

```javascript
if (a)
  b();
  c();
```

上面程式碼的原意可能是下面這樣。

```javascript
if (a) {
  b();
  c();
}
```

但是，實際效果卻是下面這樣。

```javascript
if (a) {
  b();
}
  c();
```

因此，建議總是使用大括號表示區塊。

另外，區塊起首的大括號的位置，有許多不同的寫法。最流行的有兩種，一種是起首的大括號另起一行。

```javascript
block
{
  // ...
}
```

另一種是起首的大括號跟在關鍵字的後面。

```javascript
block {
  // ...
}
```

一般來說，這兩種寫法都可以接受。但是，JavaScript 要使用後一種，因為 JavaScript 會自動新增句末的分號，導致一些難以察覺的錯誤。

```javascript
return
{
  key: value
};

// 相當於
return;
{
  key: value
};
```

上面的程式碼的原意，是要返回一個物件，但實際上返回的是`undefined`，因為 JavaScript 自動在`return`語句後面添加了分號。為了避免這一類錯誤，需要寫成下面這樣。

```javascript
return {
  key : value
};
```

因此，表示區塊起首的大括號，不要另起一行。

## 圓括號

圓括號（parentheses）在 JavaScript 中有兩種作用，一種表示函式的呼叫，另一種表示表示式的組合（grouping）。

```javascript
// 圓括號表示函式的呼叫
console.log('abc');

// 圓括號表示表示式的組合
(1 + 2) * 3
```

建議可以用空格，區分這兩種不同的括號。

> 1. 表示函式呼叫時，函式名與左括號之間沒有空格。
>
> 2. 表示函式定義時，函式名與左括號之間沒有空格。
>
> 3. 其他情況時，前面位置的語法元素與左括號之間，都有一個空格。

按照上面的規則，下面的寫法都是不規範的。

```javascript
foo (bar)
return(a+b);
if(a === 0) {...}
function foo (b) {...}
function(x) {...}
```

上面程式碼的最後一行是一個匿名函式，`function`是語法關鍵字，不是函式名，所以與左括號之間應該要有一個空格。

## 行尾的分號

分號表示一條語句的結束。JavaScript 允許省略行尾的分號。事實上，確實有一些開發者行尾從來不寫分號。但是，由於下面要討論的原因，建議還是不要省略這個分號。

### 不使用分號的情況

首先，以下三種情況，語法規定本來就不需要在結尾新增分號。

**（1）for 和 while 迴圈**

```javascript
for ( ; ; ) {
} // 沒有分號

while (true) {
} // 沒有分號
```

注意，`do...while`迴圈是有分號的。

```javascript
do {
  a--;
} while(a > 0); // 分號不能省略
```

**（2）分支語句：if，switch，try**

```javascript
if (true) {
} // 沒有分號

switch () {
} // 沒有分號

try {
} catch {
} // 沒有分號
```

**（3）函式的宣告語句**

```javascript
function f() {
} // 沒有分號
```

注意，函式表示式仍然要使用分號。

```javascript
var f = function f() {
};
```

以上三種情況，如果使用了分號，並不會出錯。因為，解釋引擎會把這個分號解釋為空語句。

### 分號的自動新增

除了上一節的三種情況，所有語句都應該使用分號。但是，如果沒有使用分號，大多數情況下，JavaScript 會自動新增。

```javascript
var a = 1
// 等同於
var a = 1;
```

這種語法特性被稱為“分號的自動新增”（Automatic Semicolon Insertion，簡稱 ASI）。

因此，有人提倡省略句尾的分號。麻煩的是，如果下一行的開始可以與本行的結尾連在一起解釋，JavaScript 就不會自動新增分號。

```javascript
// 等同於 var a = 3
var
a
=
3

// 等同於 'abc'.length
'abc'
.length

// 等同於 return a + b;
return a +
b;

// 等同於 obj.foo(arg1, arg2);
obj.foo(arg1,
arg2);

// 等同於 3 * 2 + 10 * (27 / 6)
3 * 2
+
10 * (27 / 6)
```

上面程式碼都會多行放在一起解釋，不會每一行自動新增分號。這些例子還是比較容易看出來的，但是下面這個例子就不那麼容易看出來了。

```javascript
x = y
(function () {
  // ...
})();

// 等同於
x = y(function () {...})();
```

下面是更多不會自動新增分號的例子。

```javascript
// 引擎解釋為 c(d+e)
var a = b + c
(d+e).toString();

// 引擎解釋為 a = b/hi/g.exec(c).map(d)
// 正則表示式的斜槓，會當作除法運算子
a = b
/hi/g.exec(c).map(d);

// 解釋為'b'['red', 'green']，
// 即把字串當作一個數組，按索引取值
var a = 'b'
['red', 'green'].forEach(function (c) {
  console.log(c);
})

// 解釋為 function (x) { return x }(a++)
// 即呼叫匿名函式，結果f等於0
var a = 0;
var f = function (x) { return x }
(a++)
```

只有下一行的開始與本行的結尾，無法放在一起解釋，JavaScript 引擎才會自動新增分號。

```javascript
if (a < 0) a = 0
console.log(a)

// 等同於下面的程式碼，
// 因為 0console 沒有意義
if (a < 0) a = 0;
console.log(a)
```

另外，如果一行的起首是“自增”（`++`）或“自減”（`--`）運算子，則它們的前面會自動新增分號。

```javascript
a = b = c = 1

a
++
b
--
c

console.log(a, b, c)
// 1 2 0
```

上面程式碼之所以會得到`1 2 0`的結果，原因是自增和自減運算子前，自動加上了分號。上面的程式碼實際上等同於下面的形式。

```javascript
a = b = c = 1;
a;
++b;
--c;
```

如果`continue`、`break`、`return`和`throw`這四個語句後面，直接跟換行符，則會自動新增分號。這意味著，如果`return`語句返回的是一個物件的字面量，起首的大括號一定要寫在同一行，否則得不到預期結果。

```javascript
return
{ first: 'Jane' };

// 解釋成
return;
{ first: 'Jane' };
```

由於解釋引擎自動新增分號的行為難以預測，因此編寫程式碼的時候不應該省略行尾的分號。

不應該省略結尾的分號，還有一個原因。有些 JavaScript 程式碼壓縮器（uglifier）不會自動新增分號，因此遇到沒有分號的結尾，就會讓程式碼保持原狀，而不是壓縮成一行，使得壓縮無法得到最優的結果。

另外，不寫結尾的分號，可能會導致指令碼合併出錯。所以，有的程式碼庫在第一行語句開始前，會加上一個分號。

```javascript
;var a = 1;
// ...
```

上面這種寫法就可以避免與其他指令碼合併時，排在前面的指令碼最後一行語句沒有分號，導致執行出錯的問題。

## 全域性變數

JavaScript 最大的語法缺點，可能就是全域性變數對於任何一個程式碼塊，都是可讀可寫。這對程式碼的模組化和重複使用，非常不利。

因此，建議避免使用全域性變數。如果不得不使用，可以考慮用大寫字母表示變數名，這樣更容易看出這是全域性變數，比如`UPPER_CASE`。

## 變數宣告

JavaScript 會自動將變數宣告“提升”（hoist）到程式碼塊（block）的頭部。

```javascript
if (!x) {
  var x = {};
}

// 等同於
var x;
if (!x) {
  x = {};
}
```

這意味著，變數`x`是`if`程式碼塊之前就存在了。為了避免可能出現的問題，最好把變數宣告都放在程式碼塊的頭部。

```javascript
for (var i = 0; i < 10; i++) {
  // ...
}

// 寫成
var i;
for (i = 0; i < 10; i++) {
  // ...
}
```

上面這樣的寫法，就容易看出存在一個全域性的迴圈變數`i`。

另外，所有函式都應該在使用之前定義。函式內部的變數宣告，都應該放在函式的頭部。

## with 語句

`with`可以減少程式碼的書寫，但是會造成混淆。

```javascript
with (o) {
　foo = bar;
}
```

上面的程式碼，可以有四種執行結果：

```javascript
o.foo = bar;
o.foo = o.bar;
foo = bar;
foo = o.bar;
```

這四種結果都可能發生，取決於不同的變數是否有定義。因此，不要使用`with`語句。

## 相等和嚴格相等

JavaScript 有兩個表示相等的運算子：“相等”（`==`）和“嚴格相等”（`===`）。

相等運算子會自動轉換變數型別，造成很多意想不到的情況。

```javascript
0 == ''// true
1 == true // true
2 == true // false
0 == '0' // true
false == 'false' // false
false == '0' // true
' \t\r\n ' == 0 // true
```

因此，建議不要使用相等運算子（`==`），只使用嚴格相等運算子（`===`）。

## 語句的合併

有些程式設計師追求簡潔，喜歡合併不同目的的語句。比如，原來的語句是

```javascript
a = b;
if (a) {
  // ...
}
```

他喜歡寫成下面這樣。

```javascript
if (a = b) {
  // ...
}
```

雖然語句少了一行，但是可讀性大打折扣，而且會造成誤讀，讓別人誤解這行程式碼的意思是下面這樣。

```javascript
if （a === b）{
  // ...
}
```

建議不要將不同目的的語句，合併成一行。

## 自增和自減運算子

自增（`++`）和自減（`--`）運算子，放在變數的前面或後面，返回的值不一樣，很容易發生錯誤。事實上，所有的`++`運算子都可以用`+= 1`代替。

```javascript
++x
// 等同於
x += 1;
```

改用`+= 1`，程式碼變得更清晰了。

建議自增（`++`）和自減（`--`）運算子儘量使用`+=`和`-=`代替。

## switch...case 結構

`switch...case`結構要求，在每一個`case`的最後一行必須是`break`語句，否則會接著執行下一個`case`。這樣不僅容易忘記，還會造成程式碼的冗長。

而且，`switch...case`不使用大括號，不利於程式碼形式的統一。此外，這種結構類似於`goto`語句，容易造成程式流程的混亂，使得程式碼結構混亂不堪，不符合面向物件程式設計的原則。

```javascript
function doAction(action) {
  switch (action) {
    case 'hack':
      return 'hack';
    case 'slash':
      return 'slash';
    case 'run':
      return 'run';
    default:
      throw new Error('Invalid action.');
  }
}
```

上面的程式碼建議改寫成物件結構。

```javascript
function doAction(action) {
  var actions = {
    'hack': function () {
      return 'hack';
    },
    'slash': function () {
      return 'slash';
    },
    'run': function () {
      return 'run';
    }
  };

  if (typeof actions[action] !== 'function') {
    throw new Error('Invalid action.');
  }

  return actions[action]();
}
```

因此，建議`switch...case`結構可以用物件結構代替。

## 參考連結

- Eric Elliott, Programming JavaScript Applications, [Chapter 2. JavaScript Style Guide](http://chimera.labs.oreilly.com/books/1234000000262/ch02.html), O'Reilly, 2013
- Axel Rauschmayer, [A meta style guide for JavaScript](http://www.2ality.com/2013/07/meta-style-guide.html)
- Axel Rauschmayer, [Automatic semicolon insertion in JavaScript](http://www.2ality.com/2011/05/semicolon-insertion.html)
- Rod Vagg, [JavaScript and Semicolons](http://dailyjs.com/2012/04/19/semicolons/)
