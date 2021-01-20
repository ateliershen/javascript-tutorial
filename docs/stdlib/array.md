 Array 物件

## 建構函式

`Array`是 JavaScript 的原生物件，同時也是一個建構函式，可以用它生成新的陣列。

```javascript
var arr = new Array(2);
arr.length // 2
arr // [ empty x 2 ]
```

上面程式碼中，`Array()`建構函式的引數`2`，表示生成一個兩個成員的陣列，每個位置都是空值。

如果沒有使用`new`關鍵字，執行結果也是一樣的。

```javascript
var arr = new Array(2);
// 等同於
var arr = Array(2);
```

考慮到語義性，以及與其他建構函式使用者保持一致，建議總是加上`new`。

`Array()`建構函式有一個很大的缺陷，不同的引數會導致行為不一致。

```javascript
// 無引數時，返回一個空陣列
new Array() // []

// 單個正整數引數，表示返回的新陣列的長度
new Array(1) // [ empty ]
new Array(2) // [ empty x 2 ]

// 非正整數的數值作為引數，會報錯
new Array(3.2) // RangeError: Invalid array length
new Array(-3) // RangeError: Invalid array length

// 單個非數值（比如字串、布林值、物件等）作為引數，
// 則該引數是返回的新陣列的成員
new Array('abc') // ['abc']
new Array([1]) // [Array[1]]

// 多引數時，所有引數都是返回的新陣列的成員
new Array(1, 2) // [1, 2]
new Array('a', 'b', 'c') // ['a', 'b', 'c']
```

可以看到，`Array()`作為建構函式，行為很不一致。因此，不建議使用它生成新陣列，直接使用陣列字面量是更好的做法。

```javascript
// bad
var arr = new Array(1, 2);

// good
var arr = [1, 2];
```

注意，如果引數是一個正整數，返回陣列的成員都是空位。雖然讀取的時候返回`undefined`，但實際上該位置沒有任何值。雖然這時可以讀取到`length`屬性，但是取不到鍵名。

```javascript
var a = new Array(3);
var b = [undefined, undefined, undefined];

a.length // 3
b.length // 3

a[0] // undefined
b[0] // undefined

0 in a // false
0 in b // true
```

上面程式碼中，`a`是`Array()`生成的一個長度為3的空陣列，`b`是一個三個成員都是`undefined`的陣列，這兩個陣列是不一樣的。讀取鍵值的時候，`a`和`b`都返回`undefined`，但是`a`的鍵名（成員的序號）都是空的，`b`的鍵名是有值的。

## 靜態方法

### Array.isArray()

`Array.isArray`方法返回一個布林值，表示引數是否為陣列。它可以彌補`typeof`運算子的不足。

```javascript
var arr = [1, 2, 3];

typeof arr // "object"
Array.isArray(arr) // true
```

上面程式碼中，`typeof`運算子只能顯示陣列的型別是`Object`，而`Array.isArray`方法可以識別陣列。

## 例項方法

### valueOf()，toString()

`valueOf`方法是一個所有物件都擁有的方法，表示對該物件求值。不同物件的`valueOf`方法不盡一致，陣列的`valueOf`方法返回陣列本身。

```javascript
var arr = [1, 2, 3];
arr.valueOf() // [1, 2, 3]
```

`toString`方法也是物件的通用方法，陣列的`toString`方法返回陣列的字串形式。

```javascript
var arr = [1, 2, 3];
arr.toString() // "1,2,3"

var arr = [1, 2, 3, [4, 5, 6]];
arr.toString() // "1,2,3,4,5,6"
```

### push()，pop()

`push`方法用於在陣列的末端新增一個或多個元素，並返回新增新元素後的陣列長度。注意，該方法會改變原陣列。

```javascript
var arr = [];

arr.push(1) // 1
arr.push('a') // 2
arr.push(true, {}) // 4
arr // [1, 'a', true, {}]
```

上面程式碼使用`push`方法，往陣列中添加了四個成員。

`pop`方法用於刪除陣列的最後一個元素，並返回該元素。注意，該方法會改變原陣列。

```javascript
var arr = ['a', 'b', 'c'];

arr.pop() // 'c'
arr // ['a', 'b']
```

對空陣列使用`pop`方法，不會報錯，而是返回`undefined`。

```javascript
[].pop() // undefined
```

`push`和`pop`結合使用，就構成了“後進先出”的棧結構（stack）。

```javascript
var arr = [];
arr.push(1, 2);
arr.push(3);
arr.pop();
arr // [1, 2]
```

上面程式碼中，`3`是最後進入陣列的，但是最早離開陣列。

