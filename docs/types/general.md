# 資料型別概述

## 簡介

JavaScript 語言的每一個值，都屬於某一種資料型別。JavaScript 的資料型別，共有六種。（ES6 又新增了第七種 Symbol 型別的值，本教程不涉及。）

- 數值（number）：整數和小數（比如`1`和`3.14`）
- 字串（string）：文字（比如`Hello World`）。
- 布林值（boolean）：表示真偽的兩個特殊值，即`true`（真）和`false`（假）
- `undefined`：表示“未定義”或不存在，即由於目前沒有定義，所以此處暫時沒有任何值
- `null`：表示空值，即此處的值為空。
- 物件（object）：各種值組成的集合。

通常，數值、字串、布林值這三種類型，合稱為原始型別（primitive type）的值，即它們是最基本的資料型別，不能再細分了。物件則稱為合成型別（complex type）的值，因為一個物件往往是多個原始型別的值的合成，可以看作是一個存放各種值的容器。至於`undefined`和`null`，一般將它們看成兩個特殊值。

物件是最複雜的資料型別，又可以分成三個子型別。

- 狹義的物件（object）
- 陣列（array）
- 函式（function）

狹義的物件和陣列是兩種不同的資料組合方式，除非特別宣告，本教程的“物件”都特指狹義的物件。函式其實是處理資料的方法，JavaScript 把它當成一種資料型別，可以賦值給變數，這為程式設計帶來了很大的靈活性，也為 JavaScript 的“函數語言程式設計”奠定了基礎。

## typeof 運算子

JavaScript 有三種方法，可以確定一個值到底是什麼型別。

- `typeof`運算子
- `instanceof`運算子
- `Object.prototype.toString`方法

`instanceof`運算子和`Object.prototype.toString`方法，將在後文介紹。這裡介紹`typeof`運算子。

`typeof`運算子可以返回一個值的資料型別。

數值、字串、布林值分別返回`number`、`string`、`boolean`。

```javascript
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"
```

函式返回`function`。

```javascript
function f() {}
typeof f
// "function"
```

`undefined`返回`undefined`。

```javascript
typeof undefined
// "undefined"
```

利用這一點，`typeof`可以用來檢查一個沒有宣告的變數，而不報錯。

```javascript
v
// ReferenceError: v is not defined

typeof v
// "undefined"
```

上面程式碼中，變數`v`沒有用`var`命令宣告，直接使用就會報錯。但是，放在`typeof`後面，就不報錯了，而是返回`undefined`。

實際程式設計中，這個特點通常用在判斷語句。

```javascript
// 錯誤的寫法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正確的寫法
if (typeof v === "undefined") {
  // ...
}
```

物件返回`object`。

```javascript
typeof window // "object"
typeof {} // "object"
typeof [] // "object"
```

上面程式碼中，空陣列（`[]`）的型別也是`object`，這表示在 JavaScript 內部，陣列本質上只是一種特殊的物件。這裡順便提一下，`instanceof`運算子可以區分陣列和物件。`instanceof`運算子的詳細解釋，請見《面向物件程式設計》一章。

```javascript
var o = {};
var a = [];

o instanceof Array // false
a instanceof Array // true
```

`null`返回`object`。

```javascript
typeof null // "object"
```

`null`的型別是`object`，這是由於歷史原因造成的。1995年的 JavaScript 語言第一版，只設計了五種資料型別（物件、整數、浮點數、字串和布林值），沒考慮`null`，只把它當作`object`的一種特殊值。後來`null`獨立出來，作為一種單獨的資料型別，為了相容以前的程式碼，`typeof null`返回`object`就沒法改變了。

## 參考連結

- Axel Rauschmayer, [Improving the JavaScript typeof operator](http://www.2ality.com/2011/11/improving-typeof.html)
