# Navigator 物件，Screen 物件。

`window.navigator`屬性指向一個包含瀏覽器和系統資訊的 Navigator 物件。指令碼透過這個屬性瞭解使用者的環境資訊。

## Navigator 物件的屬性

### Navigator.userAgent

`navigator.userAgent`屬性返回瀏覽器的 User Agent 字串，表示瀏覽器的廠商和版本資訊。

下面是 Chrome 瀏覽器的`userAgent`。

```javascript
navigator.userAgent
// "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.57 Safari/537.36"
```

透過`userAgent`屬性識別瀏覽器，不是一個好辦法。因為必須考慮所有的情況（不同的瀏覽器，不同的版本），非常麻煩，而且使用者可以改變這個字串。這個字串的格式並無統一規定，也無法保證未來的適用性，各種上網裝置層出不窮，難以窮盡。所以，現在一般不再透過它識別瀏覽器了，而是使用“功能識別”方法，即逐一測試當前瀏覽器是否支援要用到的 JavaScript 功能。

不過，透過`userAgent`可以大致準確地識別手機瀏覽器，方法就是測試是否包含`mobi`字串。

```javascript
var ua = navigator.userAgent.toLowerCase();

if (/mobi/i.test(ua)) {
  // 手機瀏覽器
} else {
  // 非手機瀏覽器
}
```

如果想要識別所有移動裝置的瀏覽器，可以測試更多的特徵字串。

```javascript
/mobi|android|touch|mini/i.test(ua)
```

### Navigator.plugins

`Navigator.plugins`屬性返回一個類似陣列的物件，成員是 Plugin 例項物件，表示瀏覽器安裝的外掛，比如 Flash、ActiveX 等。

```javascript
var pluginsLength = navigator.plugins.length;

for (var i = 0; i < pluginsLength; i++) {
  console.log(navigator.plugins[i].name);
  console.log(navigator.plugins[i].filename);
  console.log(navigator.plugins[i].description);
  console.log(navigator.plugins[i].version);
}
```

### Navigator.platform

`Navigator.platform`屬性返回使用者的作業系統資訊，比如`MacIntel`、`Win32`、`Linux x86_64`等 。

```javascript
navigator.platform
// "Linux x86_64"
```

### Navigator.onLine

`navigator.onLine`屬性返回一個布林值，表示使用者當前線上還是離線（瀏覽器斷線）。

```javascript
navigator.onLine // true
```

有時，瀏覽器可以連線區域網，但是區域網不能連通外網。這時，有的瀏覽器的`onLine`屬性會返回`true`，所以不能假定只要是`true`，使用者就一定能訪問網際網路。不過，如果是`false`，可以斷定使用者一定離線。

使用者變成線上會觸發`online`事件，變成離線會觸發`offline`事件，可以透過`window.ononline`和`window.onoffline`指定這兩個事件的回撥函式。

```javascript
window.addEventListener('offline', function(e) { console.log('offline'); });
window.addEventListener('online', function(e) { console.log('online'); });
```

### Navigator.language，Navigator.languages

`Navigator.language`屬性返回一個字串，表示瀏覽器的首選語言。該屬性只讀。

```javascript
navigator.language // "en"
```

`Navigator.languages`屬性返回一個數組，表示使用者可以接受的語言。`Navigator.language`總是這個陣列的第一個成員。HTTP 請求頭資訊的`Accept-Language`欄位，就來自這個陣列。

```javascript
navigator.languages  // ["en-US", "en", "zh-CN", "zh", "zh-TW"]
```

如果這個屬性發生變化，就會在`window`物件上觸發`languagechange`事件。

### Navigator.geolocation

`Navigator.geolocation`屬性返回一個 Geolocation 物件，包含使用者地理位置的資訊。注意，該 API 只有在 HTTPS 協議下可用，否則呼叫下面方法時會報錯。

Geolocation 物件提供下面三個方法。

- Geolocation.getCurrentPosition()：得到使用者的當前位置
- Geolocation.watchPosition()：監聽使用者位置變化
- Geolocation.clearWatch()：取消`watchPosition()`方法指定的監聽函式

注意，呼叫這三個方法時，瀏覽器會跳出一個對話方塊，要求使用者給予授權。

### Navigator.cookieEnabled

`navigator.cookieEnabled`屬性返回一個布林值，表示瀏覽器的 Cookie 功能是否開啟。

```javascript
navigator.cookieEnabled // true
```

注意，這個屬性反映的是瀏覽器總的特性，與是否儲存某個具體的網站的 Cookie 無關。使用者可以設定某個網站不得儲存 Cookie，這時`cookieEnabled`返回的還是`true`。

## Navigator 物件的方法

### Navigator.javaEnabled()

`navigator.javaEnabled()`方法返回一個布林值，表示瀏覽器是否能執行 Java Applet 小程式。

```javascript
navigator.javaEnabled() // false
```

### Navigator.sendBeacon()