### shift()，unshift()

`shift()`方法用於刪除陣列的第一個元素，並返回該元素。注意，該方法會改變原陣列。

```javascript
var a = ['a', 'b', 'c'];

a.shift() // 'a'
a // ['b', 'c']
```

上面程式碼中，使用`shift()`方法以後，原陣列就變了。

`shift()`方法可以遍歷並清空一個數組。

```javascript
var list = [1, 2, 3, 4];
var item;

while (item = list.shift()) {
  console.log(item);
}

list // []
```

上面程式碼透過`list.shift()`方法每次取出一個元素，從而遍歷陣列。它的前提是陣列元素不能是`0`或任何布林值等於`false`的元素，因此這樣的遍歷不是很可靠。

`push()`和`shift()`結合使用，就構成了“先進先出”的佇列結構（queue）。

`unshift()`方法用於在陣列的第一個位置新增元素，並返回新增新元素後的陣列長度。注意，該方法會改變原陣列。

```javascript
var a = ['a', 'b', 'c'];

a.unshift('x'); // 4
a // ['x', 'a', 'b', 'c']
```

`unshift()`方法可以接受多個引數，這些引數都會新增到目標陣列頭部。

```javascript
var arr = [ 'c', 'd' ];
arr.unshift('a', 'b') // 4
arr // [ 'a', 'b', 'c', 'd' ]
```

### join()

`join()`方法以指定引數作為分隔符，將所有陣列成員連線為一個字串返回。如果不提供引數，預設用逗號分隔。

```javascript
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```

如果陣列成員是`undefined`或`null`或空位，會被轉成空字串。

```javascript
[undefined, null].join('#')
// '#'

['a',, 'b'].join('-')
// 'a--b'
```

透過`call`方法，這個方法也可以用於字串或類似陣列的物件。

```javascript
Array.prototype.join.call('hello', '-')
// "h-e-l-l-o"

var obj = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.join.call(obj, '-')
// 'a-b'
```

### concat()

`concat`方法用於多個數組的合併。它將新陣列的成員，新增到原陣列成員的後部，然後返回一個新陣列，原陣列不變。

```javascript
['hello'].concat(['world'])
// ["hello", "world"]

['hello'].concat(['world'], ['!'])
// ["hello", "world", "!"]

[].concat({a: 1}, {b: 2})
// [{ a: 1 }, { b: 2 }]

[2].concat({a: 1})
// [2, {a: 1}]
```

除了陣列作為引數，`concat`也接受其他型別的值作為引數，新增到目標陣列尾部。

```javascript
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]
```

如果陣列成員包括物件，`concat`方法返回當前陣列的一個淺複製。所謂“淺複製”，指的是新陣列複製的是物件的引用。

```javascript
var obj = { a: 1 };
var oldArray = [obj];

var newArray = oldArray.concat();

obj.a = 2;
newArray[0].a // 2
```

上面程式碼中，原陣列包含一個物件，`concat`方法生成的新陣列包含這個物件的引用。所以，改變原物件以後，新陣列跟著改變。

### reverse()

`reverse`方法用於顛倒排列陣列元素，返回改變後的陣列。注意，該方法將改變原陣列。

```javascript
var a = ['a', 'b', 'c'];

a.reverse() // ["c", "b", "a"]
a // ["c", "b", "a"]
```

### slice()

`slice()`方法用於提取目標陣列的一部分，返回一個新陣列，原陣列不變。

```javascript
arr.slice(start, end);
```

它的第一個引數為起始位置（從0開始，會包括在返回的新陣列之中），第二個引數為終止位置（但該位置的元素本身不包括在內）。如果省略第二個引數，則一直返回到原陣列的最後一個成員。

```javascript
var a = ['a', 'b', 'c'];

a.slice(0) // ["a", "b", "c"]
a.slice(1) // ["b", "c"]
a.slice(1, 2) // ["b"]
a.slice(2, 6) // ["c"]
a.slice() // ["a", "b", "c"]
```

上面程式碼中，最後一個例子`slice()`沒有引數，實際上等於返回一個原陣列的複製。

如果`slice()`方法的引數是負數，則表示倒數計算的位置。

```javascript
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
```

上面程式碼中，`-2`表示倒數計算的第二個位置，`-1`表示倒數計算的第一個位置。

如果第一個引數大於等於陣列長度，或者第二個引數小於第一個引數，則返回空陣列。

```javascript
var a = ['a', 'b', 'c'];
a.slice(4) // []
a.slice(2, 1) // []
```

`slice()`方法的一個重要應用，是將類似陣列的物件轉為真正的陣列。

```javascript
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```

