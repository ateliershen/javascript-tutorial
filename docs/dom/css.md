# CSS 操作

CSS 與 JavaScript 是兩個有著明確分工的領域，前者負責頁面的視覺效果，後者負責與使用者的行為互動。但是，它們畢竟同屬網頁開發的前端，因此不可避免有著交叉和互相配合。本章介紹如何透過 JavaScript 操作 CSS。

## HTML 元素的 style 屬性

操作 CSS 樣式最簡單的方法，就是使用網頁元素節點的`getAttribute()`方法、`setAttribute()`方法和`removeAttribute()`方法，直接讀寫或刪除網頁元素的`style`屬性。

```javascript
div.setAttribute(
  'style',
  'background-color:red;' + 'border:1px solid black;'
);
```

上面的程式碼相當於下面的 HTML 程式碼。

```html
<div style="background-color:red; border:1px solid black;" />
```

`style`不僅可以使用字串讀寫，它本身還是一個物件，部署了 CSSStyleDeclaration 介面（詳見下面的介紹），可以直接讀寫個別屬性。

```javascript
e.style.fontSize = '18px';
e.style.color = 'black';
```

## CSSStyleDeclaration 介面

### 簡介

CSSStyleDeclaration 介面用來操作元素的樣式。三個地方部署了這個介面。

- 元素節點的`style`屬性（`Element.style`）
- `CSSStyle`例項的`style`屬性
- `window.getComputedStyle()`的返回值

CSSStyleDeclaration 介面可以直接讀寫 CSS 的樣式屬性，不過，連詞號需要變成駱駝拼寫法。

```javascript
var divStyle = document.querySelector('div').style;

divStyle.backgroundColor = 'red';
divStyle.border = '1px solid black';
divStyle.width = '100px';
divStyle.height = '100px';
divStyle.fontSize = '10em';

divStyle.backgroundColor // red
divStyle.border // 1px solid black
divStyle.height // 100px
divStyle.width // 100px
```

上面程式碼中，`style`屬性的值是一個 CSSStyleDeclaration 例項。這個物件所包含的屬性與 CSS 規則一一對應，但是名字需要改寫，比如`background-color`寫成`backgroundColor`。改寫的規則是將橫槓從 CSS 屬性名中去除，然後將橫槓後的第一個字母大寫。如果 CSS 屬性名是 JavaScript 保留字，則規則名之前需要加上字串`css`，比如`float`寫成`cssFloat`。

注意，該物件的屬性值都是字串，設定時必須包括單位，但是不含規則結尾的分號。比如，`divStyle.width`不能寫為`100`，而要寫為`100px`。

另外，`Element.style`返回的只是行內樣式，並不是該元素的全部樣式。透過樣式表設定的樣式，或者從父元素繼承的樣式，無法透過這個屬性得到。元素的全部樣式要透過`window.getComputedStyle()`得到。

### CSSStyleDeclaration 例項屬性

**（1）CSSStyleDeclaration.cssText**

`CSSStyleDeclaration.cssText`屬性用來讀寫當前規則的所有樣式宣告文字。

```javascript
var divStyle = document.querySelector('div').style;

divStyle.cssText = 'background-color: red;'
  + 'border: 1px solid black;'
  + 'height: 100px;'
  + 'width: 100px;';
```

注意，`cssText`的屬性值不用改寫 CSS 屬性名。

刪除一個元素的所有行內樣式，最簡便的方法就是設定`cssText`為空字串。

```javascript
divStyle.cssText = '';
```

**（2）CSSStyleDeclaration.length**

`CSSStyleDeclaration.length`屬性返回一個整數值，表示當前規則包含多少條樣式宣告。

```javascript
// HTML 程式碼如下
// <div id="myDiv"
//   style="height: 1px;width: 100%;background-color: #CA1;"
// ></div>
var myDiv = document.getElementById('myDiv');
var divStyle = myDiv.style;
divStyle.length // 3
```

