# Object 物件的相關方法

JavaScript 在`Object`物件上面，提供了很多相關方法，處理面向物件程式設計的相關操作。本章介紹這些方法。

## Object.getPrototypeOf()

`Object.getPrototypeOf`方法返回引數物件的原型。這是獲取原型物件的標準方法。

```javascript
var F = function () {};
var f = new F();
Object.getPrototypeOf(f) === F.prototype // true
```

上面程式碼中，例項物件`f`的原型是`F.prototype`。

下面是幾種特殊物件的原型。

```javascript
// 空物件的原型是 Object.prototype
Object.getPrototypeOf({}) === Object.prototype // true

// Object.prototype 的原型是 null
Object.getPrototypeOf(Object.prototype) === null // true

// 函式的原型是 Function.prototype
function f() {}
Object.getPrototypeOf(f) === Function.prototype // true
```

## Object.setPrototypeOf()

`Object.setPrototypeOf`方法為引數物件設定原型，返回該引數物件。它接受兩個引數，第一個是現有物件，第二個是原型物件。

```javascript
var a = {};
var b = {x: 1};
Object.setPrototypeOf(a, b);

Object.getPrototypeOf(a) === b // true
a.x // 1
```

上面程式碼中，`Object.setPrototypeOf`方法將物件`a`的原型，設定為物件`b`，因此`a`可以共享`b`的屬性。

`new`命令可以使用`Object.setPrototypeOf`方法模擬。

```javascript
var F = function () {
  this.foo = 'bar';
};

var f = new F();
// 等同於
var f = Object.setPrototypeOf({}, F.prototype);
F.call(f);
```

上面程式碼中，`new`命令新建例項物件，其實可以分成兩步。第一步，將一個空物件的原型設為建構函式的`prototype`屬性（上例是`F.prototype`）；第二步，將建構函式內部的`this`繫結這個空物件，然後執行建構函式，使得定義在`this`上面的方法和屬性（上例是`this.foo`），都轉移到這個空物件上。

## Object.create()

生成例項物件的常用方法是，使用`new`命令讓建構函式返回一個例項。但是很多時候，只能拿到一個例項物件，它可能根本不是由構建函式生成的，那麼能不能從一個例項物件，生成另一個例項物件呢？

JavaScript 提供了`Object.create()`方法，用來滿足這種需求。該方法接受一個物件作為引數，然後以它為原型，返回一個例項物件。該例項完全繼承原型物件的屬性。

```javascript
// 原型物件
var A = {
  print: function () {
    console.log('hello');
  }
};

// 例項物件
var B = Object.create(A);

Object.getPrototypeOf(B) === A // true
B.print() // hello
B.print === A.print // true
```

上面程式碼中，`Object.create()`方法以`A`物件為原型，生成了`B`物件。`B`繼承了`A`的所有屬性和方法。

實際上，`Object.create()`方法可以用下面的程式碼代替。

```javascript
if (typeof Object.create !== 'function') {
  Object.create = function (obj) {
    function F() {}
    F.prototype = obj;
    return new F();
  };
}
```

上面程式碼表明，`Object.create()`方法的實質是新建一個空的建構函式`F`，然後讓`F.prototype`屬性指向引數物件`obj`，最後返回一個`F`的例項，從而實現讓該例項繼承`obj`的屬性。

下面三種方式生成的新物件是等價的。

```javascript
var obj1 = Object.create({});
var obj2 = Object.create(Object.prototype);
var obj3 = new Object();
```

如果想要生成一個不繼承任何屬性（比如沒有`toString()`和`valueOf()`方法）的物件，可以將`Object.create()`的引數設為`null`。

```javascript
var obj = Object.create(null);

obj.valueOf()
// TypeError: Object [object Object] has no method 'valueOf'
```

上面程式碼中，物件`obj`的原型是`null`，它就不具備一些定義在`Object.prototype`物件上面的屬性，比如`valueOf()`方法。

使用`Object.create()`方法的時候，必須提供物件原型，即引數不能為空，或者不是物件，否則會報錯。

```javascript
Object.create()
// TypeError: Object prototype may only be an Object or null
Object.create(123)
// TypeError: Object prototype may only be an Object or null
```

`Object.create()`方法生成的新物件，動態繼承了原型。在原型上新增或修改任何方法，會立刻反映在新物件之上。

```javascript
var obj1 = { p: 1 };
var obj2 = Object.create(obj1);

obj1.p = 2;
obj2.p // 2
```

上面程式碼中，修改物件原型`obj1`會影響到例項物件`obj2`。

