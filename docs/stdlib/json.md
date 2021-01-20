# JSON 物件

## JSON 格式

JSON 格式（JavaScript Object Notation 的縮寫）是一種用於資料交換的文字格式，2001年由 Douglas Crockford 提出，目的是取代繁瑣笨重的 XML 格式。

相比 XML 格式，JSON 格式有兩個顯著的優點：書寫簡單，一目瞭然；符合 JavaScript 原生語法，可以由解釋引擎直接處理，不用另外新增解析程式碼。所以，JSON 迅速被接受，已經成為各大網站交換資料的標準格式，並被寫入標準。

每個 JSON 物件就是一個值，可能是一個數組或物件，也可能是一個原始型別的值。總之，只能是一個值，不能是兩個或更多的值。

JSON 對值的型別和格式有嚴格的規定。

> 1. 複合型別的值只能是陣列或物件，不能是函式、正則表示式物件、日期物件。
>
> 1. 原始型別的值只有四種：字串、數值（必須以十進位制表示）、布林值和`null`（不能使用`NaN`, `Infinity`, `-Infinity`和`undefined`）。
>
> 1. 字串必須使用雙引號表示，不能使用單引號。
>
> 1. 物件的鍵名必須放在雙引號裡面。
>
> 1. 陣列或物件最後一個成員的後面，不能加逗號。

以下都是合法的 JSON。

```javascript
["one", "two", "three"]

{ "one": 1, "two": 2, "three": 3 }

{"names": ["張三", "李四"] }

[ { "name": "張三"}, {"name": "李四"} ]
```

以下都是不合法的 JSON。

```javascript
{ name: "張三", 'age': 32 }  // 屬性名必須使用雙引號

[32, 64, 128, 0xFFF] // 不能使用十六進位制值

{ "name": "張三", "age": undefined } // 不能使用 undefined

{ "name": "張三",
  "birthday": new Date('Fri, 26 Aug 2011 07:13:10 GMT'),
  "getName": function () {
      return this.name;
  }
} // 屬性值不能使用函式和日期物件
```

注意，`null`、空陣列和空物件都是合法的 JSON 值。

## JSON 物件

`JSON`物件是 JavaScript 的原生物件，用來處理 JSON 格式資料。它有兩個靜態方法：`JSON.stringify()`和`JSON.parse()`。

## JSON.stringify()

### 基本用法

`JSON.stringify()`方法用於將一個值轉為 JSON 字串。該字串符合 JSON 格式，並且可以被`JSON.parse()`方法還原。

```javascript
JSON.stringify('abc') // ""abc""
JSON.stringify(1) // "1"
JSON.stringify(false) // "false"
JSON.stringify([]) // "[]"
JSON.stringify({}) // "{}"

JSON.stringify([1, "false", false])
// '[1,"false",false]'

JSON.stringify({ name: "張三" })
// '{"name":"張三"}'
```

上面程式碼將各種型別的值，轉成 JSON 字串。

注意，對於原始型別的字串，轉換結果會帶雙引號。

```javascript
JSON.stringify('foo') === "foo" // false
JSON.stringify('foo') === "\"foo\"" // true
```

上面程式碼中，字串`foo`，被轉成了`"\"foo\""`。這是因為將來還原的時候，內層雙引號可以讓 JavaScript 引擎知道，這是一個字串，而不是其他型別的值。

```javascript
JSON.stringify(false) // "false"
JSON.stringify('false') // "\"false\""
```

上面程式碼中，如果不是內層的雙引號，將來還原的時候，引擎就無法知道原始值是布林值還是字串。

如果物件的屬性是`undefined`、函式或 XML 物件，該屬性會被`JSON.stringify()`過濾。

```javascript
var obj = {
  a: undefined,
  b: function () {}
};

JSON.stringify(obj) // "{}"
```

上面程式碼中，物件`obj`的`a`屬性是`undefined`，而`b`屬性是一個函式，結果都被`JSON.stringify`過濾。

如果陣列的成員是`undefined`、函式或 XML 物件，則這些值被轉成`null`。

```javascript
var arr = [undefined, function () {}];
JSON.stringify(arr) // "[null,null]"
```

上面程式碼中，陣列`arr`的成員是`undefined`和函式，它們都被轉成了`null`。

正則物件會被轉成空物件。

```javascript
JSON.stringify(/foo/) // "{}"
```

