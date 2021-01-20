# 物件

## 概述

### 生成方法

物件（object）是 JavaScript 語言的核心概念，也是最重要的資料型別。

什麼是物件？簡單說，物件就是一組“鍵值對”（key-value）的集合，是一種無序的複合資料集合。

```javascript
var obj = {
  foo: 'Hello',
  bar: 'World'
};
```

上面程式碼中，大括號就定義了一個物件，它被賦值給變數`obj`，所以變數`obj`就指向一個物件。該物件內部包含兩個鍵值對（又稱為兩個“成員”），第一個鍵值對是`foo: 'Hello'`，其中`foo`是“鍵名”（成員的名稱），字串`Hello`是“鍵值”（成員的值）。鍵名與鍵值之間用冒號分隔。第二個鍵值對是`bar: 'World'`，`bar`是鍵名，`World`是鍵值。兩個鍵值對之間用逗號分隔。

### 鍵名

物件的所有鍵名都是字串（ES6 又引入了 Symbol 值也可以作為鍵名），所以加不加引號都可以。上面的程式碼也可以寫成下面這樣。

```javascript
var obj = {
  'foo': 'Hello',
  'bar': 'World'
};
```

如果鍵名是數值，會被自動轉為字串。

```javascript
var obj = {
  1: 'a',
  3.2: 'b',
  1e2: true,
  1e-2: true,
  .234: true,
  0xFF: true
};

obj
// Object {
//   1: "a",
//   3.2: "b",
//   100: true,
//   0.01: true,
//   0.234: true,
//   255: true
// }

obj['100'] // true
```

上面程式碼中，物件`obj`的所有鍵名雖然看上去像數值，實際上都被自動轉成了字串。

如果鍵名不符合標識名的條件（比如第一個字元為數字，或者含有空格或運算子），且也不是數字，則必須加上引號，否則會報錯。

```javascript
// 報錯
var obj = {
  1p: 'Hello World'
};

// 不報錯
var obj = {
  '1p': 'Hello World',
  'h w': 'Hello World',
  'p+q': 'Hello World'
};
```

上面物件的三個鍵名，都不符合標識名的條件，所以必須加上引號。

物件的每一個鍵名又稱為“屬性”（property），它的“鍵值”可以是任何資料型別。如果一個屬性的值為函式，通常把這個屬性稱為“方法”，它可以像函式那樣呼叫。

```javascript
var obj = {
  p: function (x) {
    return 2 * x;
  }
};

obj.p(1) // 2
```

上面程式碼中，物件`obj`的屬性`p`，就指向一個函式。

如果屬性的值還是一個物件，就形成了鏈式引用。

```javascript
var o1 = {};
var o2 = { bar: 'hello' };

o1.foo = o2;
o1.foo.bar // "hello"
```

上面程式碼中，物件`o1`的屬性`foo`指向物件`o2`，就可以鏈式引用`o2`的屬性。

物件的屬性之間用逗號分隔，最後一個屬性後面可以加逗號（trailing comma），也可以不加。

```javascript
var obj = {
  p: 123,
  m: function () { ... },
}
```

上面的程式碼中，`m`屬性後面的那個逗號，有沒有都可以。

屬性可以動態建立，不必在物件宣告時就指定。

```javascript
var obj = {};
obj.foo = 123;
obj.foo // 123
```

上面程式碼中，直接對`obj`物件的`foo`屬性賦值，結果就在執行時建立了`foo`屬性。

### 物件的引用

如果不同的變數名指向同一個物件，那麼它們都是這個物件的引用，也就是說指向同一個記憶體地址。修改其中一個變數，會影響到其他所有變數。

```javascript
var o1 = {};
var o2 = o1;

o1.a = 1;
o2.a // 1

o2.b = 2;
o1.b // 2
```

上面程式碼中，`o1`和`o2`指向同一個物件，因此為其中任何一個變數新增屬性，另一個變數都可以讀寫該屬性。

此時，如果取消某一個變數對於原物件的引用，不會影響到另一個變數。

```javascript
var o1 = {};
var o2 = o1;

o1 = 1;
o2 // {}
```

上面程式碼中，`o1`和`o2`指向同一個物件，然後`o1`的值變為1，這時不會對`o2`產生影響，`o2`還是指向原來的那個物件。

但是，這種引用只侷限於物件，如果兩個變數指向同一個原始型別的值。那麼，變數這時都是值的複製。

```javascript
var x = 1;
var y = x;

x = 2;
y // 1
```

上面的程式碼中，當`x`的值發生變化後，`y`的值並不變，這就表示`y`和`x`並不是指向同一個記憶體地址。

### 表示式還是語句？

物件採用大括號表示，這導致了一個問題：如果行首是一個大括號，它到底是表示式還是語句？

```javascript
{ foo: 123 }
```

JavaScript 引擎讀到上面這行程式碼，會發現可能有兩種含義。第一種可能是，這是一個表示式，表示一個包含`foo`屬性的物件；第二種可能是，這是一個語句，表示一個程式碼區塊，裡面有一個標籤`foo`，指向表示式`123`。

