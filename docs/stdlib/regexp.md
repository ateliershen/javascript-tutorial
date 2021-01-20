# RegExp 物件

`RegExp`物件提供正則表示式的功能。

## 概述

正則表示式（regular expression）是一種表達文字模式（即字串結構）的方法，有點像字串的模板，常常用來按照“給定模式”匹配文字。比如，正則表示式給出一個 Email 地址的模式，然後用它來確定一個字串是否為 Email 地址。JavaScript 的正則表示式體系是參照 Perl 5 建立的。

新建正則表示式有兩種方法。一種是使用字面量，以斜槓表示開始和結束。

```javascript
var regex = /xyz/;
```

另一種是使用`RegExp`建構函式。

```javascript
var regex = new RegExp('xyz');
```

上面兩種寫法是等價的，都新建了一個內容為`xyz`的正則表示式物件。它們的主要區別是，第一種方法在引擎編譯程式碼時，就會新建正則表示式，第二種方法在執行時新建正則表示式，所以前者的效率較高。而且，前者比較便利和直觀，所以實際應用中，基本上都採用字面量定義正則表示式。

`RegExp`建構函式還可以接受第二個引數，表示修飾符（詳細解釋見下文）。

```javascript
var regex = new RegExp('xyz', 'i');
// 等價於
var regex = /xyz/i;
```

上面程式碼中，正則表示式`/xyz/`有一個修飾符`i`。

## 例項屬性

正則物件的例項屬性分成兩類。

一類是修飾符相關，用於瞭解設定了什麼修飾符。

- `RegExp.prototype.ignoreCase`：返回一個布林值，表示是否設定了`i`修飾符。
- `RegExp.prototype.global`：返回一個布林值，表示是否設定了`g`修飾符。
- `RegExp.prototype.multiline`：返回一個布林值，表示是否設定了`m`修飾符。
- `RegExp.prototype.flags`：返回一個字串，包含了已經設定的所有修飾符，按字母排序。

上面四個屬性都是隻讀的。

```javascript
var r = /abc/igm;

r.ignoreCase // true
r.global // true
r.multiline // true
r.flags // 'gim'
```

另一類是與修飾符無關的屬性，主要是下面兩個。

- `RegExp.prototype.lastIndex`：返回一個整數，表示下一次開始搜尋的位置。該屬性可讀寫，但是隻在進行連續搜尋時有意義，詳細介紹請看後文。
- `RegExp.prototype.source`：返回正則表示式的字串形式（不包括反斜槓），該屬性只讀。

```javascript
var r = /abc/igm;

r.lastIndex // 0
r.source // "abc"
```

## 例項方法

### RegExp.prototype.test()

正則例項物件的`test`方法返回一個布林值，表示當前模式是否能匹配引數字串。

```javascript
/cat/.test('cats and dogs') // true
```

上面程式碼驗證引數字串之中是否包含`cat`，結果返回`true`。

如果正則表示式帶有`g`修飾符，則每一次`test`方法都從上一次結束的位置開始向後匹配。

```javascript
var r = /x/g;
var s = '_x_x';

r.lastIndex // 0
r.test(s) // true

r.lastIndex // 2
r.test(s) // true

r.lastIndex // 4
r.test(s) // false
```

上面程式碼的正則表示式使用了`g`修飾符，表示是全域性搜尋，會有多個結果。接著，三次使用`test`方法，每一次開始搜尋的位置都是上一次匹配的後一個位置。

帶有`g`修飾符時，可以透過正則物件的`lastIndex`屬性指定開始搜尋的位置。

```javascript
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false

r.lastIndex // 0
r.test(s)
```

上面程式碼指定從字串的第五個位置開始搜尋，這個位置為空，所以返回`false`。同時，`lastIndex`屬性重置為`0`，所以第二次執行`r.test(s)`會返回`true`。

注意，帶有`g`修飾符時，正則表示式內部會記住上一次的`lastIndex`屬性，這時不應該更換所要匹配的字串，否則會有一些難以察覺的錯誤。

```javascript
var r = /bb/g;
r.test('bb') // true
r.test('-bb-') // false
```

上面程式碼中，由於正則表示式`r`是從上一次的`lastIndex`位置開始匹配，導致第二次執行`test`方法時出現預期以外的結果。

`lastIndex`屬性只對同一個正則表示式有效，所以下面這樣寫是錯誤的。

```javascript
var count = 0;
while (/a/g.test('babaa')) count++;
```

上面程式碼會導致無限迴圈，因為`while`迴圈的每次匹配條件都是一個新的正則表示式，導致`lastIndex`屬性總是等於0。

