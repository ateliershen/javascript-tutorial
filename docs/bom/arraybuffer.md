# ArrayBuffer 物件，Blob 物件

## ArrayBuffer 物件

ArrayBuffer 物件表示一段二進位制資料，用來模擬記憶體裡面的資料。透過這個物件，JavaScript 可以讀寫二進位制資料。這個物件可以看作記憶體資料的表達。

這個物件是 ES6 才寫入標準的，普通的網頁程式設計用不到它，為了教程體系的完整，下面只提供一個簡略的介紹，詳細介紹請看《ES6 標準入門》裡面的章節。

瀏覽器原生提供`ArrayBuffer()`建構函式，用來生成例項。它接受一個整數作為引數，表示這段二進位制資料佔用多少個位元組。

```javascript
var buffer = new ArrayBuffer(8);
```

上面程式碼中，例項物件`buffer`佔用8個位元組。

ArrayBuffer 物件有例項屬性`byteLength`，表示當前例項佔用的記憶體長度（單位位元組）。

```javascript
var buffer = new ArrayBuffer(8);
buffer.byteLength // 8
```

ArrayBuffer 物件有例項方法`slice()`，用來複制一部分記憶體。它接受兩個整數引數，分別表示複製的開始位置（從0開始）和結束位置（複製時不包括結束位置），如果省略第二個引數，則表示一直複製到結束。

```javascript
var buf1 = new ArrayBuffer(8);
var buf2 = buf1.slice(0);
```

上面程式碼表示覆制原來的例項。

## Blob 物件

### 簡介

Blob 物件表示一個二進位制檔案的資料內容，比如一個圖片檔案的內容就可以透過 Blob 物件讀寫。它通常用來讀寫檔案，它的名字是 Binary Large Object （二進位制大型物件）的縮寫。它與 ArrayBuffer 的區別在於，它用於操作二進位制檔案，而 ArrayBuffer 用於操作記憶體。

瀏覽器原生提供`Blob()`建構函式，用來生成例項物件。

```javascript
new Blob(array [, options])
```

`Blob`建構函式接受兩個引數。第一個引數是陣列，成員是字串或二進位制物件，表示新生成的`Blob`例項物件的內容；第二個引數是可選的，是一個配置物件，目前只有一個屬性`type`，它的值是一個字串，表示資料的 MIME 型別，預設是空字串。

```javascript
var htmlFragment = ['<a id="a"><b id="b">hey!</b></a>'];
var myBlob = new Blob(htmlFragment, {type : 'text/html'});
```

上面程式碼中，例項物件`myBlob`包含的是字串。生成例項的時候，資料型別指定為`text/html`。

下面是另一個例子，Blob 儲存 JSON 資料。

```javascript
var obj = { hello: 'world' };
var blob = new Blob([ JSON.stringify(obj) ], {type : 'application/json'});
```

### 例項屬性和例項方法

`Blob`具有兩個例項屬性`size`和`type`，分別返回資料的大小和型別。

```javascript
var htmlFragment = ['<a id="a"><b id="b">hey!</b></a>'];
var myBlob = new Blob(htmlFragment, {type : 'text/html'});

myBlob.size // 32
myBlob.type // "text/html"
```

`Blob`具有一個例項方法`slice`，用來複製原來的資料，返回的也是一個`Blob`例項。

```javascript
myBlob.slice(start, end, contentType)
```

`slice`方法有三個引數，都是可選的。它們依次是起始的位元組位置（預設為0）、結束的位元組位置（預設為`size`屬性的值，該位置本身將不包含在複製的資料之中）、新例項的資料型別（預設為空字串）。

### 獲取檔案資訊

檔案選擇器`<input type="file">`用來讓使用者選取檔案。出於安全考慮，瀏覽器不允許指令碼自行設定這個控制元件的`value`屬性，即檔案必須是使用者手動選取的，不能是指令碼指定的。一旦使用者選好了檔案，指令碼就可以讀取這個檔案。

檔案選擇器返回一個 FileList 物件，該物件是一個類似陣列的成員，每個成員都是一個 File 例項物件。File 例項物件是一個特殊的 Blob 例項，增加了`name`和`lastModifiedDate`屬性。

```javascript
// HTML 程式碼如下
// <input type="file" accept="image/*" multiple onchange="fileinfo(this.files)"/>

function fileinfo(files) {
  for (var i = 0; i < files.length; i++) {
    var f = files[i];
    console.log(
      f.name, // 檔名，不含路徑
      f.size, // 檔案大小，Blob 例項屬性
      f.type, // 檔案型別，Blob 例項屬性
      f.lastModifiedDate // 檔案的最後修改時間
    );
  }
}
```

