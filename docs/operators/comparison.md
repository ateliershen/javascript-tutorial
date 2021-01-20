# 比較運算子

## 概述

比較運算子用於比較兩個值的大小，然後返回一個布林值，表示是否滿足指定的條件。

```javascript
2 > 1 // true
```

上面程式碼比較`2`是否大於`1`，返回`true`。

> 注意，比較運算子可以比較各種型別的值，不僅僅是數值。

JavaScript 一共提供了8個比較運算子。

- `>` 大於運算子
- `<` 小於運算子
- `<=` 小於或等於運算子
- `>=` 大於或等於運算子
- `==` 相等運算子
- `===` 嚴格相等運算子
- `!=` 不相等運算子
- `!==` 嚴格不相等運算子

這八個比較運算子分成兩類：相等比較和非相等比較。兩者的規則是不一樣的，對於非相等的比較，演算法是先看兩個運運算元是否都是字串，如果是的，就按照字典順序比較（實際上是比較 Unicode 碼點）；否則，將兩個運運算元都轉成數值，再比較數值的大小。

## 非相等運算子：字串的比較

字串按照字典順序進行比較。

```javascript
'cat' > 'dog' // false
'cat' > 'catalog' // false
```

JavaScript 引擎內部首先比較首字元的 Unicode 碼點。如果相等，再比較第二個字元的 Unicode 碼點，以此類推。

```javascript
'cat' > 'Cat' // true'
```

上面程式碼中，小寫的`c`的 Unicode 碼點（`99`）大於大寫的`C`的 Unicode 碼點（`67`），所以返回`true`。

由於所有字元都有 Unicode 碼點，因此漢字也可以比較。

```javascript
'大' > '小' // false
```

上面程式碼中，“大”的 Unicode 碼點是22823，“小”是23567，因此返回`false`。

## 非相等運算子：非字串的比較

如果兩個運運算元之中，至少有一個不是字串，需要分成以下兩種情況。

**（1）原始型別值**

如果兩個運運算元都是原始型別的值，則是先轉成數值再比較。

```javascript
5 > '4' // true
// 等同於 5 > Number('4')
// 即 5 > 4

true > false // true
// 等同於 Number(true) > Number(false)
// 即 1 > 0

2 > true // true
// 等同於 2 > Number(true)
// 即 2 > 1
```

上面程式碼中，字串和布林值都會先轉成數值，再進行比較。

這裡需要注意與`NaN`的比較。任何值（包括`NaN`本身）與`NaN`使用非相等運算子進行比較，返回的都是`false`。

```javascript
1 > NaN // false
1 <= NaN // false
'1' > NaN // false
'1' <= NaN // false
NaN > NaN // false
NaN <= NaN // false
```

**（2）物件**

如果運運算元是物件，會轉為原始型別的值，再進行比較。

物件轉換成原始型別的值，演算法是先呼叫`valueOf`方法；如果返回的還是物件，再接著呼叫`toString`方法，詳細解釋參見《資料型別的轉換》一章。

```javascript
var x = [2];
x > '11' // true
// 等同於 [2].valueOf().toString() > '11'
// 即 '2' > '11'

x.valueOf = function () { return '1' };
x > '11' // false
// 等同於 [2].valueOf() > '11'
// 即 '1' > '11'
```

兩個物件之間的比較也是如此。

```javascript
[2] > [1] // true
// 等同於 [2].valueOf().toString() > [1].valueOf().toString()
// 即 '2' > '1'

[2] > [11] // true
// 等同於 [2].valueOf().toString() > [11].valueOf().toString()
// 即 '2' > '11'

{ x: 2 } >= { x: 1 } // true
// 等同於 { x: 2 }.valueOf().toString() >= { x: 1 }.valueOf().toString()
// 即 '[object Object]' >= '[object Object]'
```

## 嚴格相等運算子

JavaScript 提供兩種相等運算子：`==`和`===`。

簡單說，它們的區別是相等運算子（`==`）比較兩個值是否相等，嚴格相等運算子（`===`）比較它們是否為“同一個值”。如果兩個值不是同一型別，嚴格相等運算子（`===`）直接返回`false`，而相等運算子（`==`）會將它們轉換成同一個型別，再用嚴格相等運算子進行比較。

本節介紹嚴格相等運算子的演算法。

**（1）不同型別的值**

如果兩個值的型別不同，直接返回`false`。

```javascript
1 === "1" // false
true === "true" // false
```

上面程式碼比較數值的`1`與字串的“1”、布林值的`true`與字串`"true"`，因為型別不同，結果都是`false`。

**（2）同一類的原始型別值**

同一型別的原始型別的值（數值、字串、布林值）比較時，值相同就返回`true`，值不同就返回`false`。

```javascript
1 === 0x1 // true
```

上面程式碼比較十進位制的`1`與十六進位制的`1`，因為型別和值都相同，返回`true`。

需要注意的是，`NaN`與任何值都不相等（包括自身）。另外，正`0`等於負`0`。

```javascript
NaN === NaN  // false
+0 === -0 // true
```

**（3）複合型別值**

兩個複合型別（物件、陣列、函式）的資料比較時，不是比較它們的值是否相等，而是比較它們是否指向同一個地址。

```javascript
{} === {} // false
[] === [] // false
(function () {} === function () {}) // false
```