如果正則模式是一個空字串，則匹配所有字串。

```javascript
new RegExp('').test('abc')
// true
```

### RegExp.prototype.exec()

正則例項物件的`exec()`方法，用來返回匹配結果。如果發現匹配，就返回一個數組，成員是匹配成功的子字串，否則返回`null`。

```javascript
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

r1.exec(s) // ["x"]
r2.exec(s) // null
```

上面程式碼中，正則物件`r1`匹配成功，返回一個數組，成員是匹配結果；正則物件`r2`匹配失敗，返回`null`。

如果正則表示式包含圓括號（即含有“組匹配”），則返回的陣列會包括多個成員。第一個成員是整個匹配成功的結果，後面的成員就是圓括號對應的匹配成功的組。也就是說，第二個成員對應第一個括號，第三個成員對應第二個括號，以此類推。整個陣列的`length`屬性等於組匹配的數量再加1。

```javascript
var s = '_x_x';
var r = /_(x)/;

r.exec(s) // ["_x", "x"]
```

上面程式碼的`exec()`方法，返回一個數組。第一個成員是整個匹配的結果，第二個成員是圓括號匹配的結果。

`exec()`方法的返回陣列還包含以下兩個屬性：

- `input`：整個原字串。
- `index`：模式匹配成功的開始位置（從0開始計數）。

```javascript
var r = /a(b+)a/;
var arr = r.exec('_abbba_aba_');

arr // ["abbba", "bbb"]

arr.index // 1
arr.input // "_abbba_aba_"
```

上面程式碼中的`index`屬性等於1，是因為從原字串的第二個位置開始匹配成功。

如果正則表示式加上`g`修飾符，則可以使用多次`exec()`方法，下一次搜尋的位置從上一次匹配成功結束的位置開始。

```javascript
var reg = /a/g;
var str = 'abc_abc_abc'

var r1 = reg.exec(str);
r1 // ["a"]
r1.index // 0
reg.lastIndex // 1

var r2 = reg.exec(str);
r2 // ["a"]
r2.index // 4
reg.lastIndex // 5

var r3 = reg.exec(str);
r3 // ["a"]
r3.index // 8
reg.lastIndex // 9

var r4 = reg.exec(str);
r4 // null
reg.lastIndex // 0
```

上面程式碼連續用了四次`exec()`方法，前三次都是從上一次匹配結束的位置向後匹配。當第三次匹配結束以後，整個字串已經到達尾部，匹配結果返回`null`，正則例項物件的`lastIndex`屬性也重置為`0`，意味著第四次匹配將從頭開始。

利用`g`修飾符允許多次匹配的特點，可以用一個迴圈完成全部匹配。

```javascript
var reg = /a/g;
var str = 'abc_abc_abc'

while(true) {
  var match = reg.exec(str);
  if (!match) break;
  console.log('#' + match.index + ':' + match[0]);
}
// #0:a
// #4:a
// #8:a
```

上面程式碼中，只要`exec()`方法不返回`null`，就會一直迴圈下去，每次輸出匹配的位置和匹配的文字。

正則例項物件的`lastIndex`屬性不僅可讀，還可寫。設定了`g`修飾符的時候，只要手動設定了`lastIndex`的值，就會從指定位置開始匹配。

## 字串的例項方法

字串的例項方法之中，有4種與正則表示式有關。

- `String.prototype.match()`：返回一個數組，成員是所有匹配的子字串。
- `String.prototype.search()`：按照給定的正則表示式進行搜尋，返回一個整數，表示匹配開始的位置。
- `String.prototype.replace()`：按照給定的正則表示式進行替換，返回替換後的字串。
- `String.prototype.split()`：按照給定規則進行字串分割，返回一個數組，包含分割後的各個成員。

### String.prototype.match()

字串例項物件的`match`方法對字串進行正則匹配，返回匹配結果。

```javascript
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

s.match(r1) // ["x"]
s.match(r2) // null
```

從上面程式碼可以看到，字串的`match`方法與正則物件的`exec`方法非常類似：匹配成功返回一個數組，匹配失敗返回`null`。

如果正則表示式帶有`g`修飾符，則該方法與正則物件的`exec`方法行為不同，會一次性返回所有匹配成功的結果。

```javascript
var s = 'abba';
var r = /a/g;

s.match(r) // ["a", "a"]
r.exec(s) // ["a"]
```

設定正則表示式的`lastIndex`屬性，對`match`方法無效，匹配總是從字串的第一個字元開始。