為了避免這種歧義，JavaScript 引擎的做法是，如果遇到這種情況，無法確定是物件還是程式碼塊，一律解釋為程式碼塊。

```javascript
{ console.log(123) } // 123
```

上面的語句是一個程式碼塊，而且只有解釋為程式碼塊，才能執行。

如果要解釋為物件，最好在大括號前加上圓括號。因為圓括號的裡面，只能是表示式，所以確保大括號只能解釋為物件。

```javascript
({ foo: 123 }) // 正確
({ console.log(123) }) // 報錯
```

這種差異在`eval`語句（作用是對字串求值）中反映得最明顯。

```javascript
eval('{foo: 123}') // 123
eval('({foo: 123})') // {foo: 123}
```

上面程式碼中，如果沒有圓括號，`eval`將其理解為一個程式碼塊；加上圓括號以後，就理解成一個物件。

## 屬性的操作

### 屬性的讀取

讀取物件的屬性，有兩種方法，一種是使用點運算子，還有一種是使用方括號運算子。

```javascript
var obj = {
  p: 'Hello World'
};

obj.p // "Hello World"
obj['p'] // "Hello World"
```

上面程式碼分別採用點運算子和方括號運算子，讀取屬性`p`。

請注意，如果使用方括號運算子，鍵名必須放在引號裡面，否則會被當作變數處理。

```javascript
var foo = 'bar';

var obj = {
  foo: 1,
  bar: 2
};

obj.foo  // 1
obj[foo]  // 2
```

上面程式碼中，引用物件`obj`的`foo`屬性時，如果使用點運算子，`foo`就是字串；如果使用方括號運算子，但是不使用引號，那麼`foo`就是一個變數，指向字串`bar`。

方括號運算子內部還可以使用表示式。

```javascript
obj['hello' + ' world']
obj[3 + 3]
```

數字鍵可以不加引號，因為會自動轉成字串。

```javascript
var obj = {
  0.7: 'Hello World'
};

obj['0.7'] // "Hello World"
obj[0.7] // "Hello World"
```

上面程式碼中，物件`obj`的數字鍵`0.7`，加不加引號都可以，因為會被自動轉為字串。

注意，數值鍵名不能使用點運算子（因為會被當成小數點），只能使用方括號運算子。

```javascript
var obj = {
  123: 'hello world'
};

obj.123 // 報錯
obj[123] // "hello world"
```

上面程式碼的第一個表示式，對數值鍵名`123`使用點運算子，結果報錯。第二個表示式使用方括號運算子，結果就是正確的。

### 屬性的賦值

點運算子和方括號運算子，不僅可以用來讀取值，還可以用來賦值。

```javascript
var obj = {};

obj.foo = 'Hello';
obj['bar'] = 'World';
```

上面程式碼中，分別使用點運算子和方括號運算子，對屬性賦值。

JavaScript 允許屬性的“後繫結”，也就是說，你可以在任意時刻新增屬性，沒必要在定義物件的時候，就定義好屬性。

```javascript
var obj = { p: 1 };

// 等價於

var obj = {};
obj.p = 1;
```

### 屬性的檢視

檢視一個物件本身的所有屬性，可以使用`Object.keys`方法。

```javascript
var obj = {
  key1: 1,
  key2: 2
};

Object.keys(obj);
// ['key1', 'key2']
```

### 屬性的刪除：delete 命令

`delete`命令用於刪除物件的屬性，刪除成功後返回`true`。

```javascript
var obj = { p: 1 };
Object.keys(obj) // ["p"]

delete obj.p // true
obj.p // undefined
Object.keys(obj) // []
```

上面程式碼中，`delete`命令刪除物件`obj`的`p`屬性。刪除後，再讀取`p`屬性就會返回`undefined`，而且`Object.keys`方法的返回值也不再包括該屬性。

注意，刪除一個不存在的屬性，`delete`不報錯，而且返回`true`。

```javascript
var obj = {};
delete obj.p // true
```

上面程式碼中，物件`obj`並沒有`p`屬性，但是`delete`命令照樣返回`true`。因此，不能根據`delete`命令的結果，認定某個屬性是存在的。

只有一種情況，`delete`命令會返回`false`，那就是該屬性存在，且不得刪除。

```javascript
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
});

obj.p // 123
delete obj.p // false
```

上面程式碼之中，物件`obj`的`p`屬性是不能刪除的，所以`delete`命令返回`false`（關於`Object.defineProperty`方法的介紹，請看《標準庫》的 Object 物件一章）。

另外，需要注意的是，`delete`命令只能刪除物件本身的屬性，無法刪除繼承的屬性（關於繼承參見《面向物件程式設計》章節）。

