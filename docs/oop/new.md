# 例項物件與 new 命令

JavaScript 語言具有很強的面向物件程式設計能力，本章介紹 JavaScript 面向物件程式設計的基礎知識。

## 物件是什麼

面向物件程式設計（Object Oriented Programming，縮寫為 OOP）是目前主流的程式設計正規化。它將真實世界各種複雜的關係，抽象為一個個物件，然後由物件之間的分工與合作，完成對真實世界的模擬。

每一個物件都是功能中心，具有明確分工，可以完成接受資訊、處理資料、發出資訊等任務。物件可以複用，透過繼承機制還可以定製。因此，面向物件程式設計具有靈活、程式碼可複用、高度模組化等特點，容易維護和開發，比起由一系列函式或指令組成的傳統的程序式程式設計（procedural programming），更適合多人合作的大型軟體專案。

那麼，“物件”（object）到底是什麼？我們從兩個層次來理解。

**（1）物件是單個實物的抽象。**

一本書、一輛汽車、一個人都可以是物件，一個數據庫、一張網頁、一個遠端伺服器連線也可以是物件。當實物被抽象成物件，實物之間的關係就變成了物件之間的關係，從而就可以模擬現實情況，針對物件進行程式設計。

**（2）物件是一個容器，封裝了屬性（property）和方法（method）。**

屬性是物件的狀態，方法是物件的行為（完成某種任務）。比如，我們可以把動物抽象為`animal`物件，使用“屬性”記錄具體是哪一種動物，使用“方法”表示動物的某種行為（奔跑、捕獵、休息等等）。

## 建構函式

面向物件程式設計的第一步，就是要生成物件。前面說過，物件是單個實物的抽象。通常需要一個模板，表示某一類實物的共同特徵，然後物件根據這個模板生成。

典型的面向物件程式語言（比如 C++ 和 Java），都有“類”（class）這個概念。所謂“類”就是物件的模板，物件就是“類”的例項。但是，JavaScript 語言的物件體系，不是基於“類”的，而是基於建構函式（constructor）和原型鏈（prototype）。

JavaScript 語言使用建構函式（constructor）作為物件的模板。所謂”建構函式”，就是專門用來生成例項物件的函式。它就是物件的模板，描述例項物件的基本結構。一個建構函式，可以生成多個例項物件，這些例項物件都有相同的結構。

建構函式就是一個普通的函式，但具有自己的特徵和用法。

```javascript
var Vehicle = function () {
  this.price = 1000;
};
```

上面程式碼中，`Vehicle`就是建構函式。為了與普通函式區別，建構函式名字的第一個字母通常大寫。

建構函式的特點有兩個。

- 函式體內部使用了`this`關鍵字，代表了所要生成的物件例項。
- 生成物件的時候，必須使用`new`命令。

下面先介紹`new`命令。

## new 命令

### 基本用法

`new`命令的作用，就是執行建構函式，返回一個例項物件。

```javascript
var Vehicle = function () {
  this.price = 1000;
};

var v = new Vehicle();
v.price // 1000
```

上面程式碼透過`new`命令，讓建構函式`Vehicle`生成一個例項物件，儲存在變數`v`中。這個新生成的例項物件，從建構函式`Vehicle`得到了`price`屬性。`new`命令執行時，建構函式內部的`this`，就代表了新生成的例項物件，`this.price`表示例項物件有一個`price`屬性，值是1000。

使用`new`命令時，根據需要，建構函式也可以接受引數。

```javascript
var Vehicle = function (p) {
  this.price = p;
};

var v = new Vehicle(500);
```

`new`命令本身就可以執行建構函式，所以後面的建構函式可以帶括號，也可以不帶括號。下面兩行程式碼是等價的，但是為了表示這裡是函式呼叫，推薦使用括號。

```javascript
// 推薦的寫法
var v = new Vehicle();
// 不推薦的寫法
var v = new Vehicle;
```

一個很自然的問題是，如果忘了使用`new`命令，直接呼叫建構函式會發生什麼事？

這種情況下，建構函式就變成了普通函式，並不會生成例項物件。而且由於後面會說到的原因，`this`這時代表全域性物件，將造成一些意想不到的結果。

```javascript
var Vehicle = function (){
  this.price = 1000;
};

var v = Vehicle();
v // undefined
price // 1000
```

上面程式碼中，呼叫`Vehicle`建構函式時，忘了加上`new`命令。結果，變數`v`變成了`undefined`，而`price`屬性變成了全域性變數。因此，應該非常小心，避免不使用`new`命令、直接呼叫建構函式。

為了保證建構函式必須與`new`命令一起使用，一個解決辦法是，建構函式內部使用嚴格模式，即第一行加上`use strict`。這樣的話，一旦忘了使用`new`命令，直接呼叫建構函式就會報錯。

```javascript
function Fubar(foo, bar){
  'use strict';
  this._foo = foo;
  this._bar = bar;
}

Fubar()
// TypeError: Cannot set property '_foo' of undefined
```

