# 資料型別的轉換

## 概述

JavaScript 是一種動態型別語言，變數沒有型別限制，可以隨時賦予任意值。

```javascript
var x = y ? 1 : 'a';
```

上面程式碼中，變數`x`到底是數值還是字串，取決於另一個變數`y`的值。`y`為`true`時，`x`是一個數值；`y`為`false`時，`x`是一個字串。這意味著，`x`的型別沒法在編譯階段就知道，必須等到執行時才能知道。

雖然變數的資料型別是不確定的，但是各種運算子對資料型別是有要求的。如果運算子發現，運運算元的型別與預期不符，就會自動轉換型別。比如，減法運算子預期左右兩側的運運算元應該是數值，如果不是，就會自動將它們轉為數值。

```javascript
'4' - '3' // 1
```

上面程式碼中，雖然是兩個字串相減，但是依然會得到結果數值`1`，原因就在於 JavaScript 將運運算元自動轉為了數值。

本章講解資料型別自動轉換的規則。在此之前，先講解如何手動強制轉換資料型別。

## 強制轉換

強制轉換主要指使用`Number()`、`String()`和`Boolean()`三個函式，手動將各種型別的值，分別轉換成數字、字串或者布林值。

### Number()

使用`Number`函式，可以將任意型別的值轉化成數值。

下面分成兩種情況討論，一種是引數是原始型別的值，另一種是引數是物件。

**（1）原始型別值**

原始型別值的轉換規則如下。

```javascript
// 數值：轉換後還是原來的值
Number(324) // 324

// 字串：如果可以被解析為數值，則轉換為相應的數值
Number('324') // 324

// 字串：如果不可以被解析為數值，返回 NaN
Number('324abc') // NaN

// 空字串轉為0
Number('') // 0

// 布林值：true 轉成 1，false 轉成 0
Number(true) // 1
Number(false) // 0

// undefined：轉成 NaN
Number(undefined) // NaN

// null：轉成0
Number(null) // 0
```

`Number`函式將字串轉為數值，要比`parseInt`函式嚴格很多。基本上，只要有一個字元無法轉成數值，整個字串就會被轉為`NaN`。

```javascript
parseInt('42 cats') // 42
Number('42 cats') // NaN
```

上面程式碼中，`parseInt`逐個解析字元，而`Number`函式整體轉換字串的型別。

另外，`parseInt`和`Number`函式都會自動過濾一個字串前導和字尾的空格。

```javascript
parseInt('\t\v\r12.34\n') // 12
Number('\t\v\r12.34\n') // 12.34
```

**（2）物件**

簡單的規則是，`Number`方法的引數是物件時，將返回`NaN`，除非是包含單個數值的陣列。

```javascript
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
```

之所以會這樣，是因為`Number`背後的轉換規則比較複雜。

第一步，呼叫物件自身的`valueOf`方法。如果返回原始型別的值，則直接對該值使用`Number`函式，不再進行後續步驟。

第二步，如果`valueOf`方法返回的還是物件，則改為呼叫物件自身的`toString`方法。如果`toString`方法返回原始型別的值，則對該值使用`Number`函式，不再進行後續步驟。

第三步，如果`toString`方法返回的是物件，就報錯。

請看下面的例子。

```javascript
var obj = {x: 1};
Number(obj) // NaN

// 等同於
if (typeof obj.valueOf() === 'object') {
  Number(obj.toString());
} else {
  Number(obj.valueOf());
}
```

上面程式碼中，`Number`函式將`obj`物件轉為數值。背後發生了一連串的操作，首先呼叫`obj.valueOf`方法, 結果返回物件本身；於是，繼續呼叫`obj.toString`方法，這時返回字串`[object Object]`，對這個字串使用`Number`函式，得到`NaN`。

預設情況下，物件的`valueOf`方法返回物件本身，所以一般總是會呼叫`toString`方法，而`toString`方法返回物件的型別字串（比如`[object Object]`）。所以，會有下面的結果。

```javascript
Number({}) // NaN
```

如果`toString`方法返回的不是原始型別的值，結果就會報錯。

```javascript
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

Number(obj)
// TypeError: Cannot convert object to primitive value
```

上面程式碼的`valueOf`和`toString`方法，返回的都是物件，所以轉成數值時會報錯。

從上例還可以看到，`valueOf`和`toString`方法，都是可以自定義的。

```javascript
Number({
  valueOf: function () {
    return 2;
  }
})
// 2

Number({
  toString: function () {
    return 3;
  }
})
// 3

Number({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// 2
```

上面程式碼對三個物件使用`Number`函式。第一個物件返回`valueOf`方法的值，第二個物件返回`toString`方法的值，第三個物件表示`valueOf`方法先於`toString`方法執行。

### String()

`String`函式可以將任意型別的值轉化成字串，轉換規則如下。

**（1）原始型別值**

- **數值**：轉為相應的字串。
- **字串**：轉換後還是原來的值。
- **布林值**：`true`轉為字串`"true"`，`false`轉為字串`"false"`。
- **undefined**：轉為字串`"undefined"`。
- **null**：轉為字串`"null"`。

```javascript
String(123) // "123"
String('abc') // "abc"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```

**（2）物件**

`String`方法的引數如果是物件，返回一個型別字串；如果是陣列，返回該陣列的字串形式。

```javascript
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```

`String`方法背後的轉換規則，與`Number`方法基本相同，只是互換了`valueOf`方法和`toString`方法的執行順序。

1. 先呼叫物件自身的`toString`方法。如果返回原始型別的值，則對該值使用`String`函式，不再進行以下步驟。

