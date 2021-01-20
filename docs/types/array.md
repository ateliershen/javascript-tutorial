# 陣列

## 定義

陣列（array）是按次序排列的一組值。每個值的位置都有編號（從0開始），整個陣列用方括號表示。

```javascript
var arr = ['a', 'b', 'c'];
```

上面程式碼中的`a`、`b`、`c`就構成一個數組，兩端的方括號是陣列的標誌。`a`是0號位置，`b`是1號位置，`c`是2號位置。

除了在定義時賦值，陣列也可以先定義後賦值。

```javascript
var arr = [];

arr[0] = 'a';
arr[1] = 'b';
arr[2] = 'c';
```

任何型別的資料，都可以放入陣列。

```javascript
var arr = [
  {a: 1},
  [1, 2, 3],
  function() {return true;}
];

arr[0] // Object {a: 1}
arr[1] // [1, 2, 3]
arr[2] // function (){return true;}
```

上面陣列`arr`的3個成員依次是物件、陣列、函式。

如果陣列的元素還是陣列，就形成了多維陣列。

```javascript
var a = [[1, 2], [3, 4]];
a[0][1] // 2
a[1][1] // 4
```

## 陣列的本質

本質上，陣列屬於一種特殊的物件。`typeof`運算子會返回陣列的型別是`object`。

```javascript
typeof [1, 2, 3] // "object"
```

上面程式碼表明，`typeof`運算子認為陣列的型別就是物件。

陣列的特殊性體現在，它的鍵名是按次序排列的一組整數（0，1，2...）。

```javascript
var arr = ['a', 'b', 'c'];

Object.keys(arr)
// ["0", "1", "2"]
```

上面程式碼中，`Object.keys`方法返回陣列的所有鍵名。可以看到陣列的鍵名就是整數0、1、2。

由於陣列成員的鍵名是固定的（預設總是0、1、2...），因此陣列不用為每個元素指定鍵名，而物件的每個成員都必須指定鍵名。JavaScript 語言規定，物件的鍵名一律為字串，所以，陣列的鍵名其實也是字串。之所以可以用數值讀取，是因為非字串的鍵名會被轉為字串。

```javascript
var arr = ['a', 'b', 'c'];

arr['0'] // 'a'
arr[0] // 'a'
```

上面程式碼分別用數值和字串作為鍵名，結果都能讀取陣列。原因是數值鍵名被自動轉為了字串。

注意，這點在賦值時也成立。一個值總是先轉成字串，再作為鍵名進行賦值。

```javascript
var a = [];

a[1.00] = 6;
a[1] // 6
```

上面程式碼中，由於`1.00`轉成字串是`1`，所以透過數字鍵`1`可以讀取值。

上一章說過，物件有兩種讀取成員的方法：點結構（`object.key`）和方括號結構（`object[key]`）。但是，對於數值的鍵名，不能使用點結構。

```javascript
var arr = [1, 2, 3];
arr.0 // SyntaxError
```

上面程式碼中，`arr.0`的寫法不合法，因為單獨的數值不能作為識別符號（identifier）。所以，陣列成員只能用方括號`arr[0]`表示（方括號是運算子，可以接受數值）。

## length 屬性

陣列的`length`屬性，返回陣列的成員數量。

```javascript
['a', 'b', 'c'].length // 3
```

JavaScript 使用一個32位整數，儲存陣列的元素個數。這意味著，陣列成員最多隻有 4294967295 個（2<sup>32</sup> - 1）個，也就是說`length`屬性的最大值就是 4294967295。

只要是陣列，就一定有`length`屬性。該屬性是一個動態的值，等於鍵名中的最大整數加上`1`。

```javascript
var arr = ['a', 'b'];
arr.length // 2

arr[2] = 'c';
arr.length // 3

arr[9] = 'd';
arr.length // 10

arr[1000] = 'e';
arr.length // 1001
```

上面程式碼表示，陣列的數字鍵不需要連續，`length`屬性的值總是比最大的那個整數鍵大`1`。另外，這也表明陣列是一種動態的資料結構，可以隨時增減陣列的成員。

`length`屬性是可寫的。如果人為設定一個小於當前成員個數的值，該陣列的成員數量會自動減少到`length`設定的值。

```javascript
var arr = [ 'a', 'b', 'c' ];
arr.length // 3

arr.length = 2;
arr // ["a", "b"]
```

上面程式碼表示，當陣列的`length`屬性設為2（即最大的整數鍵只能是1）那麼整數鍵2（值為`c`）就已經不在陣列中了，被自動刪除了。

清空陣列的一個有效方法，就是將`length`屬性設為0。

```javascript
var arr = [ 'a', 'b', 'c' ];

arr.length = 0;
arr // []
```

如果人為設定`length`大於當前元素個數，則陣列的成員數量會增加到這個值，新增的位置都是空位。