上面程式碼的`Fubar`為建構函式，`use strict`命令保證了該函式在嚴格模式下執行。由於嚴格模式中，函式內部的`this`不能指向全域性物件，預設等於`undefined`，導致不加`new`呼叫會報錯（JavaScript 不允許對`undefined`新增屬性）。

另一個解決辦法，建構函式內部判斷是否使用`new`命令，如果發現沒有使用，則直接返回一個例項物件。

```javascript
function Fubar(foo, bar) {
  if (!(this instanceof Fubar)) {
    return new Fubar(foo, bar);
  }

  this._foo = foo;
  this._bar = bar;
}

Fubar(1, 2)._foo // 1
(new Fubar(1, 2))._foo // 1
```

上面程式碼中的建構函式，不管加不加`new`命令，都會得到同樣的結果。

### new 命令的原理

使用`new`命令時，它後面的函式依次執行下面的步驟。

1. 建立一個空物件，作為將要返回的物件例項。
1. 將這個空物件的原型，指向建構函式的`prototype`屬性。
1. 將這個空物件賦值給函式內部的`this`關鍵字。
1. 開始執行建構函式內部的程式碼。

也就是說，建構函式內部，`this`指的是一個新生成的空物件，所有針對`this`的操作，都會發生在這個空物件上。建構函式之所以叫“建構函式”，就是說這個函式的目的，就是操作一個空物件（即`this`物件），將其“構造”為需要的樣子。

如果建構函式內部有`return`語句，而且`return`後面跟著一個物件，`new`命令會返回`return`語句指定的物件；否則，就會不管`return`語句，返回`this`物件。

```javascript
var Vehicle = function () {
  this.price = 1000;
  return 1000;
};

(new Vehicle()) === 1000
// false
```

上面程式碼中，建構函式`Vehicle`的`return`語句返回一個數值。這時，`new`命令就會忽略這個`return`語句，返回“構造”後的`this`物件。

但是，如果`return`語句返回的是一個跟`this`無關的新物件，`new`命令會返回這個新物件，而不是`this`物件。這一點需要特別引起注意。

```javascript
var Vehicle = function (){
  this.price = 1000;
  return { price: 2000 };
};

(new Vehicle()).price
// 2000
```

上面程式碼中，建構函式`Vehicle`的`return`語句，返回的是一個新物件。`new`命令會返回這個物件，而不是`this`物件。

另一方面，如果對普通函式（內部沒有`this`關鍵字的函式）使用`new`命令，則會返回一個空物件。

```javascript
function getMessage() {
  return 'this is a message';
}

var msg = new getMessage();

msg // {}
typeof msg // "object"
```

上面程式碼中，`getMessage`是一個普通函式，返回一個字串。對它使用`new`命令，會得到一個空物件。這是因為`new`命令總是返回一個物件，要麼是例項物件，要麼是`return`語句指定的物件。本例中，`return`語句返回的是字串，所以`new`命令就忽略了該語句。

`new`命令簡化的內部流程，可以用下面的程式碼表示。

```javascript
function _new(/* 建構函式 */ constructor, /* 建構函式引數 */ params) {
  // 將 arguments 物件轉為陣列
  var args = [].slice.call(arguments);
  // 取出建構函式
  var constructor = args.shift();
  // 建立一個空物件，繼承建構函式的 prototype 屬性
  var context = Object.create(constructor.prototype);
  // 執行建構函式
  var result = constructor.apply(context, args);
  // 如果返回結果是物件，就直接返回，否則返回 context 物件
  return (typeof result === 'object' && result != null) ? result : context;
}

// 例項
var actor = _new(Person, '張三', 28);
```

### new.target

函式內部可以使用`new.target`屬性。如果當前函式是`new`命令呼叫，`new.target`指向當前函式，否則為`undefined`。

```javascript
function f() {
  console.log(new.target === f);
}

f() // false
new f() // true
```

使用這個屬性，可以判斷函式呼叫的時候，是否使用`new`命令。

```javascript
function f() {
  if (!new.target) {
    throw new Error('請使用 new 命令呼叫！');
  }
  // ...
}

f() // Uncaught Error: 請使用 new 命令呼叫！
```

上面程式碼中，建構函式`f`呼叫時，沒有使用`new`命令，就丟擲一個錯誤。

## Object.create() 建立例項物件

建構函式作為模板，可以生成例項物件。但是，有時拿不到建構函式，只能拿到一個現有的物件。我們希望以這個現有的物件作為模板，生成新的例項物件，這時就可以使用`Object.create()`方法。

```javascript
var person1 = {
  name: '張三',
  age: 38,
  greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
  }
};

var person2 = Object.create(person1);

person2.name // 張三
person2.greeting() // Hi! I'm 張三.
```

上面程式碼中，物件`person1`是`person2`的模板，後者繼承了前者的屬性和方法。

`Object.create()`的詳細介紹，請看後面的相關章節。
