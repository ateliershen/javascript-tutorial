# 數值

## 概述

### 整數和浮點數

JavaScript 內部，所有數字都是以64位浮點數形式儲存，即使整數也是如此。所以，`1`與`1.0`是相同的，是同一個數。

```javascript
1 === 1.0 // true
```

這就是說，JavaScript 語言的底層根本沒有整數，所有數字都是小數（64位浮點數）。容易造成混淆的是，某些運算只有整數才能完成，此時 JavaScript 會自動把64位浮點數，轉成32位整數，然後再進行運算，參見《運算子》一章的“位運算”部分。

由於浮點數不是精確的值，所以涉及小數的比較和運算要特別小心。

```javascript
0.1 + 0.2 === 0.3
// false

0.3 / 0.1
// 2.9999999999999996

(0.3 - 0.2) === (0.2 - 0.1)
// false
```

### 數值精度

根據國際標準 IEEE 754，JavaScript 浮點數的64個二進位制位，從最左邊開始，是這樣組成的。

- 第1位：符號位，`0`表示正數，`1`表示負數
- 第2位到第12位（共11位）：指數部分
- 第13位到第64位（共52位）：小數部分（即有效數字）

符號位決定了一個數的正負，指數部分決定了數值的大小，小數部分決定了數值的精度。

指數部分一共有11個二進位制位，因此大小範圍就是0到2047。IEEE 754 規定，如果指數部分的值在0到2047之間（不含兩個端點），那麼有效數字的第一位預設總是1，不儲存在64位浮點數之中。也就是說，有效數字這時總是`1.xx...xx`的形式，其中`xx..xx`的部分儲存在64位浮點數之中，最長可能為52位。因此，JavaScript 提供的有效數字最長為53個二進位制位。

```
(-1)^符號位 * 1.xx...xx * 2^指數部分
```

上面公式是正常情況下（指數部分在0到2047之間），一個數在 JavaScript 內部實際的表示形式。

精度最多隻能到53個二進位制位，這意味著，絕對值小於2的53次方的整數，即-2<sup>53</sup>到2<sup>53</sup>，都可以精確表示。

```javascript
Math.pow(2, 53)
// 9007199254740992

Math.pow(2, 53) + 1
// 9007199254740992

Math.pow(2, 53) + 2
// 9007199254740994

Math.pow(2, 53) + 3
// 9007199254740996

Math.pow(2, 53) + 4
// 9007199254740996
```

上面程式碼中，大於2的53次方以後，整數運算的結果開始出現錯誤。所以，大於2的53次方的數值，都無法保持精度。由於2的53次方是一個16位的十進位制數值，所以簡單的法則就是，JavaScript 對15位的十進位制數都可以精確處理。

```javascript
Math.pow(2, 53)
// 9007199254740992

// 多出的三個有效數字，將無法儲存
9007199254740992111
// 9007199254740992000
```

上面示例表明，大於2的53次方以後，多出來的有效數字（最後三位的`111`）都會無法儲存，變成0。

### 數值範圍

根據標準，64位浮點數的指數部分的長度是11個二進位制位，意味著指數部分的最大值是2047（2的11次方減1）。也就是說，64位浮點數的指數部分的值最大為2047，分出一半表示負數，則 JavaScript 能夠表示的數值範圍為2<sup>1024</sup>到2<sup>-1023</sup>（開區間），超出這個範圍的數無法表示。

如果一個數大於等於2的1024次方，那麼就會發生“正向溢位”，即 JavaScript 無法表示這麼大的數，這時就會返回`Infinity`。

```javascript
Math.pow(2, 1024) // Infinity
```

如果一個數小於等於2的-1075次方（指數部分最小值-1023，再加上小數部分的52位），那麼就會發生為“負向溢位”，即 JavaScript 無法表示這麼小的數，這時會直接返回0。

```javascript
Math.pow(2, -1075) // 0
```

下面是一個實際的例子。

```javascript
var x = 0.5;

for(var i = 0; i < 25; i++) {
  x = x * x;
}

x // 0
```

