# Mutation Observer API

## 概述

Mutation Observer API 用來監視 DOM 變動。DOM 的任何變動，比如節點的增減、屬性的變動、文字內容的變動，這個 API 都可以得到通知。

概念上，它很接近事件，可以理解為 DOM 發生變動就會觸發 Mutation Observer 事件。但是，它與事件有一個本質不同：事件是同步觸發，也就是說，DOM 的變動立刻會觸發相應的事件；Mutation Observer 則是非同步觸發，DOM 的變動並不會馬上觸發，而是要等到當前所有 DOM 操作都結束才觸發。

這樣設計是為了應付 DOM 變動頻繁的特點。舉例來說，如果文件中連續插入1000個`<p>`元素，就會連續觸發1000個插入事件，執行每個事件的回撥函式，這很可能造成瀏覽器的卡頓；而 Mutation Observer 完全不同，只在1000個段落都插入結束後才會觸發，而且只觸發一次。

Mutation Observer 有以下特點。

- 它等待所有指令碼任務完成後，才會執行（即非同步觸發方式）。
- 它把 DOM 變動記錄封裝成一個數組進行處理，而不是一條條個別處理 DOM 變動。
- 它既可以觀察 DOM 的所有型別變動，也可以指定只觀察某一類變動。

## MutationObserver 建構函式

使用時，首先使用`MutationObserver`建構函式，新建一個觀察器例項，同時指定這個例項的回撥函式。

```javascript
var observer = new MutationObserver(callback);
```

上面程式碼中的回撥函式，會在每次 DOM 變動後呼叫。該回調函式接受兩個引數，第一個是變動陣列，第二個是觀察器例項，下面是一個例子。

```javascript
var observer = new MutationObserver(function (mutations, observer) {
  mutations.forEach(function(mutation) {
    console.log(mutation);
  });
});
```

## MutationObserver 的例項方法

### observe()

`observe()`方法用來啟動監聽，它接受兩個引數。

- 第一個引數：所要觀察的 DOM 節點
- 第二個引數：一個配置物件，指定所要觀察的特定變動

```javascript
var article = document.querySelector('article');

var  options = {
  'childList': true,
  'attributes':true
} ;

observer.observe(article, options);
```

上面程式碼中，`observe()`方法接受兩個引數，第一個是所要觀察的DOM元素是`article`，第二個是所要觀察的變動型別（子節點變動和屬性變動）。

觀察器所能觀察的 DOM 變動型別（即上面程式碼的`options`物件），有以下幾種。

- **childList**：子節點的變動（指新增，刪除或者更改）。
- **attributes**：屬性的變動。
- **characterData**：節點內容或節點文字的變動。

想要觀察哪一種變動型別，就在`option`物件中指定它的值為`true`。需要注意的是，至少必須同時指定這三種觀察的一種，若均未指定將報錯。

除了變動型別，`options`物件還可以設定以下屬性：

- `subtree`：布林值，表示是否將該觀察器應用於該節點的所有後代節點。
- `attributeOldValue`：布林值，表示觀察`attributes`變動時，是否需要記錄變動前的屬性值。
- `characterDataOldValue`：布林值，表示觀察`characterData`變動時，是否需要記錄變動前的值。
- `attributeFilter`：陣列，表示需要觀察的特定屬性（比如`['class','src']`）。

```javascript
// 開始監聽文件根節點（即<html>標籤）的變動
mutationObserver.observe(document.documentElement, {
  attributes: true,
  characterData: true,
  childList: true,
  subtree: true,
  attributeOldValue: true,
  characterDataOldValue: true
});
```

對一個節點新增觀察器，就像使用`addEventListener()`方法一樣，多次新增同一個觀察器是無效的，回撥函式依然只會觸發一次。如果指定不同的`options`物件，以後面新增的那個為準，類似覆蓋。

下面的例子是觀察新增的子節點。

```javascript
var insertedNodes = [];
var observer = new MutationObserver(function(mutations) {
  mutations.forEach(function(mutation) {
    for (var i = 0; i < mutation.addedNodes.length; i++) {
      insertedNodes.push(mutation.addedNodes[i]);
    }
  });
  console.log(insertedNodes);
});
observer.observe(document, { childList: true, subtree: true });
```

### disconnect()，takeRecords（）

`disconnect()`方法用來停止觀察。呼叫該方法後，DOM 再發生變動，也不會觸發觀察器。

```javascript
observer.disconnect();
```

`takeRecords()`方法用來清除變動記錄，即不再處理未處理的變動。該方法返回變動記錄的陣列。

```javascript
observer.takeRecords();
```

下面是一個例子。

```javascript
// 儲存所有沒有被觀察器處理的變動
var changes = mutationObserver.takeRecords();

// 停止觀察
mutationObserver.disconnect();
```

## MutationRecord 物件

DOM 每次發生變化，就會生成一條變動記錄（MutationRecord 例項）。該例項包含了與變動相關的所有資訊。Mutation Observer 處理的就是一個個`MutationRecord`例項所組成的陣列。

