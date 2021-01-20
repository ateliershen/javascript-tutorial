# 物件的繼承

面向物件程式設計很重要的一個方面，就是物件的繼承。A 物件透過繼承 B 物件，就能直接擁有 B 物件的所有屬性和方法。這對於程式碼的複用是非常有用的。

大部分面向物件的程式語言，都是透過“類”（class）實現物件的繼承。傳統上，JavaScript 語言的繼承不透過 class，而是透過“原型物件”（prototype）實現，本章介紹 JavaScript 的原型鏈繼承。

ES6 引入了 class 語法，基於 class 的繼承不在這個教程介紹，請參閱《ES6 標準入門》一書的相關章節。

## 原型物件概述

### 建構函式的缺點

JavaScript 透過建構函式生成新物件，因此建構函式可以視為物件的模板。例項物件的屬性和方法，可以定義在建構函式內部。

```javascript
function Cat (name, color) {
  this.name = name;
  this.color = color;
}

var cat1 = new Cat('大毛', '白色');

cat1.name // '大毛'
cat1.color // '白色'
```

上面程式碼中，`Cat`函式是一個建構函式，函式內部定義了`name`屬性和`color`屬性，所有例項物件（上例是`cat1`）都會生成這兩個屬性，即這兩個屬性會定義在例項物件上面。

透過建構函式為例項物件定義屬性，雖然很方便，但是有一個缺點。同一個建構函式的多個例項之間，無法共享屬性，從而造成對系統資源的浪費。

```javascript
function Cat(name, color) {
  this.name = name;
  this.color = color;
  this.meow = function () {
    console.log('喵喵');
  };
}

var cat1 = new Cat('大毛', '白色');
var cat2 = new Cat('二毛', '黑色');

cat1.meow === cat2.meow
// false
```

上面程式碼中，`cat1`和`cat2`是同一個建構函式的兩個例項，它們都具有`meow`方法。由於`meow`方法是生成在每個例項物件上面，所以兩個例項就生成了兩次。也就是說，每新建一個例項，就會新建一個`meow`方法。這既沒有必要，又浪費系統資源，因為所有`meow`方法都是同樣的行為，完全應該共享。

這個問題的解決方法，就是 JavaScript 的原型物件（prototype）。

### prototype 屬性的作用

JavaScript 繼承機制的設計思想就是，原型物件的所有屬性和方法，都能被例項物件共享。也就是說，如果屬性和方法定義在原型上，那麼所有例項物件就能共享，不僅節省了記憶體，還體現了例項物件之間的聯絡。

下面，先看怎麼為物件指定原型。JavaScript 規定，每個函式都有一個`prototype`屬性，指向一個物件。

```javascript
function f() {}
typeof f.prototype // "object"
```

上面程式碼中，函式`f`預設具有`prototype`屬性，指向一個物件。

對於普通函式來說，該屬性基本無用。但是，對於建構函式來說，生成例項的時候，該屬性會自動成為例項物件的原型。

```javascript
function Animal(name) {
  this.name = name;
}
Animal.prototype.color = 'white';

var cat1 = new Animal('大毛');
var cat2 = new Animal('二毛');

cat1.color // 'white'
cat2.color // 'white'
```

上面程式碼中，建構函式`Animal`的`prototype`屬性，就是例項物件`cat1`和`cat2`的原型物件。原型物件上新增一個`color`屬性，結果，例項物件都共享了該屬性。

原型物件的屬性不是例項物件自身的屬性。只要修改原型物件，變動就立刻會體現在**所有**例項物件上。

```javascript
Animal.prototype.color = 'yellow';

cat1.color // "yellow"
cat2.color // "yellow"
```

上面程式碼中，原型物件的`color`屬性的值變為`yellow`，兩個例項物件的`color`屬性立刻跟著變了。這是因為例項物件其實沒有`color`屬性，都是讀取原型物件的`color`屬性。也就是說，當例項物件本身沒有某個屬性或方法的時候，它會到原型物件去尋找該屬性或方法。這就是原型物件的特殊之處。

如果例項物件自身就有某個屬性或方法，它就不會再去原型物件尋找這個屬性或方法。

```javascript
cat1.color = 'black';

cat1.color // 'black'
cat2.color // 'yellow'
Animal.prototype.color // 'yellow';
```

上面程式碼中，例項物件`cat1`的`color`屬性改為`black`，就使得它不再去原型物件讀取`color`屬性，後者的值依然為`yellow`。

