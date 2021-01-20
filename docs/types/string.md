# 字串

## 概述

### 定義

字串就是零個或多個排在一起的字元，放在單引號或雙引號之中。

```javascript
'abc'
"abc"
```

單引號字串的內部，可以使用雙引號。雙引號字串的內部，可以使用單引號。

```javascript
'key = "value"'
"It's a long journey"
```

上面兩個都是合法的字串。

如果要在單引號字串的內部，使用單引號，就必須在內部的單引號前面加上反斜槓，用來轉義。雙引號字串內部使用雙引號，也是如此。

```javascript
'Did she say \'Hello\'?'
// "Did she say 'Hello'?"

"Did she say \"Hello\"?"
// "Did she say "Hello"?"
```

由於 HTML 語言的屬性值使用雙引號，所以很多專案約定 JavaScript 語言的字串只使用單引號，本教程遵守這個約定。當然，只使用雙引號也完全可以。重要的是堅持使用一種風格，不要一會使用單引號表示字串，一會又使用雙引號表示。

字串預設只能寫在一行內，分成多行將會報錯。

```javascript
'a
b
c'
// SyntaxError: Unexpected token ILLEGAL
```

上面程式碼將一個字串分成三行，JavaScript 就會報錯。

如果長字串必須分成多行，可以在每一行的尾部使用反斜槓。

```javascript
var longString = 'Long \
long \
long \
string';

longString
// "Long long long string"
```

上面程式碼表示，加了反斜槓以後，原來寫在一行的字串，可以分成多行書寫。但是，輸出的時候還是單行，效果與寫在同一行完全一樣。注意，反斜槓的後面必須是換行符，而不能有其他字元（比如空格），否則會報錯。

連線運算子（`+`）可以連線多個單行字串，將長字串拆成多行書寫，輸出的時候也是單行。

```javascript
var longString = 'Long '
  + 'long '
  + 'long '
  + 'string';
```

如果想輸出多行字串，有一種利用多行註釋的變通方法。

```javascript
(function () { /*
line 1
line 2
line 3
*/}).toString().split('\n').slice(1, -1).join('\n')
// "line 1
// line 2
// line 3"
```

上面的例子中，輸出的字串就是多行。

### 轉義

反斜槓（\）在字串內有特殊含義，用來表示一些特殊字元，所以又稱為轉義符。

需要用反斜槓轉義的特殊字元，主要有下面這些。

- `\0` ：null（`\u0000`）
- `\b` ：後退鍵（`\u0008`）
- `\f` ：換頁符（`\u000C`）
- `\n` ：換行符（`\u000A`）
- `\r` ：回車鍵（`\u000D`）
- `\t` ：製表符（`\u0009`）
- `\v` ：垂直製表符（`\u000B`）
- `\'` ：單引號（`\u0027`）
- `\"` ：雙引號（`\u0022`）
- `\\` ：反斜槓（`\u005C`）

上面這些字元前面加上反斜槓，都表示特殊含義。

```javascript
console.log('1\n2')
// 1
// 2
```

上面程式碼中，`\n`表示換行，輸出的時候就分成了兩行。

反斜槓還有三種特殊用法。

（1）`\HHH`

反斜槓後面緊跟三個八進位制數（`000`到`377`），代表一個字元。`HHH`對應該字元的 Unicode 碼點，比如`\251`表示版權符號。顯然，這種方法只能輸出256種字元。

（2）`\xHH`

`\x`後面緊跟兩個十六進位制數（`00`到`FF`），代表一個字元。`HH`對應該字元的 Unicode 碼點，比如`\xA9`表示版權符號。這種方法也只能輸出256種字元。

（3）`\uXXXX`

`\u`後面緊跟四個十六進位制數（`0000`到`FFFF`），代表一個字元。`XXXX`對應該字元的 Unicode 碼點，比如`\u00A9`表示版權符號。

下面是這三種字元特殊寫法的例子。

```javascript
'\251' // "©"
'\xA9' // "©"
'\u00A9' // "©"

'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
```

如果在非特殊字元前面使用反斜槓，則反斜槓會被省略。

```javascript
'\a'
// "a"
```

上面程式碼中，`a`是一個正常字元，前面加反斜槓沒有特殊含義，反斜槓會被自動省略。

如果字串的正常內容之中，需要包含反斜槓，則反斜槓前面需要再加一個反斜槓，用來對自身轉義。

```javascript
"Prev \\ Next"
// "Prev \ Next"
```

### 字串與陣列

字串可以被視為字元陣列，因此可以使用陣列的方括號運算子，用來返回某個位置的字元（位置編號從0開始）。

```javascript
var s = 'hello';
s[0] // "h"
s[1] // "e"
s[4] // "o"

// 直接對字串使用方括號運算子
'hello'[1] // "e"
```

如果方括號中的數字超過字串的長度，或者方括號中根本不是數字，則返回`undefined`。