`MutationRecord`物件包含了DOM的相關資訊，有如下屬性：

- `type`：觀察的變動型別（`attributes`、`characterData`或者`childList`）。
- `target`：發生變動的DOM節點。
- `addedNodes`：新增的DOM節點。
- `removedNodes`：刪除的DOM節點。
- `previousSibling`：前一個同級節點，如果沒有則返回`null`。
- `nextSibling`：下一個同級節點，如果沒有則返回`null`。
- `attributeName`：發生變動的屬性。如果設定了`attributeFilter`，則只返回預先指定的屬性。
- `oldValue`：變動前的值。這個屬性只對`attribute`和`characterData`變動有效，如果發生`childList`變動，則返回`null`。

## 應用示例

### 子元素的變動

下面的例子說明如何讀取變動記錄。

```javascript
var callback = function (records){
  records.map(function(record){
    console.log('Mutation type: ' + record.type);
    console.log('Mutation target: ' + record.target);
  });
};

var mo = new MutationObserver(callback);

var option = {
  'childList': true,
  'subtree': true
};

mo.observe(document.body, option);
```

上面程式碼的觀察器，觀察`<body>`的所有下級節點（`childList`表示觀察子節點，`subtree`表示觀察後代節點）的變動。回撥函式會在控制檯顯示所有變動的型別和目標節點。

### 屬性的變動

下面的例子說明如何追蹤屬性的變動。

```javascript
var callback = function (records) {
  records.map(function (record) {
    console.log('Previous attribute value: ' + record.oldValue);
  });
};

var mo = new MutationObserver(callback);

var element = document.getElementById('#my_element');

var options = {
  'attributes': true,
  'attributeOldValue': true
}

mo.observe(element, options);
```

上面程式碼先設定追蹤屬性變動（`'attributes': true`），然後設定記錄變動前的值。實際發生變動時，會將變動前的值顯示在控制檯。

### 取代 DOMContentLoaded 事件

網頁載入的時候，DOM 節點的生成會產生變動記錄，因此只要觀察 DOM 的變動，就能在第一時間觸發相關事件，也就沒有必要使用`DOMContentLoaded`事件。

```javascript
var observer = new MutationObserver(callback);
observer.observe(document.documentElement, {
  childList: true,
  subtree: true
});
```

上面程式碼中，監聽`document.documentElement`（即網頁的`<html>`HTML 節點）的子節點的變動，`subtree`屬性指定監聽還包括後代節點。因此，任意一個網頁元素一旦生成，就能立刻被監聽到。

下面的程式碼，使用`MutationObserver`物件封裝一個監聽 DOM 生成的函式。

```javascript
(function(win){
  'use strict';

  var listeners = [];
  var doc = win.document;
  var MutationObserver = win.MutationObserver || win.WebKitMutationObserver;
  var observer;

  function ready(selector, fn){
    // 儲存選擇器和回撥函式
    listeners.push({
      selector: selector,
      fn: fn
    });
    if(!observer){
      // 監聽document變化
      observer = new MutationObserver(check);
      observer.observe(doc.documentElement, {
        childList: true,
        subtree: true
      });
    }
    // 檢查該節點是否已經在DOM中
    check();
  }

  function check(){
  // 檢查是否匹配已儲存的節點
    for(var i = 0; i < listeners.length; i++){
      var listener = listeners[i];
      // 檢查指定節點是否有匹配
      var elements = doc.querySelectorAll(listener.selector);
      for(var j = 0; j < elements.length; j++){
        var element = elements[j];
        // 確保回撥函式只會對該元素呼叫一次
        if(!element.ready){
          element.ready = true;
          // 對該節點呼叫回撥函式
          listener.fn.call(element, element);
        }
      }
    }
  }

  // 對外暴露ready
  win.ready = ready;

})(this);

// 使用方法
ready('.foo', function(element){
  // ...
});
```

## 參考連結

- Paul Kinlan, [Detect DOM changes with Mutation Observers](https://developers.google.com/web/updates/2012/02/Detect-DOM-changes-with-Mutation-Observers)
- Tiffany Brown, [Getting to know mutation observers](http://dev.opera.com/articles/view/mutation-observers-tutorial/)
- Michal Budzynski, [JavaScript: The less known parts. DOM Mutations](http://michalbe.blogspot.com/2013/04/javascript-less-known-parts-dom.html)
- Jeff Griffiths, [DOM MutationObserver – reacting to DOM changes without killing browser performance](https://hacks.mozilla.org/2012/05/dom-mutationobserver-reacting-to-dom-changes-without-killing-browser-performance/)
- Addy Osmani, [Detect, Undo And Redo DOM Changes With Mutation Observers](http://addyosmani.com/blog/mutation-observers/)
- Ryan Morr, [Using Mutation Observers to Watch for Element Availability](http://ryanmorr.com/using-mutation-observers-to-watch-for-element-availability/)
