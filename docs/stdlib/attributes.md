# 屬性描述物件

## 概述

JavaScript 提供了一個內部資料結構，用來描述物件的屬性，控制它的行為，比如該屬性是否可寫、可遍歷等等。這個內部資料結構稱為“屬性描述物件”（attributes object）。每個屬性都有自己對應的屬性描述物件，儲存該屬性的一些元資訊。

下面是屬性描述物件的一個例子。

```javascript
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```

屬性描述物件提供6個元屬性。

（1）`value`

`value`是該屬性的屬性值，預設為`undefined`。

（2）`writable`

`writable`是一個布林值，表示屬性值（value）是否可改變（即是否可寫），預設為`true`。

（3）`enumerable`

`enumerable`是一個布林值，表示該屬性是否可遍歷，預設為`true`。如果設為`false`，會使得某些操作（比如`for...in`迴圈、`Object.keys()`）跳過該屬性。

（4）`configurable`

`configurable`是一個布林值，表示可配置性，預設為`true`。如果設為`false`，將阻止某些操作改寫該屬性，比如無法刪除該屬性，也不得改變該屬性的屬性描述物件（`value`屬性除外）。也就是說，`configurable`屬性控制了屬性描述物件的可寫性。

（5）`get`

`get`是一個函式，表示該屬性的取值函式（getter），預設為`undefined`。

（6）`set`

`set`是一個函式，表示該屬性的存值函式（setter），預設為`undefined`。

## Object.getOwnPropertyDescriptor()

`Object.getOwnPropertyDescriptor()`方法可以獲取屬性描述物件。它的第一個引數是目標物件，第二個引數是一個字串，對應目標物件的某個屬性名。

```javascript
var obj = { p: 'a' };

Object.getOwnPropertyDescriptor(obj, 'p')
// Object { value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

上面程式碼中，`Object.getOwnPropertyDescriptor()`方法獲取`obj.p`的屬性描述物件。

注意，`Object.getOwnPropertyDescriptor()`方法只能用於物件自身的屬性，不能用於繼承的屬性。

```javascript
var obj = { p: 'a' };

Object.getOwnPropertyDescriptor(obj, 'toString')
// undefined
```

上面程式碼中，`toString`是`obj`物件繼承的屬性，`Object.getOwnPropertyDescriptor()`無法獲取。

## Object.getOwnPropertyNames()

`Object.getOwnPropertyNames`方法返回一個數組，成員是引數物件自身的全部屬性的屬性名，不管該屬性是否可遍歷。

```javascript
var obj = Object.defineProperties({}, {
  p1: { value: 1, enumerable: true },
  p2: { value: 2, enumerable: false }
});

Object.getOwnPropertyNames(obj)
// ["p1", "p2"]
```

上面程式碼中，`obj.p1`是可遍歷的，`obj.p2`是不可遍歷的。`Object.getOwnPropertyNames`會將它們都返回。

這跟`Object.keys`的行為不同，`Object.keys`只返回物件自身的可遍歷屬性的全部屬性名。

```javascript
Object.keys([]) // []
Object.getOwnPropertyNames([]) // [ 'length' ]

Object.keys(Object.prototype) // []
Object.getOwnPropertyNames(Object.prototype)
// ['hasOwnProperty',
//  'valueOf',
//  'constructor',
//  'toLocaleString',
//  'isPrototypeOf',
//  'propertyIsEnumerable',
//  'toString']
```

上面程式碼中，陣列自身的`length`屬性是不可遍歷的，`Object.keys`不會返回該屬性。第二個例子的`Object.prototype`也是一個物件，所有例項物件都會繼承它，它自身的屬性都是不可遍歷的。

## Object.defineProperty()，Object.defineProperties()

`Object.defineProperty()`方法允許透過屬性描述物件，定義或修改一個屬性，然後返回修改後的物件，它的用法如下。

```javascript
Object.defineProperty(object, propertyName, attributesObject)
```

`Object.defineProperty`方法接受三個引數，依次如下。

- object：屬性所在的物件
- propertyName：字串，表示屬性名
- attributesObject：屬性描述物件

舉例來說，定義`obj.p`可以寫成下面這樣。

```javascript
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false
});

obj.p // 123

