# this 關鍵字

## 涵義

`this`關鍵字是一個非常重要的語法點。毫不誇張地說，不理解它的含義，大部分開發任務都無法完成。

前一章已經提到，`this`可以用在建構函式之中，表示例項物件。除此之外，`this`還可以用在別的場合。但不管是什麼場合，`this`都有一個共同點：它總是返回一個物件。

簡單說，`this`就是屬性或方法“當前”所在的物件。

```javascript
this.property
```

上面程式碼中，`this`就代表`property`屬性當前所在的物件。

下面是一個實際的例子。

```javascript
var person = {
  name: '張三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

person.describe()
// "姓名：張三"
```

上面程式碼中，`this.name`表示`name`屬性所在的那個物件。由於`this.name`是在`describe`方法中呼叫，而`describe`方法所在的當前物件是`person`，因此`this`指向`person`，`this.name`就是`person.name`。

由於物件的屬性可以賦給另一個物件，所以屬性所在的當前物件是可變的，即`this`的指向是可變的。

```javascript
var A = {
  name: '張三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

var B = {
  name: '李四'
};

B.describe = A.describe;
B.describe()
// "姓名：李四"
```

上面程式碼中，`A.describe`屬性被賦給`B`，於是`B.describe`就表示`describe`方法所在的當前物件是`B`，所以`this.name`就指向`B.name`。

稍稍重構這個例子，`this`的動態指向就能看得更清楚。

```javascript
function f() {
  return '姓名：'+ this.name;
}

var A = {
  name: '張三',
  describe: f
};

var B = {
  name: '李四',
  describe: f
};

A.describe() // "姓名：張三"
B.describe() // "姓名：李四"
```

上面程式碼中，函式`f`內部使用了`this`關鍵字，隨著`f`所在的物件不同，`this`的指向也不同。

只要函式被賦給另一個變數，`this`的指向就會變。

```javascript
var A = {
  name: '張三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

var name = '李四';
var f = A.describe;
f() // "姓名：李四"
```

上面程式碼中，`A.describe`被賦值給變數`f`，內部的`this`就會指向`f`執行時所在的物件（本例是頂層物件）。

再看一個網頁程式設計的例子。

```html
<input type="text" name="age" size=3 onChange="validate(this, 18, 99);">

<script>
function validate(obj, lowval, hival){
  if ((obj.value < lowval) || (obj.value > hival))
    console.log('Invalid Value!');
}
</script>
```

上面程式碼是一個文字輸入框，每當使用者輸入一個值，就會呼叫`onChange`回撥函式，驗證這個值是否在指定範圍。瀏覽器會向回撥函式傳入當前物件，因此`this`就代表傳入當前物件（即文字框），然後就可以從`this.value`上面讀到使用者的輸入值。

總結一下，JavaScript 語言之中，一切皆物件，執行環境也是物件，所以函式都是在某個物件之中執行，`this`就是函式執行時所在的物件（環境）。這本來並不會讓使用者糊塗，但是 JavaScript 支援執行環境動態切換，也就是說，`this`的指向是動態的，沒有辦法事先確定到底指向哪個物件，這才是最讓初學者感到困惑的地方。

## 實質

JavaScript 語言之所以有 this 的設計，跟記憶體裡面的資料結構有關係。

```javascript
var obj = { foo:  5 };
```

上面的程式碼將一個物件賦值給變數`obj`。JavaScript 引擎會先在記憶體裡面，生成一個物件`{ foo: 5 }`，然後把這個物件的記憶體地址賦值給變數`obj`。也就是說，變數`obj`是一個地址（reference）。後面如果要讀取`obj.foo`，引擎先從`obj`拿到記憶體地址，然後再從該地址讀出原始的物件，返回它的`foo`屬性。

原始的物件以字典結構儲存，每一個屬性名都對應一個屬性描述物件。舉例來說，上面例子的`foo`屬性，實際上是以下面的形式儲存的。

```javascript
{
  foo: {
    [[value]]: 5
    [[writable]]: true
    [[enumerable]]: true
    [[configurable]]: true
  }
}
```

注意，`foo`屬性的值儲存在屬性描述物件的`value`屬性裡面。

這樣的結構是很清晰的，問題在於屬性的值可能是一個函式。

```javascript
var obj = { foo: function () {} };
```

這時，引擎會將函式單獨儲存在記憶體中，然後再將函式的地址賦值給`foo`屬性的`value`屬性。

