# 錯誤處理機制

## Error 例項物件

JavaScript 解析或執行時，一旦發生錯誤，引擎就會丟擲一個錯誤物件。JavaScript 原生提供`Error`建構函式，所有丟擲的錯誤都是這個建構函式的例項。

```javascript
var err = new Error('出錯了');
err.message // "出錯了"
```

上面程式碼中，我們呼叫`Error`建構函式，生成一個例項物件`err`。`Error`建構函式接受一個引數，表示錯誤提示，可以從例項的`message`屬性讀到這個引數。丟擲`Error`例項物件以後，整個程式就中斷在發生錯誤的地方，不再往下執行。

JavaScript 語言標準只提到，`Error`例項物件必須有`message`屬性，表示出錯時的提示資訊，沒有提到其他屬性。大多數 JavaScript 引擎，對`Error`例項還提供`name`和`stack`屬性，分別表示錯誤的名稱和錯誤的堆疊，但它們是非標準的，不是每種實現都有。

- **message**：錯誤提示資訊
- **name**：錯誤名稱（非標準屬性）
- **stack**：錯誤的堆疊（非標準屬性）

使用`name`和`message`這兩個屬性，可以對發生什麼錯誤有一個大概的瞭解。

```javascript
if (error.name) {
  console.log(error.name + ': ' + error.message);
}
```

`stack`屬性用來檢視錯誤發生時的堆疊。

```javascript
function throwit() {
  throw new Error('');
}

function catchit() {
  try {
    throwit();
  } catch(e) {
    console.log(e.stack); // print stack trace
  }
}

catchit()
// Error
//    at throwit (~/examples/throwcatch.js:9:11)
//    at catchit (~/examples/throwcatch.js:3:9)
//    at repl:1:5
```

上面程式碼中，錯誤堆疊的最內層是`throwit`函式，然後是`catchit`函式，最後是函式的執行環境。

## 原生錯誤型別

`Error`例項物件是最一般的錯誤型別，在它的基礎上，JavaScript 還定義了其他6種錯誤物件。也就是說，存在`Error`的6個派生物件。

### SyntaxError 物件

`SyntaxError`物件是解析程式碼時發生的語法錯誤。

```javascript
// 變數名錯誤
var 1a;
// Uncaught SyntaxError: Invalid or unexpected token

// 缺少括號
console.log 'hello');
// Uncaught SyntaxError: Unexpected string
```

上面程式碼的錯誤，都是在語法解析階段就可以發現，所以會丟擲`SyntaxError`。第一個錯誤提示是“token 非法”，第二個錯誤提示是“字串不符合要求”。

### ReferenceError 物件

`ReferenceError`物件是引用一個不存在的變數時發生的錯誤。

```javascript
// 使用一個不存在的變數
unknownVariable
// Uncaught ReferenceError: unknownVariable is not defined
```

另一種觸發場景是，將一個值分配給無法分配的物件，比如對函式的執行結果賦值。

```javascript
// 等號左側不是變數
console.log() = 1
// Uncaught ReferenceError: Invalid left-hand side in assignment
```

上面程式碼對函式`console.log`的執行結果賦值，結果引發了`ReferenceError`錯誤。

### RangeError 物件

`RangeError`物件是一個值超出有效範圍時發生的錯誤。主要有幾種情況，一是陣列長度為負數，二是`Number`物件的方法引數超出範圍，以及函式堆疊超過最大值。

```javascript
// 陣列長度不得為負數
new Array(-1)
// Uncaught RangeError: Invalid array length
```

### TypeError 物件

`TypeError`物件是變數或引數不是預期型別時發生的錯誤。比如，對字串、布林值、數值等原始型別的值使用`new`命令，就會丟擲這種錯誤，因為`new`命令的引數應該是一個建構函式。

```javascript
new 123
// Uncaught TypeError: number is not a func

var obj = {};
obj.unknownMethod()
// Uncaught TypeError: obj.unknownMethod is not a function
```

上面程式碼的第二種情況，呼叫物件不存在的方法，也會丟擲`TypeError`錯誤，因為`obj.unknownMethod`的值是`undefined`，而不是一個函式。

### URIError 物件

`URIError`物件是 URI 相關函式的引數不正確時丟擲的錯誤，主要涉及`encodeURI()`、`decodeURI()`、`encodeURIComponent()`、`decodeURIComponent()`、`escape()`和`unescape()`這六個函式。

```javascript
decodeURI('%2')
// URIError: URI malformed
```

### EvalError 物件

