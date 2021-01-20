# String 物件

## 概述

`String`物件是 JavaScript 原生提供的三個包裝物件之一，用來生成字串物件。

```javascript
var s1 = 'abc';
var s2 = new String('abc');

typeof s1 // "string"
typeof s2 // "object"

s2.valueOf() // "abc"
```

上面程式碼中，變數`s1`是字串，`s2`是物件。由於`s2`是字串物件，`s2.valueOf`方法返回的就是它所對應的原始字串。

字串物件是一個類似陣列的物件（很像陣列，但不是陣列）。

```javascript
new String('abc')
// String {0: "a", 1: "b", 2: "c", length: 3}

(new String('abc'))[1] // "b"
```

上面程式碼中，字串`abc`對應的字串物件，有數值鍵（`0`、`1`、`2`）和`length`屬性，所以可以像陣列那樣取值。

除了用作建構函式，`String`物件還可以當作工具方法使用，將任意型別的值轉為字串。

```javascript
String(true) // "true"
String(5) // "5"
```

上面程式碼將布林值`true`和數值`5`，分別轉換為字串。

## 靜態方法

### String.fromCharCode()

`String`物件提供的靜態方法（即定義在物件本身，而不是定義在物件例項的方法），主要是`String.fromCharCode()`。該方法的引數是一個或多個數值，代表 Unicode 碼點，返回值是這些碼點組成的字串。

```javascript
String.fromCharCode() // ""
String.fromCharCode(97) // "a"
String.fromCharCode(104, 101, 108, 108, 111)
// "hello"
```

上面程式碼中，`String.fromCharCode`方法的引數為空，就返回空字串；否則，返回引數對應的 Unicode 字串。

注意，該方法不支援 Unicode 碼點大於`0xFFFF`的字元，即傳入的引數不能大於`0xFFFF`（即十進位制的 65535）。

```javascript
String.fromCharCode(0x20BB7)
// "ஷ"
String.fromCharCode(0x20BB7) === String.fromCharCode(0x0BB7)
// true
```

上面程式碼中，`String.fromCharCode`引數`0x20BB7`大於`0xFFFF`，導致返回結果出錯。`0x20BB7`對應的字元是漢字`𠮷`，但是返回結果卻是另一個字元（碼點`0x0BB7`）。這是因為`String.fromCharCode`發現引數值大於`0xFFFF`，就會忽略多出的位（即忽略`0x20BB7`裡面的`2`）。

這種現象的根本原因在於，碼點大於`0xFFFF`的字元佔用四個位元組，而 JavaScript 預設支援兩個位元組的字元。這種情況下，必須把`0x20BB7`拆成兩個字元表示。

```javascript
String.fromCharCode(0xD842, 0xDFB7)
// "𠮷"
```

上面程式碼中，`0x20BB7`拆成兩個字元`0xD842`和`0xDFB7`（即兩個兩位元組字元，合成一個四位元組字元），就能得到正確的結果。碼點大於`0xFFFF`的字元的四位元組表示法，由 UTF-16 編碼方法決定。

## 例項屬性

### String.prototype.length

字串例項的`length`屬性返回字串的長度。

```javascript
'abc'.length // 3
```

## 例項方法

### String.prototype.charAt()

`charAt`方法返回指定位置的字元，引數是從`0`開始編號的位置。

```javascript
var s = new String('abc');

s.charAt(1) // "b"
s.charAt(s.length - 1) // "c"
```

這個方法完全可以用陣列下標替代。

```javascript
'abc'.charAt(1) // "b"
'abc'[1] // "b"
```

如果引數為負數，或大於等於字串的長度，`charAt`返回空字串。

```javascript
'abc'.charAt(-1) // ""
'abc'.charAt(3) // ""
```

### String.prototype.charCodeAt()

`charCodeAt()`方法返回字串指定位置的 Unicode 碼點（十進位制表示），相當於`String.fromCharCode()`的逆操作。

```javascript
'abc'.charCodeAt(1) // 98
```

上面程式碼中，`abc`的`1`號位置的字元是`b`，它的 Unicode 碼點是`98`。

如果沒有任何引數，`charCodeAt`返回首字元的 Unicode 碼點。

```javascript
'abc'.charCodeAt() // 97
```

如果引數為負數，或大於等於字串的長度，`charCodeAt`返回`NaN`。

```javascript
'abc'.charCodeAt(-1) // NaN
'abc'.charCodeAt(4) // NaN
```

注意，`charCodeAt`方法返回的 Unicode 碼點不會大於65536（0xFFFF），也就是說，只返回兩個位元組的字元的碼點。如果遇到碼點大於 65536 的字元（四個位元組的字元），必須連續使用兩次`charCodeAt`，不僅讀入`charCodeAt(i)`，還要讀入`charCodeAt(i+1)`，將兩個值放在一起，才能得到準確的字元。

### String.prototype.concat()