上面程式碼中，`myDiv`元素的行內樣式共包含3條樣式規則。

**（3）CSSStyleDeclaration.parentRule**

`CSSStyleDeclaration.parentRule`屬性返回當前規則所屬的那個樣式塊（CSSRule 例項）。如果不存在所屬的樣式塊，該屬性返回`null`。

該屬性只讀，且只在使用 CSSRule 介面時有意義。

```javascript
var declaration = document.styleSheets[0].rules[0].style;
declaration.parentRule === document.styleSheets[0].rules[0]
// true
```

### CSSStyleDeclaration 例項方法

**（1）CSSStyleDeclaration.getPropertyPriority()**

`CSSStyleDeclaration.getPropertyPriority`方法接受 CSS 樣式的屬性名作為引數，返回一個字串，表示有沒有設定`important`優先順序。如果有就返回`important`，否則返回空字串。

```javascript
// HTML 程式碼為
// <div id="myDiv" style="margin: 10px!important; color: red;"/>
var style = document.getElementById('myDiv').style;
style.margin // "10px"
style.getPropertyPriority('margin') // "important"
style.getPropertyPriority('color') // ""
```

上面程式碼中，`margin`屬性有`important`優先順序，`color`屬性沒有。

**（2）CSSStyleDeclaration.getPropertyValue()**

`CSSStyleDeclaration.getPropertyValue`方法接受 CSS 樣式屬性名作為引數，返回一個字串，表示該屬性的屬性值。

```javascript
// HTML 程式碼為
// <div id="myDiv" style="margin: 10px!important; color: red;"/>
var style = document.getElementById('myDiv').style;
style.margin // "10px"
style.getPropertyValue("margin") // "10px"
```

**（3）CSSStyleDeclaration.item()**

`CSSStyleDeclaration.item`方法接受一個整數值作為引數，返回該位置的 CSS 屬性名。

```javascript
// HTML 程式碼為
// <div id="myDiv" style="color: red; background-color: white;"/>
var style = document.getElementById('myDiv').style;
style.item(0) // "color"
style.item(1) // "background-color"
```

上面程式碼中，`0`號位置的 CSS 屬性名是`color`，`1`號位置的 CSS 屬性名是`background-color`。

如果沒有提供引數，這個方法會報錯。如果引數值超過實際的屬性數目，這個方法返回一個空字元值。

**（4）CSSStyleDeclaration.removeProperty()**

`CSSStyleDeclaration.removeProperty`方法接受一個屬性名作為引數，在 CSS 規則裡面移除這個屬性，返回這個屬性原來的值。

```javascript
// HTML 程式碼為
// <div id="myDiv" style="color: red; background-color: white;">
//   111
// </div>
var style = document.getElementById('myDiv').style;
style.removeProperty('color') // 'red'
// HTML 程式碼變為
// <div id="myDiv" style="background-color: white;">
```

上面程式碼中，刪除`color`屬性以後，字型顏色從紅色變成預設顏色。

**（5）CSSStyleDeclaration.setProperty()**

`CSSStyleDeclaration.setProperty`方法用來設定新的 CSS 屬性。該方法沒有返回值。

該方法可以接受三個引數。

- 第一個引數：屬性名，該引數是必需的。
- 第二個引數：屬性值，該引數可選。如果省略，則引數值預設為空字串。
- 第三個引數：優先順序，該引數可選。如果設定，唯一的合法值是`important`，表示 CSS 規則裡面的`!important`。

```javascript
// HTML 程式碼為
// <div id="myDiv" style="color: red; background-color: white;">
//   111
// </div>
var style = document.getElementById('myDiv').style;
style.setProperty('border', '1px solid blue');
```

上面程式碼執行後，`myDiv`元素就會出現藍色的邊框。

## CSS 模組的偵測

CSS 的規格發展太快，新的模組層出不窮。不同瀏覽器的不同版本，對 CSS 模組的支援情況都不一樣。有時候，需要知道當前瀏覽器是否支援某個模組，這就叫做“CSS模組的偵測”。