```javascript
var a = ['a'];

a.length = 3;
a[1] // undefined
```

上面程式碼表示，當`length`屬性設為大於陣列個數時，讀取新增的位置都會返回`undefined`。

如果人為設定`length`為不合法的值，JavaScript 會報錯。

```javascript
// 設定負值
[].length = -1
// RangeError: Invalid array length

// 陣列元素個數大於等於2的32次方
[].length = Math.pow(2, 32)
// RangeError: Invalid array length

// 設定字串
[].length = 'abc'
// RangeError: Invalid array length
```

值得注意的是，由於陣列本質上是一種物件，所以可以為陣列新增屬性，但是這不影響`length`屬性的值。

```javascript
var a = [];

a['p'] = 'abc';
a.length // 0

a[2.1] = 'abc';
a.length // 0
```

上面程式碼將陣列的鍵分別設為字串和小數，結果都不影響`length`屬性。因為，`length`屬性的值就是等於最大的數字鍵加1，而這個陣列沒有整數鍵，所以`length`屬性保持為`0`。

如果陣列的鍵名是新增超出範圍的數值，該鍵名會自動轉為字串。

```javascript
var arr = [];
arr[-1] = 'a';
arr[Math.pow(2, 32)] = 'b';

arr.length // 0
arr[-1] // "a"
arr[4294967296] // "b"
```

上面程式碼中，我們為陣列`arr`添加了兩個不合法的數字鍵，結果`length`屬性沒有發生變化。這些數字鍵都變成了字串鍵名。最後兩行之所以會取到值，是因為取鍵值時，數字鍵名會預設轉為字串。

## in 運算子

檢查某個鍵名是否存在的運算子`in`，適用於物件，也適用於陣列。

```javascript
var arr = [ 'a', 'b', 'c' ];
2 in arr  // true
'2' in arr // true
4 in arr // false
```

上面程式碼表明，陣列存在鍵名為`2`的鍵。由於鍵名都是字串，所以數值`2`會自動轉成字串。

注意，如果陣列的某個位置是空位，`in`運算子返回`false`。

```javascript
var arr = [];
arr[100] = 'a';

100 in arr // true
1 in arr // false
```

上面程式碼中，陣列`arr`只有一個成員`arr[100]`，其他位置的鍵名都會返回`false`。

## for...in 迴圈和陣列的遍歷

`for...in`迴圈不僅可以遍歷物件，也可以遍歷陣列，畢竟陣列只是一種特殊物件。

```javascript
var a = [1, 2, 3];

for (var i in a) {
  console.log(a[i]);
}
// 1
// 2
// 3
```

但是，`for...in`不僅會遍歷陣列所有的數字鍵，還會遍歷非數字鍵。

```javascript
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```

上面程式碼在遍歷陣列時，也遍歷到了非整數鍵`foo`。所以，不推薦使用`for...in`遍歷陣列。

陣列的遍歷可以考慮使用`for`迴圈或`while`迴圈。

```javascript
var a = [1, 2, 3];

// for迴圈
for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}

// while迴圈
var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}

var l = a.length;
while (l--) {
  console.log(a[l]);
}
```

上面程式碼是三種遍歷陣列的寫法。最後一種寫法是逆向遍歷，即從最後一個元素向第一個元素遍歷。

陣列的`forEach`方法，也可以用來遍歷陣列，詳見《標準庫》的 Array 物件一章。

```javascript
var colors = ['red', 'green', 'blue'];
colors.forEach(function (color) {
  console.log(color);
});
// red
// green
// blue
```

## 陣列的空位

當陣列的某個位置是空元素，即兩個逗號之間沒有任何值，我們稱該陣列存在空位（hole）。

```javascript
var a = [1, , 1];
a.length // 3
```

上面程式碼表明，陣列的空位不影響`length`屬性。

需要注意的是，如果最後一個元素後面有逗號，並不會產生空位。也就是說，有沒有這個逗號，結果都是一樣的。

```javascript
var a = [1, 2, 3,];

a.length // 3
a // [1, 2, 3]
```

上面程式碼中，陣列最後一個成員後面有一個逗號，這不影響`length`屬性的值，與沒有這個逗號時效果一樣。

陣列的空位是可以讀取的，返回`undefined`。

```javascript
var a = [, , ,];
a[1] // undefined
```

使用`delete`命令刪除一個數組成員，會形成空位，並且不會影響`length`屬性。

```javascript
var a = [1, 2, 3];
delete a[1];

a[1] // undefined
a.length // 3
```

上面程式碼用`delete`命令刪除了陣列的第二個元素，這個位置就形成了空位，但是對`length`屬性沒有影響。也就是說，`length`屬性不過濾空位。所以，使用`length`屬性進行陣列遍歷，一定要非常小心。

陣列的某個位置是空位，與某個位置是`undefined`，是不一樣的。如果是空位，使用陣列的`forEach`方法、`for...in`結構、以及`Object.keys`方法進行遍歷，空位都會被跳過。

