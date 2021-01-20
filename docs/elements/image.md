# <img> 元素

## 概述

`<img>`元素用於插入圖片，主要繼承了 HTMLImageElement 介面。

瀏覽器提供一個原生建構函式`Image`，用於生成`HTMLImageElement`例項。

```javascript
var img = new Image();
img instanceof Image // true
img instanceof HTMLImageElement // true
```

`Image`建構函式可以接受兩個整數作為引數，分別表示`<img>`元素的寬度和高度。

```javascript
// 語法
Image(width, height)

// 用法
var myImage = new Image(100, 200);
```

`<img>`例項的`src`屬性可以定義影象的網址。

```javascript
var img = new Image();
img.src = 'picture.jpg';
```

新生成的`<img>`例項並不屬於文件的一部分。如果想讓它顯示在文件中，必須手動插入文件。

```javascript
var img = new Image();
img.src = 'image1.png';
document.body.appendChild(img);
```

除了使用`Image`構造，下面的方法也可以得到`HTMLImageElement`例項。

- `document.images`的成員
- 節點選取方法（比如`document.getElementById`）得到的`<img>`節點
- `document.createElement('img')`生成的`<img>`節點

```javascript
document.images[0] instanceof HTMLImageElement
// true

var img = document.getElementById('myImg');
img instanceof HTMLImageElement
// true

var img = document.createElement('img');
img instanceof HTMLImageElement
// true
```

`HTMLImageElement`例項除了具有 Node、Element、HTMLElement 介面以外，還擁有一些獨有的屬性。這個介面沒有定義自己的方法。

## 特性相關的屬性

**（1）HTMLImageElement.src**

`HTMLImageElement.src`屬性返回影象的完整網址。

```javascript
// HTML 程式碼如下
// <img width="300" height="400" id="myImg" src="http://example.com/pic.jpg">
var img = document.getElementById('img');
img.src // http://example.com/pic.jpg
```

**（2）HTMLImageElement.currentSrc**

`HTMLImageElement.currentSrc`屬性返回當前正在展示的影象的網址。JavaScript 和 CSS 的 mediaQuery 都可能改變正在展示的影象。

**（3）HTMLImageElement.alt**

`HTMLImageElement.alt`屬性可以讀寫`<img>`的 HTML 屬性`alt`，表示對圖片的文字說明。

**（4）HTMLImageElement.isMap，HTMLImageElement.useMap**

`HTMLImageElement.isMap`屬性對應`<img>`元素的 HTML 屬性`ismap`，返回一個布林值，表示影象是否為伺服器端的影象對映的一部分。

`HTMLImageElement.useMap`屬性對應`<img>`元素的 HTML 屬性`usemap`，表示當前影象對應的`<map>`元素。

**（5）HTMLImageElement.srcset，HTMLImageElement.sizes**

`HTMLImageElement.srcset`屬性和`HTMLImageElement.sizes`屬性，分別用於讀寫`<img>`元素的`srcset`屬性和`sizes`屬性。它們用於`<img>`元素的響應式載入。`srcset`屬性可以單獨使用，但是`sizes`屬性必須與`srcset`屬性同時使用。

```javascript
// HTML 程式碼如下
// <img srcset="example-320w.jpg 320w,
//              example-480w.jpg 480w,
//              example-800w.jpg 800w"
//      sizes="(max-width: 320px) 280px,
//             (max-width: 480px) 440px,
//             800px"
//      id="myImg"
//      src="example-800w.jpg">
var img = document.getElementById('myImg');
img.srcset
// "example-320w.jpg 320w,
//  example-480w.jpg 480w,
//  example-800w.jpg 800w"

img.sizes
// "(max-width: 320px) 280px,
//  (max-width: 480px) 440px,
//  800px"
```

上面程式碼中，`sizes`屬性指定，對於小於`320px`的螢幕，影象的寬度為`280px`；對於小於`480px`的螢幕，影象寬度為`440px`；其他情況下，影象寬度為`800px`。然後，瀏覽器會根據當前螢幕下的影象寬度，到`srcset`屬性載入寬度最接近的影象。

## HTMLImageElement.width，HTMLImageElement.height

`width`屬性表示`<img>`的 HTML 寬度，`height`屬性表示高度。這兩個屬性返回的都是整數。

```javascript
// HTML 程式碼如下
// <img width="300" height="400" id="myImg" src="pic.jpg">
var img = document.getElementById('img');
img.width // 300
img.height // 400
```

如果影象還沒有載入，這兩個屬性返回的都是`0`。

如果 HTML 程式碼沒有設定`width`和`height`屬性，則它們返回的是影象的實際寬度和高度，即`HTMLImageElement.naturalWidth`屬性和`HTMLImageElement.naturalHeight`屬性。

## HTMLImageElement.naturalWidth，HTMLImageElement.naturalHeight

`HTMLImageElement.naturalWidth`屬性表示影象的實際寬度（單位畫素），`HTMLImageElement.naturalHeight`屬性表示實際高度。這兩個屬性返回的都是整數。

如果影象還沒有指定或不可得，這兩個屬性都等於`0`。

```javascript
var img = document.getElementById('img');
if (img.naturalHeight > img.naturalWidth) {
  img.classList.add('portrait');
}
```

上面程式碼中，如果圖片的高度大於寬度，則設為`portrait`模式。

## HTMLImageElement.complete

`HTMLImageElement.complete`屬性返回一個布林值，表示圖表是否已經載入完成。如果`<img>`元素沒有`src`屬性，也會返回`true`。

## HTMLImageElement.crossOrigin

`HTMLImageElement.crossOrigin`屬性用於讀寫`<img>`元素的`crossorigin`屬性，表示跨域設定。

這個屬性有兩個可能的值。

- `anonymous`：跨域請求不要求使用者身份（credentials），這是預設值。
- `use-credentials`：跨域請求要求使用者身份。

```javascript
// HTML 程式碼如下
// <img crossorigin="anonymous" id="myImg" src="pic.jpg">
var img = document.getElementById('img');
img.crossOrigin // "anonymous"
```

## HTMLImageElement.referrerPolicy

`HTMLImageElement.referrerPolicy`用來讀寫`<img>`元素的 HTML 屬性`referrerpolicy`，表示請求影象資源時，如何處理 HTTP 請求的`referrer`欄位。

它有五個可能的值。

- `no-referrer`：不帶有`referrer`欄位。
- `no-referrer-when-downgrade`：如果請求的地址不是 HTTPS 協議，就不帶有`referrer`欄位，這是預設值。
- `origin`：`referrer`欄位是當前網頁的地址，包含協議、域名和埠。
- `origin-when-cross-origin`：如果請求的地址與當前網頁是同源關係，那麼`referrer`欄位將帶有完整路徑，否則將只包含協議、域名和埠。
- `unsafe-url`：`referrer`欄位包含當前網頁的地址，除了協議、域名和埠以外，還包括路徑。這個設定是不安全的，因為會洩漏路徑資訊。

## HTMLImageElement.x，HTMLImageElement.y

`HTMLImageElement.x`屬性返回影象左上角相對於頁面左上角的橫座標，`HTMLImageElement.y`屬性返回縱座標。

## 事件屬性

影象載入完成，會觸發`onload`屬性指定的回撥函式。

```javascript
// HTML 程式碼為 <img src="example.jpg" onload="loadImage()">
function loadImage() {
  console.log('Image is loaded');
}
```

影象載入過程中發生錯誤，會觸發`onerror`屬性指定的回撥函式。

```javascript
// HTML 程式碼為 <img src="image.gif" onerror="myFunction()">
function myFunction() {
  console.log('There is something wrong');
}
```