```javascript
{
  foo: {
    [[value]]: 函式的地址
    ...
  }
}
```

由於函式是一個單獨的值，所以它可以在不同的環境（上下文）執行。

```javascript
var f = function () {};
var obj = { f: f };

// 單獨執行
f()

// obj 環境執行
obj.f()
```

JavaScript 允許在函式體內部，引用當前環境的其他變數。

```javascript
var f = function () {
  console.log(x);
};
```

上面程式碼中，函式體裡面使用了變數`x`。該變數由執行環境提供。

現在問題就來了，由於函式可以在不同的執行環境執行，所以需要有一種機制，能夠在函式體內部獲得當前的執行環境（context）。所以，`this`就出現了，它的設計目的就是在函式體內部，指代函式當前的執行環境。

```javascript
var f = function () {
  console.log(this.x);
}
```

上面程式碼中，函式體裡面的`this.x`就是指當前執行環境的`x`。

```javascript
var f = function () {
  console.log(this.x);
}

var x = 1;
var obj = {
  f: f,
  x: 2,
};

// 單獨執行
f() // 1

// obj 環境執行
obj.f() // 2
```

上面程式碼中，函式`f`在全域性環境執行，`this.x`指向全域性環境的`x`；在`obj`環境執行，`this.x`指向`obj.x`。

## 使用場合

`this`主要有以下幾個使用場合。

**（1）全域性環境**

全域性環境使用`this`，它指的就是頂層物件`window`。

```javascript
this === window // true

function f() {
  console.log(this === window);
}
f() // true
```

上面程式碼說明，不管是不是在函式內部，只要是在全域性環境下執行，`this`就是指頂層物件`window`。

**（2）建構函式**

建構函式中的`this`，指的是例項物件。

```javascript
var Obj = function (p) {
  this.p = p;
};
```

上面程式碼定義了一個建構函式`Obj`。由於`this`指向例項物件，所以在建構函式內部定義`this.p`，就相當於定義例項物件有一個`p`屬性。

```javascript
var o = new Obj('Hello World!');
o.p // "Hello World!"
```

**（3）物件的方法**

如果物件的方法裡面包含`this`，`this`的指向就是方法執行時所在的物件。該方法賦值給另一個物件，就會改變`this`的指向。

但是，這條規則很不容易把握。請看下面的程式碼。

```javascript
var obj ={
  foo: function () {
    console.log(this);
  }
};

obj.foo() // obj
```

上面程式碼中，`obj.foo`方法執行時，它內部的`this`指向`obj`。

但是，下面這幾種用法，都會改變`this`的指向。

```javascript
// 情況一
(obj.foo = obj.foo)() // window
// 情況二
(false || obj.foo)() // window
// 情況三
(1, obj.foo)() // window
```

上面程式碼中，`obj.foo`就是一個值。這個值真正呼叫的時候，執行環境已經不是`obj`了，而是全域性環境，所以`this`不再指向`obj`。

可以這樣理解，JavaScript 引擎內部，`obj`和`obj.foo`儲存在兩個記憶體地址，稱為地址一和地址二。`obj.foo()`這樣呼叫時，是從地址一呼叫地址二，因此地址二的執行環境是地址一，`this`指向`obj`。但是，上面三種情況，都是直接取出地址二進行呼叫，這樣的話，執行環境就是全域性環境，因此`this`指向全域性環境。上面三種情況等同於下面的程式碼。

```javascript
// 情況一
(obj.foo = function () {
  console.log(this);
})()
// 等同於
(function () {
  console.log(this);
})()

// 情況二
(false || function () {
  console.log(this);
})()

// 情況三
(1, function () {
  console.log(this);
})()
```

如果`this`所在的方法不在物件的第一層，這時`this`只是指向當前一層的物件，而不會繼承更上面的層。

```javascript
var a = {
  p: 'Hello',
  b: {
    m: function() {
      console.log(this.p);
    }
  }
};

a.b.m() // undefined
```

上面程式碼中，`a.b.m`方法在`a`物件的第二層，該方法內部的`this`不是指向`a`，而是指向`a.b`，因為實際執行的是下面的程式碼。

```javascript
var b = {
  m: function() {
   console.log(this.p);
  }
};

var a = {
  p: 'Hello',
  b: b
};

(a.b).m() // 等同於 b.m()
```

如果要達到預期效果，只有寫成下面這樣。

```javascript
var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};
```