```javascript
var a = [, , ,];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
})
// 不產生任何輸出

for (var i in a) {
  console.log(i);
}
// 不產生任何輸出

Object.keys(a)
// []
```

如果某個位置是`undefined`，遍歷的時候就不會被跳過。

```javascript
var a = [undefined, undefined, undefined];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
});
// 0. undefined
// 1. undefined
// 2. undefined

for (var i in a) {
  console.log(i);
}
// 0
// 1
// 2

Object.keys(a)
// ['0', '1', '2']
```

這就是說，空位就是陣列沒有這個元素，所以不會被遍歷到，而`undefined`則表示陣列有這個元素，值是`undefined`，所以遍歷不會跳過。

## 類似陣列的物件

如果一個物件的所有鍵名都是正整數或零，並且有`length`屬性，那麼這個物件就很像陣列，語法上稱為“類似陣列的物件”（array-like object）。

```javascript
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3
};

obj[0] // 'a'
obj[1] // 'b'
obj.length // 3
obj.push('d') // TypeError: obj.push is not a function
```

上面程式碼中，物件`obj`就是一個類似陣列的物件。但是，“類似陣列的物件”並不是陣列，因為它們不具備陣列特有的方法。物件`obj`沒有陣列的`push`方法，使用該方法就會報錯。

“類似陣列的物件”的根本特徵，就是具有`length`屬性。只要有`length`屬性，就可以認為這個物件類似於陣列。但是有一個問題，這種`length`屬性不是動態值，不會隨著成員的變化而變化。

```javascript
var obj = {
  length: 0
};
obj[3] = 'd';
obj.length // 0
```

上面程式碼為物件`obj`添加了一個數字鍵，但是`length`屬性沒變。這就說明了`obj`不是陣列。

典型的“類似陣列的物件”是函式的`arguments`物件，以及大多數 DOM 元素集，還有字串。

```javascript
// arguments物件
function args() { return arguments }
var arrayLike = args('a', 'b');

arrayLike[0] // 'a'
arrayLike.length // 2
arrayLike instanceof Array // false

// DOM元素集
var elts = document.getElementsByTagName('h3');
elts.length // 3
elts instanceof Array // false

// 字串
'abc'[1] // 'b'
'abc'.length // 3
'abc' instanceof Array // false
```

上面程式碼包含三個例子，它們都不是陣列（`instanceof`運算子返回`false`），但是看上去都非常像陣列。

陣列的`slice`方法可以將“類似陣列的物件”變成真正的陣列。

```javascript
var arr = Array.prototype.slice.call(arrayLike);
```

除了轉為真正的陣列，“類似陣列的物件”還有一個辦法可以使用陣列的方法，就是透過`call()`把陣列的方法放到物件上面。

```javascript
function print(value, index) {
  console.log(index + ' : ' + value);
}

Array.prototype.forEach.call(arrayLike, print);
```

上面程式碼中，`arrayLike`代表一個類似陣列的物件，本來是不可以使用陣列的`forEach()`方法的，但是透過`call()`，可以把`forEach()`嫁接到`arrayLike`上面呼叫。

下面的例子就是透過這種方法，在`arguments`物件上面呼叫`forEach`方法。

```javascript
// forEach 方法
function logArgs() {
  Array.prototype.forEach.call(arguments, function (elem, i) {
    console.log(i + '. ' + elem);
  });
}

// 等同於 for 迴圈
function logArgs() {
  for (var i = 0; i < arguments.length; i++) {
    console.log(i + '. ' + arguments[i]);
  }
}
```

字串也是類似陣列的物件，所以也可以用`Array.prototype.forEach.call`遍歷。

```javascript
Array.prototype.forEach.call('abc', function (chr) {
  console.log(chr);
});
// a
// b
// c
```

注意，這種方法比直接使用陣列原生的`forEach`要慢，所以最好還是先將“類似陣列的物件”轉為真正的陣列，然後再直接呼叫陣列的`forEach`方法。

```javascript
var arr = Array.prototype.slice.call('abc');
arr.forEach(function (chr) {
  console.log(chr);
});
// a
// b
// c
```

## 參考連結

- Axel Rauschmayer, [Arrays in JavaScript](http://www.2ality.com/2012/12/arrays.html)
- Axel Rauschmayer, [JavaScript: sparse arrays vs. dense arrays](http://www.2ality.com/2012/06/dense-arrays.html)
- Felix Bohm, [What They Didn’t Tell You About ES5′s Array Extras](http://net.tutsplus.com/tutorials/javascript-ajax/what-they-didnt-tell-you-about-es5s-array-extras/)
- Juriy Zaytsev, [How ECMAScript 5 still does not allow to subclass an array](http://perfectionkills.com/how-ecmascript-5-still-does-not-allow-to-subclass-an-array/)