obj.p = 246;
obj.p // 123
```

上面程式碼中，`Object.defineProperty()`方法定義了`obj.p`屬性。由於屬性描述物件的`writable`屬性為`false`，所以`obj.p`屬性不可寫。注意，這裡的`Object.defineProperty`方法的第一個引數是`{}`（一個新建的空物件），`p`屬性直接定義在這個空物件上面，然後返回這個物件，這是`Object.defineProperty()`的常見用法。

如果屬性已經存在，`Object.defineProperty()`方法相當於更新該屬性的屬性描述物件。

如果一次性定義或修改多個屬性，可以使用`Object.defineProperties()`方法。

```javascript
var obj = Object.defineProperties({}, {
  p1: { value: 123, enumerable: true },
  p2: { value: 'abc', enumerable: true },
  p3: { get: function () { return this.p1 + this.p2 },
    enumerable:true,
    configurable:true
  }
});

obj.p1 // 123
obj.p2 // "abc"
obj.p3 // "123abc"
```

上面程式碼中，`Object.defineProperties()`同時定義了`obj`物件的三個屬性。其中，`p3`屬性定義了取值函式`get`，即每次讀取該屬性，都會呼叫這個取值函式。

注意，一旦定義了取值函式`get`（或存值函式`set`），就不能將`writable`屬性設為`true`，或者同時定義`value`屬性，否則會報錯。

```javascript
var obj = {};

Object.defineProperty(obj, 'p', {
  value: 123,
  get: function() { return 456; }
});
// TypeError: Invalid property.
// A property cannot both have accessors and be writable or have a value

Object.defineProperty(obj, 'p', {
  writable: true,
  get: function() { return 456; }
});
// TypeError: Invalid property descriptor.
// Cannot both specify accessors and a value or writable attribute
```

上面程式碼中，同時定義了`get`屬性和`value`屬性，以及將`writable`屬性設為`true`，就會報錯。

`Object.defineProperty()`和`Object.defineProperties()`引數裡面的屬性描述物件，`writable`、`configurable`、`enumerable`這三個屬性的預設值都為`false`。

```javascript
var obj = {};
Object.defineProperty(obj, 'foo', {});
Object.getOwnPropertyDescriptor(obj, 'foo')
// {
//   value: undefined,
//   writable: false,
//   enumerable: false,
//   configurable: false
// }
```

上面程式碼中，定義`obj.foo`時用了一個空的屬性描述物件，就可以看到各個元屬性的預設值。

## Object.prototype.propertyIsEnumerable()

例項物件的`propertyIsEnumerable()`方法返回一個布林值，用來判斷某個屬性是否可遍歷。注意，這個方法只能用於判斷物件自身的屬性，對於繼承的屬性一律返回`false`。

```javascript
var obj = {};
obj.p = 123;

obj.propertyIsEnumerable('p') // true
obj.propertyIsEnumerable('toString') // false
```

上面程式碼中，`obj.p`是可遍歷的，而`obj.toString`是繼承的屬性。

## 元屬性

屬性描述物件的各個屬性稱為“元屬性”，因為它們可以看作是控制屬性的屬性。

### value

`value`屬性是目標屬性的值。

```javascript
var obj = {};
obj.p = 123;

Object.getOwnPropertyDescriptor(obj, 'p').value
// 123

Object.defineProperty(obj, 'p', { value: 246 });
obj.p // 246
```

上面程式碼是透過`value`屬性，讀取或改寫`obj.p`的例子。

### writable

`writable`屬性是一個布林值，決定了目標屬性的值（value）是否可以被改變。

```javascript
var obj = {};

Object.defineProperty(obj, 'a', {
  value: 37,
  writable: false
});

obj.a // 37
obj.a = 25;
obj.a // 37
```

上面程式碼中，`obj.a`的`writable`屬性是`false`。然後，改變`obj.a`的值，不會有任何效果。

注意，正常模式下，對`writable`為`false`的屬性賦值不會報錯，只會默默失敗。但是，嚴格模式下會報錯，即使對`a`屬性重新賦予一個同樣的值。

```javascript
'use strict';
var obj = {};

Object.defineProperty(obj, 'a', {
  value: 37,
  writable: false
});

obj.a = 37;
// Uncaught TypeError: Cannot assign to read only property 'a' of object
```

上面程式碼是嚴格模式，對`obj.a`任何賦值行為都會報錯。

如果原型物件的某個屬性的`writable`為`false`，那麼子物件將無法自定義這個屬性。

```javascript
var proto = Object.defineProperty({}, 'foo', {
  value: 'a',
  writable: false
});

var obj = Object.create(proto);

obj.foo = 'b';
obj.foo // 'a'
```

上面程式碼中，`proto`是原型物件，它的`foo`屬性不可寫。`obj`物件繼承`proto`，也不可以再自定義這個屬性了。如果是嚴格模式，這樣做還會丟擲一個錯誤。

但是，有一個規避方法，就是透過覆蓋屬性描述物件，繞過這個限制。原因是這種情況下，原型鏈會被完全忽視。

```javascript
var proto = Object.defineProperty({}, 'foo', {
  value: 'a',
  writable: false
});