```javascript
var obj = {};
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

上面程式碼中，`toString`是物件`obj`繼承的屬性，雖然`delete`命令返回`true`，但該屬性並沒有被刪除，依然存在。這個例子還說明，即使`delete`返回`true`，該屬性依然可能讀取到值。

### 屬性是否存在：in 運算子

`in`運算子用於檢查物件是否包含某個屬性（注意，檢查的是鍵名，不是鍵值），如果包含就返回`true`，否則返回`false`。它的左邊是一個字串，表示屬性名，右邊是一個物件。

```javascript
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
```

`in`運算子的一個問題是，它不能識別哪些屬性是物件自身的，哪些屬性是繼承的。就像上面程式碼中，物件`obj`本身並沒有`toString`屬性，但是`in`運算子會返回`true`，因為這個屬性是繼承的。

這時，可以使用物件的`hasOwnProperty`方法判斷一下，是否為物件自身的屬性。

```javascript
var obj = {};
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')) // false
}
```

### 屬性的遍歷：for...in 迴圈

`for...in`迴圈用來遍歷一個物件的全部屬性。

```javascript
var obj = {a: 1, b: 2, c: 3};

for (var i in obj) {
  console.log('鍵名：', i);
  console.log('鍵值：', obj[i]);
}
// 鍵名： a
// 鍵值： 1
// 鍵名： b
// 鍵值： 2
// 鍵名： c
// 鍵值： 3
```

`for...in`迴圈有兩個使用注意點。

- 它遍歷的是物件所有可遍歷（enumerable）的屬性，會跳過不可遍歷的屬性。
- 它不僅遍歷物件自身的屬性，還遍歷繼承的屬性。

舉例來說，物件都繼承了`toString`屬性，但是`for...in`迴圈不會遍歷到這個屬性。

```javascript
var obj = {};

// toString 屬性是存在的
obj.toString // toString() { [native code] }

for (var p in obj) {
  console.log(p);
} // 沒有任何輸出
```

上面程式碼中，物件`obj`繼承了`toString`屬性，該屬性不會被`for...in`迴圈遍歷到，因為它預設是“不可遍歷”的。關於物件屬性的可遍歷性，參見《標準庫》章節中 Object 一章的介紹。

如果繼承的屬性是可遍歷的，那麼就會被`for...in`迴圈遍歷到。但是，一般情況下，都是隻想遍歷物件自身的屬性，所以使用`for...in`的時候，應該結合使用`hasOwnProperty`方法，在迴圈內部判斷一下，某個屬性是否為物件自身的屬性。

```javascript
var person = { name: '老張' };

for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
// name
```

## with 語句

`with`語句的格式如下：

```javascript
with (物件) {
  語句;
}
```

它的作用是操作同一個物件的多個屬性時，提供一些書寫的方便。

```javascript
// 例一
var obj = {
  p1: 1,
  p2: 2,
};
with (obj) {
  p1 = 4;
  p2 = 5;
}
// 等同於
obj.p1 = 4;
obj.p2 = 5;

// 例二
with (document.links[0]){
  console.log(href);
  console.log(title);
  console.log(style);
}
// 等同於
console.log(document.links[0].href);
console.log(document.links[0].title);
console.log(document.links[0].style);
```

注意，如果`with`區塊內部有變數的賦值操作，必須是當前物件已經存在的屬性，否則會創造一個當前作用域的全域性變數。

```javascript
var obj = {};
with (obj) {
  p1 = 4;
  p2 = 5;
}

obj.p1 // undefined
p1 // 4
```

上面程式碼中，物件`obj`並沒有`p1`屬性，對`p1`賦值等於創造了一個全域性變數`p1`。正確的寫法應該是，先定義物件`obj`的屬性`p1`，然後在`with`區塊內操作它。

這是因為`with`區塊沒有改變作用域，它的內部依然是當前作用域。這造成了`with`語句的一個很大的弊病，就是繫結物件不明確。

```javascript
with (obj) {
  console.log(x);
}
```

單純從上面的程式碼塊，根本無法判斷`x`到底是全域性變數，還是物件`obj`的一個屬性。這非常不利於程式碼的除錯和模組化，編譯器也無法對這段程式碼進行最佳化，只能留到執行時判斷，這就拖慢了執行速度。因此，建議不要使用`with`語句，可以考慮用一個臨時變數代替`with`。

```javascript
with(obj1.obj2.obj3) {
  console.log(p1 + p2);
}

// 可以寫成
var temp = obj1.obj2.obj3;
console.log(temp.p1 + temp.p2);
```

## 參考連結

- Dr. Axel Rauschmayer，[Object properties in JavaScript](http://www.2ality.com/2012/10/javascript-properties.html)
- Lakshan Perera, [Revisiting JavaScript Objects](http://www.laktek.com/2012/12/29/revisiting-javascript-objects/)
- Angus Croll, [The Secret Life of JavaScript Primitives](http://javascriptweblog.wordpress.com/2010/09/27/the-secret-life-of-javascript-primitives/)i
- Dr. Axel Rauschmayer, [JavaScript’s with statement and why it’s deprecated](http://www.2ality.com/2011/06/with-statement.html)
