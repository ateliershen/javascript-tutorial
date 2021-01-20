# window 物件

## 概述

瀏覽器裡面，`window`物件（注意，`w`為小寫）指當前的瀏覽器視窗。它也是當前頁面的頂層物件，即最高一層的物件，所有其他物件都是它的下屬。一個變數如果未宣告，那麼預設就是頂層物件的屬性。

```javascript
a = 1;
window.a // 1
```

上面程式碼中，`a`是一個沒有宣告就直接賦值的變數，它自動成為頂層物件的屬性。

`window`有自己的實體含義，其實不適合當作最高一層的頂層物件，這是一個語言的設計失誤。最早，設計這門語言的時候，原始設想是語言內建的物件越少越好，這樣可以提高瀏覽器的效能。因此，語言設計者 Brendan Eich 就把`window`物件當作頂層物件，所有未宣告就賦值的變數都自動變成`window`物件的屬性。這種設計使得編譯階段無法檢測出未宣告變數，但到了今天已經沒有辦法糾正了。

## window 物件的屬性

### window.name

`window.name`屬性是一個字串，表示當前瀏覽器視窗的名字。視窗不一定需要名字，這個屬性主要配合超連結和表單的`target`屬性使用。

```javascript
window.name = 'Hello World!';
console.log(window.name)
// "Hello World!"
```

該屬性只能儲存字串，如果寫入的值不是字串，會自動轉成字串。各個瀏覽器對這個值的儲存容量有所不同，但是一般來說，可以高達幾MB。

只要瀏覽器視窗不關閉，這個屬性是不會消失的。舉例來說，訪問`a.com`時，該頁面的指令碼設定了`window.name`，接下來在同一個窗口裡面載入了`b.com`，新頁面的指令碼可以讀到上一個網頁設定的`window.name`。頁面重新整理也是這種情況。一旦瀏覽器視窗關閉後，該屬性儲存的值就會消失，因為這時視窗已經不存在了。

### window.closed，window.opener

`window.closed`屬性返回一個布林值，表示視窗是否關閉。

```javascript
window.closed // false
```

上面程式碼檢查當前視窗是否關閉。這種檢查意義不大，因為只要能執行程式碼，當前視窗肯定沒有關閉。這個屬性一般用來檢查，使用指令碼開啟的新視窗是否關閉。

```javascript
var popup = window.open();

if ((popup !== null) && !popup.closed) {
  // 視窗仍然開啟著
}
```

`window.opener`屬性表示開啟當前視窗的父視窗。如果當前視窗沒有父視窗（即直接在位址列輸入開啟），則返回`null`。

```javascript
window.open().opener === window // true
```

上面表示式會開啟一個新視窗，然後返回`true`。

如果兩個視窗之間不需要通訊，建議將子視窗的`opener`屬性顯式設為`null`，這樣可以減少一些安全隱患。

```javascript
var newWin = window.open('example.html', 'newWindow', 'height=400,width=400');
newWin.opener = null;
```

上面程式碼中，子視窗的`opener`屬性設為`null`，兩個視窗之間就沒辦法再聯絡了。

透過`opener`屬性，可以獲得父視窗的全域性屬性和方法，但只限於兩個視窗同源的情況（參見《同源限制》一章），且其中一個視窗由另一個開啟。`<a>`元素新增`rel="noopener"`屬性，可以防止新開啟的視窗獲取父視窗，減輕被惡意網站修改父視窗 URL 的風險。

```html
<a href="https://an.evil.site" target="_blank" rel="noopener">
惡意網站
</a>
```

### window.self，window.window

`window.self`和`window.window`屬性都指向視窗本身。這兩個屬性只讀。

```javascript
window.self === window // true
window.window === window // true
```

### window.frames，window.length

`window.frames`屬性返回一個類似陣列的物件，成員為頁面內所有框架視窗，包括`frame`元素和`iframe`元素。`window.frames[0]`表示頁面中第一個框架視窗。

如果`iframe`元素設定了`id`或`name`屬性，那麼就可以用屬性值，引用這個`iframe`視窗。比如`<iframe name="myIFrame">`可以用`frames['myIFrame']`或者`frames.myIFrame`來引用。

`frames`屬性實際上是`window`物件的別名。

```javascript
frames === window // true
```

因此，`frames[0]`也可以用`window[0]`表示。但是，從語義上看，`frames`更清晰，而且考慮到`window`還是全域性物件，因此推薦表示多視窗時，總是使用`frames[0]`的寫法。更多介紹請看下文的《多視窗操作》部分。