上面程式碼的引數都不是陣列，但是透過`call`方法，在它們上面呼叫`slice()`方法，就可以把它們轉為真正的陣列。

### splice()

`splice()`方法用於刪除原陣列的一部分成員，並可以在刪除的位置新增新的陣列成員，返回值是被刪除的元素。注意，該方法會改變原陣列。

```javascript
arr.splice(start, count, addElement1, addElement2, ...);
```

`splice`的第一個引數是刪除的起始位置（從0開始），第二個引數是被刪除的元素個數。如果後面還有更多的引數，則表示這些就是要被插入陣列的新元素。

```javascript
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2) // ["e", "f"]
a // ["a", "b", "c", "d"]
```

上面程式碼從原陣列4號位置，刪除了兩個陣列成員。

```javascript
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2, 1, 2) // ["e", "f"]
a // ["a", "b", "c", "d", 1, 2]
```

上面程式碼除了刪除成員，還插入了兩個新成員。

起始位置如果是負數，就表示從倒數位置開始刪除。

```javascript
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(-4, 2) // ["c", "d"]
```

上面程式碼表示，從倒數第四個位置`c`開始刪除兩個成員。

如果只是單純地插入元素，`splice`方法的第二個引數可以設為`0`。

```javascript
var a = [1, 1, 1];

a.splice(1, 0, 2) // []
a // [1, 2, 1, 1]
```

如果只提供第一個引數，等同於將原陣列在指定位置拆分成兩個陣列。

```javascript
var a = [1, 2, 3, 4];
a.splice(2) // [3, 4]
a // [1, 2]
```

### sort()

`sort`方法對陣列成員進行排序，預設是按照字典順序排序。排序後，原陣列將被改變。

```javascript
['d', 'c', 'b', 'a'].sort()
// ['a', 'b', 'c', 'd']

[4, 3, 2, 1].sort()
// [1, 2, 3, 4]

[11, 101].sort()
// [101, 11]

[10111, 1101, 111].sort()
// [10111, 1101, 111]
```

上面程式碼的最後兩個例子，需要特殊注意。`sort()`方法不是按照大小排序，而是按照字典順序。也就是說，數值會被先轉成字串，再按照字典順序進行比較，所以`101`排在`11`的前面。

如果想讓`sort`方法按照自定義方式排序，可以傳入一個函式作為引數。

```javascript
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
```

上面程式碼中，`sort`的引數函式本身接受兩個引數，表示進行比較的兩個陣列成員。如果該函式的返回值大於`0`，表示第一個成員排在第二個成員後面；其他情況下，都是第一個元素排在第二個元素前面。

```javascript
[
  { name: "張三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "張三", age: 30 }
// ]
```

注意，自定義的排序函式應該返回數值，否則不同的瀏覽器可能有不同的實現，不能保證結果都一致。

```javascript
// bad
[1, 4, 2, 6, 0, 6, 2, 6].sort((a, b) => a > b)

// good
[1, 4, 2, 6, 0, 6, 2, 6].sort((a, b) => a - b)
```

上面程式碼中，前一種排序演算法返回的是布林值，這是不推薦使用的。後一種是數值，才是更好的寫法。

### map()

`map`方法將陣列的所有成員依次傳入引數函式，然後把每一次的執行結果組成一個新陣列返回。

```javascript
var numbers = [1, 2, 3];

numbers.map(function (n) {
  return n + 1;
});
// [2, 3, 4]

numbers
// [1, 2, 3]
```

上面程式碼中，`numbers`陣列的所有成員依次執行引數函式，執行結果組成一個新陣列返回，原陣列沒有變化。

`map`方法接受一個函式作為引數。該函式呼叫時，`map`方法向它傳入三個引數：當前成員、當前位置和陣列本身。

```javascript
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```

上面程式碼中，`map`方法的回撥函式有三個引數，`elem`為當前成員的值，`index`為當前成員的位置，`arr`為原陣列（`[1, 2, 3]`）。

`map`方法還可以接受第二個引數，用來繫結回撥函式內部的`this`變數（詳見《this 變數》一章）。

```javascript
var arr = ['a', 'b', 'c'];

[1, 2].map(function (e) {
  return this[e];
}, arr)
// ['b', 'c']
```

上面程式碼透過`map`方法的第二個引數，將回調函式內部的`this`物件，指向`arr`陣列。

如果陣列有空位，`map`方法的回撥函式在這個位置不會執行，會跳過陣列的空位。

```javascript
var f = function (n) { return 'a' };

[1, undefined, 2].map(f) // ["a", "a", "a"]
[1, null, 2].map(f) // ["a", "a", "a"]
[1, , 2].map(f) // ["a", , "a"]
```