```javascript
'abc'[3] // undefined
'abc'[-1] // undefined
'abc'['x'] // undefined
```

但是，字串與陣列的相似性僅此而已。實際上，無法改變字串之中的單個字元。

```javascript
var s = 'hello';

delete s[0];
s // "hello"

s[1] = 'a';
s // "hello"

s[5] = '!';
s // "hello"
```

上面程式碼表示，字串內部的單個字元無法改變和增刪，這些操作會默默地失敗。

### length 屬性

`length`屬性返回字串的長度，該屬性也是無法改變的。

```javascript
var s = 'hello';
s.length // 5

s.length = 3;
s.length // 5

s.length = 7;
s.length // 5
```

上面程式碼表示字串的`length`屬性無法改變，但是不會報錯。

## 字符集

JavaScript 使用 Unicode 字符集。JavaScript 引擎內部，所有字元都用 Unicode 表示。

JavaScript 不僅以 Unicode 儲存字元，還允許直接在程式中使用 Unicode 碼點表示字元，即將字元寫成`\uxxxx`的形式，其中`xxxx`代表該字元的 Unicode 碼點。比如，`\u00A9`代表版權符號。

```javascript
var s = '\u00A9';
s // "©"
```

解析程式碼的時候，JavaScript 會自動識別一個字元是字面形式表示，還是 Unicode 形式表示。輸出給使用者的時候，所有字元都會轉成字面形式。

```javascript
var f\u006F\u006F = 'abc';
foo // "abc"
```

上面程式碼中，第一行的變數名`foo`是 Unicode 形式表示，第二行是字面形式表示。JavaScript 會自動識別。

我們還需要知道，每個字元在 JavaScript 內部都是以16位（即2個位元組）的 UTF-16 格式儲存。也就是說，JavaScript 的單位字元長度固定為16位長度，即2個位元組。

但是，UTF-16 有兩種長度：對於碼點在`U+0000`到`U+FFFF`之間的字元，長度為16位（即2個位元組）；對於碼點在`U+10000`到`U+10FFFF`之間的字元，長度為32位（即4個位元組），而且前兩個位元組在`0xD800`到`0xDBFF`之間，後兩個位元組在`0xDC00`到`0xDFFF`之間。舉例來說，碼點`U+1D306`對應的字元為`𝌆，`它寫成 UTF-16 就是`0xD834 0xDF06`。

JavaScript 對 UTF-16 的支援是不完整的，由於歷史原因，只支援兩位元組的字元，不支援四位元組的字元。這是因為 JavaScript 第一版釋出的時候，Unicode 的碼點只編到`U+FFFF`，因此兩位元組足夠表示了。後來，Unicode 納入的字元越來越多，出現了四位元組的編碼。但是，JavaScript 的標準此時已經定型了，統一將字元長度限制在兩位元組，導致無法識別四位元組的字元。上一節的那個四位元組字元`𝌆`，瀏覽器會正確識別這是一個字元，但是 JavaScript 無法識別，會認為這是兩個字元。

```javascript
'𝌆'.length // 2
```

上面程式碼中，JavaScript 認為`𝌆`的長度為2，而不是1。

總結一下，對於碼點在`U+10000`到`U+10FFFF`之間的字元，JavaScript 總是認為它們是兩個字元（`length`屬性為2）。所以處理的時候，必須把這一點考慮在內，也就是說，JavaScript 返回的字串長度可能是不正確的。

## Base64 轉碼

有時，文本里麵包含一些不可列印的符號，比如 ASCII 碼0到31的符號都無法打印出來，這時可以使用 Base64 編碼，將它們轉成可以列印的字元。另一個場景是，有時需要以文字格式傳遞二進位制資料，那麼也可以使用 Base64 編碼。

所謂 Base64 就是一種編碼方法，可以將任意值轉成 0～9、A～Z、a-z、`+`和`/`這64個字元組成的可列印字元。使用它的主要目的，不是為了加密，而是為了不出現特殊字元，簡化程式的處理。

JavaScript 原生提供兩個 Base64 相關的方法。

- `btoa()`：任意值轉為 Base64 編碼
- `atob()`：Base64 編碼轉為原來的值

```javascript
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"
```

注意，這兩個方法不適合非 ASCII 碼的字元，會報錯。

```javascript
btoa('你好') // 報錯
```

要將非 ASCII 碼字元轉為 Base64 編碼，必須中間插入一個轉碼環節，再使用這兩個方法。

```javascript
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

## 參考連結

- Mathias Bynens, [JavaScript’s internal character encoding: UCS-2 or UTF-16?](http://mathiasbynens.be/notes/javascript-encoding)
- Mathias Bynens, [JavaScript has a Unicode problem](http://mathiasbynens.be/notes/javascript-unicode)
- Mozilla Developer Network, [Window.btoa](https://developer.mozilla.org/en-US/docs/Web/API/Window.btoa)