除了物件的原型，`Object.create()`方法還可以接受第二個引數。該引數是一個屬性描述物件，它所描述的物件屬性，會新增到例項物件，作為該物件自身的屬性。

```javascript
var obj = Object.create({}, {
  p1: {
    value: 123,
    enumerable: true,
    configurable: true,
    writable: true,
  },
  p2: {
    value: 'abc',
    enumerable: true,
    configurable: true,
    writable: true,
  }
});

// 等同於
var obj = Object.create({});
obj.p1 = 123;
obj.p2 = 'abc';
```

`Object.create()`方法生成的物件，繼承了它的原型物件的建構函式。

```javascript
function A() {}
var a = new A();
var b = Object.create(a);

b.constructor === A // true
b instanceof A // true
```

上面程式碼中，`b`物件的原型是`a`物件，因此繼承了`a`物件的建構函式`A`。

## Object.prototype.isPrototypeOf()

例項物件的`isPrototypeOf`方法，用來判斷該物件是否為引數物件的原型。

```javascript
var o1 = {};
var o2 = Object.create(o1);
var o3 = Object.create(o2);

o2.isPrototypeOf(o3) // true
o1.isPrototypeOf(o3) // true
```

上面程式碼中，`o1`和`o2`都是`o3`的原型。這表明只要例項物件處在引數物件的原型鏈上，`isPrototypeOf`方法都返回`true`。

```javascript
Object.prototype.isPrototypeOf({}) // true
Object.prototype.isPrototypeOf([]) // true
Object.prototype.isPrototypeOf(/xyz/) // true
Object.prototype.isPrototypeOf(Object.create(null)) // false
```

上面程式碼中，由於`Object.prototype`處於原型鏈的最頂端，所以對各種例項都返回`true`，只有直接繼承自`null`的物件除外。

## Object.prototype.\_\_proto\_\_

例項物件的`__proto__`屬性（前後各兩個下劃線），返回該物件的原型。該屬性可讀寫。

```javascript
var obj = {};
var p = {};

obj.__proto__ = p;
Object.getPrototypeOf(obj) === p // true
```

上面程式碼透過`__proto__`屬性，將`p`物件設為`obj`物件的原型。

根據語言標準，`__proto__`屬性只有瀏覽器才需要部署，其他環境可以沒有這個屬性。它前後的兩根下劃線，表明它本質是一個內部屬性，不應該對使用者暴露。因此，應該儘量少用這個屬性，而是用`Object.getPrototypeOf()`和`Object.setPrototypeOf()`，進行原型物件的讀寫操作。

原型鏈可以用`__proto__`很直觀地表示。

```javascript
var A = {
  name: '張三'
};
var B = {
  name: '李四'
};

var proto = {
  print: function () {
    console.log(this.name);
  }
};

A.__proto__ = proto;
B.__proto__ = proto;

A.print() // 張三
B.print() // 李四

A.print === B.print // true
A.print === proto.print // true
B.print === proto.print // true
```

上面程式碼中，`A`物件和`B`物件的原型都是`proto`物件，它們都共享`proto`物件的`print`方法。也就是說，`A`和`B`的`print`方法，都是在呼叫`proto`物件的`print`方法。

## 獲取原型物件方法的比較

如前所述，`__proto__`屬性指向當前物件的原型物件，即建構函式的`prototype`屬性。

```javascript
var obj = new Object();

obj.__proto__ === Object.prototype
// true
obj.__proto__ === obj.constructor.prototype
// true
```

上面程式碼首先新建了一個物件`obj`，它的`__proto__`屬性，指向建構函式（`Object`或`obj.constructor`）的`prototype`屬性。

因此，獲取例項物件`obj`的原型物件，有三種方法。

- `obj.__proto__`
- `obj.constructor.prototype`
- `Object.getPrototypeOf(obj)`

上面三種方法之中，前兩種都不是很可靠。`__proto__`屬性只有瀏覽器才需要部署，其他環境可以不部署。而`obj.constructor.prototype`在手動改變原型物件時，可能會失效。

```javascript
var P = function () {};
var p = new P();

var C = function () {};
C.prototype = p;
var c = new C();

c.constructor.prototype === p // false
```

上面程式碼中，建構函式`C`的原型物件被改成了`p`，但是例項物件的`c.constructor.prototype`卻沒有指向`p`。所以，在改變原型物件時，一般要同時設定`constructor`屬性。

```javascript
C.prototype = p;
C.prototype.constructor = C;

var c = new C();
c.constructor.prototype === p // true
```

因此，推薦使用第三種`Object.getPrototypeOf`方法，獲取原型物件。