總結一下，原型物件的作用，就是定義所有例項物件共享的屬性和方法。這也是它被稱為原型物件的原因，而例項物件可以視作從原型物件衍生出來的子物件。

```javascript
Animal.prototype.walk = function () {
  console.log(this.name + ' is walking');
};
```

上面程式碼中，`Animal.prototype`物件上面定義了一個`walk`方法，這個方法將可以在所有`Animal`例項物件上面呼叫。

### 原型鏈

JavaScript 規定，所有物件都有自己的原型物件（prototype）。一方面，任何一個物件，都可以充當其他物件的原型；另一方面，由於原型物件也是物件，所以它也有自己的原型。因此，就會形成一個“原型鏈”（prototype chain）：物件到原型，再到原型的原型……

如果一層層地上溯，所有物件的原型最終都可以上溯到`Object.prototype`，即`Object`建構函式的`prototype`屬性。也就是說，所有物件都繼承了`Object.prototype`的屬性。這就是所有物件都有`valueOf`和`toString`方法的原因，因為這是從`Object.prototype`繼承的。

那麼，`Object.prototype`物件有沒有它的原型呢？回答是`Object.prototype`的原型是`null`。`null`沒有任何屬性和方法，也沒有自己的原型。因此，原型鏈的盡頭就是`null`。

```javascript
Object.getPrototypeOf(Object.prototype)
// null
```

上面程式碼表示，`Object.prototype`物件的原型是`null`，由於`null`沒有任何屬性，所以原型鏈到此為止。`Object.getPrototypeOf`方法返回引數物件的原型，具體介紹請看後文。

讀取物件的某個屬性時，JavaScript 引擎先尋找物件本身的屬性，如果找不到，就到它的原型去找，如果還是找不到，就到原型的原型去找。如果直到最頂層的`Object.prototype`還是找不到，則返回`undefined`。如果物件自身和它的原型，都定義了一個同名屬性，那麼優先讀取物件自身的屬性，這叫做“覆蓋”（overriding）。

注意，一級級向上，在整個原型鏈上尋找某個屬性，對效能是有影響的。所尋找的屬性在越上層的原型物件，對效能的影響越大。如果尋找某個不存在的屬性，將會遍歷整個原型鏈。

舉例來說，如果讓建構函式的`prototype`屬性指向一個數組，就意味著例項物件可以呼叫陣列方法。

```javascript
var MyArray = function () {};

MyArray.prototype = new Array();
MyArray.prototype.constructor = MyArray;

var mine = new MyArray();
mine.push(1, 2, 3);
mine.length // 3
mine instanceof Array // true
```

上面程式碼中，`mine`是建構函式`MyArray`的例項物件，由於`MyArray.prototype`指向一個數組例項，使得`mine`可以呼叫陣列方法（這些方法定義在陣列例項的`prototype`物件上面）。最後那行`instanceof`表示式，用來比較一個物件是否為某個建構函式的例項，結果就是證明`mine`為`Array`的例項，`instanceof`運算子的詳細解釋詳見後文。

上面程式碼還出現了原型物件的`constructor`屬性，這個屬性的含義下一節就來解釋。

### constructor 屬性

`prototype`物件有一個`constructor`屬性，預設指向`prototype`物件所在的建構函式。

```javascript
function P() {}
P.prototype.constructor === P // true
```

由於`constructor`屬性定義在`prototype`物件上面，意味著可以被所有例項物件繼承。

```javascript
function P() {}
var p = new P();

p.constructor === P // true
p.constructor === P.prototype.constructor // true
p.hasOwnProperty('constructor') // false
```

上面程式碼中，`p`是建構函式`P`的例項物件，但是`p`自身沒有`constructor`屬性，該屬性其實是讀取原型鏈上面的`P.prototype.constructor`屬性。

`constructor`屬性的作用是，可以得知某個例項物件，到底是哪一個建構函式產生的。

```javascript
function F() {};
var f = new F();

f.constructor === F // true
f.constructor === RegExp // false
```

上面程式碼中，`constructor`屬性確定了例項物件`f`的建構函式是`F`，而不是`RegExp`。

另一方面，有了`constructor`屬性，就可以從一個例項物件新建另一個例項。

```javascript
function Constr() {}
var x = new Constr();

var y = new x.constructor();
y instanceof Constr // true
```

上面程式碼中，`x`是建構函式`Constr`的例項，可以從`x.constructor`間接呼叫建構函式。這使得在例項方法中，呼叫自身的建構函式成為可能。

```javascript
Constr.prototype.createCopy = function () {
  return new this.constructor();
};
```