`concat`方法用於連線兩個字串，返回一個新字串，不改變原字串。

```javascript
var s1 = 'abc';
var s2 = 'def';

s1.concat(s2) // "abcdef"
s1 // "abc"
```

該方法可以接受多個引數。

```javascript
'a'.concat('b', 'c') // "abc"
```

如果引數不是字串，`concat`方法會將其先轉為字串，然後再連線。

```javascript
var one = 1;
var two = 2;
var three = '3';

''.concat(one, two, three) // "123"
one + two + three // "33"
```

上面程式碼中，`concat`方法將引數先轉成字串再連線，所以返回的是一個三個字元的字串。作為對比，加號運算子在兩個運算數都是數值時，不會轉換型別，所以返回的是一個兩個字元的字串。

### String.prototype.slice()

`slice()`方法用於從原字串取出子字串並返回，不改變原字串。它的第一個引數是子字串的開始位置，第二個引數是子字串的結束位置（不含該位置）。

```javascript
'JavaScript'.slice(0, 4) // "Java"
```

如果省略第二個引數，則表示子字串一直到原字串結束。

```javascript
'JavaScript'.slice(4) // "Script"
```

如果引數是負值，表示從結尾開始倒數計算的位置，即該負值加上字串長度。

```javascript
'JavaScript'.slice(-6) // "Script"
'JavaScript'.slice(0, -6) // "Java"
'JavaScript'.slice(-2, -1) // "p"
```

如果第一個引數大於第二個引數（正數情況下），`slice()`方法返回一個空字串。

```javascript
'JavaScript'.slice(2, 1) // ""
```

### String.prototype.substring()

`substring`方法用於從原字串取出子字串並返回，不改變原字串，跟`slice`方法很相像。它的第一個引數表示子字串的開始位置，第二個位置表示結束位置（返回結果不含該位置）。

```javascript
'JavaScript'.substring(0, 4) // "Java"
```

如果省略第二個引數，則表示子字串一直到原字串的結束。

```javascript
'JavaScript'.substring(4) // "Script"
```

如果第一個引數大於第二個引數，`substring`方法會自動更換兩個引數的位置。

```javascript
'JavaScript'.substring(10, 4) // "Script"
// 等同於
'JavaScript'.substring(4, 10) // "Script"
```

上面程式碼中，調換`substring`方法的兩個引數，都得到同樣的結果。

如果引數是負數，`substring`方法會自動將負數轉為0。

```javascript
'JavaScript'.substring(-3) // "JavaScript"
'JavaScript'.substring(4, -3) // "Java"
```

上面程式碼中，第二個例子的引數`-3`會自動變成`0`，等同於`'JavaScript'.substring(4, 0)`。由於第二個引數小於第一個引數，會自動互換位置，所以返回`Java`。

由於這些規則違反直覺，因此不建議使用`substring`方法，應該優先使用`slice`。

### String.prototype.substr()

`substr`方法用於從原字串取出子字串並返回，不改變原字串，跟`slice`和`substring`方法的作用相同。

`substr`方法的第一個引數是子字串的開始位置（從0開始計算），第二個引數是子字串的長度。

```javascript
'JavaScript'.substr(4, 6) // "Script"
```

如果省略第二個引數，則表示子字串一直到原字串的結束。

```javascript
'JavaScript'.substr(4) // "Script"
```

如果第一個引數是負數，表示倒數計算的字元位置。如果第二個引數是負數，將被自動轉為0，因此會返回空字串。

```javascript
'JavaScript'.substr(-6) // "Script"
'JavaScript'.substr(4, -1) // ""
```

上面程式碼中，第二個例子的引數`-1`自動轉為`0`，表示子字串長度為`0`，所以返回空字串。

### String.prototype.indexOf()，String.prototype.lastIndexOf()

`indexOf`方法用於確定一個字串在另一個字串中第一次出現的位置，返回結果是匹配開始的位置。如果返回`-1`，就表示不匹配。

```javascript
'hello world'.indexOf('o') // 4
'JavaScript'.indexOf('script') // -1
```

`indexOf`方法還可以接受第二個引數，表示從該位置開始向後匹配。

```javascript
'hello world'.indexOf('o', 6) // 7
```

`lastIndexOf`方法的用法跟`indexOf`方法一致，主要的區別是`lastIndexOf`從尾部開始匹配，`indexOf`則是從頭部開始匹配。

```javascript
'hello world'.lastIndexOf('o') // 7
```

另外，`lastIndexOf`的第二個引數表示從該位置起向前匹配。

```javascript
'hello world'.lastIndexOf('o', 6) // 4
```

### String.prototype.trim()

`trim`方法用於去除字串兩端的空格，返回一個新字串，不改變原字串。

```javascript
'  hello world  '.trim()
// "hello world"
```

該方法去除的不僅是空格，還包括製表符（`\t`、`\v`）、換行符（`\n`）和回車符（`\r`）。

