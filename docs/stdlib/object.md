# Object 物件

## 概述

JavaScript 原生提供`Object`物件（注意起首的`O`是大寫），本章介紹該物件原生的各種方法。

JavaScript 的所有其他物件都繼承自`Object`物件，即那些物件都是`Object`的例項。

`Object`物件的原生方法分成兩類：`Object`本身的方法與`Object`的例項方法。

**（1）`Object`物件本身的方法**

所謂“本身的方法”就是直接定義在`Object`物件的方法。

```javascript
Object.print = function (o) { console.log(o) };
```

上面程式碼中，`print`方法就是直接定義在`Object`物件上。

**（2）`Object`的例項方法**

所謂例項方法就是定義在`Object`原型物件`Object.prototype`上的方法。它可以被`Object`例項直接使用。

```javascript
Object.prototype.print = function () {
  console.log(this);
};

var obj = new Object();
obj.print() // Object
```

上面程式碼中，`Object.prototype`定義了一個`print`方法，然後生成一個`Object`的例項`obj`。`obj`直接繼承了`Object.prototype`的屬性和方法，可以直接使用`obj.print`呼叫`print`方法。也就是說，`obj`物件的`print`方法實質上就是呼叫`Object.prototype.print`方法。

關於原型物件`object.prototype`的詳細解釋，參見《面向物件程式設計》章節。這裡只要知道，凡是定義在`Object.prototype`物件上面的屬性和方法，將被所有例項物件共享就可以了。

以下先介紹`Object`作為函式的用法，然後再介紹`Object`物件的原生方法，分成物件自身的方法（又稱為“靜態方法”）和例項方法兩部分。

## Object()

`Object`本身是一個函式，可以當作工具方法使用，將任意值轉為物件。這個方法常用於保證某個值一定是物件。

如果引數為空（或者為`undefined`和`null`），`Object()`返回一個空物件。

```javascript
var obj = Object();
// 等同於
var obj = Object(undefined);
var obj = Object(null);

obj instanceof Object // true
```

上面程式碼的含義，是將`undefined`和`null`轉為物件，結果得到了一個空物件`obj`。

`instanceof`運算子用來驗證，一個物件是否為指定的建構函式的例項。`obj instanceof Object`返回`true`，就表示`obj`物件是`Object`的例項。

如果引數是原始型別的值，`Object`方法將其轉為對應的包裝物件的例項（參見《原始型別的包裝物件》一章）。

```javascript
var obj = Object(1);
obj instanceof Object // true
obj instanceof Number // true

var obj = Object('foo');
obj instanceof Object // true
obj instanceof String // true

var obj = Object(true);
obj instanceof Object // true
obj instanceof Boolean // true
```

上面程式碼中，`Object`函式的引數是各種原始型別的值，轉換成物件就是原始型別值對應的包裝物件。

如果`Object`方法的引數是一個物件，它總是返回該物件，即不用轉換。

```javascript
var arr = [];
var obj = Object(arr); // 返回原陣列
obj === arr // true

var value = {};
var obj = Object(value) // 返回原物件
obj === value // true

var fn = function () {};
var obj = Object(fn); // 返回原函式
obj === fn // true
```

利用這一點，可以寫一個判斷變數是否為物件的函式。

```javascript
function isObject(value) {
  return value === Object(value);
}

isObject([]) // true
isObject(true) // false
```

## Object 建構函式

`Object`不僅可以當作工具函式使用，還可以當作建構函式使用，即前面可以使用`new`命令。

`Object`建構函式的首要用途，是直接透過它來生成新物件。

```javascript
var obj = new Object();
```

> 注意，透過`var obj = new Object()`的寫法生成新物件，與字面量的寫法`var obj = {}`是等價的。或者說，後者只是前者的一種簡便寫法。

`Object`建構函式的用法與工具方法很相似，幾乎一模一樣。使用時，可以接受一個引數，如果該引數是一個物件，則直接返回這個物件；如果是一個原始型別的值，則返回該值對應的包裝物件（詳見《包裝物件》一章）。

