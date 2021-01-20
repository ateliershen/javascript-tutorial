# 包裝物件

## 定義

物件是 JavaScript 語言最主要的資料型別，三種原始型別的值——數值、字串、布林值——在一定條件下，也會自動轉為物件，也就是原始型別的“包裝物件”（wrapper）。

所謂“包裝物件”，指的是與數值、字串、布林值分別相對應的`Number`、`String`、`Boolean`三個原生物件。這三個原生物件可以把原始型別的值變成（包裝成）物件。

```javascript
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);

typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```

上面程式碼中，基於原始型別的值，生成了三個對應的包裝物件。可以看到，`v1`、`v2`、`v3`都是物件，且與對應的簡單型別值不相等。

包裝物件的設計目的，首先是使得“物件”這種型別可以覆蓋 JavaScript 所有的值，整門語言有一個通用的資料模型，其次是使得原始型別的值也有辦法呼叫自己的方法。

`Number`、`String`和`Boolean`這三個原生物件，如果不作為建構函式呼叫（即呼叫時不加`new`），而是作為普通函式呼叫，常常用於將任意型別的值轉為數值、字串和布林值。

```javascript
// 字串轉為數值
Number('123') // 123

// 數值轉為字串
String(123) // "123"

// 數值轉為布林值
Boolean(123) // true
```

上面這種資料型別的轉換，詳見《資料型別轉換》一節。

總結一下，這三個物件作為建構函式使用（帶有`new`）時，可以將原始型別的值轉為物件；作為普通函式使用時（不帶有`new`），可以將任意型別的值，轉為原始型別的值。

## 例項方法

三種包裝物件各自提供了許多例項方法，詳見後文。這裡介紹兩種它們共同具有、從`Object`物件繼承的方法：`valueOf()`和`toString()`。

### valueOf()

`valueOf()`方法返回包裝物件例項對應的原始型別的值。

```javascript
new Number(123).valueOf()  // 123
new String('abc').valueOf() // "abc"
new Boolean(true).valueOf() // true
```

### toString()

`toString()`方法返回對應的字串形式。

```javascript
new Number(123).toString() // "123"
new String('abc').toString() // "abc"
new Boolean(true).toString() // "true"
```

## 原始型別與例項物件的自動轉換

某些場合，原始型別的值會自動當作包裝物件呼叫，即呼叫包裝物件的屬性和方法。這時，JavaScript 引擎會自動將原始型別的值轉為包裝物件例項，並在使用後立刻銷燬例項。

比如，字串可以呼叫`length`屬性，返回字串的長度。

```javascript
'abc'.length // 3
```

上面程式碼中，`abc`是一個字串，本身不是物件，不能呼叫`length`屬性。JavaScript 引擎自動將其轉為包裝物件，在這個物件上呼叫`length`屬性。呼叫結束後，這個臨時物件就會被銷燬。這就叫原始型別與例項物件的自動轉換。

```javascript
var str = 'abc';
str.length // 3

// 等同於
var strObj = new String(str)
// String {
//   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
// }
strObj.length // 3
```

上面程式碼中，字串`abc`的包裝物件提供了多個屬性，`length`只是其中之一。

自動轉換生成的包裝物件是隻讀的，無法修改。所以，字串無法新增新屬性。

```javascript
var s = 'Hello World';
s.x = 123;
s.x // undefined
```

上面程式碼為字串`s`添加了一個`x`屬性，結果無效，總是返回`undefined`。

另一方面，呼叫結束後，包裝物件例項會自動銷燬。這意味著，下一次呼叫字串的屬性時，實際是呼叫一個新生成的物件，而不是上一次呼叫時生成的那個物件，所以取不到賦值在上一個物件的屬性。如果要為字串新增屬性，只有在它的原型物件`String.prototype`上定義（參見《面向物件程式設計》章節）。

## 自定義方法

除了原生的例項方法，包裝物件還可以自定義方法和屬性，供原始型別的值直接呼叫。

比如，我們可以新增一個`double`方法，使得字串和數字翻倍。

```javascript
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double() // 246
```

上面程式碼在`String`和`Number`這兩個物件的原型上面，分別自定義了一個方法，從而可以在所有例項物件上呼叫。注意，最後一行的`123`外面必須要加上圓括號，否則後面的點運算子（`.`）會被解釋成小數點。