`eval`函式沒有被正確執行時，會丟擲`EvalError`錯誤。該錯誤型別已經不再使用了，只是為了保證與以前程式碼相容，才繼續保留。

### 總結

以上這6種派生錯誤，連同原始的`Error`物件，都是建構函式。開發者可以使用它們，手動生成錯誤物件的例項。這些建構函式都接受一個引數，代表錯誤提示資訊（message）。

```javascript
var err1 = new Error('出錯了！');
var err2 = new RangeError('出錯了，變數超出有效範圍！');
var err3 = new TypeError('出錯了，變數型別無效！');

err1.message // "出錯了！"
err2.message // "出錯了，變數超出有效範圍！"
err3.message // "出錯了，變數型別無效！"
```

## 自定義錯誤

除了 JavaScript 原生提供的七種錯誤物件，還可以定義自己的錯誤物件。

```javascript
function UserError(message) {
  this.message = message || '預設資訊';
  this.name = 'UserError';
}

UserError.prototype = new Error();
UserError.prototype.constructor = UserError;
```

上面程式碼自定義一個錯誤物件`UserError`，讓它繼承`Error`物件。然後，就可以生成這種自定義型別的錯誤了。

```javascript
new UserError('這是自定義的錯誤！');
```

## throw 語句

`throw`語句的作用是手動中斷程式執行，丟擲一個錯誤。

```javascript
if (x <= 0) {
  throw new Error('x 必須為正數');
}
// Uncaught ReferenceError: x is not defined
```

上面程式碼中，如果變數`x`小於等於`0`，就手動丟擲一個錯誤，告訴使用者`x`的值不正確，整個程式就會在這裡中斷執行。可以看到，`throw`丟擲的錯誤就是它的引數，這裡是一個`Error`例項。

`throw`也可以丟擲自定義錯誤。

```javascript
function UserError(message) {
  this.message = message || '預設資訊';
  this.name = 'UserError';
}

throw new UserError('出錯了！');
// Uncaught UserError {message: "出錯了！", name: "UserError"}
```

上面程式碼中，`throw`丟擲的是一個`UserError`例項。

實際上，`throw`可以丟擲任何型別的值。也就是說，它的引數可以是任何值。

```javascript
// 丟擲一個字串
throw 'Error！';
// Uncaught Error！

// 丟擲一個數值
throw 42;
// Uncaught 42

// 丟擲一個布林值
throw true;
// Uncaught true

// 丟擲一個物件
throw {
  toString: function () {
    return 'Error!';
  }
};
// Uncaught {toString: ƒ}
```

對於 JavaScript 引擎來說，遇到`throw`語句，程式就中止了。引擎會接收到`throw`丟擲的資訊，可能是一個錯誤例項，也可能是其他型別的值。

## try...catch 結構

一旦發生錯誤，程式就中止執行了。JavaScript 提供了`try...catch`結構，允許對錯誤進行處理，選擇是否往下執行。

```javascript
try {
  throw new Error('出錯了!');
} catch (e) {
  console.log(e.name + ": " + e.message);
  console.log(e.stack);
}
// Error: 出錯了!
//   at <anonymous>:3:9
//   ...
```

上面程式碼中，`try`程式碼塊丟擲錯誤（上例用的是`throw`語句），JavaScript 引擎就立即把程式碼的執行，轉到`catch`程式碼塊，或者說錯誤被`catch`程式碼塊捕獲了。`catch`接受一個引數，表示`try`程式碼塊丟擲的值。

如果你不確定某些程式碼是否會報錯，就可以把它們放在`try...catch`程式碼塊之中，便於進一步對錯誤進行處理。

```javascript
try {
  f();
} catch(e) {
  // 處理錯誤
}
```

上面程式碼中，如果函式`f`執行報錯，就會進行`catch`程式碼塊，接著對錯誤進行處理。

`catch`程式碼塊捕獲錯誤之後，程式不會中斷，會按照正常流程繼續執行下去。

```javascript
try {
  throw "出錯了";
} catch (e) {
  console.log(111);
}
console.log(222);
// 111
// 222
```

上面程式碼中，`try`程式碼塊丟擲的錯誤，被`catch`程式碼塊捕獲後，程式會繼續向下執行。

`catch`程式碼塊之中，還可以再丟擲錯誤，甚至使用巢狀的`try...catch`結構。

```javascript
var n = 100;

try {
  throw n;
} catch (e) {
  if (e <= 50) {
    // ...
  } else {
    throw e;
  }
}
// Uncaught 100
```

上面程式碼中，`catch`程式碼之中又丟擲了一個錯誤。