2. 如果`toString`方法返回的是物件，再呼叫原物件的`valueOf`方法。如果`valueOf`方法返回原始型別的值，則對該值使用`String`函式，不再進行以下步驟。

3. 如果`valueOf`方法返回的是物件，就報錯。

下面是一個例子。

```javascript
String({a: 1})
// "[object Object]"

// 等同於
String({a: 1}.toString())
// "[object Object]"
```

上面程式碼先呼叫物件的`toString`方法，發現返回的是字串`[object Object]`，就不再呼叫`valueOf`方法了。

如果`toString`法和`valueOf`方法，返回的都是物件，就會報錯。

```javascript
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

String(obj)
// TypeError: Cannot convert object to primitive value
```

下面是透過自定義`toString`方法，改變返回值的例子。

```javascript
String({
  toString: function () {
    return 3;
  }
})
// "3"

String({
  valueOf: function () {
    return 2;
  }
})
// "[object Object]"

String({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// "3"
```

上面程式碼對三個物件使用`String`函式。第一個物件返回`toString`方法的值（數值3），第二個物件返回的還是`toString`方法的值（`[object Object]`），第三個物件表示`toString`方法先於`valueOf`方法執行。

### Boolean()

`Boolean()`函式可以將任意型別的值轉為布林值。

它的轉換規則相對簡單：除了以下五個值的轉換結果為`false`，其他的值全部為`true`。

- `undefined`
- `null`
- `0`（包含`-0`和`+0`）
- `NaN`
- `''`（空字串）

```javascript
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```

當然，`true`和`false`這兩個布林值不會發生變化。

```javascript
Boolean(true) // true
Boolean(false) // false
```

注意，所有物件（包括空物件）的轉換結果都是`true`，甚至連`false`對應的布林物件`new Boolean(false)`也是`true`（詳見《原始型別值的包裝物件》一章）。

```javascript
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```

所有物件的布林值都是`true`，這是因為 JavaScript 語言設計的時候，出於效能的考慮，如果物件需要計算才能得到布林值，對於`obj1 && obj2`這樣的場景，可能會需要較多的計算。為了保證效能，就統一規定，物件的布林值為`true`。

## 自動轉換

下面介紹自動轉換，它是以強制轉換為基礎的。

遇到以下三種情況時，JavaScript 會自動轉換資料型別，即轉換是自動完成的，使用者不可見。

第一種情況，不同型別的資料互相運算。

```javascript
123 + 'abc' // "123abc"
```

第二種情況，對非布林值型別的資料求布林值。

```javascript
if ('abc') {
  console.log('hello')
}  // "hello"
```

第三種情況，對非數值型別的值使用一元運算子（即`+`和`-`）。

```javascript
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```

自動轉換的規則是這樣的：預期什麼型別的值，就呼叫該型別的轉換函式。比如，某個位置預期為字串，就呼叫`String()`函式進行轉換。如果該位置既可以是字串，也可能是數值，那麼預設轉為數值。

由於自動轉換具有不確定性，而且不易除錯，建議在預期為布林值、數值、字串的地方，全部使用`Boolean()`、`Number()`和`String()`函式進行顯式轉換。

### 自動轉換為布林值

JavaScript 遇到預期為布林值的地方（比如`if`語句的條件部分），就會將非布林值的引數自動轉換為布林值。系統內部會自動呼叫`Boolean()`函式。

因此除了以下五個值，其他都是自動轉為`true`。

- `undefined`
- `null`
- `+0`或`-0`
- `NaN`
- `''`（空字串）

下面這個例子中，條件部分的每個值都相當於`false`，使用否定運算子後，就變成了`true`。

```javascript
if ( !undefined
  && !null
  && !0
  && !NaN
  && !''
) {
  console.log('true');
} // true
```

下面兩種寫法，有時也用於將一個表示式轉為布林值。它們內部呼叫的也是`Boolean()`函式。

```javascript
// 寫法一
expression ? true : false

// 寫法二
!! expression
```

### 自動轉換為字串

JavaScript 遇到預期為字串的地方，就會將非字串的值自動轉為字串。具體規則是，先將複合型別的值轉為原始型別的值，再將原始型別的值轉為字串。

字串的自動轉換，主要發生在字串的加法運算時。當一個值為字串，另一個值為非字串，則後者轉為字串。

```javascript
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"
```

這種自動轉換很容易出錯。

```javascript
var obj = {
  width: '100'
};

obj.width + 20 // "10020"
```

上面程式碼中，開發者可能期望返回`120`，但是由於自動轉換，實際上返回了一個字元`10020`。

### 自動轉換為數值

JavaScript 遇到預期為數值的地方，就會將引數值自動轉換為數值。系統內部會自動呼叫`Number()`函式。

除了加法運算子（`+`）有可能把運運算元轉為字串，其他運算子都會把運運算元自動轉成數值。

```javascript
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN
```

上面程式碼中，運算子兩側的運運算元，都被轉成了數值。

> 注意：`null`轉為數值時為`0`，而`undefined`轉為數值時為`NaN`。

一元運算子也會把運運算元轉成數值。

```javascript
+'abc' // NaN
-'abc' // NaN
+true // 1
-false // 0
```

## 參考連結

- Axel Rauschmayer, [What is {} + {} in JavaScript?](http://www.2ality.com/2012/01/object-plus-object.html)
- Axel Rauschmayer, [JavaScript quirk 1: implicit conversion of values](http://www.2ality.com/2013/04/quirk-implicit-conversion.html)
- Benjie Gillam, [Quantum JavaScript?](http://www.benjiegillam.com/2013/06/quantum-javascript/)