`window.length`屬性返回當前網頁包含的框架總數。如果當前網頁不包含`frame`和`iframe`元素，那麼`window.length`就返回`0`。

```javascript
window.frames.length === window.length // true
```

上面程式碼表示，`window.frames.length`與`window.length`應該是相等的。

### window.frameElement

`window.frameElement`屬性主要用於當前視窗嵌在另一個網頁的情況（嵌入`<object>`、`<iframe>`或`<embed>`元素），返回當前視窗所在的那個元素節點。如果當前視窗是頂層視窗，或者所嵌入的那個網頁不是同源的，該屬性返回`null`。

```javascript
// HTML 程式碼如下
// <iframe src="about.html"></iframe>

// 下面的指令碼在 about.html 裡面
var frameEl = window.frameElement;
if (frameEl) {
  frameEl.src = 'other.html';
}
```

上面程式碼中，`frameEl`變數就是`<iframe>`元素。

### window.top，window.parent

`window.top`屬性指向最頂層視窗，主要用於在框架視窗（frame）裡面獲取頂層視窗。

`window.parent`屬性指向父視窗。如果當前視窗沒有父視窗，`window.parent`指向自身。

```javascript
if (window.parent !== window.top) {
  // 表明當前視窗嵌入不止一層
}
```

對於不包含框架的網頁，這兩個屬性等同於`window`物件。

### window.status

`window.status`屬性用於讀寫瀏覽器狀態列的文字。但是，現在很多瀏覽器都不允許改寫狀態列文字，所以使用這個方法不一定有效。

### window.devicePixelRatio

`window.devicePixelRatio`屬性返回一個數值，表示一個 CSS 畫素的大小與一個物理畫素的大小之間的比率。也就是說，它表示一個 CSS 畫素由多少個物理畫素組成。它可以用於判斷使用者的顯示環境，如果這個比率較大，就表示使用者正在使用高畫質螢幕，因此可以顯示較大畫素的圖片。

### 位置大小屬性

以下屬性返回`window`物件的位置資訊和大小資訊。

**（1）window.screenX，window.screenY**

`window.screenX`和`window.screenY`屬性，返回瀏覽器視窗左上角相對於當前螢幕左上角的水平距離和垂直距離（單位畫素）。這兩個屬性只讀。

**（2） window.innerHeight，window.innerWidth**

`window.innerHeight`和`window.innerWidth`屬性，返回網頁在當前視窗中可見部分的高度和寬度，即“視口”（viewport）的大小（單位畫素）。這兩個屬性只讀。

使用者放大網頁的時候（比如將網頁從100%的大小放大為200%），這兩個屬性會變小。因為這時網頁的畫素大小不變（比如寬度還是960畫素），只是每個畫素佔據的螢幕空間變大了，因此可見部分（視口）就變小了。

注意，這兩個屬性值包括捲軸的高度和寬度。

**（3）window.outerHeight，window.outerWidth**

`window.outerHeight`和`window.outerWidth`屬性返回瀏覽器視窗的高度和寬度，包括瀏覽器選單和邊框（單位畫素）。這兩個屬性只讀。

**（4）window.scrollX，window.scrollY**

`window.scrollX`屬性返回頁面的水平滾動距離，`window.scrollY`屬性返回頁面的垂直滾動距離，單位都為畫素。這兩個屬性只讀。

注意，這兩個屬性的返回值不是整數，而是雙精度浮點數。如果頁面沒有滾動，它們的值就是`0`。

舉例來說，如果使用者向下拉動了垂直捲軸75畫素，那麼`window.scrollY`就是75左右。使用者水平向右拉動水平捲軸200畫素，`window.scrollX`就是200左右。

```javascript
if (window.scrollY < 75) {
  window.scroll(0, 75);
}
```

上面程式碼中，如果頁面向下滾動的距離小於75畫素，那麼頁面向下滾動75畫素。

**（5）window.pageXOffset，window.pageYOffset**

`window.pageXOffset`屬性和`window.pageYOffset`屬性，是`window.scrollX`和`window.scrollY`別名。

### 元件屬性

元件屬性返回瀏覽器的元件物件。這樣的屬性有下面幾個。

- `window.locationbar`：位址列物件
- `window.menubar`：選單欄物件
- `window.scrollbars`：視窗的捲軸物件
- `window.toolbar`：工具欄物件
- `window.statusbar`：狀態列物件
- `window.personalbar`：使用者安裝的個人工具欄物件

這些物件的`visible`屬性是一個布林值，表示這些元件是否可見。這些屬性只讀。