var obj = Object.create(proto);
Object.defineProperty(obj, 'foo', {
  value: 'b'
});

obj.foo // "b"
```

### enumerable

`enumerable`（可遍歷性）返回一個布林值，表示目標屬性是否可遍歷。

JavaScript 的早期版本，`for...in`迴圈是基於`in`運算子的。我們知道，`in`運算子不管某個屬性是物件自身的還是繼承的，都會返回`true`。

```javascript
var obj = {};
'toString' in obj // true
```

上面程式碼中，`toString`不是`obj`物件自身的屬性，但是`in`運算子也返回`true`，這導致了`toString`屬性也會被`for...in`迴圈遍歷。

這顯然不太合理，後來就引入了“可遍歷性”這個概念。只有可遍歷的屬性，才會被`for...in`迴圈遍歷，同時還規定`toString`這一類例項物件繼承的原生屬性，都是不可遍歷的，這樣就保證了`for...in`迴圈的可用性。

具體來說，如果一個屬性的`enumerable`為`false`，下面三個操作不會取到該屬性。

- `for..in`迴圈
- `Object.keys`方法
- `JSON.stringify`方法

因此，`enumerable`可以用來設定“祕密”屬性。

```javascript
var obj = {};

Object.defineProperty(obj, 'x', {
  value: 123,
  enumerable: false
});

obj.x // 123

for (var key in obj) {
  console.log(key);
}
// undefined

Object.keys(obj)  // []
JSON.stringify(obj) // "{}"
```

上面程式碼中，`obj.x`屬性的`enumerable`為`false`，所以一般的遍歷操作都無法獲取該屬性，使得它有點像“祕密”屬性，但不是真正的私有屬性，還是可以直接獲取它的值。

注意，`for...in`迴圈包括繼承的屬性，`Object.keys`方法不包括繼承的屬性。如果需要獲取物件自身的所有屬性，不管是否可遍歷，可以使用`Object.getOwnPropertyNames`方法。

另外，`JSON.stringify`方法會排除`enumerable`為`false`的屬性，有時可以利用這一點。如果物件的 JSON 格式輸出要排除某些屬性，就可以把這些屬性的`enumerable`設為`false`。

### configurable

`configurable`(可配置性）返回一個布林值，決定了是否可以修改屬性描述物件。也就是說，`configurable`為`false`時，`value`、`writable`、`enumerable`和`configurable`都不能被修改了。

```javascript
var obj = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  enumerable: false,
  configurable: false
});

Object.defineProperty(obj, 'p', {value: 2})
// TypeError: Cannot redefine property: p

Object.defineProperty(obj, 'p', {writable: true})
// TypeError: Cannot redefine property: p

Object.defineProperty(obj, 'p', {enumerable: true})
// TypeError: Cannot redefine property: p

Object.defineProperty(obj, 'p', {configurable: true})
// TypeError: Cannot redefine property: p
```

上面程式碼中，`obj.p`的`configurable`為`false`。然後，改動`value`、`writable`、`enumerable`、`configurable`，結果都報錯。

注意，`writable`只有在`false`改為`true`會報錯，`true`改為`false`是允許的。

```javascript
var obj = Object.defineProperty({}, 'p', {
  writable: true,
  configurable: false
});

Object.defineProperty(obj, 'p', {writable: false})
// 修改成功
```

至於`value`，只要`writable`和`configurable`有一個為`true`，就允許改動。

```javascript
var o1 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: true,
  configurable: false
});

Object.defineProperty(o1, 'p', {value: 2})
// 修改成功

var o2 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  configurable: true
});

Object.defineProperty(o2, 'p', {value: 2})
// 修改成功
```

另外，`writable`為`false`時，直接目標屬性賦值，不報錯，但不會成功。

```javascript
var obj = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  configurable: false
});

obj.p = 2;
obj.p // 1
```

上面程式碼中，`obj.p`的`writable`為`false`，對`obj.p`直接賦值不會生效。如果是嚴格模式，還會報錯。

可配置性決定了目標屬性是否可以被刪除（delete）。

```javascript
var obj = Object.defineProperties({}, {
  p1: { value: 1, configurable: true },
  p2: { value: 2, configurable: false }
});

delete obj.p1 // true
delete obj.p2 // false