除了檔案選擇器，拖放 API 的`dataTransfer.files`返回的也是一個FileList 物件，它的成員因此也是 File 例項物件。

### 下載檔案

AJAX 請求時，如果指定`responseType`屬性為`blob`，下載下來的就是一個 Blob 物件。

```javascript
function getBlob(url, callback) {
  var xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.responseType = 'blob';
  xhr.onload = function () {
    callback(xhr.response);
  }
  xhr.send(null);
}
```

上面程式碼中，`xhr.response`拿到的就是一個 Blob 物件。

### 生成 URL

瀏覽器允許使用`URL.createObjectURL()`方法，針對 Blob 物件生成一個臨時 URL，以便於某些 API 使用。這個 URL 以`blob://`開頭，表明對應一個 Blob 物件，協議頭後面是一個識別符，用來唯一對應記憶體裡面的 Blob 物件。這一點與`data://URL`（URL 包含實際資料）和`file://URL`（本地檔案系統裡面的檔案）都不一樣。

```javascript
var droptarget = document.getElementById('droptarget');

droptarget.ondrop = function (e) {
  var files = e.dataTransfer.files;
  for (var i = 0; i < files.length; i++) {
    var type = files[i].type;
    if (type.substring(0,6) !== 'image/')
      continue;
    var img = document.createElement('img');
    img.src = URL.createObjectURL(files[i]);
    img.onload = function () {
      this.width = 100;
      document.body.appendChild(this);
      URL.revokeObjectURL(this.src);
    }
  }
}
```

上面程式碼透過為拖放的圖片檔案生成一個 URL，產生它們的縮圖，從而使得使用者可以預覽選擇的檔案。

瀏覽器處理 Blob URL 就跟普通的 URL 一樣，如果 Blob 物件不存在，返回404狀態碼；如果跨域請求，返回403狀態碼。Blob URL 只對 GET 請求有效，如果請求成功，返回200狀態碼。由於 Blob URL 就是普通 URL，因此可以下載。

### 讀取檔案

取得 Blob 物件以後，可以透過`FileReader`物件，讀取 Blob 物件的內容，即檔案內容。

FileReader 物件提供四個方法，處理 Blob 物件。Blob 物件作為引數傳入這些方法，然後以指定的格式返回。

- `FileReader.readAsText()`：返回文字，需要指定文字編碼，預設為 UTF-8。
- `FileReader.readAsArrayBuffer()`：返回 ArrayBuffer 物件。
- `FileReader.readAsDataURL()`：返回 Data URL。
- `FileReader.readAsBinaryString()`：返回原始的二進位制字串。

下面是`FileReader.readAsText()`方法的例子，用來讀取文字檔案。

```javascript
// HTML 程式碼如下
// <input type="file" onchange="readfile(this.files[0])"></input>
// <pre id="output"></pre>
function readfile(f) {
  var reader = new FileReader();
  reader.readAsText(f);
  reader.onload = function () {
    var text = reader.result;
    var out = document.getElementById('output');
    out.innerHTML = '';
    out.appendChild(document.createTextNode(text));
  }
  reader.onerror = function(e) {
    console.log('Error', e);
  };
}
```

上面程式碼中，透過指定 FileReader 例項物件的`onload`監聽函式，在例項的`result`屬性上拿到檔案內容。

下面是`FileReader.readAsArrayBuffer()`方法的例子，用於讀取二進位制檔案。

```javascript
// HTML 程式碼如下
// <input type="file" onchange="typefile(this.files[0])"></input>
function typefile(file) {
  // 檔案開頭的四個位元組，生成一個 Blob 物件
  var slice = file.slice(0, 4);
  var reader = new FileReader();
  // 讀取這四個位元組
  reader.readAsArrayBuffer(slice);
  reader.onload = function (e) {
    var buffer = reader.result;
    // 將這四個位元組的內容，視作一個32位整數
    var view = new DataView(buffer);
    var magic = view.getUint32(0, false);
    // 根據檔案的前四個位元組，判斷它的型別
    switch(magic) {
      case 0x89504E47: file.verified_type = 'image/png'; break;
      case 0x47494638: file.verified_type = 'image/gif'; break;
      case 0x25504446: file.verified_type = 'application/pdf'; break;
      case 0x504b0304: file.verified_type = 'application/zip'; break;
    }
    console.log(file.name, file.verified_type);
  };
}
```