為了捕捉不同型別的錯誤，`catch`程式碼塊之中可以加入判斷語句。

```javascript
try {
  foo.bar();
} catch (e) {
  if (e instanceof EvalError) {
    console.log(e.name + ": " + e.message);
  } else if (e instanceof RangeError) {
    console.log(e.name + ": " + e.message);
  }
  // ...
}
```

上面程式碼中，`catch`捕獲錯誤之後，會判斷錯誤型別（`EvalError`還是`RangeError`），進行不同的處理。

## finally 程式碼塊

`try...catch`結構允許在最後新增一個`finally`程式碼塊，表示不管是否出現錯誤，都必需在最後執行的語句。

```javascript
function cleansUp() {
  try {
    throw new Error('出錯了……');
    console.log('此行不會執行');
  } finally {
    console.log('完成清理工作');
  }
}

cleansUp()
// 完成清理工作
// Uncaught Error: 出錯了……
//    at cleansUp (<anonymous>:3:11)
//    at <anonymous>:10:1
```

上面程式碼中，由於沒有`catch`語句塊，一旦發生錯誤，程式碼就會中斷執行。中斷執行之前，會先執行`finally`程式碼塊，然後再向使用者提示報錯資訊。

```javascript
function idle(x) {
  try {
    console.log(x);
    return 'result';
  } finally {
    console.log('FINALLY');
  }
}

idle('hello')
// hello
// FINALLY
```

上面程式碼中，`try`程式碼塊沒有發生錯誤，而且裡面還包括`return`語句，但是`finally`程式碼塊依然會執行。而且，這個函式的返回值還是`result`。

下面的例子說明，`return`語句的執行是排在`finally`程式碼之前，只是等`finally`程式碼執行完畢後才返回。

```javascript
var count = 0;
function countUp() {
  try {
    return count;
  } finally {
    count++;
  }
}

countUp()
// 0
count
// 1
```

上面程式碼說明，`return`語句裡面的`count`的值，是在`finally`程式碼塊執行之前就獲取了。

下面是`finally`程式碼塊用法的典型場景。

```javascript
openFile();

try {
  writeFile(Data);
} catch(e) {
  handleError(e);
} finally {
  closeFile();
}
```

上面程式碼首先開啟一個檔案，然後在`try`程式碼塊中寫入檔案，如果沒有發生錯誤，則執行`finally`程式碼塊關閉檔案；一旦發生錯誤，則先使用`catch`程式碼塊處理錯誤，再使用`finally`程式碼塊關閉檔案。

下面的例子充分反映了`try...catch...finally`這三者之間的執行順序。

```javascript
function f() {
  try {
    console.log(0);
    throw 'bug';
  } catch(e) {
    console.log(1);
    return true; // 這句原本會延遲到 finally 程式碼塊結束再執行
    console.log(2); // 不會執行
  } finally {
    console.log(3);
    return false; // 這句會覆蓋掉前面那句 return
    console.log(4); // 不會執行
  }

  console.log(5); // 不會執行
}

var result = f();
// 0
// 1
// 3

result
// false
```

上面程式碼中，`catch`程式碼塊結束執行之前，會先執行`finally`程式碼塊。

`catch`程式碼塊之中，觸發轉入`finally`程式碼塊的標誌，不僅有`return`語句，還有`throw`語句。

```javascript
function f() {
  try {
    throw '出錯了！';
  } catch(e) {
    console.log('捕捉到內部錯誤');
    throw e; // 這句原本會等到finally結束再執行
  } finally {
    return false; // 直接返回
  }
}

try {
  f();
} catch(e) {
  // 此處不會執行
  console.log('caught outer "bogus"');
}

//  捕捉到內部錯誤
```

上面程式碼中，進入`catch`程式碼塊之後，一遇到`throw`語句，就會去執行`finally`程式碼塊，其中有`return false`語句，因此就直接返回了，不再會回去執行`catch`程式碼塊剩下的部分了。

`try`程式碼塊內部，還可以再使用`try`程式碼塊。

```javascript
try {
  try {
    consle.log('Hello world!'); // 報錯
  }
  finally {
    console.log('Finally');
  }
  console.log('Will I run?');
} catch(error) {
  console.error(error.message);
}
// Finally
// consle is not defined
```

上面程式碼中，`try`裡面還有一個`try`。內層的`try`報錯（`console`拼錯了），這時會執行內層的`finally`程式碼塊，然後丟擲錯誤，被外層的`catch`捕獲。

## 參考連線

- Jani Hartikainen, [JavaScript Errors and How to Fix Them](http://davidwalsh.name/fix-javascript-errors)
