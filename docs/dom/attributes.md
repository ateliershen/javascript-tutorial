# 屬性的操作

HTML 元素包括標籤名和若干個鍵值對，這個鍵值對就稱為“屬性”（attribute）。

```html
<a id="test" href="http://www.example.com">
  連結
</a>
```

上面程式碼中，`a`元素包括兩個屬性：`id`屬性和`href`屬性。

屬性本身是一個物件（`Attr`物件），但是實際上，這個物件極少使用。一般都是透過元素節點物件（`HTMlElement`物件）來操作屬性。本章介紹如何操作這些屬性。

## Element.attributes 屬性

元素物件有一個`attributes`屬性，返回一個類似陣列的動態物件，成員是該元素標籤的所有屬性節點物件，屬性的實時變化都會反映在這個節點物件上。其他型別的節點物件，雖然也有`attributes`屬性，但返回的都是`null`，因此可以把這個屬性視為元素物件獨有的。

單個屬性可以透過序號引用，也可以透過屬性名引用。

```javascript
// HTML 程式碼如下
// <body bgcolor="yellow" onload="">
document.body.attributes[0]
document.body.attributes.bgcolor
document.body.attributes['ONLOAD']
```

注意，上面程式碼的三種方法，返回的都是屬性節點物件，而不是屬性值。

屬性節點物件有`name`和`value`屬性，對應該屬性的屬性名和屬性值，等同於`nodeName`屬性和`nodeValue`屬性。

```javascript
// HTML程式碼為
// <div id="mydiv">
var n = document.getElementById('mydiv');

n.attributes[0].name // "id"
n.attributes[0].nodeName // "id"

n.attributes[0].value // "mydiv"
n.attributes[0].nodeValue // "mydiv"
```

下面程式碼可以遍歷一個元素節點的所有屬性。

```javascript
var para = document.getElementsByTagName('p')[0];
var result = document.getElementById('result');

if (para.hasAttributes()) {
  var attrs = para.attributes;
  var output = '';
  for(var i = attrs.length - 1; i >= 0; i--) {
    output += attrs[i].name + '->' + attrs[i].value;
  }
  result.textContent = output;
} else {
  result.textContent = 'No attributes to show';
}
```

## 元素的標準屬性

HTML 元素的標準屬性（即在標準中定義的屬性），會自動成為元素節點物件的屬性。

```javascript
var a = document.getElementById('test');
a.id // "test"
a.href // "http://www.example.com/"
```

上面程式碼中，`a`元素標籤的屬性`id`和`href`，自動成為節點物件的屬性。

這些屬性都是可寫的。

```javascript
var img = document.getElementById('myImage');
img.src = 'http://www.example.com/image.jpg';
```

上面的寫法，會立刻替換掉`img`物件的`src`屬性，即會顯示另外一張圖片。

這種修改屬性的方法，常常用於新增表單的屬性。

```javascript
var f = document.forms[0];
f.action = 'submit.php';
f.method = 'POST';
```

上面程式碼為表單新增提交網址和提交方法。

注意，這種用法雖然可以讀寫屬性，但是無法刪除屬性，`delete`運算子在這裡不會生效。

HTML 元素的屬性名是大小寫不敏感的，但是 JavaScript 物件的屬性名是大小寫敏感的。轉換規則是，轉為 JavaScript 屬性名時，一律採用小寫。如果屬性名包括多個單詞，則採用駱駝拼寫法，即從第二個單詞開始，每個單詞的首字母採用大寫，比如`onClick`。

有些 HTML 屬性名是 JavaScript 的保留字，轉為 JavaScript 屬性時，必須改名。主要是以下兩個。

- `for`屬性改為`htmlFor`
- `class`屬性改為`className`

另外，HTML 屬性值一般都是字串，但是 JavaScript 屬性會自動轉換型別。比如，將字串`true`轉為布林值，將`onClick`的值轉為一個函式，將`style`屬性的值轉為一個`CSSStyleDeclaration`物件。因此，可以對這些屬性賦予各種型別的值。

## 屬性操作的標準方法

### 概述

元素節點提供六個方法，用來操作屬性。

- `getAttribute()`
- `getAttributeNames()`
- `setAttribute()`
- `hasAttribute()`
- `hasAttributes()`
- `removeAttribute()`

這有幾點注意。

（1）適用性

這六個方法對所有屬性（包括使用者自定義的屬性）都適用。

（2）返回值

`getAttribute()`只返回字串，不會返回其他型別的值。

（3）屬性名

這些方法只接受屬性的標準名稱，不用改寫保留字，比如`for`和`class`都可以直接使用。另外，這些方法對於屬性名是大小寫不敏感的。

```javascript
var image = document.images[0];
image.setAttribute('class', 'myImage');
```

上面程式碼中，`setAttribute`方法直接使用`class`作為屬性名，不用寫成`className`。

