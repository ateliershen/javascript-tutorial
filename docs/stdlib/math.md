# Math 物件

`Math`是 JavaScript 的原生物件，提供各種數學功能。該物件不是建構函式，不能生成例項，所有的屬性和方法都必須在`Math`物件上呼叫。

## 靜態屬性

`Math`物件的靜態屬性，提供以下一些數學常數。

- `Math.E`：常數`e`。
- `Math.LN2`：2 的自然對數。
- `Math.LN10`：10 的自然對數。
- `Math.LOG2E`：以 2 為底的`e`的對數。
- `Math.LOG10E`：以 10 為底的`e`的對數。
- `Math.PI`：常數`π`。
- `Math.SQRT1_2`：0.5 的平方根。
- `Math.SQRT2`：2 的平方根。

```javascript
Math.E // 2.718281828459045
Math.LN2 // 0.6931471805599453
Math.LN10 // 2.302585092994046
Math.LOG2E // 1.4426950408889634
Math.LOG10E // 0.4342944819032518
Math.PI // 3.141592653589793
Math.SQRT1_2 // 0.7071067811865476
Math.SQRT2 // 1.4142135623730951
```

這些屬性都是隻讀的，不能修改。

## 靜態方法

`Math`物件提供以下一些靜態方法。

- `Math.abs()`：絕對值
- `Math.ceil()`：向上取整
- `Math.floor()`：向下取整
- `Math.max()`：最大值
- `Math.min()`：最小值
- `Math.pow()`：冪運算
- `Math.sqrt()`：平方根
- `Math.log()`：自然對數
- `Math.exp()`：`e`的指數
- `Math.round()`：四捨五入
- `Math.random()`：隨機數

### Math.abs()

`Math.abs`方法返回引數值的絕對值。

```javascript
Math.abs(1) // 1
Math.abs(-1) // 1
```

### Math.max()，Math.min()

`Math.max`方法返回引數之中最大的那個值，`Math.min`返回最小的那個值。如果引數為空, `Math.min`返回`Infinity`, `Math.max`返回`-Infinity`。

```javascript
Math.max(2, -1, 5) // 5
Math.min(2, -1, 5) // -1
Math.min() // Infinity
Math.max() // -Infinity
```

### Math.floor()，Math.ceil()

`Math.floor`方法返回小於或等於引數值的最大整數（地板值）。

```javascript
Math.floor(3.2) // 3
Math.floor(-3.2) // -4
```

`Math.ceil`方法返回大於或等於引數值的最小整數（天花板值）。

```javascript
Math.ceil(3.2) // 4
Math.ceil(-3.2) // -3
```

這兩個方法可以結合起來，實現一個總是返回數值的整數部分的函式。

```javascript
function ToInteger(x) {
  x = Number(x);
  return x < 0 ? Math.ceil(x) : Math.floor(x);
}

ToInteger(3.2) // 3
ToInteger(3.5) // 3
ToInteger(3.8) // 3
ToInteger(-3.2) // -3
ToInteger(-3.5) // -3
ToInteger(-3.8) // -3
```

上面程式碼中，不管正數或負數，`ToInteger`函式總是返回一個數值的整數部分。

### Math.round()

`Math.round`方法用於四捨五入。

```javascript
Math.round(0.1) // 0
Math.round(0.5) // 1
Math.round(0.6) // 1

// 等同於
Math.floor(x + 0.5)
```

注意，它對負數的處理（主要是對`0.5`的處理）。

```javascript
Math.round(-1.1) // -1
Math.round(-1.5) // -1
Math.round(-1.6) // -2
```

### Math.pow()

`Math.pow`方法返回以第一個引數為底數、第二個引數為指數的冪運算值。

```javascript
// 等同於 2 ** 2
Math.pow(2, 2) // 4
// 等同於 2 ** 3
Math.pow(2, 3) // 8
```

下面是計算圓面積的方法。

```javascript
var radius = 20;
var area = Math.PI * Math.pow(radius, 2);
```

### Math.sqrt()

`Math.sqrt`方法返回引數值的平方根。如果引數是一個負值，則返回`NaN`。

```javascript
Math.sqrt(4) // 2
Math.sqrt(-4) // NaN
```

### Math.log()

`Math.log`方法返回以`e`為底的自然對數值。

```javascript
Math.log(Math.E) // 1
Math.log(10) // 2.302585092994046
```

如果要計算以10為底的對數，可以先用`Math.log`求出自然對數，然後除以`Math.LN10`；求以2為底的對數，可以除以`Math.LN2`。

```javascript
Math.log(100)/Math.LN10 // 2
Math.log(8)/Math.LN2 // 3
```

### Math.exp()

`Math.exp`方法返回常數`e`的引數次方。

```javascript
Math.exp(1) // 2.718281828459045
Math.exp(3) // 20.085536923187668
```

### Math.random()

`Math.random()`返回0到1之間的一個偽隨機數，可能等於0，但是一定小於1。

```javascript
Math.random() // 0.7151307314634323
```

任意範圍的隨機數生成函式如下。

```javascript
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

getRandomArbitrary(1.5, 6.5)
// 2.4942810038223864
```

任意範圍的隨機整數生成函式如下。

```javascript
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

getRandomInt(1, 6) // 5
```

返回隨機字元的例子如下。

```javascript
function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i = 0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"
```

上面程式碼中，`random_str`函式接受一個整數作為引數，返回變數`ALPHABET`內的隨機字元所組成的指定長度的字串。

### 三角函式方法

`Math`物件還提供一系列三角函式方法。

- `Math.sin()`：返回引數的正弦（引數為弧度值）
- `Math.cos()`：返回引數的餘弦（引數為弧度值）
- `Math.tan()`：返回引數的正切（引數為弧度值）
- `Math.asin()`：返回引數的反正弦（返回值為弧度值）
- `Math.acos()`：返回引數的反餘弦（返回值為弧度值）
- `Math.atan()`：返回引數的反正切（返回值為弧度值）

```javascript
Math.sin(0) // 0
Math.cos(0) // 1
Math.tan(0) // 0

Math.sin(Math.PI / 2) // 1

Math.asin(1) // 1.5707963267948966
Math.acos(1) // 0
Math.atan(1) // 0.7853981633974483
```