```javascript
window.locationbar.visible
window.menubar.visible
window.scrollbars.visible
window.toolbar.visible
window.statusbar.visible
window.personalbar.visible
```

### 全域性物件屬性

全域性物件屬性指向一些瀏覽器原生的全域性物件。

- `window.document`：指向`document`物件，詳見《document 物件》一章。注意，這個屬性有同源限制。只有來自同源的指令碼才能讀取這個屬性。
- `window.location`：指向`Location`物件，用於獲取當前視窗的 URL 資訊。它等同於`document.location`屬性，詳見《Location 物件》一章。
- `window.navigator`：指向`Navigator`物件，用於獲取環境資訊，詳見《Navigator 物件》一章。
- `window.history`：指向`History`物件，表示瀏覽器的瀏覽歷史，詳見《History 物件》一章。
- `window.localStorage`：指向本地儲存的 localStorage 資料，詳見《Storage 介面》一章。
- `window.sessionStorage`：指向本地儲存的 sessionStorage 資料，詳見《Storage 介面》一章。
- `window.console`：指向`console`物件，用於操作控制檯，詳見《console 物件》一章。
- `window.screen`：指向`Screen`物件，表示螢幕資訊，詳見《Screen 物件》一章。

### window.isSecureContext

`window.isSecureContext`屬性返回一個布林值，表示當前視窗是否處在加密環境。如果是 HTTPS 協議，就是`true`，否則就是`false`。

## window 物件的方法

### window.alert()，window.prompt()，window.confirm()

`window.alert()`、`window.prompt()`、`window.confirm()`都是瀏覽器與使用者互動的全域性方法。它們會彈出不同的對話方塊，要求使用者做出迴應。注意，這三個方法彈出的對話方塊，都是瀏覽器統一規定的式樣，無法定製。

**（1）window.alert()**

`window.alert()`方法彈出的對話方塊，只有一個“確定”按鈕，往往用來通知使用者某些資訊。

```javascript
window.alert('Hello World');
```

使用者只有點選“確定”按鈕，對話方塊才會消失。對話方塊彈出期間，瀏覽器視窗處於凍結狀態，如果不點“確定”按鈕，使用者什麼也幹不了。

`window.alert()`方法的引數只能是字串，沒法使用 CSS 樣式，但是可以用`\n`指定換行。

```javascript
alert('本條提示\n分成兩行');
```

**（2）window.prompt()**

`window.prompt()`方法彈出的對話方塊，提示文字的下方，還有一個輸入框，要求使用者輸入資訊，並有“確定”和“取消”兩個按鈕。它往往用來獲取使用者輸入的資料。

```javascript
var result = prompt('您的年齡？', 25)
```

上面程式碼會跳出一個對話方塊，文字提示為“您的年齡？”，要求使用者在對話方塊中輸入自己的年齡（預設顯示25）。使用者填入的值，會作為返回值存入變數`result`。

`window.prompt()`的返回值有兩種情況，可能是字串（有可能是空字串），也有可能是`null`。具體分成三種情況。

1. 使用者輸入資訊，並點選“確定”，則使用者輸入的資訊就是返回值。
2. 使用者沒有輸入資訊，直接點選“確定”，則輸入框的預設值就是返回值。
3. 使用者點選了“取消”（或者按了 ESC 按鈕），則返回值是`null`。

`window.prompt()`方法的第二個引數是可選的，但是最好總是提供第二個引數，作為輸入框的預設值。

**（3）window.confirm()**

`window.confirm()`方法彈出的對話方塊，除了提示資訊之外，只有“確定”和“取消”兩個按鈕，往往用來徵詢使用者是否同意。

```javascript
var result = confirm('你最近好嗎？');
```

上面程式碼彈出一個對話方塊，上面只有一行文字“你最近好嗎？”，使用者選擇點選“確定”或“取消”。

`confirm`方法返回一個布林值，如果使用者點選“確定”，返回`true`；如果使用者點選“取消”，則返回`false`。

```javascript
var okay = confirm('Please confirm this message.');
if (okay) {
  // 使用者按下“確定”
} else {
  // 使用者按下“取消”
}
```

`confirm`的一個用途是，使用者離開當前頁面時，彈出一個對話方塊，問使用者是否真的要離開。

```javascript
window.onunload = function () {
  return window.confirm('你確定要離開當面頁面嗎？');
}
```

這三個方法都具有堵塞效應，一旦彈出對話方塊，整個頁面就是暫停執行，等待使用者做出反應。

### window.open(), window.close()，window.stop()

**（1）window.open()**