如果這時將巢狀物件內部的方法賦值給一個變數，`this`依然會指向全域性物件。

```javascript
var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};

var hello = a.b.m;
hello() // undefined
```

上面程式碼中，`m`是多層物件內部的一個方法。為求簡便，將其賦值給`hello`變數，結果呼叫時，`this`指向了頂層物件。為了避免這個問題，可以只將`m`所在的物件賦值給`hello`，這樣呼叫時，`this`的指向就不會變。

```javascript
var hello = a.b;
hello.m() // Hello
```

## 使用注意點

### 避免多層 this

由於`this`的指向是不確定的，所以切勿在函式中包含多層的`this`。

```javascript
var o = {
  f1: function () {
    console.log(this);
    var f2 = function () {
      console.log(this);
    }();
  }
}

o.f1()
// Object
// Window
```

上面程式碼包含兩層`this`，結果執行後，第一層指向物件`o`，第二層指向全域性物件，因為實際執行的是下面的程式碼。

```javascript
var temp = function () {
  console.log(this);
};

var o = {
  f1: function () {
    console.log(this);
    var f2 = temp();
  }
}
```

一個解決方法是在第二層改用一個指向外層`this`的變數。

```javascript
var o = {
  f1: function() {
    console.log(this);
    var that = this;
    var f2 = function() {
      console.log(that);
    }();
  }
}

o.f1()
// Object
// Object
```

上面程式碼定義了變數`that`，固定指向外層的`this`，然後在內層使用`that`，就不會發生`this`指向的改變。

事實上，使用一個變數固定`this`的值，然後內層函式呼叫這個變數，是非常常見的做法，請務必掌握。

JavaScript 提供了嚴格模式，也可以硬性避免這種問題。嚴格模式下，如果函式內部的`this`指向頂層物件，就會報錯。

```javascript
var counter = {
  count: 0
};
counter.inc = function () {
  'use strict';
  this.count++
};
var f = counter.inc;
f()
// TypeError: Cannot read property 'count' of undefined
```

上面程式碼中，`inc`方法透過`'use strict'`宣告採用嚴格模式，這時內部的`this`一旦指向頂層物件，就會報錯。

### 避免陣列處理方法中的 this

陣列的`map`和`foreach`方法，允許提供一個函式作為引數。這個函式內部不應該使用`this`。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    });
  }
}

o.f()
// undefined a1
// undefined a2
```

上面程式碼中，`foreach`方法的回撥函式中的`this`，其實是指向`window`物件，因此取不到`o.v`的值。原因跟上一段的多層`this`是一樣的，就是內層的`this`不指向外部，而指向頂層物件。

解決這個問題的一種方法，就是前面提到的，使用中間變數固定`this`。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    var that = this;
    this.p.forEach(function (item) {
      console.log(that.v+' '+item);
    });
  }
}

o.f()
// hello a1
// hello a2
```

另一種方法是將`this`當作`foreach`方法的第二個引數，固定它的執行環境。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    }, this);
  }
}

o.f()
// hello a1
// hello a2
```

### 避免回撥函式中的 this

回撥函式中的`this`往往會改變指向，最好避免使用。

```javascript
var o = new Object();
o.f = function () {
  console.log(this === o);
}

// jQuery 的寫法
$('#button').on('click', o.f);
```

上面程式碼中，點選按鈕以後，控制檯會顯示`false`。原因是此時`this`不再指向`o`物件，而是指向按鈕的 DOM 物件，因為`f`方法是在按鈕物件的環境中被呼叫的。這種細微的差別，很容易在程式設計中忽視，導致難以察覺的錯誤。

為了解決這個問題，可以採用下面的一些方法對`this`進行繫結，也就是使得`this`固定指向某個物件，減少不確定性。

## 繫結 this 的方法

`this`的動態切換，固然為 JavaScript 創造了巨大的靈活性，但也使得程式設計變得困難和模糊。有時，需要把`this`固定下來，避免出現意想不到的情況。JavaScript 提供了`call`、`apply`、`bind`這三個方法，來切換/固定`this`的指向。

### Function.prototype.call()

函式例項的`call`方法，可以指定函式內部`this`的指向（即函式執行時所在的作用域），然後在所指定的作用域中，呼叫該函式。

```javascript
var obj = {};

var f = function () {
  return this;
};