上面程式碼分別比較兩個空物件、兩個空陣列、兩個空函式，結果都是不相等。原因是對於複合型別的值，嚴格相等運算比較的是，它們是否引用同一個記憶體地址，而運算子兩邊的空物件、空陣列、空函式的值，都存放在不同的記憶體地址，結果當然是`false`。

如果兩個變數引用同一個物件，則它們相等。

```javascript
var v1 = {};
var v2 = v1;
v1 === v2 // true
```

注意，對於兩個物件的比較，嚴格相等運算子比較的是地址，而大於或小於運算子比較的是值。

```javascript
var obj1 = {};
var obj2 = {};

obj1 > obj2 // false
obj1 < obj2 // false
obj1 === obj2 // false
```

上面的三個比較，前兩個比較的是值，最後一個比較的是地址，所以都返回`false`。

**（4）undefined 和 null**

`undefined`和`null`與自身嚴格相等。

```javascript
undefined === undefined // true
null === null // true
```

由於變數聲明後預設值是`undefined`，因此兩個只宣告未賦值的變數是相等的。

```javascript
var v1;
var v2;
v1 === v2 // true
```

## 嚴格不相等運算子

嚴格相等運算子有一個對應的“嚴格不相等運算子”（`!==`），它的演算法就是先求嚴格相等運算子的結果，然後返回相反值。

```javascript
1 !== '1' // true
// 等同於
!(1 === '1')
```

上面程式碼中，感嘆號`!`是求出後面表示式的相反值。

## 相等運算子

相等運算子用來比較相同型別的資料時，與嚴格相等運算子完全一樣。

```javascript
1 == 1.0
// 等同於
1 === 1.0
```

比較不同型別的資料時，相等運算子會先將資料進行型別轉換，然後再用嚴格相等運算子比較。下面分成幾種情況，討論不同型別的值互相比較的規則。

**（1）原始型別值**

原始型別的值會轉換成數值再進行比較。

```javascript
1 == true // true
// 等同於 1 === Number(true)

0 == false // true
// 等同於 0 === Number(false)

2 == true // false
// 等同於 2 === Number(true)

2 == false // false
// 等同於 2 === Number(false)

'true' == true // false
// 等同於 Number('true') === Number(true)
// 等同於 NaN === 1

'' == 0 // true
// 等同於 Number('') === 0
// 等同於 0 === 0

'' == false  // true
// 等同於 Number('') === Number(false)
// 等同於 0 === 0

'1' == true  // true
// 等同於 Number('1') === Number(true)
// 等同於 1 === 1

'\n  123  \t' == 123 // true
// 因為字串轉為數字時，省略前置和後置的空格
```

上面程式碼將字串和布林值都轉為數值，然後再進行比較。具體的字串與布林值的型別轉換規則，參見《資料型別轉換》一章。

**（2）物件與原始型別值比較**

物件（這裡指廣義的物件，包括陣列和函式）與原始型別的值比較時，物件轉換成原始型別的值，再進行比較。

具體來說，先呼叫物件的`valueOf()`方法，如果得到原始型別的值，就按照上一小節的規則，互相比較；如果得到的還是物件，則再呼叫`toString()`方法，得到字串形式，再進行比較。

下面是陣列與原始型別值比較的例子。

```javascript
// 陣列與數值的比較
[1] == 1 // true

// 陣列與字串的比較
[1] == '1' // true
[1, 2] == '1,2' // true

// 物件與布林值的比較
[1] == true // true
[2] == true // false
```

上面例子中，JavaScript 引擎會先對陣列`[1]`呼叫陣列的`valueOf()`方法，由於返回的還是一個數組，所以會接著呼叫陣列的`toString()`方法，得到字串形式，再按照上一小節的規則進行比較。

下面是一個更直接的例子。

```javascript
const obj = {
  valueOf: function () {
    console.log('執行 valueOf()');
    return obj;
  },
  toString: function () {
    console.log('執行 toString()');
    return 'foo';
  }
};

obj == 'foo'
// 執行 valueOf()
// 執行 toString()
// true
```

上面例子中，`obj`是一個自定義了`valueOf()`和`toString()`方法的物件。這個物件與字串`'foo'`進行比較時，會依次呼叫`valueOf()`和`toString()`方法，最後返回`'foo'`，所以比較結果是`true`。

**（3）undefined 和 null**

`undefined`和`null`只有與自身比較，或者互相比較時，才會返回`true`；與其他型別的值比較時，結果都為`false`。

```javascript
undefined == undefined // true
null == null // true
undefined == null // true

false == null // false
false == undefined // false

0 == null // false
0 == undefined // false
```

**（4）相等運算子的缺點**

相等運算子隱藏的型別轉換，會帶來一些違反直覺的結果。

```javascript
0 == ''             // true
0 == '0'            // true

2 == true           // false
2 == false          // false

false == 'false'    // false
false == '0'        // true

false == undefined  // false
false == null       // false
null == undefined   // true

' \t\r\n ' == 0     // true
```

上面這些表示式都不同於直覺，很容易出錯。因此建議不要使用相等運算子（`==`），最好只使用嚴格相等運算子（`===`）。

## 不相等運算子

相等運算子有一個對應的“不相等運算子”（`!=`），它的演算法就是先求相等運算子的結果，然後返回相反值。

```javascript
1 != '1' // false

// 等同於
!(1 == '1')
```