上面程式碼中，對`0.5`連續做25次平方，由於最後結果太接近0，超出了可表示的範圍，JavaScript 就直接將其轉為0。

JavaScript 提供`Number`物件的`MAX_VALUE`和`MIN_VALUE`屬性，返回可以表示的具體的最大值和最小值。

```javascript
Number.MAX_VALUE // 1.7976931348623157e+308
Number.MIN_VALUE // 5e-324
```

## 數值的表示法

JavaScript 的數值有多種表示方法，可以用字面形式直接表示，比如`35`（十進位制）和`0xFF`（十六進位制）。

數值也可以採用科學計數法表示，下面是幾個科學計數法的例子。

```javascript
123e3 // 123000
123e-3 // 0.123
-3.1E+12
.1e-23
```

科學計數法允許字母`e`或`E`的後面，跟著一個整數，表示這個數值的指數部分。

以下兩種情況，JavaScript 會自動將數值轉為科學計數法表示，其他情況都採用字面形式直接表示。

**（1）小數點前的數字多於21位。**

```javascript
1234567890123456789012
// 1.2345678901234568e+21

123456789012345678901
// 123456789012345680000
```

**（2）小數點後的零多於5個。**

```javascript
// 小數點後緊跟5個以上的零，
// 就自動轉為科學計數法
0.0000003 // 3e-7

// 否則，就保持原來的字面形式
0.000003 // 0.000003
```

## 數值的進位制

使用字面量（literal）直接表示一個數值時，JavaScript 對整數提供四種進位制的表示方法：十進位制、十六進位制、八進位制、二進位制。

- 十進位制：沒有前導0的數值。
- 八進位制：有字首`0o`或`0O`的數值，或者有前導0、且只用到0-7的八個阿拉伯數字的數值。
- 十六進位制：有字首`0x`或`0X`的數值。
- 二進位制：有字首`0b`或`0B`的數值。

預設情況下，JavaScript 內部會自動將八進位制、十六進位制、二進位制轉為十進位制。下面是一些例子。

```javascript
0xff // 255
0o377 // 255
0b11 // 3
```

如果八進位制、十六進位制、二進位制的數值裡面，出現不屬於該進位制的數字，就會報錯。

```javascript
0xzz // 報錯
0o88 // 報錯
0b22 // 報錯
```

上面程式碼中，十六進位制出現了字母`z`、八進位制出現數字`8`、二進位制出現數字`2`，因此報錯。

通常來說，有前導0的數值會被視為八進位制，但是如果前導0後面有數字`8`和`9`，則該數值被視為十進位制。

```javascript
0888 // 888
0777 // 511
```

前導0表示八進位制，處理時很容易造成混亂。ES5 的嚴格模式和 ES6，已經廢除了這種表示法，但是瀏覽器為了相容以前的程式碼，目前還繼續支援這種表示法。

## 特殊數值

JavaScript 提供了幾個特殊的數值。

### 正零和負零

前面說過，JavaScript 的64位浮點數之中，有一個二進位制位是符號位。這意味著，任何一個數都有一個對應的負值，就連`0`也不例外。

JavaScript 內部實際上存在2個`0`：一個是`+0`，一個是`-0`，區別就是64位浮點數表示法的符號位不同。它們是等價的。

```javascript
-0 === +0 // true
0 === -0 // true
0 === +0 // true
```

幾乎所有場合，正零和負零都會被當作正常的`0`。

```javascript
+0 // 0
-0 // 0
(-0).toString() // '0'
(+0).toString() // '0'
```

唯一有區別的場合是，`+0`或`-0`當作分母，返回的值是不相等的。

```javascript
(1 / +0) === (1 / -0) // false
```

上面的程式碼之所以出現這樣結果，是因為除以正零得到`+Infinity`，除以負零得到`-Infinity`，這兩者是不相等的（關於`Infinity`詳見下文）。

### NaN

**（1）含義**

`NaN`是 JavaScript 的特殊值，表示“非數字”（Not a Number），主要出現在將字串解析成數字出錯的場合。

```javascript
5 - 'x' // NaN
```