```javascript
var r = /a|b/g;
r.lastIndex = 7;
'xaxb'.match(r) // ['a', 'b']
r.lastIndex // 0
```

上面程式碼表示，設定正則物件的`lastIndex`屬性是無效的。

### String.prototype.search()

字串物件的`search`方法，返回第一個滿足條件的匹配結果在整個字串中的位置。如果沒有任何匹配，則返回`-1`。

```javascript
'_x_x'.search(/x/)
// 1
```

上面程式碼中，第一個匹配結果出現在字串的`1`號位置。

### String.prototype.replace()

字串物件的`replace`方法可以替換匹配的值。它接受兩個引數，第一個是正則表示式，表示搜尋模式，第二個是替換的內容。

```javascript
str.replace(search, replacement)
```

正則表示式如果不加`g`修飾符，就替換第一個匹配成功的值，否則替換所有匹配成功的值。

```javascript
'aaa'.replace('a', 'b') // "baa"
'aaa'.replace(/a/, 'b') // "baa"
'aaa'.replace(/a/g, 'b') // "bbb"
```

上面程式碼中，最後一個正則表示式使用了`g`修飾符，導致所有的`a`都被替換掉了。

`replace`方法的一個應用，就是消除字串首尾兩端的空格。

```javascript
var str = '  #id div.class  ';

str.replace(/^\s+|\s+$/g, '')
// "#id div.class"
```

`replace`方法的第二個引數可以使用美元符號`$`，用來指代所替換的內容。

- `$&`：匹配的子字串。
- `` $` ``：匹配結果前面的文字。
- `$'`：匹配結果後面的文字。
- `$n`：匹配成功的第`n`組內容，`n`是從1開始的自然數。
- `$$`：指代美元符號`$`。

```javascript
'hello world'.replace(/(\w+)\s(\w+)/, '$2 $1')
// "world hello"

'abc'.replace('b', '[$`-$&-$\']')
// "a[a-b-c]c"
```

上面程式碼中，第一個例子是將匹配的組互換位置，第二個例子是改寫匹配的值。

`replace`方法的第二個引數還可以是一個函式，將每一個匹配內容替換為函式返回值。

```javascript
'3 and 5'.replace(/[0-9]+/g, function (match) {
  return 2 * match;
})
// "6 and 10"

var a = 'The quick brown fox jumped over the lazy dog.';
var pattern = /quick|brown|lazy/ig;

a.replace(pattern, function replacer(match) {
  return match.toUpperCase();
});
// The QUICK BROWN fox jumped over the LAZY dog.
```

作為`replace`方法第二個引數的替換函式，可以接受多個引數。其中，第一個引數是捕捉到的內容，第二個引數是捕捉到的組匹配（有多少個組匹配，就有多少個對應的引數）。此外，最後還可以新增兩個引數，倒數第二個引數是捕捉到的內容在整個字串中的位置（比如從第五個位置開始），最後一個引數是原字串。下面是一個網頁模板替換的例子。

```javascript
var prices = {
  'p1': '$1.99',
  'p2': '$9.99',
  'p3': '$5.00'
};

var template = '<span id="p1"></span>'
  + '<span id="p2"></span>'
  + '<span id="p3"></span>';

template.replace(
  /(<span id=")(.*?)(">)(<\/span>)/g,
  function(match, $1, $2, $3, $4){
    return $1 + $2 + $3 + prices[$2] + $4;
  }
);
// "<span id="p1">$1.99</span><span id="p2">$9.99</span><span id="p3">$5.00</span>"
```

上面程式碼的捕捉模式中，有四個括號，所以會產生四個組匹配，在匹配函式中用`$1`到`$4`表示。匹配函式的作用是將價格插入模板中。

### String.prototype.split()

字串物件的`split`方法按照正則規則分割字串，返回一個由分割後的各個部分組成的陣列。

```javascript
str.split(separator, [limit])
```

該方法接受兩個引數，第一個引數是正則表示式，表示分隔規則，第二個引數是返回陣列的最大成員數。

```javascript
// 非正則分隔
'a,  b,c, d'.split(',')
// [ 'a', '  b', 'c', ' d' ]

// 正則分隔，去除多餘的空格
'a,  b,c, d'.split(/, */)
// [ 'a', 'b', 'c', 'd' ]

// 指定返回陣列的最大成員
'a,  b,c, d'.split(/, */, 2)
[ 'a', 'b' ]
```

上面程式碼使用正則表示式，去除了子字串的逗號後面的空格。

```javascript
// 例一
'aaa*a*'.split(/a*/)
// [ '', '*', '*' ]