上面程式碼中，`createCopy`方法呼叫建構函式，新建另一個例項。

`constructor`屬性表示原型物件與建構函式之間的關聯關係，如果修改了原型物件，一般會同時修改`constructor`屬性，防止引用的時候出錯。

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.constructor === Person // true

Person.prototype = {
  method: function () {}
};

Person.prototype.constructor === Person // false
Person.prototype.constructor === Object // true
```

上面程式碼中，建構函式`Person`的原型物件改掉了，但是沒有修改`constructor`屬性，導致這個屬性不再指向`Person`。由於`Person`的新原型是一個普通物件，而普通物件的`constructor`屬性指向`Object`建構函式，導致`Person.prototype.constructor`變成了`Object`。

所以，修改原型物件時，一般要同時修改`constructor`屬性的指向。

```javascript
// 壞的寫法
C.prototype = {
  method1: function (...) { ... },
  // ...
};

// 好的寫法
C.prototype = {
  constructor: C,
  method1: function (...) { ... },
  // ...
};

// 更好的寫法
C.prototype.method1 = function (...) { ... };
```

上面程式碼中，要麼將`constructor`屬性重新指向原來的建構函式，要麼只在原型物件上新增方法，這樣可以保證`instanceof`運算子不會失真。

如果不能確定`constructor`屬性是什麼函式，還有一個辦法：透過`name`屬性，從例項得到建構函式的名稱。

```javascript
function Foo() {}
var f = new Foo();
f.constructor.name // "Foo"
```

## instanceof 運算子

`instanceof`運算子返回一個布林值，表示物件是否為某個建構函式的例項。

```javascript
var v = new Vehicle();
v instanceof Vehicle // true
```

上面程式碼中，物件`v`是建構函式`Vehicle`的例項，所以返回`true`。

`instanceof`運算子的左邊是例項物件，右邊是建構函式。它會檢查右邊建構函式的原型物件（prototype），是否在左邊物件的原型鏈上。因此，下面兩種寫法是等價的。

```javascript
v instanceof Vehicle
// 等同於
Vehicle.prototype.isPrototypeOf(v)
```

上面程式碼中，`Vehicle`是物件`v`的建構函式，它的原型物件是`Vehicle.prototype`，`isPrototypeOf()`方法是 JavaScript 提供的原生方法，用於檢查某個物件是否為另一個物件的原型，詳細解釋見後文。

由於`instanceof`檢查整個原型鏈，因此同一個例項物件，可能會對多個建構函式都返回`true`。

```javascript
var d = new Date();
d instanceof Date // true
d instanceof Object // true
```

上面程式碼中，`d`同時是`Date`和`Object`的例項，因此對這兩個建構函式都返回`true`。

由於任意物件（除了`null`）都是`Object`的例項，所以`instanceof`運算子可以判斷一個值是否為非`null`的物件。

```javascript
var obj = { foo: 123 };
obj instanceof Object // true