`window.open`方法用於新建另一個瀏覽器視窗，類似於瀏覽器選單的新建視窗選項。它會返回新視窗的引用，如果無法新建視窗，則返回`null`。

```javascript
var popup = window.open('somefile.html');
```

上面程式碼會讓瀏覽器彈出一個新建視窗，網址是當前域名下的`somefile.html`。

`open`方法一共可以接受三個引數。

```javascript
window.open(url, windowName, [windowFeatures])
```

- `url`：字串，表示新視窗的網址。如果省略，預設網址就是`about:blank`。
- `windowName`：字串，表示新視窗的名字。如果該名字的視窗已經存在，則佔用該視窗，不再新建視窗。如果省略，就預設使用`_blank`，表示新建一個沒有名字的視窗。另外還有幾個預設值，`_self`表示當前視窗，`_top`表示頂層視窗，`_parent`表示上一層視窗。
- `windowFeatures`：字串，內容為逗號分隔的鍵值對（詳見下文），表示新視窗的引數，比如有沒有提示欄、工具條等等。如果省略，則預設開啟一個完整 UI 的新視窗。如果新建的是一個已經存在的視窗，則該引數不起作用，瀏覽器沿用以前視窗的引數。

下面是一個例子。

```javascript
var popup = window.open(
  'somepage.html',
  'DefinitionsWindows',
  'height=200,width=200,location=no,status=yes,resizable=yes,scrollbars=yes'
);
```

上面程式碼表示，開啟的新視窗高度和寬度都為200畫素，沒有位址列，但有狀態列和捲軸，允許使用者調整大小。

第三個引數可以設定如下屬性。

- left：新視窗距離螢幕最左邊的距離（單位畫素）。注意，新視窗必須是可見的，不能設定在螢幕以外的位置。
- top：新視窗距離螢幕最頂部的距離（單位畫素）。
- height：新視窗內容區域的高度（單位畫素），不得小於100。
- width：新視窗內容區域的寬度（單位畫素），不得小於100。
- outerHeight：整個瀏覽器視窗的高度（單位畫素），不得小於100。
- outerWidth：整個瀏覽器視窗的寬度（單位畫素），不得小於100。
- menubar：是否顯示選單欄。
- toolbar：是否顯示工具欄。
- location：是否顯示位址列。
- personalbar：是否顯示使用者自己安裝的工具欄。
- status：是否顯示狀態列。
- dependent：是否依賴父視窗。如果依賴，那麼父視窗最小化，該視窗也最小化；父視窗關閉，該視窗也關閉。
- minimizable：是否有最小化按鈕，前提是`dialog=yes`。
- noopener：新視窗將與父視窗切斷聯絡，即新視窗的`window.opener`屬性返回`null`，父視窗的`window.open()`方法也返回`null`。
- resizable：新視窗是否可以調節大小。
- scrollbars：是否允許新窗口出現捲軸。
- dialog：新視窗標題欄是否出現最大化、最小化、恢復原始大小的控制元件。
- titlebar：新視窗是否顯示標題欄。
- alwaysRaised：是否顯示在所有視窗的頂部。
- alwaysLowered：是否顯示在父視窗的底下。
- close：新視窗是否顯示關閉按鈕。

對於那些可以開啟和關閉的屬性，設為`yes`或`1`或不設任何值就表示開啟，比如`status=yes`、`status=1`、`status`都會得到同樣的結果。如果想設為關閉，不用寫`no`，而是直接省略這個屬性即可。也就是說，如果在第三個引數中設定了一部分屬性，其他沒有被設定的`yes/no`屬性都會被設成`no`，只有`titlebar`和關閉按鈕除外（它們的值預設為`yes`）。

上面這些屬性，屬性名與屬性值之間用等號連線，屬性與屬性之間用逗號分隔。

```javascript
'height=200,width=200,location=no,status=yes,resizable=yes,scrollbars=yes'
```

另外，`open()`方法的第二個引數雖然可以指定已經存在的視窗，但是不等於可以任意控制其他視窗。為了防止被不相干的視窗控制，瀏覽器只有在兩個視窗同源，或者目標視窗被當前網頁開啟的情況下，才允許`open`方法指向該視窗。

`window.open`方法返回新視窗的引用。

```javascript
var windowB = window.open('windowB.html', 'WindowB');
windowB.window.name // "WindowB"
```

注意，如果新視窗和父視窗不是同源的（即不在同一個域），它們彼此不能獲取對方視窗物件的內部屬性。

下面是另一個例子。

```javascript
var w = window.open();
console.log('已經開啟新視窗');
w.location = 'http://example.com';
```