上面程式碼執行時，會自動將字串`x`轉為數值，但是由於`x`不是數值，所以最後得到結果為`NaN`，表示它是“非數字”（`NaN`）。

另外，一些數學函式的運算結果會出現`NaN`。

```javascript
Math.acos(2) // NaN
Math.log(-1) // NaN
Math.sqrt(-1) // NaN
```

`0`除以`0`也會得到`NaN`。

```javascript
0 / 0 // NaN
```

需要注意的是，`NaN`不是獨立的資料型別，而是一個特殊數值，它的資料型別依然屬於`Number`，使用`typeof`運算子可以看得很清楚。

```javascript
typeof NaN // 'number'
```

**（2）運算規則**

`NaN`不等於任何值，包括它本身。

```javascript
NaN === NaN // false
```

陣列的`indexOf`方法內部使用的是嚴格相等運算子，所以該方法對`NaN`不成立。

```javascript
[NaN].indexOf(NaN) // -1
```

`NaN`在布林運算時被當作`false`。

```javascript
Boolean(NaN) // false
```

`NaN`與任何數（包括它自己）的運算，得到的都是`NaN`。

```javascript
NaN + 32 // NaN
NaN - 32 // NaN
NaN * 32 // NaN
NaN / 32 // NaN
```

### Infinity

**（1）含義**

`Infinity`表示“無窮”，用來表示兩種場景。一種是一個正的數值太大，或一個負的數值太小，無法表示；另一種是非0數值除以0，得到`Infinity`。

```javascript
// 場景一
Math.pow(2, 1024)
// Infinity

// 場景二
0 / 0 // NaN
1 / 0 // Infinity
```

上面程式碼中，第一個場景是一個表示式的計算結果太大，超出了能夠表示的範圍，因此返回`Infinity`。第二個場景是`0`除以`0`會得到`NaN`，而非0數值除以`0`，會返回`Infinity`。

`Infinity`有正負之分，`Infinity`表示正的無窮，`-Infinity`表示負的無窮。

```javascript
Infinity === -Infinity // false

1 / -0 // -Infinity
-1 / -0 // Infinity
```

上面程式碼中，非零正數除以`-0`，會得到`-Infinity`，負數除以`-0`，會得到`Infinity`。

由於數值正向溢位（overflow）、負向溢位（underflow）和被`0`除，JavaScript 都不報錯，所以單純的數學運算幾乎沒有可能丟擲錯誤。

`Infinity`大於一切數值（除了`NaN`），`-Infinity`小於一切數值（除了`NaN`）。

```javascript
Infinity > 1000 // true
-Infinity < -1000 // true
```

`Infinity`與`NaN`比較，總是返回`false`。

```javascript
Infinity > NaN // false
-Infinity > NaN // false

Infinity < NaN // false
-Infinity < NaN // false
```

**（2）運算規則**

`Infinity`的四則運算，符合無窮的數學計算規則。

```javascript
5 * Infinity // Infinity
5 - Infinity // -Infinity
Infinity / 5 // Infinity
5 / Infinity // 0
```

0乘以`Infinity`，返回`NaN`；0除以`Infinity`，返回`0`；`Infinity`除以0，返回`Infinity`。

```javascript
0 * Infinity // NaN
0 / Infinity // 0
Infinity / 0 // Infinity
```

`Infinity`加上或乘以`Infinity`，返回的還是`Infinity`。

```javascript
Infinity + Infinity // Infinity
Infinity * Infinity // Infinity
```

`Infinity`減去或除以`Infinity`，得到`NaN`。

```javascript
Infinity - Infinity // NaN
Infinity / Infinity // NaN
```

`Infinity`與`null`計算時，`null`會轉成0，等同於與`0`的計算。

```javascript
null * Infinity // NaN
null / Infinity // 0
Infinity / null // Infinity
```

`Infinity`與`undefined`計算，返回的都是`NaN`。

```javascript
undefined + Infinity // NaN
undefined - Infinity // NaN
undefined * Infinity // NaN
undefined / Infinity // NaN
Infinity / undefined // NaN
```