f() === window // true
f.call(obj) === obj // true
```

上面程式碼中，全域性環境執行函式`f`時，`this`指向全域性環境（瀏覽器為`window`物件）；`call`方法可以改變`this`的指向，指定`this`指向物件`obj`，然後在物件`obj`的作用域中執行函式`f`。

`call`方法的引數，應該是一個物件。如果引數為空、`null`和`undefined`，則預設傳入全域性物件。

```javascript
var n = 123;
var obj = { n: 456 };

function a() {
  console.log(this.n);
}

a.call() // 123
a.call(null) // 123
a.call(undefined) // 123
a.call(window) // 123
a.call(obj) // 456
```

上面程式碼中，`a`函式中的`this`關鍵字，如果指向全域性物件，返回結果為`123`。如果使用`call`方法將`this`關鍵字指向`obj`物件，返回結果為`456`。可以看到，如果`call`方法沒有引數，或者引數為`null`或`undefined`，則等同於指向全域性物件。

如果`call`方法的引數是一個原始值，那麼這個原始值會自動轉成對應的包裝物件，然後傳入`call`方法。

```javascript
var f = function () {
  return this;
};

f.call(5)
// Number {[[PrimitiveValue]]: 5}
```

上面程式碼中，`call`的引數為`5`，不是物件，會被自動轉成包裝物件（`Number`的例項），繫結`f`內部的`this`。

`call`方法還可以接受多個引數。

```javascript
func.call(thisValue, arg1, arg2, ...)
```

`call`的第一個引數就是`this`所要指向的那個物件，後面的引數則是函式呼叫時所需的引數。

```javascript
function add(a, b) {
  return a + b;
}

add.call(this, 1, 2) // 3
```

上面程式碼中，`call`方法指定函式`add`內部的`this`綁定當前環境（物件），並且引數為`1`和`2`，因此函式`add`執行後得到`3`。

`call`方法的一個應用是呼叫物件的原生方法。

```javascript
var obj = {};
obj.hasOwnProperty('toString') // false

// 覆蓋掉繼承的 hasOwnProperty 方法
obj.hasOwnProperty = function () {
  return true;
};
obj.hasOwnProperty('toString') // true

Object.prototype.hasOwnProperty.call(obj, 'toString') // false
```

上面程式碼中，`hasOwnProperty`是`obj`物件繼承的方法，如果這個方法一旦被覆蓋，就不會得到正確結果。`call`方法可以解決這個問題，它將`hasOwnProperty`方法的原始定義放到`obj`物件上執行，這樣無論`obj`上有沒有同名方法，都不會影響結果。

### Function.prototype.apply()

`apply`方法的作用與`call`方法類似，也是改變`this`指向，然後再呼叫該函式。唯一的區別就是，它接收一個數組作為函式執行時的引數，使用格式如下。

```javascript
func.apply(thisValue, [arg1, arg2, ...])
```

`apply`方法的第一個引數也是`this`所要指向的那個物件，如果設為`null`或`undefined`，則等同於指定全域性物件。第二個引數則是一個數組，該陣列的所有成員依次作為引數，傳入原函式。原函式的引數，在`call`方法中必須一個個新增，但是在`apply`方法中，必須以陣列形式新增。

```javascript
function f(x, y){
  console.log(x + y);
}

f.call(null, 1, 1) // 2
f.apply(null, [1, 1]) // 2
```

上面程式碼中，`f`函式本來接受兩個引數，使用`apply`方法以後，就變成可以接受一個數組作為引數。

利用這一點，可以做一些有趣的應用。

**（1）找出陣列最大元素**

JavaScript 不提供找出陣列最大元素的函式。結合使用`apply`方法和`Math.max`方法，就可以返回陣列的最大元素。

```javascript
var a = [10, 2, 4, 15, 9];
Math.max.apply(null, a) // 15
```

**（2）將陣列的空元素變為`undefined`**

透過`apply`方法，利用`Array`建構函式將陣列的空元素變成`undefined`。

```javascript
Array.apply(null, ['a', ,'b'])
// [ 'a', undefined, 'b' ]
```

空元素與`undefined`的差別在於，陣列的`forEach`方法會跳過空元素，但是不會跳過`undefined`。因此，遍歷內部元素的時候，會得到不同的結果。

```javascript
var a = ['a', , 'b'];

function print(i) {
  console.log(i);
}

a.forEach(print)
// a
// b