// 例二
'aaa**a*'.split(/a*/)
// ["", "*", "*", "*"]
```

上面程式碼的分割規則是0次或多次的`a`，由於正則預設是貪婪匹配，所以例一的第一個分隔符是`aaa`，第二個分割符是`a`，將字串分成三個部分，包含開始處的空字串。例二的第一個分隔符是`aaa`，第二個分隔符是0個`a`（即空字元），第三個分隔符是`a`，所以將字串分成四個部分。

如果正則表示式帶有括號，則括號匹配的部分也會作為陣列成員返回。

```javascript
'aaa*a*'.split(/(a*)/)
// [ '', 'aaa', '*', 'a', '*' ]
```

上面程式碼的正則表示式使用了括號，第一個組匹配是`aaa`，第二個組匹配是`a`，它們都作為陣列成員返回。

## 匹配規則

正則表示式的規則很複雜，下面一一介紹這些規則。

### 字面量字元和元字元

大部分字元在正則表示式中，就是字面的含義，比如`/a/`匹配`a`，`/b/`匹配`b`。如果在正則表示式之中，某個字元只表示它字面的含義（就像前面的`a`和`b`），那麼它們就叫做“字面量字元”（literal characters）。

```javascript
/dog/.test('old dog') // true
```

上面程式碼中正則表示式的`dog`，就是字面量字元，所以`/dog/`匹配`old dog`，因為它就表示`d`、`o`、`g`三個字母連在一起。

除了字面量字元以外，還有一部分字元有特殊含義，不代表字面的意思。它們叫做“元字元”（metacharacters），主要有以下幾個。

**（1）點字元（.)**

點字元（`.`）匹配除回車（`\r`）、換行(`\n`) 、行分隔符（`\u2028`）和段分隔符（`\u2029`）以外的所有字元。注意，對於碼點大於`0xFFFF`字元，點字元不能正確匹配，會認為這是兩個字元。

```javascript
/c.t/
```

上面程式碼中，`c.t`匹配`c`和`t`之間包含任意一個字元的情況，只要這三個字元在同一行，比如`cat`、`c2t`、`c-t`等等，但是不匹配`coot`。

**（2）位置字元**

位置字元用來提示字元所處的位置，主要有兩個字元。

- `^` 表示字串的開始位置
- `$` 表示字串的結束位置

```javascript
// test必須出現在開始位置
/^test/.test('test123') // true

// test必須出現在結束位置
/test$/.test('new test') // true

// 從開始位置到結束位置只有test
/^test$/.test('test') // true
/^test$/.test('test test') // false
```

**（3）選擇符（`|`）**

豎線符號（`|`）在正則表示式中表示“或關係”（OR），即`cat|dog`表示匹配`cat`或`dog`。

```javascript
/11|22/.test('911') // true
```

上面程式碼中，正則表示式指定必須匹配`11`或`22`。

多個選擇符可以聯合使用。

```javascript
// 匹配fred、barney、betty之中的一個
/fred|barney|betty/
```

選擇符會包括它前後的多個字元，比如`/ab|cd/`指的是匹配`ab`或者`cd`，而不是指匹配`b`或者`c`。如果想修改這個行為，可以使用圓括號。

```javascript
/a( |\t)b/.test('a\tb') // true
```

上面程式碼指的是，`a`和`b`之間有一個空格或者一個製表符。

其他的元字元還包括`\`、`*`、`+`、`?`、`()`、`[]`、`{}`等，將在下文解釋。

### 轉義符

正則表示式中那些有特殊含義的元字元，如果要匹配它們本身，就需要在它們前面要加上反斜槓。比如要匹配`+`，就要寫成`\+`。

```javascript
/1+1/.test('1+1')
// false