```javascript
var o1 = {a: 1};
var o2 = new Object(o1);
o1 === o2 // true

var obj = new Object(123);
obj instanceof Number // true
```

雖然用法相似，但是`Object(value)`與`new Object(value)`兩者的語義是不同的，`Object(value)`表示將`value`轉成一個物件，`new Object(value)`則表示新生成一個物件，它的值是`value`。

## Object 的靜態方法

所謂“靜態方法”，是指部署在`Object`物件自身的方法。

### Object.keys()，Object.getOwnPropertyNames()

`Object.keys`方法和`Object.getOwnPropertyNames`方法都用來遍歷物件的屬性。

`Object.keys`方法的引數是一個物件，返回一個數組。該陣列的成員都是該物件自身的（而不是繼承的）所有屬性名。

```javascript
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj) // ["p1", "p2"]
```

`Object.getOwnPropertyNames`方法與`Object.keys`類似，也是接受一個物件作為引數，返回一個數組，包含了該物件自身的所有屬性名。

```javascript
var obj = {
  p1: 123,
  p2: 456
};

Object.getOwnPropertyNames(obj) // ["p1", "p2"]
```

對於一般的物件來說，`Object.keys()`和`Object.getOwnPropertyNames()`返回的結果是一樣的。只有涉及不可列舉屬性時，才會有不一樣的結果。`Object.keys`方法只返回可列舉的屬性（詳見《物件屬性的描述物件》一章），`Object.getOwnPropertyNames`方法還返回不可列舉的屬性名。

```javascript
var a = ['Hello', 'World'];

Object.keys(a) // ["0", "1"]
Object.getOwnPropertyNames(a) // ["0", "1", "length"]
```

上面程式碼中，陣列的`length`屬性是不可列舉的屬性，所以只出現在`Object.getOwnPropertyNames`方法的返回結果中。

由於 JavaScript 沒有提供計算物件屬性個數的方法，所以可以用這兩個方法代替。

```javascript
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj).length // 2
Object.getOwnPropertyNames(obj).length // 2
```

一般情況下，幾乎總是使用`Object.keys`方法，遍歷物件的屬性。

### 其他方法

除了上面提到的兩個方法，`Object`還有不少其他靜態方法，將在後文逐一詳細介紹。

**（1）物件屬性模型的相關方法**

- `Object.getOwnPropertyDescriptor()`：獲取某個屬性的描述物件。
- `Object.defineProperty()`：透過描述物件，定義某個屬性。
- `Object.defineProperties()`：透過描述物件，定義多個屬性。

**（2）控制物件狀態的方法**

- `Object.preventExtensions()`：防止物件擴充套件。
- `Object.isExtensible()`：判斷物件是否可擴充套件。
- `Object.seal()`：禁止物件配置。
- `Object.isSealed()`：判斷一個物件是否可配置。
- `Object.freeze()`：凍結一個物件。
- `Object.isFrozen()`：判斷一個物件是否被凍結。

**（3）原型鏈相關方法**

- `Object.create()`：該方法可以指定原型物件和屬性，返回一個新的物件。
- `Object.getPrototypeOf()`：獲取物件的`Prototype`物件。

## Object 的例項方法

除了靜態方法，還有不少方法定義在`Object.prototype`物件。它們稱為例項方法，所有`Object`的例項物件都繼承了這些方法。

`Object`例項物件的方法，主要有以下六個。

- `Object.prototype.valueOf()`：返回當前物件對應的值。
- `Object.prototype.toString()`：返回當前物件對應的字串形式。
- `Object.prototype.toLocaleString()`：返回當前物件對應的本地字串形式。
- `Object.prototype.hasOwnProperty()`：判斷某個屬性是否為當前物件自身的屬性，還是繼承自原型物件的屬性。
- `Object.prototype.isPrototypeOf()`：判斷當前物件是否為另一個物件的原型。
- `Object.prototype.propertyIsEnumerable()`：判斷某個屬性是否可列舉。

