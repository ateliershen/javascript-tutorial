# Storage 介面

## 概述

Storage 介面用於指令碼在瀏覽器儲存資料。兩個物件部署了這個介面：`window.sessionStorage`和`window.localStorage`。

`sessionStorage`儲存的資料用於瀏覽器的一次會話（session），當會話結束（通常是視窗關閉），資料被清空；`localStorage`儲存的資料長期存在，下一次訪問該網站的時候，網頁可以直接讀取以前儲存的資料。除了儲存期限的長短不同，這兩個物件的其他方面都一致。

儲存的資料都以“鍵值對”的形式存在。也就是說，每一項資料都有一個鍵名和對應的值。所有的資料都是以文字格式儲存。

這個介面很像 Cookie 的強化版，能夠使用大得多的儲存空間。目前，每個域名的儲存上限視瀏覽器而定，Chrome 是 2.5MB，Firefox 和 Opera 是 5MB，IE 是 10MB。其中，Firefox 的儲存空間由一級域名決定，而其他瀏覽器沒有這個限制。也就是說，Firefox 中，`a.example.com`和`b.example.com`共享 5MB 的儲存空間。另外，與 Cookie 一樣，它們也受同域限制。某個網頁存入的資料，只有同域下的網頁才能讀取，如果跨域操作會報錯。

## 屬性和方法

Storage 介面只有一個屬性。

- `Storage.length`：返回儲存的資料項個數。

```javascript
window.localStorage.setItem('foo', 'a');
window.localStorage.setItem('bar', 'b');
window.localStorage.setItem('baz', 'c');

window.localStorage.length // 3
```

該介面提供5個方法。

### Storage.setItem()

`Storage.setItem()`方法用於存入資料。它接受兩個引數，第一個是鍵名，第二個是儲存的資料。如果鍵名已經存在，該方法會更新已有的鍵值。該方法沒有返回值。

```javascript
window.sessionStorage.setItem('key', 'value');
window.localStorage.setItem('key', 'value');
```

注意，`Storage.setItem()`兩個引數都是字串。如果不是字串，會自動轉成字串，再存入瀏覽器。

```javascript
window.sessionStorage.setItem(3, { foo: 1 });
window.sessionStorage.getItem('3') // "[object Object]"
```

上面程式碼中，`setItem`方法的兩個引數都不是字串，但是存入的值都是字串。

如果儲存空間已滿，該方法會拋錯。

寫入不一定要用這個方法，直接賦值也是可以的。

```javascript
// 下面三種寫法等價
window.localStorage.foo = '123';
window.localStorage['foo'] = '123';
window.localStorage.setItem('foo', '123');
```

### Storage.getItem()

`Storage.getItem()`方法用於讀取資料。它只有一個引數，就是鍵名。如果鍵名不存在，該方法返回`null`。

```javascript
window.sessionStorage.getItem('key')
window.localStorage.getItem('key')
```

鍵名應該是一個字串，否則會被自動轉為字串。

### Storage.removeItem()

`Storage.removeItem()`方法用於清除某個鍵名對應的鍵值。它接受鍵名作為引數，如果鍵名不存在，該方法不會做任何事情。

```javascript
sessionStorage.removeItem('key');
localStorage.removeItem('key');
```

### Storage.clear()

`Storage.clear()`方法用於清除所有儲存的資料。該方法的返回值是`undefined`。

```javascript
window.sessionStorage.clear()
window.localStorage.clear()
```

### Storage.key()

`Storage.key()`接受一個整數作為引數（從零開始），返回該位置對應的鍵值。

```javascript
window.sessionStorage.setItem('key', 'value');
window.sessionStorage.key(0) // "key"
```

結合使用`Storage.length`屬性和`Storage.key()`方法，可以遍歷所有的鍵。

```javascript
for (var i = 0; i < window.localStorage.length; i++) {
  console.log(localStorage.key(i));
}
```

## storage 事件

Storage 介面儲存的資料發生變化時，會觸發 storage 事件，可以指定這個事件的監聽函式。

```javascript
window.addEventListener('storage', onStorageChange);
```

監聽函式接受一個`event`例項物件作為引數。這個例項物件繼承了 StorageEvent 介面，有幾個特有的屬性，都是隻讀屬性。

- `StorageEvent.key`：字串，表示發生變動的鍵名。如果 storage 事件是由`clear()`方法引起，該屬性返回`null`。
- `StorageEvent.newValue`：字串，表示新的鍵值。如果 storage 事件是由`clear()`方法或刪除該鍵值對引發的，該屬性返回`null`。
- `StorageEvent.oldValue`：字串，表示舊的鍵值。如果該鍵值對是新增的，該屬性返回`null`。
- `StorageEvent.storageArea`：物件，返回鍵值對所在的整個物件。也說是說，可以從這個屬性上面拿到當前域名儲存的所有鍵值對。
- `StorageEvent.url`：字串，表示原始觸發 storage 事件的那個網頁的網址。

下面是`StorageEvent.key`屬性的例子。

```javascript
function onStorageChange(e) {
  console.log(e.key);
}

window.addEventListener('storage', onStorageChange);
```

注意，該事件有一個很特別的地方，就是它不在導致資料變化的當前頁面觸發，而是在同一個域名的其他視窗觸發。也就是說，如果瀏覽器只打開一個視窗，可能觀察不到這個事件。比如同時開啟多個視窗，當其中的一個視窗導致儲存的資料發生改變時，只有在其他窗口才能觀察到監聽函式的執行。可以透過這種機制，實現多個視窗之間的通訊。

## 參考連結

- Ryan Stewart，[Introducing the HTML5 storage APIs](http://www.adobe.com/devnet/html5/articles/html5-storage-apis.html)
- [Getting Started with LocalStorage](http://codular.com/localstorage)
- Feross Aboukhadijeh, [Introducing the HTML5 Hard Disk Filler™ API](http://feross.org/fill-disk/)
- Ben Summers, [Inter-window messaging using localStorage](http://bens.me.uk/2013/localstorage-inter-window-messaging)
- Stack Overflow, [Why does Internet Explorer fire the window “storage” event on the window that stored the data?](http://stackoverflow.com/questions/18265556/why-does-internet-explorer-fire-the-window-storage-event-on-the-window-that-st)
- Stack Overflow, [localStorage eventListener is not called](https://stackoverflow.com/questions/5370784/localstorage-eventlistener-is-not-called)
