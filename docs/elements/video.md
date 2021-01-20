# <video>，<audio>

## 概述

`<video>`元素用來載入影片，是`HTMLVideoElement`物件的例項。`<audio>`元素用來載入音訊，是`HTMLAudioElement`物件的例項。而`HTMLVideoElement`和`HTMLAudioElement`都繼承了`HTMLMediaElement`，所以這兩個 HTML 元素有許多共同的屬性和方法，可以放在一起介紹。

理論上，這兩個 HTML 元素直接用`src`屬性指定媒體檔案，就可以使用了。

```html
<audio src="background_music.mp3"/>
<video src="news.mov" width=320 height=240/>
```

注意，`<video>`元素有`width`屬性和`height`屬性，可以指定寬和高。`<audio>`元素沒有這兩個屬性，因為它的播放器外形是瀏覽器給定的，不能指定。

實際上，不同的瀏覽器支援不同的媒體格式，我們不得不用`<source>`元素指定同一個媒體檔案的不同格式。

```html
<audio id="music">
  <source src="music.mp3" type="audio/mpeg">  
  <source src="music.ogg" type='audio/ogg; codec="vorbis"'>
</audio>
```

瀏覽器遇到支援的格式，就會忽略後面的格式。

這兩個元素都有一個`controls`屬性，只有開啟這個屬性，才會顯示控制條。注意，`<audio>`元素如果不開啟`controls`屬性，根本不會顯示，而是直接在背景播放。

## HTMLMediaElement 介面

`HTMLMediaElement`並沒有對應的 HTML 元素，而是作為`<video>`和`<audio>`的基類，定義一些它們共同的屬性和方法。

`HTMLMediaElement`介面有以下屬性。

- HTMLMediaElement.audioTracks：返回一個類似陣列的物件，表示媒體檔案包含的音軌。
- HTMLMediaElement.autoplay：布林值，表示媒體檔案是否自動播放，對應 HTML 屬性`autoplay`。
- HTMLMediaElement.buffered：返回一個 TimeRanges 物件，表示瀏覽器緩衝的內容。該物件的`length`屬性返回快取裡面有多少段內容，`start(rangeId)`方法返回指定的某段內容（從0開始）開始的時間點，`end()`返回指定的某段內容結束的時間點。該屬性只讀。
- HTMLMediaElement.controls：布林值，表示是否顯示媒體檔案的控制欄，對應 HTML 屬性`controls`。
- HTMLMediaElement.controlsList：返回一個類似陣列的物件，表示是否顯示控制欄的某些控制元件。該物件包含三個可能的值：`nodownload`、`nofullscreen`和`noremoteplayback`。該屬性只讀。
- HTMLMediaElement.crossOrigin：字串，表示跨域請求時是否附帶使用者資訊（比如 Cookie），對應 HTML 屬性`crossorigin`。該屬性只有兩個可能的值：`anonymous`和`use-credentials`。
- HTMLMediaElement.currentSrc：字串，表示當前正在播放的媒體檔案的絕對路徑。該屬性只讀。
- HTMLMediaElement.currentTime：浮點數，表示當前播放的時間點。
- HTMLMediaElement.defaultMuted：布林值，表示預設是否關閉音量，對應 HTML 屬性`muted`。
- HTMLMediaElement.defaultPlaybackRate：浮點數，表示預設的播放速率，預設是1.0。
- HTMLMediaElement.disableRemotePlayback：布林值，是否允許遠端回放，即遠端回放的時候是否會有工具欄。
- HTMLMediaElement.duration：浮點數，表示媒體檔案的時間長度（單位秒）。如果當前沒有媒體檔案，該屬性返回0。該屬性只讀。
- HTMLMediaElement.ended：布林值，表示當前媒體檔案是否已經播放結束。該屬性只讀。
- HTMLMediaElement.error：返回最近一次報錯的錯誤物件，如果沒有報錯，返回`null`。
- HTMLMediaElement.loop：布林值，表示媒體檔案是否會迴圈播放，對應 HTML 屬性`loop`。
- HTMLMediaElement.muted：布林值，表示音量是否關閉。
- HTMLMediaElement.networkState：當前網路狀態，共有四個可能的值。0表示沒有資料；1表示媒體元素處在啟用狀態，但是還沒開始下載；2表示下載中；3表示沒有找到媒體檔案。
- HTMLMediaElement.paused：布林值，表示媒體檔案是否處在暫停狀態。該屬性只讀。
- HTMLMediaElement.playbackRate：浮點數，表示媒體檔案的播放速度，1.0是正常速度。如果是負數，表示向後播放。
- HTMLMediaElement.played：返回一個 TimeRanges 物件，表示播放的媒體內容。該屬性只讀。
- HTMLMediaElement.preload：字串，表示應該預載入哪些內容，可能的值為`none`、`metadata`和`auto`。
- HTMLMediaElement.readyState：整數，表示媒體檔案的準備狀態，可能的值為0（沒有任何資料）、1（已獲取元資料）、2（可播放當前幀，但不足以播放多個幀）、3（可以播放多幀，至少為兩幀）、4（可以流暢播放）。該屬性只讀。
- HTMLMediaElement.seekable：返回一個 TimeRanges 物件，表示一個使用者可以搜尋的媒體內容範圍。該屬性只讀。
- HTMLMediaElement.seeking：布林值，表示媒體檔案是否正在尋找新位置。該屬性只讀。
- HTMLMediaElement.src：布林值，表示媒體檔案的 URL，對應 HTML 屬性`src`。
- HTMLMediaElement.srcObject：返回`src`屬性對應的媒體檔案資源，可能是`MediaStream`、`MediaSource`、`Blob`或`File`物件。直接指定這個屬性，就可以播放媒體檔案。
- HTMLMediaElement.textTracks：返回一個類似陣列的物件，包含所有文字軌道。該屬性只讀。
- HTMLMediaElement.videoTracks：返回一個類似陣列的物件，包含多有影片軌道。該屬性只讀。
- HTMLMediaElement.volume：浮點數，表示音量。0.0 表示靜音，1.0 表示最大音量。