上面程式碼先開啟一個新視窗，然後在該視窗彈出一個對話方塊，再將網址導向`example.com`。

由於`open`這個方法很容易被濫用，許多瀏覽器預設都不允許指令碼自動新建視窗。只允許在使用者點選連結或按鈕時，指令碼做出反應，彈出新視窗。因此，有必要檢查一下開啟新視窗是否成功。

```javascript
var popup = window.open();
if (popup === null) {
  // 新建視窗失敗
}
```

**（2）window.close()**

`window.close`方法用於關閉當前視窗，一般只用來關閉`window.open`方法新建的視窗。

```javascript
popup.close()
```

該方法只對頂層視窗有效，`iframe`框架之中的視窗使用該方法無效。

**（3）window.stop()**

`window.stop()`方法完全等同於單擊瀏覽器的停止按鈕，會停止載入影象、影片等正在或等待載入的物件。

```javascript
window.stop()
```

### window.moveTo()，window.moveBy()

`window.moveTo()`方法用於移動瀏覽器視窗到指定位置。它接受兩個引數，分別是視窗左上角距離螢幕左上角的水平距離和垂直距離，單位為畫素。

```javascript
window.moveTo(100, 200)
```

上面程式碼將視窗移動到螢幕`(100, 200)`的位置。

`window.moveBy()`方法將視窗移動到一個相對位置。它接受兩個引數，分別是視窗左上角向右移動的水平距離和向下移動的垂直距離，單位為畫素。

```javascript
window.moveBy(25, 50)
```

上面程式碼將視窗向右移動25畫素、向下移動50畫素。

為了防止有人濫用這兩個方法，隨意移動使用者的視窗，目前只有一種情況，瀏覽器允許用指令碼移動視窗：該視窗是用`window.open()`方法新建的，並且窗口裡只有它一個 Tab 頁。除此以外的情況，使用上面兩個方法都是無效的。

### window.resizeTo()，window.resizeBy()

`window.resizeTo()`方法用於縮放視窗到指定大小。

它接受兩個引數，第一個是縮放後的視窗寬度（`outerWidth`屬性，包含捲軸、標題欄等等），第二個是縮放後的視窗高度（`outerHeight`屬性）。

```javascript
window.resizeTo(
  window.screen.availWidth / 2,
  window.screen.availHeight / 2
)
```

上面程式碼將當前視窗縮放到，螢幕可用區域的一半寬度和高度。

`window.resizeBy()`方法用於縮放視窗。它與`window.resizeTo()`的區別是，它按照相對的量縮放，`window.resizeTo()`需要給出縮放後的絕對大小。

它接受兩個引數，第一個是水平縮放的量，第二個是垂直縮放的量，單位都是畫素。

```javascript
window.resizeBy(-200, -200)
```

上面的程式碼將當前視窗的寬度和高度，都縮小200畫素。

### window.scrollTo()，window.scroll()，window.scrollBy()

`window.scrollTo`方法用於將文件滾動到指定位置。它接受兩個引數，表示滾動後位於視窗左上角的頁面座標。

```javascript
window.scrollTo(x-coord, y-coord)
```

它也可以接受一個配置物件作為引數。

```javascript
window.scrollTo(options)
```

配置物件`options`有三個屬性。

- `top`：滾動後頁面左上角的垂直座標，即 y 座標。
- `left`：滾動後頁面左上角的水平座標，即 x 座標。
- `behavior`：字串，表示滾動的方式，有三個可能值（`smooth`、`instant`、`auto`），預設值為`auto`。

```javascript
window.scrollTo({
  top: 1000,
  behavior: 'smooth'
});
```

`window.scroll()`方法是`window.scrollTo()`方法的別名。

`window.scrollBy()`方法用於將網頁滾動指定距離（單位畫素）。它接受兩個引數：水平向右滾動的畫素，垂直向下滾動的畫素。

```javascript
window.scrollBy(0, window.innerHeight)
```

上面程式碼用於將網頁向下滾動一屏。

如果不是要滾動整個文件，而是要滾動某個元素，可以使用下面三個屬性和方法。

- Element.scrollTop
- Element.scrollLeft
- Element.scrollIntoView()

### window.print()

`window.print`方法會跳出列印對話方塊，與使用者點選選單裡面的“列印”命令效果相同。

常見的列印按鈕程式碼如下。

```javascript
document.getElementById('printLink').onclick = function () {
  window.print();
}
```

非桌面裝置（比如手機）可能沒有列印功能，這時可以這樣判斷。