`JSON.stringify()`方法會忽略物件的不可遍歷的屬性。

```javascript
var obj = {};
Object.defineProperties(obj, {
  'foo': {
    value: 1,
    enumerable: true
  },
  'bar': {
    value: 2,
    enumerable: false
  }
});

JSON.stringify(obj); // "{"foo":1}"
```

上面程式碼中，`bar`是`obj`物件的不可遍歷屬性，`JSON.stringify`方法會忽略這個屬性。

### 第二個引數

`JSON.stringify()`方法還可以接受一個數組，作為第二個引數，指定引數物件的哪些屬性需要轉成字串。

```javascript
var obj = {
  'prop1': 'value1',
  'prop2': 'value2',
  'prop3': 'value3'
};

var selectedProperties = ['prop1', 'prop2'];

JSON.stringify(obj, selectedProperties)
// "{"prop1":"value1","prop2":"value2"}"
```

上面程式碼中，`JSON.stringify()`方法的第二個引數指定，只轉`prop1`和`prop2`兩個屬性。

這個類似白名單的陣列，只對物件的屬性有效，對陣列無效。

```javascript
JSON.stringify(['a', 'b'], ['0'])
// "["a","b"]"

JSON.stringify({0: 'a', 1: 'b'}, ['0'])
// "{"0":"a"}"
```

上面程式碼中，第二個引數指定 JSON 格式只轉`0`號屬性，實際上對陣列是無效的，只對物件有效。

第二個引數還可以是一個函式，用來更改`JSON.stringify()`的返回值。

```javascript
function f(key, value) {
  if (typeof value === "number") {
    value = 2 * value;
  }
  return value;
}

JSON.stringify({ a: 1, b: 2 }, f)
// '{"a": 2,"b": 4}'
```

上面程式碼中的`f`函式，接受兩個引數，分別是被轉換的物件的鍵名和鍵值。如果鍵值是數值，就將它乘以`2`，否則就原樣返回。

注意，這個處理函式是遞迴處理所有的鍵。

```javascript
var obj = {a: {b: 1}};

function f(key, value) {
  console.log("["+ key +"]:" + value);
  return value;
}

JSON.stringify(obj, f)
// []:[object Object]
// [a]:[object Object]
// [b]:1
// '{"a":{"b":1}}'
```

上面程式碼中，物件`obj`一共會被`f`函式處理三次，輸出的最後那行是`JSON.stringify()`的預設輸出。第一次鍵名為空，鍵值是整個物件`obj`；第二次鍵名為`a`，鍵值是`{b: 1}`；第三次鍵名為`b`，鍵值為1。

遞迴處理中，每一次處理的物件，都是前一次返回的值。

```javascript
var obj = {a: 1};

function f(key, value) {
  if (typeof value === 'object') {
    return {b: 2};
  }
  return value * 2;
}

JSON.stringify(obj, f)
// "{"b": 4}"
```

上面程式碼中，`f`函式修改了物件`obj`，接著`JSON.stringify()`方法就遞迴處理修改後的物件`obj`。

如果處理函式返回`undefined`或沒有返回值，則該屬性會被忽略。

```javascript
function f(key, value) {
  if (typeof(value) === "string") {
    return undefined;
  }
  return value;
}

JSON.stringify({ a: "abc", b: 123 }, f)
// '{"b": 123}'
```

上面程式碼中，`a`屬性經過處理後，返回`undefined`，於是該屬性被忽略了。

### 第三個引數

`JSON.stringify()`還可以接受第三個引數，用於增加返回的 JSON 字串的可讀性。

預設返回的是單行字串，對於大型的 JSON 物件，可讀性非常差。第三個引數使得每個屬性單獨佔據一行，並且將每個屬性前面新增指定的字首（不超過10個字元）。

```javascript
// 預設輸出
JSON.stringify({ p1: 1, p2: 2 })
// JSON.stringify({ p1: 1, p2: 2 })

// 分行輸出
JSON.stringify({ p1: 1, p2: 2 }, null, '\t')
// {
// 	"p1": 1,
// 	"p2": 2
// }
```

上面例子中，第三個屬性`\t`在每個屬性前面新增一個製表符，然後分行顯示。

第三個屬性如果是一個數字，則表示每個屬性前面新增的空格（最多不超過10個）。