Array.apply(null, a).forEach(print)
// a
// undefined
// b
```

**（3）轉換類似陣列的物件**

另外，利用陣列物件的`slice`方法，可以將一個類似陣列的物件（比如`arguments`物件）轉為真正的陣列。

```javascript
Array.prototype.slice.apply({0: 1, length: 1}) // [1]
Array.prototype.slice.apply({0: 1}) // []
Array.prototype.slice.apply({0: 1, length: 2}) // [1, undefined]
Array.prototype.slice.apply({length: 1}) // [undefined]
```

上面程式碼的`apply`方法的引數都是物件，但是返回結果都是陣列，這就起到了將物件轉成陣列的目的。從上面程式碼可以看到，這個方法起作用的前提是，被處理的物件必須有`length`屬性，以及相對應的數字鍵。

**（4）繫結回撥函式的物件**

前面的按鈕點選事件的例子，可以改寫如下。

```javascript
var o = new Object();

o.f = function () {
  console.log(this === o);
}

var f = function (){
  o.f.apply(o);
  // 或者 o.f.call(o);
};

// jQuery 的寫法
$('#button').on('click', f);
```

上面程式碼中，點選按鈕以後，控制檯將會顯示`true`。由於`apply()`方法（或者`call()`方法）不僅繫結函式執行時所在的物件，還會立即執行函式，因此不得不把繫結語句寫在一個函式體內。更簡潔的寫法是採用下面介紹的`bind()`方法。

### Function.prototype.bind()

`bind()`方法用於將函式體內的`this`繫結到某個物件，然後返回一個新函式。

```javascript
var d = new Date();
d.getTime() // 1481869925657

var print = d.getTime;
print() // Uncaught TypeError: this is not a Date object.
```

上面程式碼中，我們將`d.getTime()`方法賦給變數`print`，然後呼叫`print()`就報錯了。這是因為`getTime()`方法內部的`this`，繫結`Date`物件的例項，賦給變數`print`以後，內部的`this`已經不指向`Date`物件的例項了。

`bind()`方法可以解決這個問題。

```javascript
var print = d.getTime.bind(d);
print() // 1481869925657
```

上面程式碼中，`bind()`方法將`getTime()`方法內部的`this`繫結到`d`物件，這時就可以安全地將這個方法賦值給其他變量了。

`bind`方法的引數就是所要繫結`this`的物件，下面是一個更清晰的例子。

```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

var func = counter.inc.bind(counter);
func();
counter.count // 1
```

上面程式碼中，`counter.inc()`方法被賦值給變數`func`。這時必須用`bind()`方法將`inc()`內部的`this`，繫結到`counter`，否則就會出錯。

`this`繫結到其他物件也是可以的。

```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

var obj = {
  count: 100
};
var func = counter.inc.bind(obj);
func();
obj.count // 101
```

上面程式碼中，`bind()`方法將`inc()`方法內部的`this`，繫結到`obj`物件。結果呼叫`func`函式以後，遞增的就是`obj`內部的`count`屬性。

`bind()`還可以接受更多的引數，將這些引數繫結原函式的引數。

```javascript
var add = function (x, y) {
  return x * this.m + y * this.n;
}

var obj = {
  m: 2,
  n: 2
};

var newAdd = add.bind(obj, 5);
newAdd(5) // 20
```

上面程式碼中，`bind()`方法除了繫結`this`物件，還將`add()`函式的第一個引數`x`繫結成`5`，然後返回一個新函式`newAdd()`，這個函式只要再接受一個引數`y`就能運行了。

如果`bind()`方法的第一個引數是`null`或`undefined`，等於將`this`繫結到全域性物件，函式執行時`this`指向頂層物件（瀏覽器為`window`）。

```javascript
function add(x, y) {
  return x + y;
}

var plus5 = add.bind(null, 5);
plus5(10) // 15
```

上面程式碼中，函式`add()`內部並沒有`this`，使用`bind()`方法的主要目的是繫結引數`x`，以後每次執行新函式`plus5()`，就只需要提供另一個引數`y`就夠了。而且因為`add()`內部沒有`this`，所以`bind()`的第一個引數是`null`，不過這裡如果是其他物件，也沒有影響。

`bind()`方法有一些使用注意點。

**（1）每一次返回一個新函式**

`bind()`方法每執行一次，就返回一個新函式，這會產生一些問題。比如，監聽事件的時候，不能寫成下面這樣。

```javascript
element.addEventListener('click', o.m.bind(o));
```

上面程式碼中，`click`事件繫結`bind()`方法生成的一個匿名函式。這樣會導致無法取消繫結，所以下面的程式碼是無效的。

```javascript
element.removeEventListener('click', o.m.bind(o));
```

正確的方法是寫成下面這樣：

```javascript
var listener = o.m.bind(o);
element.addEventListener('click', listener);
//  ...
element.removeEventListener('click', listener);
```

**（2）結合回撥函式使用**

回撥函式是 JavaScript 最常用的模式之一，但是一個常見的錯誤是，將包含`this`的方法直接當作回撥函式。解決方法就是使用`bind()`方法，將`counter.inc()`繫結`counter`。

```javascript
var counter = {
  count: 0,
  inc: function () {
    'use strict';
    this.count++;
  }
};