### Element.getAttribute()

`Element.getAttribute`方法返回當前元素節點的指定屬性。如果指定屬性不存在，則返回`null`。

```javascript
// HTML 程式碼為
// <div id="div1" align="left">
var div = document.getElementById('div1');
div.getAttribute('align') // "left"
```

### Element.getAttributeNames()

`Element.getAttributeNames()`返回一個數組，成員是當前元素的所有屬性的名字。如果當前元素沒有任何屬性，則返回一個空陣列。使用`Element.attributes`屬性，也可以拿到同樣的結果，唯一的區別是它返回的是類似陣列的物件。

```javascript
var mydiv = document.getElementById('mydiv');

mydiv.getAttributeNames().forEach(function (key) {
  var value = mydiv.getAttribute(key);
  console.log(key, value);
})
```

上面程式碼用於遍歷某個節點的所有屬性。

### Element.setAttribute()

`Element.setAttribute`方法用於為當前元素節點新增屬性。如果同名屬性已存在，則相當於編輯已存在的屬性。該方法沒有返回值。

```javascript
// HTML 程式碼為
// <button>Hello World</button>
var b = document.querySelector('button');
b.setAttribute('name', 'myButton');
b.setAttribute('disabled', true);
```

上面程式碼中，`button`元素的`name`屬性被設成`myButton`，`disabled`屬性被設成`true`。

這裡有兩個地方需要注意，首先，屬性值總是字串，其他型別的值會自動轉成字串，比如布林值`true`就會變成字串`true`；其次，上例的`disable`屬性是一個布林屬性，對於`<button>`元素來說，這個屬性不需要屬性值，只要設定了就總是會生效，因此`setAttribute`方法裡面可以將`disabled`屬性設成任意值。

### Element.hasAttribute()

`Element.hasAttribute`方法返回一個布林值，表示當前元素節點是否包含指定屬性。

```javascript
var d = document.getElementById('div1');

if (d.hasAttribute('align')) {
  d.setAttribute('align', 'center');
}
```

上面程式碼檢查`div`節點是否含有`align`屬性。如果有，則設定為居中對齊。

### Element.hasAttributes()

`Element.hasAttributes`方法返回一個布林值，表示當前元素是否有屬性，如果沒有任何屬性，就返回`false`，否則返回`true`。

```javascript
var foo = document.getElementById('foo');
foo.hasAttributes() // true
```

### Element.removeAttribute()

`Element.removeAttribute`方法移除指定屬性。該方法沒有返回值。

```javascript
// HTML 程式碼為
// <div id="div1" align="left" width="200px">
document.getElementById('div1').removeAttribute('align');
// 現在的HTML程式碼為
// <div id="div1" width="200px">
```

## dataset 屬性

有時，需要在HTML元素上附加資料，供 JavaScript 指令碼使用。一種解決方法是自定義屬性。

```html
<div id="mydiv" foo="bar">
```

上面程式碼為`div`元素自定義了`foo`屬性，然後可以用`getAttribute()`和`setAttribute()`讀寫這個屬性。

```javascript
var n = document.getElementById('mydiv');
n.getAttribute('foo') // bar
n.setAttribute('foo', 'baz')
```

這種方法雖然可以達到目的，但是會使得 HTML 元素的屬性不符合標準，導致網頁程式碼通不過校驗。

更好的解決方法是，使用標準提供的`data-*`屬性。

```html
<div id="mydiv" data-foo="bar">
```

然後，使用元素節點物件的`dataset`屬性，它指向一個物件，可以用來操作 HTML 元素標籤的`data-*`屬性。

```javascript
var n = document.getElementById('mydiv');
n.dataset.foo // bar
n.dataset.foo = 'baz'
```

上面程式碼中，透過`dataset.foo`讀寫`data-foo`屬性。

刪除一個`data-*`屬性，可以直接使用`delete`命令。

```javascript
delete document.getElementById('myDiv').dataset.foo;
```

除了`dataset`屬性，也可以用`getAttribute('data-foo')`、`removeAttribute('data-foo')`、`setAttribute('data-foo')`、`hasAttribute('data-foo')`等方法操作`data-*`屬性。

注意，`data-`後面的屬性名有限制，只能包含字母、數字、連詞線（`-`）、點（`.`）、冒號（`:`）和下劃線（`_`)。而且，屬性名不應該使用`A`到`Z`的大寫字母，比如不能有`data-helloWorld`這樣的屬性名，而要寫成`data-hello-world`。

轉成`dataset`的鍵名時，連詞線後面如果跟著一個小寫字母，那麼連詞線會被移除，該小寫字母轉為大寫字母，其他字元不變。反過來，`dataset`的鍵名轉成屬性名時，所有大寫字母都會被轉成連詞線+該字母的小寫形式，其他字元不變。比如，`dataset.helloWorld`會轉成`data-hello-world`。