obj.p1 // undefined
obj.p2 // 2
```

上面程式碼中，`obj.p1`的`configurable`是`true`，所以可以被刪除，`obj.p2`就無法刪除。

## 存取器

除了直接定義以外，屬性還可以用存取器（accessor）定義。其中，存值函式稱為`setter`，使用屬性描述物件的`set`屬性；取值函式稱為`getter`，使用屬性描述物件的`get`屬性。

一旦對目標屬性定義了存取器，那麼存取的時候，都將執行對應的函式。利用這個功能，可以實現許多高階特性，比如定製屬性的讀取和賦值行為。

```javascript
var obj = Object.defineProperty({}, 'p', {
  get: function () {
    return 'getter';
  },
  set: function (value) {
    console.log('setter: ' + value);
  }
});

obj.p // "getter"
obj.p = 123 // "setter: 123"
```

上面程式碼中，`obj.p`定義了`get`和`set`屬性。`obj.p`取值時，就會呼叫`get`；賦值時，就會呼叫`set`。

JavaScript 還提供了存取器的另一種寫法。

```javascript
// 寫法二
var obj = {
  get p() {
    return 'getter';
  },
  set p(value) {
    console.log('setter: ' + value);
  }
};
```

上面兩種寫法，雖然屬性`p`的讀取和賦值行為是一樣的，但是有一些細微的區別。第一種寫法，屬性`p`的`configurable`和`enumerable`都為`false`，從而導致屬性`p`是不可遍歷的；第二種寫法，屬性`p`的`configurable`和`enumerable`都為`true`，因此屬性`p`是可遍歷的。實際開發中，寫法二更常用。

注意，取值函式`get`不能接受引數，存值函式`set`只能接受一個引數（即屬性的值）。

存取器往往用於，屬性的值依賴物件內部資料的場合。

```javascript
var obj ={
  $n : 5,
  get next() { return this.$n++ },
  set next(n) {
    if (n >= this.$n) this.$n = n;
    else throw new Error('新的值必須大於當前值');
  }
};

obj.next // 5

obj.next = 10;
obj.next // 10

obj.next = 5;
// Uncaught Error: 新的值必須大於當前值
```

上面程式碼中，`next`屬性的存值函式和取值函式，都依賴於內部屬性`$n`。

## 物件的複製

有時，我們需要將一個物件的所有屬性，複製到另一個物件，可以用下面的方法實現。

```javascript
var extend = function (to, from) {
  for (var property in from) {
    to[property] = from[property];
  }

  return to;
}

extend({}, {
  a: 1
})
// {a: 1}
```

上面這個方法的問題在於，如果遇到存取器定義的屬性，會只複製值。

```javascript
extend({}, {
  get a() { return 1 }
})
// {a: 1}
```

為了解決這個問題，我們可以透過`Object.defineProperty`方法來複製屬性。

```javascript
var extend = function (to, from) {
  for (var property in from) {
    if (!from.hasOwnProperty(property)) continue;
    Object.defineProperty(
      to,
      property,
      Object.getOwnPropertyDescriptor(from, property)
    );
  }

  return to;
}

extend({}, { get a(){ return 1 } })
// { get a(){ return 1 } })
```

上面程式碼中，`hasOwnProperty`那一行用來過濾掉繼承的屬性，否則可能會報錯，因為`Object.getOwnPropertyDescriptor`讀不到繼承屬性的屬性描述物件。

## 控制物件狀態

有時需要凍結物件的讀寫狀態，防止物件被改變。JavaScript 提供了三種凍結方法，最弱的一種是`Object.preventExtensions`，其次是`Object.seal`，最強的是`Object.freeze`。

### Object.preventExtensions()

`Object.preventExtensions`方法可以使得一個物件無法再新增新的屬性。

```javascript
var obj = new Object();
Object.preventExtensions(obj);

Object.defineProperty(obj, 'p', {
  value: 'hello'
});
// TypeError: Cannot define property:p, object is not extensible.

obj.p = 1;
obj.p // undefined
```

上面程式碼中，`obj`物件經過`Object.preventExtensions`以後，就無法新增新屬性了。

### Object.isExtensible()

`Object.isExtensible`方法用於檢查一個物件是否使用了`Object.preventExtensions`方法。也就是說，檢查是否可以為一個物件新增屬性。

```javascript
var obj = new Object();

Object.isExtensible(obj) // true
Object.preventExtensions(obj);
Object.isExtensible(obj) // false
```

上面程式碼中，對`obj`物件使用`Object.preventExtensions`方法以後，再使用`Object.isExtensible`方法，返回`false`，表示已經不能新增新屬性了。

### Object.seal()

`Object.seal`方法使得一個物件既無法新增新屬性，也無法刪除舊屬性。

```javascript
var obj = { p: 'hello' };
Object.seal(obj);