一個比較普遍適用的方法是，判斷元素的`style`物件的某個屬性值是否為字串。

```javascript
typeof element.style.animationName === 'string';
typeof element.style.transform === 'string';
```

如果該 CSS 屬性確實存在，會返回一個字串。即使該屬性實際上並未設定，也會返回一個空字串。如果該屬性不存在，則會返回`undefined`。

```javascript
document.body.style['maxWidth'] // ""
document.body.style['maximumWidth'] // undefined
```

上面程式碼說明，這個瀏覽器支援`max-width`屬性，但是不支援`maximum-width`屬性。

注意，不管 CSS 屬性名的寫法帶不帶連詞線，`style`屬性上都能反映出該屬性是否存在。

```javascript
document.body.style['backgroundColor'] // ""
document.body.style['background-color'] // ""
```

另外，使用的時候，需要把不同瀏覽器的 CSS 字首也考慮進去。

```javascript
var content = document.getElementById('content');
typeof content.style['webkitAnimation'] === 'string'
```

這種偵測方法可以寫成一個函式。

```javascript
function isPropertySupported(property) {
  if (property in document.body.style) return true;
  var prefixes = ['Moz', 'Webkit', 'O', 'ms', 'Khtml'];
  var prefProperty = property.charAt(0).toUpperCase() + property.substr(1);

  for(var i = 0; i < prefixes.length; i++){
    if((prefixes[i] + prefProperty) in document.body.style) return true;
  }

  return false;
}

isPropertySupported('background-clip')
// true
```

## CSS 物件

瀏覽器原生提供 CSS 物件，為 JavaScript 操作 CSS 提供一些工具方法。

這個物件目前有兩個靜態方法。

### CSS.escape()

`CSS.escape`方法用於轉義 CSS 選擇器裡面的特殊字元。

```html
<div id="foo#bar">
```

上面程式碼中，該元素的`id`屬性包含一個`#`號，該字元在 CSS 選擇器裡面有特殊含義。不能直接寫成`document.querySelector('#foo#bar')`，只能寫成`document.querySelector('#foo\\#bar')`。這裡必須使用雙斜槓的原因是，單引號字串本身會轉義一次斜槓。

`CSS.escape`方法就用來轉義那些特殊字元。

```javascript
document.querySelector('#' + CSS.escape('foo#bar'))
```

### CSS.supports()

`CSS.supports`方法返回一個布林值，表示當前環境是否支援某一句 CSS 規則。

它的引數有兩種寫法，一種是第一個引數是屬性名，第二個引數是屬性值；另一種是整個引數就是一行完整的 CSS 語句。

```javascript
// 第一種寫法
CSS.supports('transform-origin', '5px') // true

// 第二種寫法
CSS.supports('display: table-cell') // true
```

注意，第二種寫法的引數結尾不能帶有分號，否則結果不準確。

```javascript
CSS.supports('display: table-cell;') // false
```

## window.getComputedStyle()

行內樣式（inline style）具有最高的優先順序，改變行內樣式，通常會立即反映出來。但是，網頁元素最終的樣式是綜合各種規則計算出來的。因此，如果想得到元素實際的樣式，只讀取行內樣式是不夠的，需要得到瀏覽器最終計算出來的樣式規則。

`window.getComputedStyle`方法，就用來返回瀏覽器計算後得到的最終規則。它接受一個節點物件作為引數，返回一個 CSSStyleDeclaration  例項，包含了指定節點的最終樣式資訊。所謂“最終樣式資訊”，指的是各種 CSS 規則疊加後的結果。

```javascript
var div = document.querySelector('div');
var styleObj = window.getComputedStyle(div);
styleObj.backgroundColor
```

上面程式碼中，得到的背景色就是`div`元素真正的背景色。