null instanceof Object // false
```

上面程式碼中，除了`null`，其他物件的`instanceOf Object`的運算結果都是`true`。

`instanceof`的原理是檢查右邊建構函式的`prototype`屬性，是否在左邊物件的原型鏈上。有一種特殊情況，就是左邊物件的原型鏈上，只有`null`物件。這時，`instanceof`判斷會失真。

```javascript
var obj = Object.create(null);
typeof obj // "object"
obj instanceof Object // false
```

上面程式碼中，`Object.create(null)`返回一個新物件`obj`，它的原型是`null`（`Object.create()`的詳細介紹見後文）。右邊的建構函式`Object`的`prototype`屬性，不在左邊的原型鏈上，因此`instanceof`就認為`obj`不是`Object`的例項。這是唯一的`instanceof`運算子判斷會失真的情況（一個物件的原型是`null`）。

`instanceof`運算子的一個用處，是判斷值的型別。

```javascript
var x = [1, 2, 3];
var y = {};
x instanceof Array // true
y instanceof Object // true
```

上面程式碼中，`instanceof`運算子判斷，變數`x`是陣列，變數`y`是物件。

注意，`instanceof`運算子只能用於物件，不適用原始型別的值。

```javascript
var s = 'hello';
s instanceof String // false
```

上面程式碼中，字串不是`String`物件的例項（因為字串不是物件），所以返回`false`。

此外，對於`undefined`和`null`，`instanceof`運算子總是返回`false`。

```javascript
undefined instanceof Object // false
null instanceof Object // false
```

利用`instanceof`運算子，還可以巧妙地解決，呼叫建構函式時，忘了加`new`命令的問題。

```javascript
function Fubar (foo, bar) {
  if (this instanceof Fubar) {
    this._foo = foo;
    this._bar = bar;
  } else {
    return new Fubar(foo, bar);
  }
}
```

上面程式碼使用`instanceof`運算子，在函式體內部判斷`this`關鍵字是否為建構函式`Fubar`的例項。如果不是，就表明忘了加`new`命令。

## 建構函式的繼承

讓一個建構函式繼承另一個建構函式，是非常常見的需求。這可以分成兩步實現。第一步是在子類的建構函式中，呼叫父類的建構函式。

```javascript
function Sub(value) {
  Super.call(this);
  this.prop = value;
}
```

上面程式碼中，`Sub`是子類的建構函式，`this`是子類的例項。在例項上呼叫父類的建構函式`Super`，就會讓子類例項具有父類例項的屬性。

第二步，是讓子類的原型指向父類的原型，這樣子類就可以繼承父類原型。

```javascript
Sub.prototype = Object.create(Super.prototype);
Sub.prototype.constructor = Sub;
Sub.prototype.method = '...';
```

上面程式碼中，`Sub.prototype`是子類的原型，要將它賦值為`Object.create(Super.prototype)`，而不是直接等於`Super.prototype`。否則後面兩行對`Sub.prototype`的操作，會連父類的原型`Super.prototype`一起修改掉。

另外一種寫法是`Sub.prototype`等於一個父類例項。

```javascript
Sub.prototype = new Super();
```

上面這種寫法也有繼承的效果，但是子類會具有父類例項的方法。有時，這可能不是我們需要的，所以不推薦使用這種寫法。

舉例來說，下面是一個`Shape`建構函式。

```javascript
function Shape() {
  this.x = 0;
  this.y = 0;
}

Shape.prototype.move = function (x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};
```

我們需要讓`Rectangle`建構函式繼承`Shape`。

```javascript
// 第一步，子類繼承父類的例項
function Rectangle() {
  Shape.call(this); // 呼叫父類建構函式
}
// 另一種寫法
function Rectangle() {
  this.base = Shape;
  this.base();
}

// 第二步，子類繼承父類的原型
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
```

採用這樣的寫法以後，`instanceof`運算子會對子類和父類的建構函式，都返回`true`。

```javascript
var rect = new Rectangle();

rect instanceof Rectangle  // true
rect instanceof Shape  // true
```

上面程式碼中，子類是整體繼承父類。有時只需要單個方法的繼承，這時可以採用下面的寫法。

```javascript
ClassB.prototype.print = function() {
  ClassA.prototype.print.call(this);
  // some code
}
```

上面程式碼中，子類`B`的`print`方法先呼叫父類`A`的`print`方法，再部署自己的程式碼。這就等於繼承了父類`A`的`print`方法。

## 多重繼承

JavaScript 不提供多重繼承功能，即不允許一個物件同時繼承多個物件。但是，可以透過變通方法，實現這個功能。

```javascript
function M1() {
  this.hello = 'hello';
}

function M2() {
  this.world = 'world';
}

function S() {
  M1.call(this);
  M2.call(this);
}

// 繼承 M1
S.prototype = Object.create(M1.prototype);
// 繼承鏈上加入 M2
Object.assign(S.prototype, M2.prototype);

// 指定建構函式
S.prototype.constructor = S;