delete obj.p;
obj.p // "hello"

obj.x = 'world';
obj.x // undefined
```

上面程式碼中，`obj`物件執行`Object.seal`方法以後，就無法新增新屬性和刪除舊屬性了。

`Object.seal`實質是把屬性描述物件的`configurable`屬性設為`false`，因此屬性描述物件不再能改變了。

```javascript
var obj = {
  p: 'a'
};

// seal方法之前
Object.getOwnPropertyDescriptor(obj, 'p')
// Object {
//   value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

Object.seal(obj);

// seal方法之後
Object.getOwnPropertyDescriptor(obj, 'p')
// Object {
//   value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: false
// }

Object.defineProperty(o, 'p', {
  enumerable: false
})
// TypeError: Cannot redefine property: p
```

上面程式碼中，使用`Object.seal`方法之後，屬性描述物件的`configurable`屬性就變成了`false`，然後改變`enumerable`屬性就會報錯。

`Object.seal`只是禁止新增或刪除屬性，並不影響修改某個屬性的值。

```javascript
var obj = { p: 'a' };
Object.seal(obj);
obj.p = 'b';
obj.p // 'b'
```

上面程式碼中，`Object.seal`方法對`p`屬性的`value`無效，是因為此時`p`屬性的可寫性由`writable`決定。

### Object.isSealed()

`Object.isSealed`方法用於檢查一個物件是否使用了`Object.seal`方法。

```javascript
var obj = { p: 'a' };

Object.seal(obj);
Object.isSealed(obj) // true
```

這時，`Object.isExtensible`方法也返回`false`。

```javascript
var obj = { p: 'a' };

Object.seal(obj);
Object.isExtensible(obj) // false
```

### Object.freeze()

`Object.freeze`方法可以使得一個物件無法新增新屬性、無法刪除舊屬性、也無法改變屬性的值，使得這個物件實際上變成了常量。

```javascript
var obj = {
  p: 'hello'
};

Object.freeze(obj);

obj.p = 'world';
obj.p // "hello"

obj.t = 'hello';
obj.t // undefined

delete obj.p // false
obj.p // "hello"
```

上面程式碼中，對`obj`物件進行`Object.freeze()`以後，修改屬性、新增屬性、刪除屬性都無效了。這些操作並不報錯，只是默默地失敗。如果在嚴格模式下，則會報錯。

### Object.isFrozen()

`Object.isFrozen`方法用於檢查一個物件是否使用了`Object.freeze`方法。

```javascript
var obj = {
  p: 'hello'
};

Object.freeze(obj);
Object.isFrozen(obj) // true
```

使用`Object.freeze`方法以後，`Object.isSealed`將會返回`true`，`Object.isExtensible`返回`false`。

```javascript
var obj = {
  p: 'hello'
};

Object.freeze(obj);

Object.isSealed(obj) // true
Object.isExtensible(obj) // false
```

`Object.isFrozen`的一個用途是，確認某個物件沒有被凍結後，再對它的屬性賦值。

```javascript
var obj = {
  p: 'hello'
};

Object.freeze(obj);

if (!Object.isFrozen(obj)) {
  obj.p = 'world';
}
```

上面程式碼中，確認`obj`沒有被凍結後，再對它的屬性賦值，就不會報錯了。

### 侷限性

上面的三個方法鎖定物件的可寫性有一個漏洞：可以透過改變原型物件，來為物件增加屬性。

```javascript
var obj = new Object();
Object.preventExtensions(obj);

var proto = Object.getPrototypeOf(obj);
proto.t = 'hello';
obj.t
// hello
```

上面程式碼中，物件`obj`本身不能新增屬性，但是可以在它的原型物件上新增屬性，就依然能夠在`obj`上讀到。

一種解決方案是，把`obj`的原型也凍結住。

```javascript
var obj = new Object();
Object.preventExtensions(obj);

var proto = Object.getPrototypeOf(obj);
Object.preventExtensions(proto);

proto.t = 'hello';
obj.t // undefined
```

另外一個侷限是，如果屬性值是物件，上面這些方法只能凍結屬性指向的物件，而不能凍結物件本身的內容。

```javascript
var obj = {
  foo: 1,
  bar: ['a', 'b']
};
Object.freeze(obj);

obj.bar.push('c');
obj.bar // ["a", "b", "c"]
```

上面程式碼中，`obj.bar`屬性指向一個數組，`obj`物件被凍結以後，這個指向無法改變，即無法指向其他值，但是所指向的陣列是可以改變的。