## 與數值相關的全域性方法

### parseInt()

**（1）基本用法**

`parseInt`方法用於將字串轉為整數。

```javascript
parseInt('123') // 123
```

如果字串頭部有空格，空格會被自動去除。

```javascript
parseInt('   81') // 81
```

如果`parseInt`的引數不是字串，則會先轉為字串再轉換。

```javascript
parseInt(1.23) // 1
// 等同於
parseInt('1.23') // 1
```

字串轉為整數的時候，是一個個字元依次轉換，如果遇到不能轉為數字的字元，就不再進行下去，返回已經轉好的部分。

```javascript
parseInt('8a') // 8
parseInt('12**') // 12
parseInt('12.34') // 12
parseInt('15e2') // 15
parseInt('15px') // 15
```

上面程式碼中，`parseInt`的引數都是字串，結果只返回字串頭部可以轉為數字的部分。

如果字串的第一個字元不能轉化為數字（後面跟著數字的正負號除外），返回`NaN`。

```javascript
parseInt('abc') // NaN
parseInt('.3') // NaN
parseInt('') // NaN
parseInt('+') // NaN
parseInt('+1') // 1
```

所以，`parseInt`的返回值只有兩種可能，要麼是一個十進位制整數，要麼是`NaN`。

如果字串以`0x`或`0X`開頭，`parseInt`會將其按照十六進位制數解析。

```javascript
parseInt('0x10') // 16
```

如果字串以`0`開頭，將其按照10進位制解析。

```javascript
parseInt('011') // 11
```

對於那些會自動轉為科學計數法的數字，`parseInt`會將科學計數法的表示方法視為字串，因此導致一些奇怪的結果。

```javascript
parseInt(1000000000000000000000.5) // 1
// 等同於
parseInt('1e+21') // 1

parseInt(0.0000008) // 8
// 等同於
parseInt('8e-7') // 8
```

**（2）進位制轉換**

`parseInt`方法還可以接受第二個引數（2到36之間），表示被解析的值的進位制，返回該值對應的十進位制數。預設情況下，`parseInt`的第二個引數為10，即預設是十進位制轉十進位制。

```javascript
parseInt('1000') // 1000
// 等同於
parseInt('1000', 10) // 1000
```

下面是轉換指定進位制的數的例子。

```javascript
parseInt('1000', 2) // 8
parseInt('1000', 6) // 216
parseInt('1000', 8) // 512
```

上面程式碼中，二進位制、六進位制、八進位制的`1000`，分別等於十進位制的8、216和512。這意味著，可以用`parseInt`方法進行進位制的轉換。

如果第二個引數不是數值，會被自動轉為一個整數。這個整數只有在2到36之間，才能得到有意義的結果，超出這個範圍，則返回`NaN`。如果第二個引數是`0`、`undefined`和`null`，則直接忽略。

```javascript
parseInt('10', 37) // NaN
parseInt('10', 1) // NaN
parseInt('10', 0) // 10
parseInt('10', null) // 10
parseInt('10', undefined) // 10
```

如果字串包含對於指定進位制無意義的字元，則從最高位開始，只返回可以轉換的數值。如果最高位無法轉換，則直接返回`NaN`。

```javascript
parseInt('1546', 2) // 1
parseInt('546', 2) // NaN
```

上面程式碼中，對於二進位制來說，`1`是有意義的字元，`5`、`4`、`6`都是無意義的字元，所以第一行返回1，第二行返回`NaN`。

前面說過，如果`parseInt`的第一個引數不是字串，會被先轉為字串。這會導致一些令人意外的結果。

```javascript
parseInt(0x11, 36) // 43
parseInt(0x11, 2) // 1

// 等同於
parseInt(String(0x11), 36)
parseInt(String(0x11), 2)

// 等同於
parseInt('17', 36)
parseInt('17', 2)
```

上面程式碼中，十六進位制的`0x11`會被先轉為十進位制的17，再轉為字串。然後，再用36進位制或二進位制解讀字串`17`，最後返回結果`43`和`1`。

