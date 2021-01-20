# Boolean 物件

## 概述

`Boolean`物件是 JavaScript 的三個包裝物件之一。作為建構函式，它主要用於生成布林值的包裝物件例項。

```javascript
var b = new Boolean(true);

typeof b // "object"
b.valueOf() // true
```

上面程式碼的變數`b`是一個`Boolean`物件的例項，它的型別是物件，值為布林值`true`。

注意，`false`對應的包裝物件例項，布林運算結果也是`true`。

```javascript
if (new Boolean(false)) {
  console.log('true');
} // true

if (new Boolean(false).valueOf()) {
  console.log('true');
} // 無輸出
```

上面程式碼的第一個例子之所以得到`true`，是因為`false`對應的包裝物件例項是一個物件，進行邏輯運算時，被自動轉化成布林值`true`（因為所有物件對應的布林值都是`true`）。而例項的`valueOf`方法，則返回例項對應的原始值，本例為`false`。

## Boolean 函式的型別轉換作用

`Boolean`物件除了可以作為建構函式，還可以單獨使用，將任意值轉為布林值。這時`Boolean`就是一個單純的工具方法。

```javascript
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean('') // false
Boolean(NaN) // false

Boolean(1) // true
Boolean('false') // true
Boolean([]) // true
Boolean({}) // true
Boolean(function () {}) // true
Boolean(/foo/) // true
```

上面程式碼中幾種得到`true`的情況，都值得認真記住。

順便提一下，使用雙重的否運算子（`!`）也可以將任意值轉為對應的布林值。

```javascript
!!undefined // false
!!null // false
!!0 // false
!!'' // false
!!NaN // false

!!1 // true
!!'false' // true
!![] // true
!!{} // true
!!function(){} // true
!!/foo/ // true
```

最後，對於一些特殊值，`Boolean`物件前面加不加`new`，會得到完全相反的結果，必須小心。

```javascript
if (Boolean(false)) {
  console.log('true');
} // 無輸出

if (new Boolean(false)) {
  console.log('true');
} // true

if (Boolean(null)) {
  console.log('true');
} // 無輸出

if (new Boolean(null)) {
  console.log('true');
} // true
```