`HTMLMediaElement`介面有如下方法。

- HTMLMediaElement.addTextTrack()：新增文字軌道（比如字幕）到媒體檔案。
- HTMLMediaElement.captureStream()：返回一個 MediaStream 物件，用來捕獲當前媒體檔案的流內容。
- HTMLMediaElement.canPlayType()：該方法接受一個 MIME 字串作為引數，用來判斷這種型別的媒體檔案是否可以播放。該方法返回一個字串，有三種可能的值，`probably`表示似乎可播放，`maybe`表示無法在不播放的情況下判斷是否可播放，空字串表示無法播放。
- HTMLMediaElement.fastSeek()：該方法接受一個浮點數作為引數，表示指定的時間（單位秒）。該方法將媒體檔案移動到指定時間。
- HTMLMediaElement.load()：重新載入媒體檔案。
- HTMLMediaElement.pause()：暫停播放。該方法沒有返回值。
- HTMLMediaElement.play()：開始播放。該方法返回一個 Promise 物件。

下面是`play()`方法的一個例子。

```javascript
var myVideo = document.getElementById('myVideoElement');

myVideo
.play()
.then(() => {
  console.log('playing');
})
.catch((error) => {
  console.log(error);
});
```

## HTMLVideoElement 介面

`HTMLVideoElement`介面代表了`<video>`元素。這個介面繼承了`HTMLMediaElement`介面，並且有一些自己的屬性和方法。

HTMLVideoElement 介面的屬性。

- HTMLVideoElement.height：字串，表示影片播放區域的高度（單位畫素），對應 HTML 屬性`height`。
- HTMLVideoElement.width：字串，表示影片播放區域的寬度（單位畫素），對應 HTML 屬性`width`。
- HTMLVideoElement.videoHeight：該屬性只讀，返回一個整數，表示影片檔案自身的高度（單位畫素）。
- HTMLVideoElement.videoWidth：該屬性只讀，返回一個整數，表示影片檔案自身的寬度（單位畫素）。
- HTMLVideoElement.poster：字串，表示一個影象檔案的 URL，用來在無法獲取影片檔案時替代顯示，對應 HTML 屬性`poster`。

HTMLVideoElement 介面的方法。

- HTMLVideoElement.getVideoPlaybackQuality()：返回一個物件，包含了當前影片回放的一些資料。

## HTMLAudioElement 介面

`HTMLAudioElement`介面代表了`<audio>`元素。

該介面繼承了`HTMLMediaElement`，但是沒有定義自己的屬性和方法。瀏覽器原生提供一個`Audio()`建構函式，返回的就是`HTMLAudioElement`例項。

```javascript
var song = new Audio([URLString]);
```

`Audio()`建構函式接受一個字串作為引數，表示媒體檔案的 URL。如果省略這個引數，可以稍後透過`src`屬性指定。

生成`HTMLAudioElement`例項以後，不用插入 DOM，可以直接用`play()`方法在背景播放。

```javascript
var a = new Audio();
if (a.canPlayType('audio/wav')) {
  a.src = 'soundeffect.wav';
  a.play();
}
 ```

## 事件

`<video>`和`<audio>`元素有以下事件。

- loadstart：開始載入媒體檔案時觸發。
- progress：媒體檔案載入過程中觸發，大概是每秒觸發2到8次。
- loadedmetadata：媒體檔案元資料載入成功時觸發。
- loadeddata：當前播放位置載入成功後觸發。
- canplay：已經載入了足夠的資料，可以開始播放時觸發，後面可能還會請求資料。
- canplaythrough：已經載入了足夠的資料，可以一直播放時觸發，後面不需要繼續請求資料。
- suspend：已經緩衝了足夠的資料，暫時停止下載時觸發。
- stalled：嘗試載入資料，但是沒有資料返回時觸發。
- play：呼叫`play()`方法時或自動播放啟動時觸發。如果已經載入了足夠的資料，這個事件後面會緊跟`playing`事件，否則會觸發`waiting`事件。
- waiting：由於沒有足夠的快取資料，無法播放或播放停止時觸發。一旦緩衝資料足夠開始播放，後面就會緊跟`playing`事件。
- playing：媒體開始播放時觸發。
- timeupdate：`currentTime`屬性變化時觸發，每秒可能觸發4到60次。
- pause：呼叫`pause()`方法、播放暫停時觸發。
- seeking：指令碼或者使用者要求播放某個沒有緩衝的位置，播放停止開始載入資料時觸發。此時，`seeking`屬性返回`true`。
- seeked：`seeking`屬性變回`false`時觸發。
- ended：媒體檔案播放完畢時觸發。
- durationchange：`duration`屬性變化時觸發。
- volumechange：音量變化時觸發。
- ratechange：播放速度或預設的播放速度變化時觸發。
- abort：停止載入媒體檔案時觸發，通常是使用者主動要求停止下載。
- error：網路或其他原因導致媒體檔案無法載入時觸發。
- emptied：由於`error`或`abort`事件導致`networkState`屬性變成無法獲取資料時觸發。