```javascript
if (typeof window.print === 'function') {
  // 支援列印功能
}
```

### window.focus()，window.blur()

`window.focus()`方法會啟用視窗，使其獲得焦點，出現在其他視窗的前面。

```javascript
var popup = window.open('popup.html', 'Popup Window');

if ((popup !== null) && !popup.closed) {
  popup.focus();
}
```

上面程式碼先檢查`popup`視窗是否依然存在，確認後啟用該視窗。

`window.blur()`方法將焦點從視窗移除。

當前視窗獲得焦點時，會觸發`focus`事件；當前視窗失去焦點時，會觸發`blur`事件。

### window.getSelection()

`window.getSelection`方法返回一個`Selection`物件，表示使用者現在選中的文字。

```javascript
var selObj = window.getSelection();
```

使用`Selection`物件的`toString`方法可以得到選中的文字。

```javascript
var selectedText = selObj.toString();
```

### window.getComputedStyle()，window.matchMedia()

`window.getComputedStyle()`方法接受一個元素節點作為引數，返回一個包含該元素的最終樣式資訊的物件，詳見《CSS 操作》一章。

`window.matchMedia()`方法用來檢查 CSS 的`mediaQuery`語句，詳見《CSS 操作》一章。

### window.requestAnimationFrame()

`window.requestAnimationFrame()`方法跟`setTimeout`類似，都是推遲某個函式的執行。不同之處在於，`setTimeout`必須指定推遲的時間，`window.requestAnimationFrame()`則是推遲到瀏覽器下一次重流時執行，執行完才會進行下一次重繪。重繪通常是 16ms 執行一次，不過瀏覽器會自動調節這個速率，比如網頁切換到後臺 Tab 頁時，`requestAnimationFrame()`會暫停執行。

如果某個函式會改變網頁的佈局，一般就放在`window.requestAnimationFrame()`裡面執行，這樣可以節省系統資源，使得網頁效果更加平滑。因為慢速裝置會用較慢的速率重流和重繪，而速度更快的裝置會有更快的速率。

該方法接受一個回撥函式作為引數。

```javascript
window.requestAnimationFrame(callback)
```

上面程式碼中，`callback`是一個回撥函式。`callback`執行時，它的引數就是系統傳入的一個高精度時間戳（`performance.now()`的返回值），單位是毫秒，表示距離網頁載入的時間。

`window.requestAnimationFrame()`的返回值是一個整數，這個整數可以傳入`window.cancelAnimationFrame()`，用來取消回撥函式的執行。

下面是一個`window.requestAnimationFrame()`執行網頁動畫的例子。

```javascript
var element = document.getElementById('animate');
element.style.position = 'absolute';

var start = null;

function step(timestamp) {
  if (!start) start = timestamp;
  var progress = timestamp - start;
  // 元素不斷向左移，最大不超過200畫素
  element.style.left = Math.min(progress / 10, 200) + 'px';
  // 如果距離第一次執行不超過 2000 毫秒，
  // 就繼續執行動畫
  if (progress < 2000) {
    window.requestAnimationFrame(step);
  }
}

window.requestAnimationFrame(step);
```

上面程式碼定義了一個網頁動畫，持續時間是2秒，會讓元素向右移動。

### window.requestIdleCallback()

`window.requestIdleCallback()`跟`setTimeout`類似，也是將某個函式推遲執行，但是它保證將回調函式推遲到系統資源空閒時執行。也就是說，如果某個任務不是很關鍵，就可以使用`window.requestIdleCallback()`將其推遲執行，以保證網頁效能。

它跟`window.requestAnimationFrame()`的區別在於，後者指定回撥函式在下一次瀏覽器重排時執行，問題在於下一次重排時，系統資源未必空閒，不一定能保證在16毫秒之內完成；`window.requestIdleCallback()`可以保證回撥函式在系統資源空閒時執行。

該方法接受一個回撥函式和一個配置物件作為引數。配置物件可以指定一個推遲執行的最長時間，如果過了這個時間，回撥函式不管系統資源有無空閒，都會執行。

```javascript
window.requestIdleCallback(callback[, options])
```

`callback`引數是一個回撥函式。該回調函式執行時，系統會傳入一個`IdleDeadline`物件作為引數。`IdleDeadline`物件有一個`didTimeout`屬性（布林值，表示是否為超時呼叫）和一個`timeRemaining()`方法（返回該空閒時段剩餘的毫秒數）。

`options`引數是一個配置物件，目前只有`timeout`一個屬性，用來指定回撥函式推遲執行的最大毫秒數。該引數可選。