```javascript
'\r\nabc \t'.trim() // 'abc'
```

### String.prototype.toLowerCase()，String.prototype.toUpperCase()

`toLowerCase`方法用於將一個字串全部轉為小寫，`toUpperCase`則是全部轉為大寫。它們都返回一個新字串，不改變原字串。

```javascript
'Hello World'.toLowerCase()
// "hello world"

'Hello World'.toUpperCase()
// "HELLO WORLD"
```

### String.prototype.match()

`match`方法用於確定原字串是否匹配某個子字串，返回一個數組，成員為匹配的第一個字串。如果沒有找到匹配，則返回`null`。

```javascript
'cat, bat, sat, fat'.match('at') // ["at"]
'cat, bat, sat, fat'.match('xt') // null
```

返回的陣列還有`index`屬性和`input`屬性，分別表示匹配字串開始的位置和原始字串。

```javascript
var matches = 'cat, bat, sat, fat'.match('at');
matches.index // 1
matches.input // "cat, bat, sat, fat"
```

`match`方法還可以使用正則表示式作為引數，詳見《正則表示式》一章。

### String.prototype.search()，String.prototype.replace()

`search`方法的用法基本等同於`match`，但是返回值為匹配的第一個位置。如果沒有找到匹配，則返回`-1`。

```javascript
'cat, bat, sat, fat'.search('at') // 1
```

`search`方法還可以使用正則表示式作為引數，詳見《正則表示式》一節。

`replace`方法用於替換匹配的子字串，一般情況下只替換第一個匹配（除非使用帶有`g`修飾符的正則表示式）。

```javascript
'aaa'.replace('a', 'b') // "baa"
```

`replace`方法還可以使用正則表示式作為引數，詳見《正則表示式》一節。

### String.prototype.split()

`split`方法按照給定規則分割字串，返回一個由分割出來的子字串組成的陣列。

```javascript
'a|b|c'.split('|') // ["a", "b", "c"]
```

如果分割規則為空字串，則返回陣列的成員是原字串的每一個字元。

```javascript
'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
```

如果省略引數，則返回陣列的唯一成員就是原字串。

```javascript
'a|b|c'.split() // ["a|b|c"]
```

如果滿足分割規則的兩個部分緊鄰著（即兩個分割符中間沒有其他字元），則返回陣列之中會有一個空字串。

```javascript
'a||c'.split('|') // ['a', '', 'c']
```

如果滿足分割規則的部分處於字串的開頭或結尾（即它的前面或後面沒有其他字元），則返回陣列的第一個或最後一個成員是一個空字串。

```javascript
'|b|c'.split('|') // ["", "b", "c"]
'a|b|'.split('|') // ["a", "b", ""]
```

`split`方法還可以接受第二個引數，限定返回陣列的最大成員數。

```javascript
'a|b|c'.split('|', 0) // []
'a|b|c'.split('|', 1) // ["a"]
'a|b|c'.split('|', 2) // ["a", "b"]
'a|b|c'.split('|', 3) // ["a", "b", "c"]
'a|b|c'.split('|', 4) // ["a", "b", "c"]
```

上面程式碼中，`split`方法的第二個引數，決定了返回陣列的成員數。

`split`方法還可以使用正則表示式作為引數，詳見《正則表示式》一節。

### String.prototype.localeCompare()

`localeCompare`方法用於比較兩個字串。它返回一個整數，如果小於0，表示第一個字串小於第二個字串；如果等於0，表示兩者相等；如果大於0，表示第一個字串大於第二個字串。

```javascript
'apple'.localeCompare('banana') // -1
'apple'.localeCompare('apple') // 0
```

該方法的最大特點，就是會考慮自然語言的順序。舉例來說，正常情況下，大寫的英文字母小於小寫字母。

```javascript
'B' > 'a' // false
```

上面程式碼中，字母`B`小於字母`a`。因為 JavaScript 採用的是 Unicode 碼點比較，`B`的碼點是66，而`a`的碼點是97。

但是，`localeCompare`方法會考慮自然語言的排序情況，將`B`排在`a`的前面。

```javascript
'B'.localeCompare('a') // 1
```

上面程式碼中，`localeCompare`方法返回整數1，表示`B`較大。

`localeCompare`還可以有第二個引數，指定所使用的語言（預設是英語），然後根據該語言的規則進行比較。

```javascript
'ä'.localeCompare('z', 'de') // -1
'ä'.localeCompare('z', 'sv') // 1
```

上面程式碼中，`de`表示德語，`sv`表示瑞典語。德語中，`ä`小於`z`，所以返回`-1`；瑞典語中，`ä`大於`z`，所以返回`1`。

## 參考連結

- Ariya Hidayat, [JavaScript String: substring, substr, slice](http://ariya.ofilabs.com/2014/02/javascript-string-substring-substr-slice.html)
