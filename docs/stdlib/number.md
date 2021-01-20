# Number 物件

## 概述

`Number`物件是數值對應的包裝物件，可以作為建構函式使用，也可以作為工具函式使用。

作為建構函式時，它用於生成值為數值的物件。

```javascript
var n = new Number(1);
typeof n // "object"
```

上面程式碼中，`Number`物件作為建構函式使用，返回一個值為`1`的物件。

作為工具函式時，它可以將任何型別的值轉為數值。

```javascript
Number(true) // 1
```

上面程式碼將布林值`true`轉為數值`1`。`Number`作為工具函式的用法，詳見《資料型別轉換》一章。

## 靜態屬性

`Number`物件擁有以下一些靜態屬性（即直接定義在`Number`物件上的屬性，而不是定義在例項上的屬性）。

- `Number.POSITIVE_INFINITY`：正的無限，指向`Infinity`。
- `Number.NEGATIVE_INFINITY`：負的無限，指向`-Infinity`。
- `Number.NaN`：表示非數值，指向`NaN`。
- `Number.MIN_VALUE`：表示最小的正數（即最接近0的正數，在64位浮點數體系中為`5e-324`），相應的，最接近0的負數為`-Number.MIN_VALUE`。
- `Number.MAX_SAFE_INTEGER`：表示能夠精確表示的最大整數，即`9007199254740991`。
- `Number.MIN_SAFE_INTEGER`：表示能夠精確表示的最小整數，即`-9007199254740991`。

```javascript
Number.POSITIVE_INFINITY // Infinity
Number.NEGATIVE_INFINITY // -Infinity
Number.NaN // NaN

Number.MAX_VALUE
// 1.7976931348623157e+308
Number.MAX_VALUE < Infinity
// true

Number.MIN_VALUE
// 5e-324
Number.MIN_VALUE > 0
// true

Number.MAX_SAFE_INTEGER // 9007199254740991
Number.MIN_SAFE_INTEGER // -9007199254740991
```

## 例項方法

`Number`物件有4個例項方法，都跟將數值轉換成指定格式有關。

### Number.prototype.toString()

`Number`物件部署了自己的`toString`方法，用來將一個數值轉為字串形式。

```javascript
(10).toString() // "10"
```

`toString`方法可以接受一個引數，表示輸出的進位制。如果省略這個引數，預設將數值先轉為十進位制，再輸出字串；否則，就根據引數指定的進位制，將一個數字轉化成某個進位制的字串。

```javascript
(10).toString(2) // "1010"
(10).toString(8) // "12"
(10).toString(16) // "a"
```

上面程式碼中，`10`一定要放在括號裡，這樣表明後面的點表示呼叫物件屬性。如果不加括號，這個點會被 JavaScript 引擎解釋成小數點，從而報錯。

```javascript
10.toString(2)
// SyntaxError: Unexpected token ILLEGAL
```

只要能夠讓 JavaScript 引擎不混淆小數點和物件的點運算子，各種寫法都能用。除了為`10`加上括號，還可以在`10`後面加兩個點，JavaScript 會把第一個點理解成小數點（即`10.0`），把第二個點理解成呼叫物件屬性，從而得到正確結果。

```javascript
10..toString(2)
// "1010"

// 其他方法還包括
10 .toString(2) // "1010"
10.0.toString(2) // "1010"
```

這實際上意味著，可以直接對一個小數使用`toString`方法。

```javascript
10.5.toString() // "10.5"
10.5.toString(2) // "1010.1"
10.5.toString(8) // "12.4"
10.5.toString(16) // "a.8"
```

透過方括號運算子也可以呼叫`toString`方法。

```javascript
10['toString'](2) // "1010"
```

`toString`方法只能將十進位制的數，轉為其他進位制的字串。如果要將其他進位制的數，轉回十進位制，需要使用`parseInt`方法。

### Number.prototype.toFixed()

`toFixed()`方法先將一個數轉為指定位數的小數，然後返回這個小數對應的字串。

```javascript
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01"
```

上面程式碼中，`10`和`10.005`先轉成2位小數，然後轉成字串。其中`10`必須放在括號裡，否則後面的點會被處理成小數點。

`toFixed()`方法的引數為小數位數，有效範圍為0到100，超出這個範圍將丟擲 RangeError 錯誤。

由於浮點數的原因，小數`5`的四捨五入是不確定的，使用的時候必須小心。

```javascript
(10.055).toFixed(2) // 10.05
(10.005).toFixed(2) // 10.01
```

