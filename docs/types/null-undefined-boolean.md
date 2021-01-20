# null, undefined 和布林值

## null 和 undefined

### 概述

`null`與`undefined`都可以表示“沒有”，含義非常相似。將一個變數賦值為`undefined`或`null`，老實說，語法效果幾乎沒區別。

```javascript
var a = undefined;
// 或者
var a = null;
```

上面程式碼中，變數`a`分別被賦值為`undefined`和`null`，這兩種寫法的效果幾乎等價。

在`if`語句中，它們都會被自動轉為`false`，相等運算子（`==`）甚至直接報告兩者相等。

```javascript
if (!undefined) {
  console.log('undefined is false');
}
// undefined is false

if (!null) {
  console.log('null is false');
}
// null is false

undefined == null
// true
```

從上面程式碼可見，兩者的行為是何等相似！谷歌公司開發的 JavaScript 語言的替代品 Dart 語言，就明確規定只有`null`，沒有`undefined`！

既然含義與用法都差不多，為什麼要同時設定兩個這樣的值，這不是無端增加複雜度，令初學者困擾嗎？這與歷史原因有關。

1995年 JavaScript 誕生時，最初像 Java 一樣，只設置了`null`表示"無"。根據 C 語言的傳統，`null`可以自動轉為`0`。

```javascript
Number(null) // 0
5 + null // 5
```

上面程式碼中，`null`轉為數字時，自動變成0。

但是，JavaScript 的設計者 Brendan Eich，覺得這樣做還不夠。首先，第一版的 JavaScript 裡面，`null`就像在 Java 裡一樣，被當成一個物件，Brendan Eich 覺得表示“無”的值最好不是物件。其次，那時的 JavaScript 不包括錯誤處理機制，Brendan Eich 覺得，如果`null`自動轉為0，很不容易發現錯誤。

因此，他又設計了一個`undefined`。區別是這樣的：`null`是一個表示“空”的物件，轉為數值時為`0`；`undefined`是一個表示"此處無定義"的原始值，轉為數值時為`NaN`。

```javascript
Number(undefined) // NaN
5 + undefined // NaN
```

### 用法和含義

對於`null`和`undefined`，大致可以像下面這樣理解。

`null`表示空值，即該處的值現在為空。呼叫函式時，某個引數未設定任何值，這時就可以傳入`null`，表示該引數為空。比如，某個函式接受引擎丟擲的錯誤作為引數，如果執行過程中未出錯，那麼這個引數就會傳入`null`，表示未發生錯誤。

`undefined`表示“未定義”，下面是返回`undefined`的典型場景。

```javascript
// 變數聲明瞭，但沒有賦值
var i;
i // undefined

// 呼叫函式時，應該提供的引數沒有提供，該引數等於 undefined
function f(x) {
  return x;
}
f() // undefined

// 物件沒有賦值的屬性
var  o = new Object();
o.p // undefined

// 函式沒有返回值時，預設返回 undefined
function f() {}
f() // undefined
```

## 布林值

布林值代表“真”和“假”兩個狀態。“真”用關鍵字`true`表示，“假”用關鍵字`false`表示。布林值只有這兩個值。

下列運算子會返回布林值：

- 前置邏輯運算子： `!` (Not)
- 相等運算子：`===`，`!==`，`==`，`!=`
- 比較運算子：`>`，`>=`，`<`，`<=`

如果 JavaScript 預期某個位置應該是布林值，會將該位置上現有的值自動轉為布林值。轉換規則是除了下面六個值被轉為`false`，其他值都視為`true`。

- `undefined`
- `null`
- `false`
- `0`
- `NaN`
- `""`或`''`（空字串）

布林值往往用於程式流程的控制，請看一個例子。

```javascript
if ('') {
  console.log('true');
}
// 沒有任何輸出
```

上面程式碼中，`if`命令後面的判斷條件，預期應該是一個布林值，所以 JavaScript 自動將空字串，轉為布林值`false`，導致程式不會進入程式碼塊，所以沒有任何輸出。

注意，空陣列（`[]`）和空物件（`{}`）對應的布林值，都是`true`。

```javascript
if ([]) {
  console.log('true');
}
// true

if ({}) {
  console.log('true');
}
// true
```

更多關於資料型別轉換的介紹，參見《資料型別轉換》一章。

## 參考連結

- Axel Rauschmayer, [Categorizing values in JavaScript](http://www.2ality.com/2013/01/categorizing-values.html)