`Navigator.sendBeacon()`方法用於向伺服器非同步傳送資料，詳見《XMLHttpRequest 物件》一章。

## Navigator 的實驗性屬性

Navigator 物件有一些實驗性屬性，在部分瀏覽器可用。

### Navigator.deviceMemory

`navigator.deviceMemory`屬性返回當前計算機的記憶體數量（單位為 GB）。該屬性只讀，只在 HTTPS 環境下可用。

它的返回值是一個近似值，四捨五入到最接近的2的冪，通常是 0.25、0.5、1、2、4、8。實際記憶體超過 8GB，也返回`8`。

```javascript
if (navigator.deviceMemory > 1) {
  await import('./costly-module.js');
}
```

上面示例中，只有當前記憶體大於 1GB，才載入大型的指令碼。

### Navigator.hardwareConcurrency

`navigator.hardwareConcurrency`屬性返回使用者計算機上可用的邏輯處理器的數量。該屬性只讀。

現代計算機的 CPU 有多個物理核心，每個物理核心有時支援一次執行多個執行緒。因此，四核 CPU 可以提供八個邏輯處理器核心。

```javascript
if (navigator.hardwareConcurrency > 4) {
  await import('./costly-module.js');
}
```

上面示例中，可用的邏輯處理器大於4，才會載入大型指令碼。

該屬性透過用於建立 Web Worker，每個可用的邏輯處理器都建立一個 Worker。

```javascript
let workerList = [];

for (let i = 0; i < window.navigator.hardwareConcurrency; i++) {
  let newWorker = {
    worker: new Worker('cpuworker.js'),
    inUse: false
  };
  workerList.push(newWorker);
}
```

上面示例中，有多少個可用的邏輯處理器，就建立多少個 Web Worker。

### Navigator.connection

`navigator.connection`屬性返回一個物件，包含當前網路連線的相關資訊。

- downlink：有效頻寬估計值（單位：兆位元/秒，Mbps），四捨五入到每秒 25KB 的最接近倍數。
- downlinkMax：當前連線的最大下行鏈路速度（單位：兆位元每秒，Mbps）。
- effectiveType：返回連線的等效型別，可能的值為`slow-2g`、`2g`、`3g`、`4g`。
- rtt：當前連線的估計有效往返時間，四捨五入到最接近的25毫秒的倍數。
- saveData：使用者是否設定了瀏覽器的減少資料使用量選項（比如不載入圖片），返回`true`或者`false`。
- type：當前連線的介質型別，可能的值為`bluetooth`、`cellular`、`ethernet`、`none`、`wifi`、`wimax`、`other`、`unknown`。

```javascript
if (navigator.connection.effectiveType === '4g') {
  await import('./costly-module.js');
}
```

上面示例中，如果網路連線是 4G，則載入大型指令碼。

## Screen 物件

Screen 物件表示當前視窗所在的螢幕，提供顯示裝置的資訊。`window.screen`屬性指向這個物件。

該物件有下面的屬性。

- `Screen.height`：瀏覽器視窗所在的螢幕的高度（單位畫素）。除非調整顯示器的解析度，否則這個值可以看作常量，不會發生變化。顯示器的解析度與瀏覽器設定無關，縮放網頁並不會改變解析度。
- `Screen.width`：瀏覽器視窗所在的螢幕的寬度（單位畫素）。
- `Screen.availHeight`：瀏覽器視窗可用的螢幕高度（單位畫素）。因為部分空間可能不可用，比如系統的工作列或者 Mac 系統螢幕底部的 Dock 區，這個屬性等於`height`減去那些被系統元件的高度。
- `Screen.availWidth`：瀏覽器視窗可用的螢幕寬度（單位畫素）。
- `Screen.pixelDepth`：整數，表示螢幕的色彩位數，比如`24`表示螢幕提供24位色彩。
- `Screen.colorDepth`：`Screen.pixelDepth`的別名。嚴格地說，colorDepth 表示應用程式的顏色深度，pixelDepth 表示螢幕的顏色深度，絕大多數情況下，它們都是同一件事。
- `Screen.orientation`：返回一個物件，表示螢幕的方向。該物件的`type`屬性是一個字串，表示螢幕的具體方向，`landscape-primary`表示橫放，`landscape-secondary`表示顛倒的橫放，`portrait-primary`表示豎放，`portrait-secondary`表示顛倒的豎放。

下面是`Screen.orientation`的例子。

```javascript
window.screen.orientation
// { angle: 0, type: "landscape-primary", onchange: null }
```

下面的例子保證螢幕解析度大於 1024 x 768。

```javascript
if (window.screen.width >= 1024 && window.screen.height >= 768) {
  // 解析度不低於 1024x768
}
```

下面是根據螢幕的寬度，將使用者導向不同網頁的程式碼。

```javascript
if ((screen.width <= 800) && (screen.height <= 600)) {
  window.location.replace('small.html');
} else {
  window.location.replace('wide.html');
}
```