注意，CSSStyleDeclaration 例項是一個活的物件，任何對於樣式的修改，會實時反映到這個例項上面。另外，這個例項是隻讀的。

`getComputedStyle`方法還可以接受第二個引數，表示當前元素的偽元素（比如`:before`、`:after`、`:first-line`、`:first-letter`等）。

```javascript
var result = window.getComputedStyle(div, ':before');
```

下面的例子是如何獲取元素的高度。

```javascript
var elem = document.getElementById('elem-container');
var styleObj = window.getComputedStyle(elem, null)
var height = styleObj.height;
// 等同於
var height = styleObj['height'];
var height = styleObj.getPropertyValue('height');
```

上面程式碼得到的`height`屬性，是瀏覽器最終渲染出來的高度，比其他方法得到的高度更可靠。由於`styleObj`是 CSSStyleDeclaration 例項，所以可以使用各種 CSSStyleDeclaration 的例項屬性和方法。

有幾點需要注意。

- CSSStyleDeclaration 例項返回的 CSS 值都是絕對單位。比如，長度都是畫素單位（返回值包括`px`字尾），顏色是`rgb(#, #, #)`或`rgba(#, #, #, #)`格式。
- CSS 規則的簡寫形式無效。比如，想讀取`margin`屬性的值，不能直接讀，只能讀`marginLeft`、`marginTop`等屬性；再比如，`font`屬性也是不能直接讀的，只能讀`font-size`等單個屬性。
- 如果讀取 CSS 原始的屬性名，要用方括號運算子，比如`styleObj['z-index']`；如果讀取駱駝拼寫法的 CSS 屬性名，可以直接讀取`styleObj.zIndex`。
- 該方法返回的 CSSStyleDeclaration 例項的`cssText`屬性無效，返回`undefined`。

## CSS 偽元素

CSS 偽元素是透過 CSS 向 DOM 新增的元素，主要是透過`:before`和`:after`選擇器生成，然後用`content`屬性指定偽元素的內容。

下面是一段 HTML 程式碼。

```html
<div id="test">Test content</div>
```

CSS 新增偽元素`:before`的寫法如下。

```css
#test:before {
  content: 'Before ';
  color: #FF0;
}
```

節點元素的`style`物件無法讀寫偽元素的樣式，這時就要用到`window.getComputedStyle()`。JavaScript 獲取偽元素，可以使用下面的方法。

```javascript
var test = document.querySelector('#test');

var result = window.getComputedStyle(test, ':before').content;
var color = window.getComputedStyle(test, ':before').color;
```

此外，也可以使用 CSSStyleDeclaration 例項的`getPropertyValue`方法，獲取偽元素的屬性。

```javascript
var result = window.getComputedStyle(test, ':before')
  .getPropertyValue('content');
var color = window.getComputedStyle(test, ':before')
  .getPropertyValue('color');
```

## StyleSheet 介面

### 概述

`StyleSheet`介面代表網頁的一張樣式表，包括`<link>`元素載入的樣式表和`<style>`元素內嵌的樣式表。

`document`物件的`styleSheets`屬性，可以返回當前頁面的所有`StyleSheet`例項（即所有樣式表）。它是一個類似陣列的物件。

```javascript
var sheets = document.styleSheets;
var sheet = document.styleSheets[0];
sheet instanceof StyleSheet // true
```

如果是`<style>`元素嵌入的樣式表，還有另一種獲取`StyleSheet`例項的方法，就是這個節點元素的`sheet`屬性。

```javascript
// HTML 程式碼為 <style id="myStyle"></style>
var myStyleSheet = document.getElementById('myStyle').sheet;
myStyleSheet instanceof StyleSheet // true
```

嚴格地說，`StyleSheet`介面不僅包括網頁樣式表，還包括 XML 文件的樣式表。所以，它有一個子類`CSSStyleSheet`表示網頁的 CSS 樣式表。我們在網頁裡面拿到的樣式表例項，實際上是`CSSStyleSheet`的例項。這個子介面繼承了`StyleSheet`的所有屬性和方法，並且定義了幾個自己的屬性，下面把這兩個介面放在一起介紹。