`window.requestIdleCallback()`方法返回一個整數。該整數可以傳入`window.cancelIdleCallback()`取消回撥函式。

下面是一個例子。

```javascript
requestIdleCallback(myNonEssentialWork);

function myNonEssentialWork(deadline) {
  while (deadline.timeRemaining() > 0) {
    doWorkIfNeeded();
  }
}
```

上面程式碼中，`requestIdleCallback()`用來執行非關鍵任務`myNonEssentialWork`。該任務先確認本次空閒時段有剩餘時間，然後才真正開始執行任務。

下面是指定`timeout`的例子。

```javascript
requestIdleCallback(processPendingAnalyticsEvents, { timeout: 2000 });
```

上面程式碼指定，`processPendingAnalyticsEvents`必須在未來2秒之內執行。

如果由於超時導致回撥函式執行，則`deadline.timeRemaining()`返回`0`，`deadline.didTimeout`返回`true`。

如果多次執行`window.requestIdleCallback()`，指定多個回撥函式，那麼這些回撥函式將排成一個佇列，按照先進先出的順序執行。

## 事件

`window`物件可以接收以下事件。

### load 事件和 onload 屬性

`load`事件發生在文件在瀏覽器視窗載入完畢時。`window.onload`屬性可以指定這個事件的回撥函式。

```javascript
window.onload = function() {
  var elements = document.getElementsByClassName('example');
  for (var i = 0; i < elements.length; i++) {
    var elt = elements[i];
    // ...
  }
};
```

上面程式碼在網頁載入完畢後，獲取指定元素並進行處理。

### error 事件和 onerror 屬性

瀏覽器指令碼發生錯誤時，會觸發`window`物件的`error`事件。我們可以透過`window.onerror`屬性對該事件指定回撥函式。

```javascript
window.onerror = function (message, filename, lineno, colno, error) {
  console.log("出錯了！--> %s", error.stack);
};
```

由於歷史原因，`window`的`error`事件的回撥函式不接受錯誤物件作為引數，而是一共可以接受五個引數，它們的含義依次如下。

- 出錯資訊
- 出錯指令碼的網址
- 行號
- 列號
- 錯誤物件

老式瀏覽器只支援前三個引數。

並不是所有的錯誤，都會觸發 JavaScript 的`error`事件（即讓 JavaScript 報錯）。一般來說，只有 JavaScript 指令碼的錯誤，才會觸發這個事件，而像資原始檔不存在之類的錯誤，都不會觸發。

下面是一個例子，如果整個頁面未捕獲錯誤超過3個，就顯示警告。

```javascript
window.onerror = function(msg, url, line) {
  if (onerror.num++ > onerror.max) {
    alert('ERROR: ' + msg + '\n' + url + ':' + line);
    return true;
  }
}
onerror.max = 3;
onerror.num = 0;
```

需要注意的是，如果指令碼網址與網頁網址不在同一個域（比如使用了 CDN），瀏覽器根本不會提供詳細的出錯資訊，只會提示出錯，錯誤型別是“Script error.”，行號為0，其他資訊都沒有。這是瀏覽器防止向外部指令碼洩漏資訊。一個解決方法是在指令碼所在的伺服器，設定`Access-Control-Allow-Origin`的 HTTP 頭資訊。

```bash
Access-Control-Allow-Origin: *
```

然後，在網頁的`<script>`標籤中設定`crossorigin`屬性。

```html
<script crossorigin="anonymous" src="//example.com/file.js"></script>
```

上面程式碼的`crossorigin="anonymous"`表示，讀取檔案不需要身份資訊，即不需要 cookie 和 HTTP 認證資訊。如果設為`crossorigin="use-credentials"`，就表示瀏覽器會上傳 cookie 和 HTTP 認證資訊，同時還需要伺服器端開啟 HTTP 頭資訊`Access-Control-Allow-Credentials`。

### window 物件的事件監聽屬性

除了具備元素節點都有的 GlobalEventHandlers 介面，`window`物件還具有以下的事件監聽函式屬性。

- `window.onafterprint`：`afterprint`事件的監聽函式。
- `window.onbeforeprint`：`beforeprint`事件的監聽函式。
- `window.onbeforeunload`：`beforeunload`事件的監聽函式。
- `window.onhashchange`：`hashchange`事件的監聽函式。
- `window.onlanguagechange`: `languagechange`的監聽函式。
- `window.onmessage`：`message`事件的監聽函式。
- `window.onmessageerror`：`MessageError`事件的監聽函式。
- `window.onoffline`：`offline`事件的監聽函式。
- `window.ononline`：`online`事件的監聽函式。
- `window.onpagehide`：`pagehide`事件的監聽函式。
- `window.onpageshow`：`pageshow`事件的監聽函式。
- `window.onpopstate`：`popstate`事件的監聽函式。
- `window.onstorage`：`storage`事件的監聽函式。
- `window.onunhandledrejection`：未處理的 Promise 物件的`reject`事件的監聽函式。
- `window.onunload`：`unload`事件的監聽函式。