var s = new S();
s.hello // 'hello'
s.world // 'world'
```

上面程式碼中，子類`S`同時繼承了父類`M1`和`M2`。這種模式又稱為 Mixin（混入）。

## 模組

隨著網站逐漸變成“網際網路應用程式”，嵌入網頁的 JavaScript 程式碼越來越龐大，越來越複雜。網頁越來越像桌面程式，需要一個團隊分工協作、進度管理、單元測試等等……開發者必須使用軟體工程的方法，管理網頁的業務邏輯。

JavaScript 模組化程式設計，已經成為一個迫切的需求。理想情況下，開發者只需要實現核心的業務邏輯，其他都可以載入別人已經寫好的模組。

但是，JavaScript 不是一種模組化程式語言，ES6 才開始支援“類”和“模組”。下面介紹傳統的做法，如何利用物件實現模組的效果。

### 基本的實現方法

模組是實現特定功能的一組屬性和方法的封裝。

簡單的做法是把模組寫成一個物件，所有的模組成員都放到這個物件裡面。

```javascript
var module1 = new Object({
　_count : 0,
　m1 : function (){
　　//...
　},
　m2 : function (){
  　//...
　}
});
```

上面的函式`m1`和`m2`，都封裝在`module1`物件裡。使用的時候，就是呼叫這個物件的屬性。

```javascript
module1.m1();
```

但是，這樣的寫法會暴露所有模組成員，內部狀態可以被外部改寫。比如，外部程式碼可以直接改變內部計數器的值。

```javascript
module1._count = 5;
```

### 封裝私有變數：建構函式的寫法

我們可以利用建構函式，封裝私有變數。

```javascript
function StringBuilder() {
  var buffer = [];

  this.add = function (str) {
     buffer.push(str);
  };

  this.toString = function () {
    return buffer.join('');
  };

}
```

上面程式碼中，`buffer`是模組的私有變數。一旦生成例項物件，外部是無法直接訪問`buffer`的。但是，這種方法將私有變數封裝在建構函式中，導致建構函式與例項物件是一體的，總是存在於記憶體之中，無法在使用完成後清除。這意味著，建構函式有雙重作用，既用來塑造例項物件，又用來儲存例項物件的資料，違背了建構函式與例項物件在資料上相分離的原則（即例項物件的資料，不應該儲存在例項物件以外）。同時，非常耗費記憶體。

```javascript
function StringBuilder() {
  this._buffer = [];
}

StringBuilder.prototype = {
  constructor: StringBuilder,
  add: function (str) {
    this._buffer.push(str);
  },
  toString: function () {
    return this._buffer.join('');
  }
};
```

這種方法將私有變數放入例項物件中，好處是看上去更自然，但是它的私有變數可以從外部讀寫，不是很安全。

### 封裝私有變數：立即執行函式的寫法

另一種做法是使用“立即執行函式”（Immediately-Invoked Function Expression，IIFE），將相關的屬性和方法封裝在一個函式作用域裡面，可以達到不暴露私有成員的目的。

```javascript
var module1 = (function () {
　var _count = 0;
　var m1 = function () {
　  //...
　};
　var m2 = function () {
　　//...
　};
　return {
　　m1 : m1,
　　m2 : m2
　};
})();
```

使用上面的寫法，外部程式碼無法讀取內部的`_count`變數。

```javascript
console.info(module1._count); //undefined
```

上面的`module1`就是 JavaScript 模組的基本寫法。下面，再對這種寫法進行加工。

### 模組的放大模式

如果一個模組很大，必須分成幾個部分，或者一個模組需要繼承另一個模組，這時就有必要採用“放大模式”（augmentation）。

```javascript
var module1 = (function (mod){
　mod.m3 = function () {
　　//...
　};
　return mod;
})(module1);
```

上面的程式碼為`module1`模組添加了一個新方法`m3()`，然後返回新的`module1`模組。

在瀏覽器環境中，模組的各個部分通常都是從網上獲取的，有時無法知道哪個部分會先載入。如果採用上面的寫法，第一個執行的部分有可能載入一個不存在空物件，這時就要採用"寬放大模式"（Loose augmentation）。

```javascript
var module1 = (function (mod) {
　//...
　return mod;
})(window.module1 || {});
```

與"放大模式"相比，“寬放大模式”就是“立即執行函式”的引數可以是空物件。

### 輸入全域性變數

獨立性是模組的重要特點，模組內部最好不與程式的其他部分直接互動。

為了在模組內部呼叫全域性變數，必須顯式地將其他變數輸入模組。

```javascript
var module1 = (function ($, YAHOO) {
　//...
})(jQuery, YAHOO);
```

上面的`module1`模組需要使用 jQuery 庫和 YUI 庫，就把這兩個庫（其實是兩個模組）當作引數輸入`module1`。這樣做除了保證模組的獨立性，還使得模組之間的依賴關係變得明顯。

立即執行函式還可以起到名稱空間的作用。

```javascript
(function($, window, document) {

  function go(num) {
  }

  function handleEvents() {
  }

  function initialize() {
  }

  function dieCarouselDie() {
  }

  //attach to the global scope
  window.finalCarousel = {
    init : initialize,
    destroy : dieCarouselDie
  }

})( jQuery, window, document );
```

上面程式碼中，`finalCarousel`物件輸出到全域性，對外暴露`init`和`destroy`介面，內部方法`go`、`handleEvents`、`initialize`、`dieCarouselDie`都是外部無法呼叫的。

## 參考連結

- [JavaScript Modules: A Beginner’s Guide](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc), by Preethi Kasireddy