本節介紹前四個方法，另外兩個方法將在後文相關章節介紹。

### Object.prototype.valueOf()

`valueOf`方法的作用是返回一個物件的“值”，預設情況下返回物件本身。

```javascript
var obj = new Object();
obj.valueOf() === obj // true
```

上面程式碼比較`obj.valueOf()`與`obj`本身，兩者是一樣的。

`valueOf`方法的主要用途是，JavaScript 自動型別轉換時會預設呼叫這個方法（詳見《資料型別轉換》一章）。

```javascript
var obj = new Object();
1 + obj // "1[object Object]"
```

上面程式碼將物件`obj`與數字`1`相加，這時 JavaScript 就會預設呼叫`valueOf()`方法，求出`obj`的值再與`1`相加。所以，如果自定義`valueOf`方法，就可以得到想要的結果。

```javascript
var obj = new Object();
obj.valueOf = function () {
  return 2;
};

1 + obj // 3
```

上面程式碼自定義了`obj`物件的`valueOf`方法，於是`1 + obj`就得到了`3`。這種方法就相當於用自定義的`obj.valueOf`，覆蓋`Object.prototype.valueOf`。

### Object.prototype.toString()

`toString`方法的作用是返回一個物件的字串形式，預設情況下返回型別字串。

```javascript
var o1 = new Object();
o1.toString() // "[object Object]"

var o2 = {a:1};
o2.toString() // "[object Object]"
```

上面程式碼表示，對於一個物件呼叫`toString`方法，會返回字串`[object Object]`，該字串說明物件的型別。

字串`[object Object]`本身沒有太大的用處，但是透過自定義`toString`方法，可以讓物件在自動型別轉換時，得到想要的字串形式。

```javascript
var obj = new Object();

obj.toString = function () {
  return 'hello';
};

obj + ' ' + 'world' // "hello world"
```

上面程式碼表示，當物件用於字串加法時，會自動呼叫`toString`方法。由於自定義了`toString`方法，所以返回字串`hello world`。

陣列、字串、函式、Date 物件都分別部署了自定義的`toString`方法，覆蓋了`Object.prototype.toString`方法。

```javascript
[1, 2, 3].toString() // "1,2,3"

'123'.toString() // "123"

(function () {
  return 123;
}).toString()
// "function () {
//   return 123;
// }"

(new Date()).toString()
// "Tue May 10 2016 09:11:31 GMT+0800 (CST)"
```

上面程式碼中，陣列、字串、函式、Date 物件呼叫`toString`方法，並不會返回`[object Object]`，因為它們都自定義了`toString`方法，覆蓋原始方法。

### toString() 的應用：判斷資料型別

`Object.prototype.toString`方法返回物件的型別字串，因此可以用來判斷一個值的型別。

```javascript
var obj = {};
obj.toString() // "[object Object]"
```

上面程式碼呼叫空物件的`toString`方法，結果返回一個字串`object Object`，其中第二個`Object`表示該值的建構函式。這是一個十分有用的判斷資料型別的方法。

由於例項物件可能會自定義`toString`方法，覆蓋掉`Object.prototype.toString`方法，所以為了得到型別字串，最好直接使用`Object.prototype.toString`方法。透過函式的`call`方法，可以在任意值上呼叫這個方法，幫助我們判斷這個值的型別。

```javascript
Object.prototype.toString.call(value)
```

上面程式碼表示對`value`這個值呼叫`Object.prototype.toString`方法。

不同資料型別的`Object.prototype.toString`方法返回值如下。

- 數值：返回`[object Number]`。
- 字串：返回`[object String]`。
- 布林值：返回`[object Boolean]`。
- undefined：返回`[object Undefined]`。
- null：返回`[object Null]`。
- 陣列：返回`[object Array]`。
- arguments 物件：返回`[object Arguments]`。
- 函式：返回`[object Function]`。
- Error 物件：返回`[object Error]`。
- Date 物件：返回`[object Date]`。
- RegExp 物件：返回`[object RegExp]`。
- 其他物件：返回`[object Object]`。