## Object.getOwnPropertyNames()

`Object.getOwnPropertyNames`方法返回一個數組，成員是引數物件本身的所有屬性的鍵名，不包含繼承的屬性鍵名。

```javascript
Object.getOwnPropertyNames(Date)
// ["parse", "arguments", "UTC", "caller", "name", "prototype", "now", "length"]
```

上面程式碼中，`Object.getOwnPropertyNames`方法返回`Date`所有自身的屬性名。

物件本身的屬性之中，有的是可以遍歷的（enumerable），有的是不可以遍歷的。`Object.getOwnPropertyNames`方法返回所有鍵名，不管是否可以遍歷。只獲取那些可以遍歷的屬性，使用`Object.keys`方法。

```javascript
Object.keys(Date) // []
```

上面程式碼表明，`Date`物件所有自身的屬性，都是不可以遍歷的。

## Object.prototype.hasOwnProperty()

物件例項的`hasOwnProperty`方法返回一個布林值，用於判斷某個屬性定義在物件自身，還是定義在原型鏈上。

```javascript
Date.hasOwnProperty('length') // true
Date.hasOwnProperty('toString') // false
```

上面程式碼表明，`Date.length`（建構函式`Date`可以接受多少個引數）是`Date`自身的屬性，`Date.toString`是繼承的屬性。

另外，`hasOwnProperty`方法是 JavaScript 之中唯一一個處理物件屬性時，不會遍歷原型鏈的方法。

## in 運算子和 for...in 迴圈

`in`運算子返回一個布林值，表示一個物件是否具有某個屬性。它不區分該屬性是物件自身的屬性，還是繼承的屬性。

```javascript
'length' in Date // true
'toString' in Date // true
```

`in`運算子常用於檢查一個屬性是否存在。

獲得物件的所有可遍歷屬性（不管是自身的還是繼承的），可以使用`for...in`迴圈。

```javascript
var o1 = { p1: 123 };

var o2 = Object.create(o1, {
  p2: { value: "abc", enumerable: true }
});

for (p in o2) {
  console.info(p);
}
// p2
// p1
```

上面程式碼中，物件`o2`的`p2`屬性是自身的，`p1`屬性是繼承的。這兩個屬性都會被`for...in`迴圈遍歷。

為了在`for...in`迴圈中獲得物件自身的屬性，可以採用`hasOwnProperty`方法判斷一下。

```javascript
for ( var name in object ) {
  if ( object.hasOwnProperty(name) ) {
    /* loop code */
  }
}
```

獲得物件的所有屬性（不管是自身的還是繼承的，也不管是否可列舉），可以使用下面的函式。

```javascript
function inheritedPropertyNames(obj) {
  var props = {};
  while(obj) {
    Object.getOwnPropertyNames(obj).forEach(function(p) {
      props[p] = true;
    });
    obj = Object.getPrototypeOf(obj);
  }
  return Object.getOwnPropertyNames(props);
}
```

上面程式碼依次獲取`obj`物件的每一級原型物件“自身”的屬性，從而獲取`obj`物件的“所有”屬性，不管是否可遍歷。

下面是一個例子，列出`Date`物件的所有屬性。

```javascript
inheritedPropertyNames(Date)
// [
//  "caller",
//  "constructor",
//  "toString",
//  "UTC",
//  ...
// ]
```

## 物件的複製

如果要複製一個物件，需要做到下面兩件事情。

- 確保複製後的物件，與原物件具有同樣的原型。
- 確保複製後的物件，與原物件具有同樣的例項屬性。

下面就是根據上面兩點，實現的物件複製函式。

```javascript
function copyObject(orig) {
  var copy = Object.create(Object.getPrototypeOf(orig));
  copyOwnPropertiesFrom(copy, orig);
  return copy;
}

function copyOwnPropertiesFrom(target, source) {
  Object
    .getOwnPropertyNames(source)
    .forEach(function (propKey) {
      var desc = Object.getOwnPropertyDescriptor(source, propKey);
      Object.defineProperty(target, propKey, desc);
    });
  return target;
}
```

另一種更簡單的寫法，是利用 ES2017 才引入標準的`Object.getOwnPropertyDescriptors`方法。

```javascript
function copyObject(orig) {
  return Object.create(
    Object.getPrototypeOf(orig),
    Object.getOwnPropertyDescriptors(orig)
  );
}
```

## 參考連結

- Dr. Axel Rauschmayer, [JavaScript properties: inheritance and enumerability](http://www.2ality.com/2011/07/js-properties.html)