```javascript
JSON.stringify({ p1: 1, p2: 2 }, null, 2);
/*
"{
  "p1": 1,
  "p2": 2
}"
*/
```

### 引數物件的 toJSON() 方法

如果引數物件有自定義的`toJSON()`方法，那麼`JSON.stringify()`會使用這個方法的返回值作為引數，而忽略原物件的其他屬性。

下面是一個普通的物件。

```javascript
var user = {
  firstName: '三',
  lastName: '張',

  get fullName(){
    return this.lastName + this.firstName;
  }
};

JSON.stringify(user)
// "{"firstName":"三","lastName":"張","fullName":"張三"}"
```

現在，為這個物件加上`toJSON()`方法。

```javascript
var user = {
  firstName: '三',
  lastName: '張',

  get fullName(){
    return this.lastName + this.firstName;
  },

  toJSON: function () {
    return {
      name: this.lastName + this.firstName
    };
  }
};

JSON.stringify(user)
// "{"name":"張三"}"
```

上面程式碼中，`JSON.stringify()`發現引數物件有`toJSON()`方法，就直接使用這個方法的返回值作為引數，而忽略原物件的其他引數。

`Date`物件就有一個自己的`toJSON()`方法。

```javascript
var date = new Date('2015-01-01');
date.toJSON() // "2015-01-01T00:00:00.000Z"
JSON.stringify(date) // ""2015-01-01T00:00:00.000Z""
```

上面程式碼中，`JSON.stringify()`發現處理的是`Date`物件例項，就會呼叫這個例項物件的`toJSON()`方法，將該方法的返回值作為引數。

`toJSON()`方法的一個應用是，將正則物件自動轉為字串。因為`JSON.stringify()`預設不能轉換正則物件，但是設定了`toJSON()`方法以後，就可以轉換正則物件了。

```javascript
var obj = {
  reg: /foo/
};

// 不設定 toJSON 方法時
JSON.stringify(obj) // "{"reg":{}}"

// 設定 toJSON 方法時
RegExp.prototype.toJSON = RegExp.prototype.toString;
JSON.stringify(/foo/) // ""/foo/""
```

上面程式碼在正則物件的原型上面部署了`toJSON()`方法，將其指向`toString()`方法，因此轉換成 JSON 格式時，正則物件就先呼叫`toJSON()`方法轉為字串，然後再被`JSON.stringify()`方法處理。

## JSON.parse()

`JSON.parse()`方法用於將 JSON 字串轉換成對應的值。

```javascript
JSON.parse('{}') // {}
JSON.parse('true') // true
JSON.parse('"foo"') // "foo"
JSON.parse('[1, 5, "false"]') // [1, 5, "false"]
JSON.parse('null') // null

var o = JSON.parse('{"name": "張三"}');
o.name // 張三
```

如果傳入的字串不是有效的 JSON 格式，`JSON.parse()`方法將報錯。

```javascript
JSON.parse("'String'") // illegal single quotes
// SyntaxError: Unexpected token ILLEGAL
```

上面程式碼中，雙引號字串中是一個單引號字串，因為單引號字串不符合 JSON 格式，所以報錯。

為了處理解析錯誤，可以將`JSON.parse()`方法放在`try...catch`程式碼塊中。

```javascript
try {
  JSON.parse("'String'");
} catch(e) {
  console.log('parsing error');
}
```

`JSON.parse()`方法可以接受一個處理函式，作為第二個引數，用法與`JSON.stringify()`方法類似。

```javascript
function f(key, value) {
  if (key === 'a') {
    return value + 10;
  }
  return value;
}

JSON.parse('{"a": 1, "b": 2}', f)
// {a: 11, b: 2}
```

上面程式碼中，`JSON.parse()`的第二個引數是一個函式，如果鍵名是`a`，該函式會將鍵值加上10。

## 參考連結

- MDN, [Using native JSON](https://developer.mozilla.org/en-US/docs/Using_native_JSON)
- MDN, [JSON.parse](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/JSON/parse)
- Dr. Axel Rauschmayer, [JavaScript’s JSON API](http://www.2ality.com/2011/08/json-api.html)
- Jim Cowart, [What You Might Not Know About JSON.stringify()](http://freshbrewedcode.com/jimcowart/2013/01/29/what-you-might-not-know-about-json-stringify/)
- Marco Rogers, [What is JSON?](https://docs.nodejitsu.com/articles/javascript-conventions/what-is-json/)