/1\+1/.test('1+1')
// true
```

上面程式碼中，第一個正則表示式之所以不匹配，因為加號是元字元，不代表自身。第二個正則表示式使用反斜槓對加號轉義，就能匹配成功。

正則表示式中，需要反斜槓轉義的，一共有12個字元：`^`、`.`、`[`、`$`、`(`、`)`、`|`、`*`、`+`、`?`、`{`和`\`。需要特別注意的是，如果使用`RegExp`方法生成正則物件，轉義需要使用兩個斜槓，因為字串內部會先轉義一次。

```javascript
(new RegExp('1\+1')).test('1+1')
// false

(new RegExp('1\\+1')).test('1+1')
// true
```

上面程式碼中，`RegExp`作為建構函式，引數是一個字串。但是，在字串內部，反斜槓也是跳脫字元，所以它會先被反斜槓轉義一次，然後再被正則表示式轉義一次，因此需要兩個反斜槓轉義。

### 特殊字元

正則表示式對一些不能列印的特殊字元，提供了表達方法。

- `\cX` 表示`Ctrl-[X]`，其中的`X`是A-Z之中任一個英文字母，用來匹配控制字元。
- `[\b]` 匹配退格鍵(U+0008)，不要與`\b`混淆。
- `\n` 匹配換行鍵。
- `\r` 匹配回車鍵。
- `\t` 匹配製表符 tab（U+0009）。
- `\v` 匹配垂直製表符（U+000B）。
- `\f` 匹配換頁符（U+000C）。
- `\0` 匹配`null`字元（U+0000）。
- `\xhh` 匹配一個以兩位十六進位制數（`\x00`-`\xFF`）表示的字元。
- `\uhhhh` 匹配一個以四位十六進位制數（`\u0000`-`\uFFFF`）表示的 Unicode 字元。

### 字元類

字元類（class）表示有一系列字元可供選擇，只要匹配其中一個就可以了。所有可供選擇的字元都放在方括號內，比如`[xyz]` 表示`x`、`y`、`z`之中任選一個匹配。

```javascript
/[abc]/.test('hello world') // false
/[abc]/.test('apple') // true
```

上面程式碼中，字串`hello world`不包含`a`、`b`、`c`這三個字母中的任一個，所以返回`false`；字串`apple`包含字母`a`，所以返回`true`。

有兩個字元在字元類中有特殊含義。

**（1）脫字元（&#94;）**

如果方括號內的第一個字元是`[^]`，則表示除了字元類之中的字元，其他字元都可以匹配。比如，`[^xyz]`表示除了`x`、`y`、`z`之外都可以匹配。

```javascript
/[^abc]/.test('bbc news') // true
/[^abc]/.test('bbc') // false
```

上面程式碼中，字串`bbc news`包含`a`、`b`、`c`以外的其他字元，所以返回`true`；字串`bbc`不包含`a`、`b`、`c`以外的其他字元，所以返回`false`。

如果方括號內沒有其他字元，即只有`[^]`，就表示匹配一切字元，其中包括換行符。相比之下，點號作為元字元（`.`）是不包括換行符的。

```javascript
var s = 'Please yes\nmake my day!';

s.match(/yes.*day/) // null
s.match(/yes[^]*day/) // [ 'yes\nmake my day']
```

上面程式碼中，字串`s`含有一個換行符，點號不包括換行符，所以第一個正則表示式匹配失敗；第二個正則表示式`[^]`包含一切字元，所以匹配成功。

> 注意，脫字元只有在字元類的第一個位置才有特殊含義，否則就是字面含義。

**（2）連字元（-）**

某些情況下，對於連續序列的字元，連字元（`-`）用來提供簡寫形式，表示字元的連續範圍。比如，`[abc]`可以寫成`[a-c]`，`[0123456789]`可以寫成`[0-9]`，同理`[A-Z]`表示26個大寫字母。

```javascript
/a-z/.test('b') // false
/[a-z]/.test('b') // true
```

上面程式碼中，當連字號（dash）不出現在方括號之中，就不具備簡寫的作用，只代表字面的含義，所以不匹配字元`b`。只有當連字號用在方括號之中，才表示連續的字元序列。

以下都是合法的字元類簡寫形式。

```javascript
[0-9.,]
[0-9a-fA-F]
[a-zA-Z0-9-]
[1-31]
```

上面程式碼中最後一個字元類`[1-31]`，不代表`1`到`31`，只代表`1`到`3`。

連字元還可以用來指定 Unicode 字元的範圍。

```javascript
var str = "\u0130\u0131\u0132";
/[\u0128-\uFFFF]/.test(str)
// true
```

上面程式碼中，`\u0128-\uFFFF`表示匹配碼點在`0128`到`FFFF`之間的所有字元。

另外，不要過分使用連字元，設定一個很大的範圍，否則很可能選中意料之外的字元。最典型的例子就是`[A-z]`，表面上它是選中從大寫的`A`到小寫的`z`之間52個字母，但是由於在 ASCII 編碼之中，大寫字母與小寫字母之間還有其他字元，結果就會出現意料之外的結果。

```javascript
/[A-z]/.test('\\') // true
```

上面程式碼中，由於反斜槓（'\\'）的ASCII碼在大寫字母與小寫字母之間，結果會被選中。

### 預定義模式

預定義模式指的是某些常見模式的簡寫方式。

- `\d` 匹配0-9之間的任一數字，相當於`[0-9]`。
- `\D` 匹配所有0-9以外的字元，相當於`[^0-9]`。
- `\w` 匹配任意的字母、數字和下劃線，相當於`[A-Za-z0-9_]`。
- `\W` 除所有字母、數字和下劃線以外的字元，相當於`[^A-Za-z0-9_]`。
- `\s` 匹配空格（包括換行符、製表符、空格符等），相等於`[ \t\r\n\v\f]`。
- `\S` 匹配非空格的字元，相當於`[^ \t\r\n\v\f]`。
- `\b` 匹配詞的邊界。
- `\B` 匹配非詞邊界，即在詞的內部。

下面是一些例子。

```javascript
// \s 的例子
/\s\w*/.exec('hello world') // [" world"]

// \b 的例子
/\bworld/.test('hello world') // true
/\bworld/.test('hello-world') // true
/\bworld/.test('helloworld') // false

// \B 的例子
/\Bworld/.test('hello-world') // false
/\Bworld/.test('helloworld') // true
```

上面程式碼中，`\s`表示空格，所以匹配結果會包括空格。`\b`表示詞的邊界，所以`world`的詞首必須獨立（詞尾是否獨立未指定），才會匹配。同理，`\B`表示非詞的邊界，只有`world`的詞首不獨立，才會匹配。

通常，正則表示式遇到換行符（`\n`）就會停止匹配。

```javascript
var html = "<b>Hello</b>\n<i>world!</i>";

/.*/.exec(html)[0]
// "<b>Hello</b>"
```

上面程式碼中，字串`html`包含一個換行符，結果點字元（`.`）不匹配換行符，導致匹配結果可能不符合原意。這時使用`\s`字元類，就能包括換行符。

```javascript
var html = "<b>Hello</b>\n<i>world!</i>";

/[\S\s]*/.exec(html)[0]
// "<b>Hello</b>\n<i>world!</i>"
```

上面程式碼中，`[\S\s]`指代一切字元。

### 重複類

模式的精確匹配次數，使用大括號（`{}`）表示。`{n}`表示恰好重複`n`次，`{n,}`表示至少重複`n`次，`{n,m}`表示重複不少於`n`次，不多於`m`次。

```javascript
/lo{2}k/.test('look') // true
/lo{2,5}k/.test('looook') // true
```

上面程式碼中，第一個模式指定`o`連續出現2次，第二個模式指定`o`連續出現2次到5次之間。

### 量詞符

量詞符用來設定某個模式出現的次數。

- `?` 問號表示某個模式出現0次或1次，等同於`{0, 1}`。
- `*` 星號表示某個模式出現0次或多次，等同於`{0,}`。
- `+` 加號表示某個模式出現1次或多次，等同於`{1,}`。

```javascript
// t 出現0次或1次
/t?est/.test('test') // true
/t?est/.test('est') // true

// t 出現1次或多次
/t+est/.test('test') // true
/t+est/.test('ttest') // true
/t+est/.test('est') // false

// t 出現0次或多次
/t*est/.test('test') // true
/t*est/.test('ttest') // true
/t*est/.test('tttest') // true
/t*est/.test('est') // true
```

### 貪婪模式

上一小節的三個量詞符，預設情況下都是最大可能匹配，即匹配到下一個字元不滿足匹配規則為止。這被稱為貪婪模式。

```javascript
var s = 'aaa';
s.match(/a+/) // ["aaa"]
```

上面程式碼中，模式是`/a+/`，表示匹配1個`a`或多個`a`，那麼到底會匹配幾個`a`呢？因為預設是貪婪模式，會一直匹配到字元`a`不出現為止，所以匹配結果是3個`a`。

除了貪婪模式，還有非貪婪模式，即最小可能匹配。只要一發現匹配，就返回結果，不要往下檢查。如果想將貪婪模式改為非貪婪模式，可以在量詞符後面加一個問號。

```javascript
var s = 'aaa';
s.match(/a+?/) // ["a"]
```

上面例子中，模式結尾添加了一個問號`/a+?/`，這時就改為非貪婪模式，一旦條件滿足，就不再往下匹配，`+?`表示只要發現一個`a`，就不再往下匹配了。

除了非貪婪模式的加號（`+?`），還有非貪婪模式的星號（`*?`）和非貪婪模式的問號（`??`）。

- `+?`：表示某個模式出現1次或多次，匹配時採用非貪婪模式。
- `*?`：表示某個模式出現0次或多次，匹配時採用非貪婪模式。
- `??`：表格某個模式出現0次或1次，匹配時採用非貪婪模式。

```javascript
'abb'.match(/ab*/) // ["abb"]
'abb'.match(/ab*?/) // ["a"]

'abb'.match(/ab?/) // ["ab"]
'abb'.match(/ab??/) // ["a"]
```

上面例子中，`/ab*/`表示如果`a`後面有多個`b`，那麼匹配儘可能多的`b`；`/ab*?/`表示匹配儘可能少的`b`，也就是0個`b`。

### 修飾符

修飾符（modifier）表示模式的附加規則，放在正則模式的最尾部。

修飾符可以單個使用，也可以多個一起使用。

```javascript
// 單個修飾符
var regex = /test/i;

// 多個修飾符
var regex = /test/ig;
```

**（1）g 修飾符**

預設情況下，第一次匹配成功後，正則物件就停止向下匹配了。`g`修飾符表示全域性匹配（global），加上它以後，正則物件將匹配全部符合條件的結果，主要用於搜尋和替換。

```javascript
var regex = /b/;
var str = 'abba';

regex.test(str); // true
regex.test(str); // true
regex.test(str); // true
```

上面程式碼中，正則模式不含`g`修飾符，每次都是從字串頭部開始匹配。所以，連續做了三次匹配，都返回`true`。

```javascript
var regex = /b/g;
var str = 'abba';

regex.test(str); // true
regex.test(str); // true
regex.test(str); // false
```

上面程式碼中，正則模式含有`g`修飾符，每次都是從上一次匹配成功處，開始向後匹配。因為字串`abba`只有兩個`b`，所以前兩次匹配結果為`true`，第三次匹配結果為`false`。

**（2）i 修飾符**

預設情況下，正則物件區分字母的大小寫，加上`i`修飾符以後表示忽略大小寫（ignoreCase）。

```javascript
/abc/.test('ABC') // false
/abc/i.test('ABC') // true
```

上面程式碼表示，加了`i`修飾符以後，不考慮大小寫，所以模式`abc`匹配字串`ABC`。

**（3）m 修飾符**

`m`修飾符表示多行模式（multiline），會修改`^`和`$`的行為。預設情況下（即不加`m`修飾符時），`^`和`$`匹配字串的開始處和結尾處，加上`m`修飾符以後，`^`和`$`還會匹配行首和行尾，即`^`和`$`會識別換行符（`\n`）。

```javascript
/world$/.test('hello world\n') // false
/world$/m.test('hello world\n') // true
```

上面的程式碼中，字串結尾處有一個換行符。如果不加`m`修飾符，匹配不成功，因為字串的結尾不是`world`；加上以後，`$`可以匹配行尾。

```javascript
/^b/m.test('a\nb') // true
```

上面程式碼要求匹配行首的`b`，如果不加`m`修飾符，就相當於`b`只能處在字串的開始處。加上`m`修飾符以後，換行符`\n`也會被認為是一行的開始。

### 組匹配

**（1）概述**

正則表示式的括號表示分組匹配，括號中的模式可以用來匹配分組的內容。

```javascript
/fred+/.test('fredd') // true
/(fred)+/.test('fredfred') // true
```

上面程式碼中，第一個模式沒有括號，結果`+`只表示重複字母`d`，第二個模式有括號，結果`+`就表示匹配`fred`這個詞。

下面是另外一個分組捕獲的例子。

```javascript
var m = 'abcabc'.match(/(.)b(.)/);
m
// ['abc', 'a', 'c']
```

上面程式碼中，正則表示式`/(.)b(.)/`一共使用兩個括號，第一個括號捕獲`a`，第二個括號捕獲`c`。

注意，使用組匹配時，不宜同時使用`g`修飾符，否則`match`方法不會捕獲分組的內容。

```javascript
var m = 'abcabc'.match(/(.)b(.)/g);
m // ['abc', 'abc']
```

上面程式碼使用帶`g`修飾符的正則表示式，結果`match`方法只捕獲了匹配整個表示式的部分。這時必須使用正則表示式的`exec`方法，配合迴圈，才能讀到每一輪匹配的組捕獲。

```javascript
var str = 'abcabc';
var reg = /(.)b(.)/g;
while (true) {
  var result = reg.exec(str);
  if (!result) break;
  console.log(result);
}
// ["abc", "a", "c"]
// ["abc", "a", "c"]
```

正則表示式內部，還可以用`\n`引用括號匹配的內容，`n`是從1開始的自然數，表示對應順序的括號。

```javascript
/(.)b(.)\1b\2/.test("abcabc")
// true
```

上面的程式碼中，`\1`表示第一個括號匹配的內容（即`a`），`\2`表示第二個括號匹配的內容（即`c`）。

下面是另外一個例子。

```javascript
/y(..)(.)\2\1/.test('yabccab') // true
```

括號還可以巢狀。

```javascript
/y((..)\2)\1/.test('yabababab') // true
```

上面程式碼中，`\1`指向外層括號，`\2`指向內層括號。

組匹配非常有用，下面是一個匹配網頁標籤的例子。

```javascript
var tagName = /<([^>]+)>[^<]*<\/\1>/;

tagName.exec("<b>bold</b>")[1]
// 'b'
```

上面程式碼中，圓括號匹配尖括號之中的標籤，而`\1`就表示對應的閉合標籤。

上面程式碼略加修改，就能捕獲帶有屬性的標籤。

```javascript
var html = '<b class="hello">Hello</b><i>world</i>';
var tag = /<(\w+)([^>]*)>(.*?)<\/\1>/g;

var match = tag.exec(html);

match[1] // "b"
match[2] // " class="hello""
match[3] // "Hello"

match = tag.exec(html);

match[1] // "i"
match[2] // ""
match[3] // "world"
```

**（2）非捕獲組**

`(?:x)`稱為非捕獲組（Non-capturing group），表示不返回該組匹配的內容，即匹配的結果中不計入這個括號。

非捕獲組的作用請考慮這樣一個場景，假定需要匹配`foo`或者`foofoo`，正則表示式就應該寫成`/(foo){1, 2}/`，但是這樣會佔用一個組匹配。這時，就可以使用非捕獲組，將正則表示式改為`/(?:foo){1, 2}/`，它的作用與前一個正則是一樣的，但是不會單獨輸出括號內部的內容。

請看下面的例子。

```javascript
var m = 'abc'.match(/(?:.)b(.)/);
m // ["abc", "c"]
```

上面程式碼中的模式，一共使用了兩個括號。其中第一個括號是非捕獲組，所以最後返回的結果中沒有第一個括號，只有第二個括號匹配的內容。

下面是用來分解網址的正則表示式。

```javascript
// 正常匹配
var url = /(http|ftp):\/\/([^/\r\n]+)(\/[^\r\n]*)?/;

url.exec('http://google.com/');
// ["http://google.com/", "http", "google.com", "/"]

// 非捕獲組匹配
var url = /(?:http|ftp):\/\/([^/\r\n]+)(\/[^\r\n]*)?/;

url.exec('http://google.com/');
// ["http://google.com/", "google.com", "/"]
```

上面的程式碼中，前一個正則表示式是正常匹配，第一個括號返回網路協議；後一個正則表示式是非捕獲匹配，返回結果中不包括網路協議。

**（3）先行斷言**

`x(?=y)`稱為先行斷言（Positive look-ahead），`x`只有在`y`前面才匹配，`y`不會被計入返回結果。比如，要匹配後面跟著百分號的數字，可以寫成`/\d+(?=%)/`。

“先行斷言”中，括號裡的部分是不會返回的。

```javascript
var m = 'abc'.match(/b(?=c)/);
m // ["b"]
```

上面的程式碼使用了先行斷言，`b`在`c`前面所以被匹配，但是括號對應的`c`不會被返回。

**（4）先行否定斷言**

`x(?!y)`稱為先行否定斷言（Negative look-ahead），`x`只有不在`y`前面才匹配，`y`不會被計入返回結果。比如，要匹配後面跟的不是百分號的數字，就要寫成`/\d+(?!%)/`。

```javascript
/\d+(?!\.)/.exec('3.14')
// ["14"]
```

上面程式碼中，正則表示式指定，只有不在小數點前面的數字才會被匹配，因此返回的結果就是`14`。

“先行否定斷言”中，括號裡的部分是不會返回的。

```javascript
var m = 'abd'.match(/b(?!c)/);
m // ['b']
```

上面的程式碼使用了先行否定斷言，`b`不在`c`前面所以被匹配，而且括號對應的`d`不會被返回。

## 參考連結

- Axel Rauschmayer, [JavaScript: an overview of the regular expression API](http://www.2ality.com/2011/04/javascript-overview-of-regular.html)
- Mozilla Developer Network, [Regular Expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
- Axel Rauschmayer, [The flag /g of JavaScript’s regular expressions](http://www.2ality.com/2013/08/regexp-g.html)
- Sam Hughes, [Learn regular expressions in about 55 minutes](http://qntm.org/files/re/re.html)