### 例項屬性

`StyleSheet`例項有以下屬性。

**（1）StyleSheet.disabled**

`StyleSheet.disabled`返回一個布林值，表示該樣式表是否處於禁用狀態。手動設定`disabled`屬性為`true`，等同於在`<link>`元素裡面，將這張樣式表設為`alternate stylesheet`，即該樣式表將不會生效。

注意，`disabled`屬性只能在 JavaScript 指令碼中設定，不能在 HTML 語句中設定。

**（2）Stylesheet.href**

`Stylesheet.href`返回樣式表的網址。對於內嵌樣式表，該屬性返回`null`。該屬性只讀。

```javascript
document.styleSheets[0].href
```

**（3）StyleSheet.media**

`StyleSheet.media`屬性返回一個類似陣列的物件（`MediaList`例項），成員是表示適用媒介的字串。表示當前樣式表是用於螢幕（screen），還是用於列印（print）或手持裝置（handheld），或各種媒介都適用（all）。該屬性只讀，預設值是`screen`。

```javascript
document.styleSheets[0].media.mediaText
// "all"
```

`MediaList`例項的`appendMedium`方法，用於增加媒介；`deleteMedium`方法用於刪除媒介。

```javascript
document.styleSheets[0].media.appendMedium('handheld');
document.styleSheets[0].media.deleteMedium('print');
```

**（4）StyleSheet.title**

`StyleSheet.title`屬性返回樣式表的`title`屬性。

**（5）StyleSheet.type**

`StyleSheet.type`屬性返回樣式表的`type`屬性，通常是`text/css`。

```javascript
document.styleSheets[0].type  // "text/css"
```

**（6）StyleSheet.parentStyleSheet**

CSS 的`@import`命令允許在樣式表中載入其他樣式表。`StyleSheet.parentStyleSheet`屬性返回包含了當前樣式表的那張樣式表。如果當前樣式表是頂層樣式表，則該屬性返回`null`。

```javascript
if (stylesheet.parentStyleSheet) {
  sheet = stylesheet.parentStyleSheet;
} else {
  sheet = stylesheet;
}
```

**（7）StyleSheet.ownerNode**

`StyleSheet.ownerNode`屬性返回`StyleSheet`物件所在的 DOM 節點，通常是`<link>`或`<style>`。對於那些由其他樣式表引用的樣式表，該屬性為`null`。

```javascript
// HTML程式碼為
// <link rel="StyleSheet" href="example.css" type="text/css" />
document.styleSheets[0].ownerNode // [object HTMLLinkElement]
```

**（8）CSSStyleSheet.cssRules**

`CSSStyleSheet.cssRules`屬性指向一個類似陣列的物件（`CSSRuleList`例項），裡面每一個成員就是當前樣式表的一條 CSS 規則。使用該規則的`cssText`屬性，可以得到 CSS 規則對應的字串。

```javascript
var sheet = document.querySelector('#styleElement').sheet;

sheet.cssRules[0].cssText
// "body { background-color: red; margin: 20px; }"

sheet.cssRules[1].cssText
// "p { line-height: 1.4em; color: blue; }"
```

每條 CSS 規則還有一個`style`屬性，指向一個物件，用來讀寫具體的 CSS 命令。

```javascript
cssStyleSheet.cssRules[0].style.color = 'red';
cssStyleSheet.cssRules[1].style.color = 'purple';
```

**（9）CSSStyleSheet.ownerRule**

有些樣式表是透過`@import`規則輸入的，它的`ownerRule`屬性會返回一個`CSSRule`例項，代表那行`@import`規則。如果當前樣式表不是透過`@import`引入的，`ownerRule`屬性返回`null`。

### 例項方法

**（1）CSSStyleSheet.insertRule()**

`CSSStyleSheet.insertRule`方法用於在當前樣式表的插入一個新的 CSS 規則。