上面程式碼中，`map`方法不會跳過`undefined`和`null`，但是會跳過空位。

### forEach()

`forEach`方法與`map`方法很相似，也是對陣列的所有成員依次執行引數函式。但是，`forEach`方法不返回值，只用來操作資料。這就是說，如果陣列遍歷的目的是為了得到返回值，那麼使用`map`方法，否則使用`forEach`方法。

`forEach`的用法與`map`方法一致，引數是一個函式，該函式同樣接受三個引數：當前值、當前位置、整個陣列。

```javascript
function log(element, index, array) {
  console.log('[' + index + '] = ' + element);
}

[2, 5, 9].forEach(log);
// [0] = 2
// [1] = 5
// [2] = 9
```

上面程式碼中，`forEach`遍歷陣列不是為了得到返回值，而是為了在螢幕輸出內容，所以不必使用`map`方法。

`forEach`方法也可以接受第二個引數，繫結引數函式的`this`變數。

```javascript
var out = [];

[1, 2, 3].forEach(function(elem) {
  this.push(elem * elem);
}, out);

out // [1, 4, 9]
```

上面程式碼中，空陣列`out`是`forEach`方法的第二個引數，結果，回撥函式內部的`this`關鍵字就指向`out`。

注意，`forEach`方法無法中斷執行，總是會將所有成員遍歷完。如果希望符合某種條件時，就中斷遍歷，要使用`for`迴圈。

```javascript
var arr = [1, 2, 3];

for (var i = 0; i < arr.length; i++) {
  if (arr[i] === 2) break;
  console.log(arr[i]);
}
// 1
```

上面程式碼中，執行到陣列的第二個成員時，就會中斷執行。`forEach`方法做不到這一點。

`forEach`方法也會跳過陣列的空位。

```javascript
var log = function (n) {
  console.log(n + 1);
};

[1, undefined, 2].forEach(log)
// 2
// NaN
// 3

[1, null, 2].forEach(log)
// 2
// 1
// 3

[1, , 2].forEach(log)
// 2
// 3
```

上面程式碼中，`forEach`方法不會跳過`undefined`和`null`，但會跳過空位。

### filter()

`filter`方法用於過濾陣列成員，滿足條件的成員組成一個新陣列返回。

它的引數是一個函式，所有陣列成員依次執行該函式，返回結果為`true`的成員組成一個新陣列返回。該方法不會改變原陣列。

```javascript
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```

上面程式碼將大於`3`的陣列成員，作為一個新陣列返回。

```javascript
var arr = [0, 1, 'a', false];

arr.filter(Boolean)
// [1, "a"]
```

上面程式碼中，`filter`方法返回陣列`arr`裡面所有布林值為`true`的成員。

`filter`方法的引數函式可以接受三個引數：當前成員，當前位置和整個陣列。

```javascript
[1, 2, 3, 4, 5].filter(function (elem, index, arr) {
  return index % 2 === 0;
});
// [1, 3, 5]
```

上面程式碼返回偶數位置的成員組成的新陣列。

`filter`方法還可以接受第二個引數，用來繫結引數函式內部的`this`變數。

```javascript
var obj = { MAX: 3 };
var myFilter = function (item) {
  if (item > this.MAX) return true;
};

var arr = [2, 8, 3, 4, 1, 3, 2, 9];
arr.filter(myFilter, obj) // [8, 4, 9]
```

上面程式碼中，過濾器`myFilter`內部有`this`變數，它可以被`filter`方法的第二個引數`obj`繫結，返回大於`3`的成員。

### some()，every()

這兩個方法類似“斷言”（assert），返回一個布林值，表示判斷陣列成員是否符合某種條件。

它們接受一個函式作為引數，所有陣列成員依次執行該函式。該函式接受三個引數：當前成員、當前位置和整個陣列，然後返回一個布林值。

`some`方法是隻要一個成員的返回值是`true`，則整個`some`方法的返回值就是`true`，否則返回`false`。

```javascript
var arr = [1, 2, 3, 4, 5];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true
```

上面程式碼中，如果陣列`arr`有一個成員大於等於3，`some`方法就返回`true`。

`every`方法是所有成員的返回值都是`true`，整個`every`方法才返回`true`，否則返回`false`。

```javascript
var arr = [1, 2, 3, 4, 5];
arr.every(function (elem, index, arr) {
  return elem >= 3;
});
// false
```

上面程式碼中，陣列`arr`並非所有成員大於等於`3`，所以返回`false`。

注意，對於空陣列，`some`方法返回`false`，`every`方法返回`true`，回撥函式都不會執行。