這種處理方式，對於八進位制的字首0，尤其需要注意。

```javascript
parseInt(011, 2) // NaN

// 等同於
parseInt(String(011), 2)

// 等同於
parseInt(String(9), 2)
```

上面程式碼中，第一行的`011`會被先轉為字串`9`，因為`9`不是二進位制的有效字元，所以返回`NaN`。如果直接計算`parseInt('011', 2)`，`011`則是會被當作二進位制處理，返回3。

JavaScript 不再允許將帶有字首0的數字視為八進位制數，而是要求忽略這個`0`。但是，為了保證相容性，大部分瀏覽器並沒有部署這一條規定。

### parseFloat()

`parseFloat`方法用於將一個字串轉為浮點數。

```javascript
parseFloat('3.14') // 3.14
```

如果字串符合科學計數法，則會進行相應的轉換。

```javascript
parseFloat('314e-2') // 3.14
parseFloat('0.0314E+2') // 3.14
```

如果字串包含不能轉為浮點數的字元，則不再進行往後轉換，返回已經轉好的部分。

```javascript
parseFloat('3.14more non-digit characters') // 3.14
```

`parseFloat`方法會自動過濾字串前導的空格。

```javascript
parseFloat('\t\v\r12.34\n ') // 12.34
```

如果引數不是字串，或者字串的第一個字元不能轉化為浮點數，則返回`NaN`。

```javascript
parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN
```

上面程式碼中，尤其值得注意，`parseFloat`會將空字串轉為`NaN`。

這些特點使得`parseFloat`的轉換結果不同於`Number`函式。

```javascript
parseFloat(true)  // NaN
Number(true) // 1

parseFloat(null) // NaN
Number(null) // 0

parseFloat('') // NaN
Number('') // 0

parseFloat('123.45#') // 123.45
Number('123.45#') // NaN
```

### isNaN()

`isNaN`方法可以用來判斷一個值是否為`NaN`。

```javascript
isNaN(NaN) // true
isNaN(123) // false
```

但是，`isNaN`只對數值有效，如果傳入其他值，會被先轉成數值。比如，傳入字串的時候，字串會被先轉成`NaN`，所以最後返回`true`，這一點要特別引起注意。也就是說，`isNaN`為`true`的值，有可能不是`NaN`，而是一個字串。

```javascript
isNaN('Hello') // true
// 相當於
isNaN(Number('Hello')) // true
```

出於同樣的原因，對於物件和陣列，`isNaN`也返回`true`。

```javascript
isNaN({}) // true
// 等同於
isNaN(Number({})) // true

isNaN(['xzy']) // true
// 等同於
isNaN(Number(['xzy'])) // true
```

但是，對於空陣列和只有一個數值成員的陣列，`isNaN`返回`false`。

```javascript
isNaN([]) // false
isNaN([123]) // false
isNaN(['123']) // false
```

上面程式碼之所以返回`false`，原因是這些陣列能被`Number`函式轉成數值，請參見《資料型別轉換》一章。

因此，使用`isNaN`之前，最好判斷一下資料型別。

```javascript
function myIsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

判斷`NaN`更可靠的方法是，利用`NaN`為唯一不等於自身的值的這個特點，進行判斷。

```javascript
function myIsNaN(value) {
  return value !== value;
}
```

### isFinite()

`isFinite`方法返回一個布林值，表示某個值是否為正常的數值。

```javascript
isFinite(Infinity) // false
isFinite(-Infinity) // false
isFinite(NaN) // false
isFinite(undefined) // false
isFinite(null) // true
isFinite(-1) // true
```

除了`Infinity`、`-Infinity`、`NaN`和`undefined`這幾個值會返回`false`，`isFinite`對於其他的數值都會返回`true`。

## 參考連結

- Dr. Axel Rauschmayer, [How numbers are encoded in JavaScript](http://www.2ality.com/2012/04/number-encoding.html)
- Humphry, [JavaScript 中 Number 的一些表示上/下限](http://blog.segmentfault.com/humphry/1190000000407658)