### Number.prototype.toExponential()

`toExponential`方法用於將一個數轉為科學計數法形式。

```javascript
(10).toExponential()  // "1e+1"
(10).toExponential(1) // "1.0e+1"
(10).toExponential(2) // "1.00e+1"

(1234).toExponential()  // "1.234e+3"
(1234).toExponential(1) // "1.2e+3"
(1234).toExponential(2) // "1.23e+3"
```

`toExponential`方法的引數是小數點後有效數字的位數，範圍為0到100，超出這個範圍，會丟擲一個 RangeError 錯誤。

### Number.prototype.toPrecision()

`Number.prototype.toPrecision()`方法用於將一個數轉為指定位數的有效數字。

```javascript
(12.34).toPrecision(1) // "1e+1"
(12.34).toPrecision(2) // "12"
(12.34).toPrecision(3) // "12.3"
(12.34).toPrecision(4) // "12.34"
(12.34).toPrecision(5) // "12.340"
```

該方法的引數為有效數字的位數，範圍是1到100，超出這個範圍會丟擲 RangeError 錯誤。

該方法用於四捨五入時不太可靠，跟浮點數不是精確儲存有關。

```javascript
(12.35).toPrecision(3) // "12.3"
(12.25).toPrecision(3) // "12.3"
(12.15).toPrecision(3) // "12.2"
(12.45).toPrecision(3) // "12.4"
```

### Number.prototype.toLocaleString()

`Number.prototype.toLocaleString()`方法接受一個地區碼作為引數，返回一個字串，表示當前數字在該地區的當地書寫形式。

```javascript
(123).toLocaleString('zh-Hans-CN-u-nu-hanidec')
// "一二三"
```

該方法還可以接受第二個引數配置物件，用來定製指定用途的返回字串。該物件的`style`屬性指定輸出樣式，預設值是`decimal`，表示輸出十進位制形式。如果值為`percent`，表示輸出百分數。

```javascript
(123).toLocaleString('zh-Hans-CN', { style: 'percent' })
// "12,300%"
```

如果`style`屬性的值為`currency`，則可以搭配`currency`屬性，輸出指定格式的貨幣字串形式。

```javascript
(123).toLocaleString('zh-Hans-CN', { style: 'currency', currency: 'CNY' })
// "￥123.00"

(123).toLocaleString('de-DE', { style: 'currency', currency: 'EUR' })
// "123,00 €"

(123).toLocaleString('en-US', { style: 'currency', currency: 'USD' })
// "$123.00"
```

如果`Number.prototype.toLocaleString()`省略了引數，則由瀏覽器自行決定如何處理，通常會使用作業系統的地區設定。注意，該方法如果使用瀏覽器不認識的地區碼，會丟擲一個錯誤。

```javascript
(123).toLocaleString('123') // 出錯
```

## 自定義方法

與其他物件一樣，`Number.prototype`物件上面可以自定義方法，被`Number`的例項繼承。

```javascript
Number.prototype.add = function (x) {
  return this + x;
};

8['add'](2) // 10
```

上面程式碼為`Number`物件例項定義了一個`add`方法。在數值上呼叫某個方法，數值會自動轉為`Number`的例項物件，所以就可以呼叫`add`方法了。由於`add`方法返回的還是數值，所以可以鏈式運算。

```javascript
Number.prototype.subtract = function (x) {
  return this - x;
};

(8).add(2).subtract(4)
// 6
```

上面程式碼在`Number`物件的例項上部署了`subtract`方法，它可以與`add`方法鏈式呼叫。

我們還可以部署更復雜的方法。

```javascript
Number.prototype.iterate = function () {
  var result = [];
  for (var i = 0; i <= this; i++) {
    result.push(i);
  }
  return result;
};

(8).iterate()
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

上面程式碼在`Number`物件的原型上部署了`iterate`方法，將一個數值自動遍歷為一個數組。

注意，數值的自定義方法，只能定義在它的原型物件`Number.prototype`上面，數值本身是無法自定義屬性的。

```javascript
var n = 1;
n.x = 1;
n.x // undefined
```

上面程式碼中，`n`是一個原始型別的數值。直接在它上面新增一個屬性`x`，不會報錯，但毫無作用，總是返回`undefined`。這是因為一旦被呼叫屬性，`n`就自動轉為`Number`的例項物件，呼叫結束後，該物件自動銷燬。所以，下一次呼叫`n`的屬性時，實際取到的是另一個物件，屬性`x`當然就讀不出來。