```javascript
var sheet = document.querySelector('#styleElement').sheet;
sheet.insertRule('#block { color: white }', 0);
sheet.insertRule('p { color: red }', 1);
```

該方法可以接受兩個引數，第一個引數是表示 CSS 規則的字串，這裡只能有一條規則，否則會報錯。第二個引數是該規則在樣式表的插入位置（從0開始），該引數可選，預設為0（即預設插在樣式表的頭部）。注意，如果插入位置大於現有規則的數目，會報錯。

該方法的返回值是新插入規則的位置序號。

注意，瀏覽器對指令碼在樣式表裡面插入規則有很多[限制](https://drafts.csswg.org/cssom/#insert-a-css-rule)。所以，這個方法最好放在`try...catch`裡使用。

**（2）CSSStyleSheet.deleteRule()**

`CSSStyleSheet.deleteRule`方法用來在樣式表裡面移除一條規則，它的引數是該條規則在`cssRules`物件中的位置。該方法沒有返回值。

```javascript
document.styleSheets[0].deleteRule(1);
```

## 例項：新增樣式表

網頁新增樣式表有兩種方式。一種是新增一張內建樣式表，即在文件中新增一個`<style>`節點。

```javascript
// 寫法一
var style = document.createElement('style');
style.setAttribute('media', 'screen');
style.innerHTML = 'body{color:red}';
document.head.appendChild(style);

// 寫法二
var style = (function () {
  var style = document.createElement('style');
  document.head.appendChild(style);
  return style;
})();
style.sheet.insertRule('.foo{color:red;}', 0);
```

另一種是新增外部樣式表，即在文件中新增一個`<link>`節點，然後將`href`屬性指向外部樣式表的 URL。

```javascript
var linkElm = document.createElement('link');
linkElm.setAttribute('rel', 'stylesheet');
linkElm.setAttribute('type', 'text/css');
linkElm.setAttribute('href', 'reset-min.css');

document.head.appendChild(linkElm);
```

## CSSRuleList 介面

CSSRuleList 介面是一個類似陣列的物件，表示一組 CSS 規則，成員都是 CSSRule 例項。

獲取 CSSRuleList 例項，一般是透過`StyleSheet.cssRules`屬性。

```javascript
// HTML 程式碼如下
// <style id="myStyle">
//   h1 { color: red; }
//   p { color: blue; }
// </style>
var myStyleSheet = document.getElementById('myStyle').sheet;
var crl = myStyleSheet.cssRules;
crl instanceof CSSRuleList // true
```

CSSRuleList 例項裡面，每一條規則（CSSRule 例項）可以透過`rules.item(index)`或者`rules[index]`拿到。CSS 規則的條數透過`rules.length`拿到。還是用上面的例子。

```javascript
crl[0] instanceof CSSRule // true
crl.length // 2
```

注意，新增規則和刪除規則不能在 CSSRuleList 例項操作，而要在它的父元素 StyleSheet 例項上，透過`StyleSheet.insertRule()`和`StyleSheet.deleteRule()`操作。

## CSSRule 介面

### 概述

一條 CSS 規則包括兩個部分：CSS 選擇器和樣式宣告。下面就是一條典型的 CSS 規則。

```css
.myClass {
  color: red;
  background-color: yellow;
}
```

JavaScript 透過 CSSRule 介面操作 CSS 規則。一般透過 CSSRuleList 介面（`StyleSheet.cssRules`）獲取 CSSRule 例項。

```javascript
// HTML 程式碼如下
// <style id="myStyle">
//   .myClass {
//     color: red;
//     background-color: yellow;
//   }
// </style>
var myStyleSheet = document.getElementById('myStyle').sheet;
var ruleList = myStyleSheet.cssRules;
var rule = ruleList[0];
rule instanceof CSSRule // true
```

### CSSRule 例項的屬性

**（1）CSSRule.cssText**

`CSSRule.cssText`屬性返回當前規則的文字，還是使用上面的例子。

```javascript
rule.cssText
// ".myClass { color: red; background-color: yellow; }"
```

如果規則是載入（`@import`）其他樣式表，`cssText`屬性返回`@import 'url'`。

**（2）CSSRule.parentStyleSheet**

`CSSRule.parentStyleSheet`屬性返回當前規則所在的樣式表物件（StyleSheet 例項），還是使用上面的例子。

```javascript
rule.parentStyleSheet === myStyleSheet // true
```

**（3）CSSRule.parentRule**

`CSSRule.parentRule`屬性返回包含當前規則的父規則，如果不存在父規則（即當前規則是頂層規則），則返回`null`。

父規則最常見的情況是，當前規則包含在`@media`規則程式碼塊之中。

```javascript
// HTML 程式碼如下
// <style id="myStyle">
//   @supports (display: flex) {
//     @media screen and (min-width: 900px) {
//       article {
//         display: flex;
//       }
//     }
//  }
// </style>
var myStyleSheet = document.getElementById('myStyle').sheet;
var ruleList = myStyleSheet.cssRules;

var rule0 = ruleList[0];
rule0.cssText
// "@supports (display: flex) {
//    @media screen and (min-width: 900px) {
//      article { display: flex; }
//    }
// }"

// 由於這條規則內嵌其他規則，
// 所以它有 cssRules 屬性，且該屬性是 CSSRuleList 例項
rule0.cssRules instanceof CSSRuleList // true

var rule1 = rule0.cssRules[0];
rule1.cssText
// "@media screen and (min-width: 900px) {
//   article { display: flex; }
// }"

var rule2 = rule1.cssRules[0];
rule2.cssText
// "article { display: flex; }"

rule1.parentRule === rule0 // true
rule2.parentRule === rule1 // true
```

**（4）CSSRule.type**

`CSSRule.type`屬性返回一個整數值，表示當前規則的型別。

最常見的型別有以下幾種。

- 1：普通樣式規則（CSSStyleRule 例項）
- 3：`@import`規則
- 4：`@media`規則（CSSMediaRule 例項）
- 5：`@font-face`規則

### CSSStyleRule 介面

如果一條 CSS 規則是普通的樣式規則（不含特殊的 CSS 命令），那麼除了 CSSRule 介面，它還部署了 CSSStyleRule 介面。

CSSStyleRule 介面有以下兩個屬性。

**（1）CSSStyleRule.selectorText**

`CSSStyleRule.selectorText`屬性返回當前規則的選擇器。

```javascript
var stylesheet = document.styleSheets[0];
stylesheet.cssRules[0].selectorText // ".myClass"
```

注意，這個屬性是可寫的。

**（2）CSSStyleRule.style**

`CSSStyleRule.style`屬性返回一個物件（CSSStyleDeclaration 例項），代表當前規則的樣式宣告，也就是選擇器後面的大括號裡面的部分。

```javascript
// HTML 程式碼為
// <style id="myStyle">
//   p { color: red; }
// </style>
var styleSheet = document.getElementById('myStyle').sheet;
styleSheet.cssRules[0].style instanceof CSSStyleDeclaration
// true
```

CSSStyleDeclaration 例項的`cssText`屬性，可以返回所有樣式宣告，格式為字串。

```javascript
styleSheet.cssRules[0].style.cssText
// "color: red;"
styleSheet.cssRules[0].selectorText
// "p"
```

### CSSMediaRule 介面

如果一條 CSS 規則是`@media`程式碼塊，那麼它除了 CSSRule 介面，還部署了 CSSMediaRule 介面。

該介面主要提供`media`屬性和`conditionText`屬性。前者返回代表`@media`規則的一個物件（MediaList 例項），後者返回`@media`規則的生效條件。

```javascript
// HTML 程式碼如下
// <style id="myStyle">
//   @media screen and (min-width: 900px) {
//     article { display: flex; }
//   }
// </style>
var styleSheet = document.getElementById('myStyle').sheet;
styleSheet.cssRules[0] instanceof CSSMediaRule
// true

styleSheet.cssRules[0].media
//  {
//    0: "screen and (min-width: 900px)",
//    appendMedium: function,
//    deleteMedium: function,
//    item: function,
//    length: 1,
//    mediaText: "screen and (min-width: 900px)"
// }

styleSheet.cssRules[0].conditionText
// "screen and (min-width: 900px)"
```

## window.matchMedia()

### 基本用法

`window.matchMedia`方法用來將 CSS 的[`MediaQuery`](https://developer.mozilla.org/en-US/docs/DOM/Using_media_queries_from_code)條件語句，轉換成一個 MediaQueryList 例項。

```javascript
var mdl = window.matchMedia('(min-width: 400px)');
mdl instanceof MediaQueryList // true
```

上面程式碼中，變數`mdl`就是 mediaQueryList 的例項。

注意，如果引數不是有效的`MediaQuery`條件語句，`window.matchMedia`不會報錯，依然返回一個 MediaQueryList 例項。

```javascript
window.matchMedia('bad string') instanceof MediaQueryList // true
```

### MediaQueryList 介面的例項屬性

MediaQueryList 例項有三個屬性。

**（1）MediaQueryList.media**

`MediaQueryList.media`屬性返回一個字串，表示對應的 MediaQuery 條件語句。

```javascript
var mql = window.matchMedia('(min-width: 400px)');
mql.media // "(min-width: 400px)"
```

**（2）MediaQueryList.matches**

`MediaQueryList.matches`屬性返回一個布林值，表示當前頁面是否符合指定的 MediaQuery 條件語句。

```javascript
if (window.matchMedia('(min-width: 400px)').matches) {
  /* 當前視口不小於 400 畫素 */
} else {
  /* 當前視口小於 400 畫素 */
}
```

下面的例子根據`mediaQuery`是否匹配當前環境，載入相應的 CSS 樣式表。

```javascript
var result = window.matchMedia("(max-width: 700px)");

if (result.matches){
  var linkElm = document.createElement('link');
  linkElm.setAttribute('rel', 'stylesheet');
  linkElm.setAttribute('type', 'text/css');
  linkElm.setAttribute('href', 'small.css');

  document.head.appendChild(linkElm);
}
```

**（3）MediaQueryList.onchange**

如果 MediaQuery 條件語句的適配環境發生變化，會觸發`change`事件。`MediaQueryList.onchange`屬性用來指定`change`事件的監聽函式。該函式的引數是`change`事件物件（MediaQueryListEvent 例項），該物件與 MediaQueryList 例項類似，也有`media`和`matches`屬性。

```javascript
var mql = window.matchMedia('(max-width: 600px)');

mql.onchange = function(e) {
  if (e.matches) {
    /* 視口不超過 600 畫素 */
  } else {
    /* 視口超過 600 畫素 */
  }
}
```

上面程式碼中，`change`事件發生後，存在兩種可能。一種是顯示寬度從600畫素以上變為以下，另一種是從600畫素以下變為以上，所以在監聽函式內部要判斷一下當前是哪一種情況。

### MediaQueryList 介面的例項方法

MediaQueryList 例項有兩個方法`MediaQueryList.addListener()`和`MediaQueryList.removeListener()`，用來為`change`事件新增或撤銷監聽函式。

```javascript
var mql = window.matchMedia('(max-width: 600px)');

// 指定監聽函式
mql.addListener(mqCallback);

// 撤銷監聽函式
mql.removeListener(mqCallback);

function mqCallback(e) {
  if (e.matches) {
    /* 視口不超過 600 畫素 */
  } else {
    /* 視口超過 600 畫素 */
  }
}
```

注意，`MediaQueryList.removeListener()`方法不能撤銷`MediaQueryList.onchange`屬性指定的監聽函式。