```javascript
function isEven(x) { return x % 2 === 0 }

[].some(isEven) // false
[].every(isEven) // true
```

`some`和`every`方法還可以接受第二個引數，用來繫結引數函式內部的`this`變數。

### reduce()，reduceRight()

`reduce`方法和`reduceRight`方法依次處理陣列的每個成員，最終累計為一個值。它們的差別是，`reduce`是從左到右處理（從第一個成員到最後一個成員），`reduceRight`則是從右到左（從最後一個成員到第一個成員），其他完全一樣。

```javascript
[1, 2, 3, 4, 5].reduce(function (a, b) {
  console.log(a, b);
  return a + b;
})
// 1 2
// 3 3
// 6 4
// 10 5
//最後結果：15
```

上面程式碼中，`reduce`方法求出陣列所有成員的和。第一次執行，`a`是陣列的第一個成員`1`，`b`是陣列的第二個成員`2`。第二次執行，`a`為上一輪的返回值`3`，`b`為第三個成員`3`。第三次執行，`a`為上一輪的返回值`6`，`b`為第四個成員`4`。第四次執行，`a`為上一輪返回值`10`，`b`為第五個成員`5`。至此所有成員遍歷完成，整個方法的返回值就是最後一輪的返回值`15`。

`reduce`方法和`reduceRight`方法的第一個引數都是一個函式。該函式接受以下四個引數。

1. 累積變數，預設為陣列的第一個成員
2. 當前變數，預設為陣列的第二個成員
3. 當前位置（從0開始）
4. 原陣列

這四個引數之中，只有前兩個是必須的，後兩個則是可選的。

如果要對累積變數指定初值，可以把它放在`reduce`方法和`reduceRight`方法的第二個引數。

```javascript
[1, 2, 3, 4, 5].reduce(function (a, b) {
  return a + b;
}, 10);
// 25
```

上面程式碼指定引數`a`的初值為10，所以陣列從10開始累加，最終結果為25。注意，這時`b`是從陣列的第一個成員開始遍歷。

上面的第二個引數相當於設定了預設值，處理空陣列時尤其有用。

```javascript
function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
```

上面程式碼中，由於空陣列取不到初始值，`reduce`方法會報錯。這時，加上第二個引數，就能保證總是會返回一個值。

下面是一個`reduceRight`方法的例子。

```javascript
function subtract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(subtract) // 0
[3, 2, 1].reduceRight(subtract) // -4
```

上面程式碼中，`reduce`方法相當於`3`減去`2`再減去`1`，`reduceRight`方法相當於`1`減去`2`再減去`3`。

由於這兩個方法會遍歷陣列，所以實際上還可以用來做一些遍歷相關的操作。比如，找出字元長度最長的陣列成員。

```javascript
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```

上面程式碼中，`reduce`的引數函式會將字元長度較長的那個陣列成員，作為累積值。這導致遍歷所有成員之後，累積值就是字元長度最長的那個成員。

### indexOf()，lastIndexOf()

`indexOf`方法返回給定元素在陣列中第一次出現的位置，如果沒有出現則返回`-1`。

```javascript
var a = ['a', 'b', 'c'];

a.indexOf('b') // 1
a.indexOf('y') // -1
```

`indexOf`方法還可以接受第二個引數，表示搜尋的開始位置。

```javascript
['a', 'b', 'c'].indexOf('a', 1) // -1
```

上面程式碼從1號位置開始搜尋字元`a`，結果為`-1`，表示沒有搜尋到。

`lastIndexOf`方法返回給定元素在陣列中最後一次出現的位置，如果沒有出現則返回`-1`。

```javascript
var a = [2, 5, 9, 2];
a.lastIndexOf(2) // 3
a.lastIndexOf(7) // -1
```

注意，這兩個方法不能用來搜尋`NaN`的位置，即它們無法確定陣列成員是否包含`NaN`。

```javascript
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```

這是因為這兩個方法內部，使用嚴格相等運算子（`===`）進行比較，而`NaN`是唯一一個不等於自身的值。

### 鏈式使用

上面這些陣列方法之中，有不少返回的還是陣列，所以可以鏈式使用。

```javascript
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(function (email) {
  console.log(email);
});
// "tom@example.com"
```

上面程式碼中，先產生一個所有 Email 地址組成的陣列，然後再過濾出以`t`開頭的 Email 地址，最後將它打印出來。

## 參考連結

- Nicolas Bevacqua, [Fun with JavaScript Native Array Functions](http://flippinawesome.org/2013/11/25/fun-with-javascript-native-array-functions/)