這就是說，`Object.prototype.toString`可以看出一個值到底是什麼型別。

```javascript
Object.prototype.toString.call(2) // "[object Number]"
Object.prototype.toString.call('') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(Math) // "[object Math]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call([]) // "[object Array]"
```

利用這個特性，可以寫出一個比`typeof`運算子更準確的型別判斷函式。

```javascript
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

type({}); // "object"
type([]); // "array"
type(5); // "number"
type(null); // "null"
type(); // "undefined"
type(/abcd/); // "regex"
type(new Date()); // "date"
```

在上面這個`type`函式的基礎上，還可以加上專門判斷某種型別資料的方法。

```javascript
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

['Null',
 'Undefined',
 'Object',
 'Array',
 'String',
 'Number',
 'Boolean',
 'Function',
 'RegExp'
].forEach(function (t) {
  type['is' + t] = function (o) {
    return type(o) === t.toLowerCase();
  };
});

type.isObject({}) // true
type.isNumber(NaN) // true
type.isRegExp(/abc/) // true
```

### Object.prototype.toLocaleString()

`Object.prototype.toLocaleString`方法與`toString`的返回結果相同，也是返回一個值的字串形式。

```javascript
var obj = {};
obj.toString(obj) // "[object Object]"
obj.toLocaleString(obj) // "[object Object]"
```

這個方法的主要作用是留出一個介面，讓各種不同的物件實現自己版本的`toLocaleString`，用來返回針對某些地域的特定的值。

```javascript
var person = {
  toString: function () {
    return 'Henry Norman Bethune';
  },
  toLocaleString: function () {
    return '白求恩';
  }
};

person.toString() // Henry Norman Bethune
person.toLocaleString() // 白求恩
```

上面程式碼中，`toString()`方法返回物件的一般字串形式，`toLocaleString()`方法返回本地的字串形式。

目前，主要有三個物件自定義了`toLocaleString`方法。

- Array.prototype.toLocaleString()
- Number.prototype.toLocaleString()
- Date.prototype.toLocaleString()

舉例來說，日期的例項物件的`toString`和`toLocaleString`返回值就不一樣，而且`toLocaleString`的返回值跟使用者設定的所在地域相關。

```javascript
var date = new Date();
date.toString() // "Tue Jan 01 2018 12:01:33 GMT+0800 (CST)"
date.toLocaleString() // "1/01/2018, 12:01:33 PM"
```

### Object.prototype.hasOwnProperty()

`Object.prototype.hasOwnProperty`方法接受一個字串作為引數，返回一個布林值，表示該例項物件自身是否具有該屬性。

```javascript
var obj = {
  p: 123
};

obj.hasOwnProperty('p') // true
obj.hasOwnProperty('toString') // false
```

上面程式碼中，物件`obj`自身具有`p`屬性，所以返回`true`。`toString`屬性是繼承的，所以返回`false`。

## 參考連結

- Axel Rauschmayer, [Protecting objects in JavaScript](http://www.2ality.com/2013/08/protecting-objects.html)
- kangax, [Understanding delete](http://perfectionkills.com/understanding-delete/)
- Jon Bretman, [Type Checking in JavaScript](http://techblog.badoo.com/blog/2013/11/01/type-checking-in-javascript/)
- Cody Lindley, [Thinking About ECMAScript 5 Parts](http://tech.pro/tutorial/1671/thinking-about-ecmascript-5-parts)
- Bjorn Tipling, [Advanced objects in JavaScript](http://bjorn.tipling.com/advanced-objects-in-javascript)
- Javier Márquez, [JavaScript properties are enumerable, writable and configurable](http://arqex.com/967/javascript-properties-enumerable-writable-configurable)
- Sella Rafaeli, [Native JavaScript Data-Binding](http://www.sellarafaeli.com/blog/native_javascript_data_binding): 使用存取函式實現model與view的雙向繫結
- Lea Verou, [Copying object properties, the robust way](http://lea.verou.me/2015/08/copying-properties-the-robust-way/)