## 多視窗操作

由於網頁可以使用`iframe`元素，嵌入其他網頁，因此一個網頁之中會形成多個視窗。如果子視窗之中又嵌入別的網頁，就會形成多級視窗。

### 視窗的引用

各個視窗之中的指令碼，可以引用其他視窗。瀏覽器提供了一些特殊變數，用來返回其他視窗。

- `top`：頂層視窗，即最上層的那個視窗
- `parent`：父視窗
- `self`：當前視窗，即自身

下面程式碼可以判斷，當前視窗是否為頂層視窗。

```javascript
if (window.top === window.self) {
  // 當前視窗是頂層視窗
} else {
  // 當前視窗是子視窗
}
```

下面的程式碼讓父視窗的訪問歷史後退一次。

```javascript
window.parent.history.back();
```

與這些變數對應，瀏覽器還提供一些特殊的視窗名，供`window.open()`方法、`<a>`標籤、`<form>`標籤等引用。

- `_top`：頂層視窗
- `_parent`：父視窗
- `_blank`：新視窗

下面程式碼就表示在頂層視窗開啟連結。

```html
<a href="somepage.html" target="_top">Link</a>
```

### iframe 元素

對於`iframe`嵌入的視窗，`document.getElementById`方法可以拿到該視窗的 DOM 節點，然後使用`contentWindow`屬性獲得`iframe`節點包含的`window`物件。

```javascript
var frame = document.getElementById('theFrame');
var frameWindow = frame.contentWindow;
```

上面程式碼中，`frame.contentWindow`可以拿到子視窗的`window`物件。然後，在滿足同源限制的情況下，可以讀取子視窗內部的屬性。

```javascript
// 獲取子視窗的標題
frameWindow.title
```

`<iframe>`元素的`contentDocument`屬性，可以拿到子視窗的`document`物件。

```javascript
var frame = document.getElementById('theFrame');
var frameDoc = frame.contentDocument;

// 等同於
var frameDoc = frame.contentWindow.document;
```

`<iframe>`元素遵守同源政策，只有當父視窗與子視窗在同一個域時，兩者之間才可以用指令碼通訊，否則只有使用`window.postMessage`方法。

`<iframe>`視窗內部，使用`window.parent`引用父視窗。如果當前頁面沒有父視窗，則`window.parent`屬性返回自身。因此，可以透過`window.parent`是否等於`window.self`，判斷當前視窗是否為`iframe`視窗。

```javascript
if (window.parent !== window.self) {
  // 當前視窗是子視窗
}
```

`<iframe>`視窗的`window`物件，有一個`frameElement`屬性，返回`<iframe>`在父視窗中的 DOM 節點。對於非嵌入的視窗，該屬性等於`null`。

```javascript
var f1Element = document.getElementById('f1');
var f1Window = f1Element.contentWindow;

f1Window.frameElement === f1Element // true
window.frameElement === null // true
```

### window.frames 屬性

`window.frames`屬性返回一個類似陣列的物件，成員是所有子視窗的`window`物件。可以使用這個屬性，實現視窗之間的互相引用。比如，`frames[0]`返回第一個子視窗，`frames[1].frames[2]`返回第二個子視窗內部的第三個子視窗，`parent.frames[1]`返回父視窗的第二個子視窗。

注意，`window.frames`每個成員的值，是框架內的視窗（即框架的`window`物件），而不是`iframe`標籤在父視窗的 DOM 節點。如果要獲取每個框架內部的 DOM 樹，需要使用`window.frames[0].document`的寫法。

另外，如果`<iframe>`元素設定了`name`或`id`屬性，那麼屬性值會自動成為全域性變數，並且可以透過`window.frames`屬性引用，返回子視窗的`window`物件。

```javascript
// HTML 程式碼為 <iframe id="myFrame">
window.myFrame // [HTMLIFrameElement]
frames.myframe === myFrame // true
```

另外，`name`屬性的值會自動成為子視窗的名稱，可以用在`window.open`方法的第二個引數，或者`<a>`和`<frame>`標籤的`target`屬性。