function callIt(callback) {
  callback();
}

callIt(counter.inc.bind(counter));
counter.count // 1
```

上面程式碼中，`callIt()`方法會呼叫回撥函式。這時如果直接把`counter.inc`傳入，呼叫時`counter.inc()`內部的`this`就會指向全域性物件。使用`bind()`方法將`counter.inc`繫結`counter`以後，就不會有這個問題，`this`總是指向`counter`。

還有一種情況比較隱蔽，就是某些陣列方法可以接受一個函式當作引數。這些函式內部的`this`指向，很可能也會出錯。

```javascript
var obj = {
  name: '張三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    });
  }
};

obj.print()
// 沒有任何輸出
```

上面程式碼中，`obj.print`內部`this.times`的`this`是指向`obj`的，這個沒有問題。但是，`forEach()`方法的回撥函式內部的`this.name`卻是指向全域性物件，導致沒有辦法取到值。稍微改動一下，就可以看得更清楚。

```javascript
obj.print = function () {
  this.times.forEach(function (n) {
    console.log(this === window);
  });
};

obj.print()
// true
// true
// true
```

解決這個問題，也是透過`bind()`方法繫結`this`。

```javascript
obj.print = function () {
  this.times.forEach(function (n) {
    console.log(this.name);
  }.bind(this));
};

obj.print()
// 張三
// 張三
// 張三
```

**（3）結合`call()`方法使用**

利用`bind()`方法，可以改寫一些 JavaScript 原生方法的使用形式，以陣列的`slice()`方法為例。

```javascript
[1, 2, 3].slice(0, 1) // [1]
// 等同於
Array.prototype.slice.call([1, 2, 3], 0, 1) // [1]
```

上面的程式碼中，陣列的`slice`方法從`[1, 2, 3]`裡面，按照指定的開始位置和結束位置，切分出另一個數組。這樣做的本質是在`[1, 2, 3]`上面呼叫`Array.prototype.slice()`方法，因此可以用`call`方法表達這個過程，得到同樣的結果。

`call()`方法實質上是呼叫`Function.prototype.call()`方法，因此上面的表示式可以用`bind()`方法改寫。

```javascript
var slice = Function.prototype.call.bind(Array.prototype.slice);
slice([1, 2, 3], 0, 1) // [1]
```

上面程式碼的含義就是，將`Array.prototype.slice`變成`Function.prototype.call`方法所在的物件，呼叫時就變成了`Array.prototype.slice.call`。類似的寫法還可以用於其他陣列方法。

```javascript
var push = Function.prototype.call.bind(Array.prototype.push);
var pop = Function.prototype.call.bind(Array.prototype.pop);

var a = [1 ,2 ,3];
push(a, 4)
a // [1, 2, 3, 4]

pop(a)
a // [1, 2, 3]
```

如果再進一步，將`Function.prototype.call`方法繫結到`Function.prototype.bind`物件，就意味著`bind`的呼叫形式也可以被改寫。

```javascript
function f() {
  console.log(this.v);
}

var o = { v: 123 };
var bind = Function.prototype.call.bind(Function.prototype.bind);
bind(f, o)() // 123
```

上面程式碼的含義就是，將`Function.prototype.bind`方法繫結在`Function.prototype.call`上面，所以`bind`方法就可以直接使用，不需要在函式例項上使用。

## 參考連結

- Jonathan Creamer, [Avoiding the "this" problem in JavaScript](http://tech.pro/tutorial/1192/avoiding-the-this-problem-in-javascript)
- Erik Kronberg, [Bind, Call and Apply in JavaScript](https://variadic.me/posts/2013-10-22-bind-call-and-apply-in-javascript.html)
- Axel Rauschmayer, [JavaScript’s this: how it works, where it can trip you up](http://www.2ality.com/2014/05/this.html)
