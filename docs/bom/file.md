# File 物件，FileList 物件，FileReader 物件

## File 物件

File 物件代表一個檔案，用來讀寫檔案資訊。它繼承了 Blob 物件，或者說是一種特殊的 Blob 物件，所有可以使用 Blob 物件的場合都可以使用它。

最常見的使用場合是表單的檔案上傳控制元件（`<input type="file">`），使用者選中檔案以後，瀏覽器就會生成一個數組，裡面是每一個使用者選中的檔案，它們都是 File 例項物件。

```javascript
// HTML 程式碼如下
// <input id="fileItem" type="file">
var file = document.getElementById('fileItem').files[0];
file instanceof File // true
```

上面程式碼中，`file`是使用者選中的第一個檔案，它是 File 的例項。

### 建構函式

瀏覽器原生提供一個`File()`建構函式，用來生成 File 例項物件。

```javascript
new File(array, name [, options])
```

`File()`建構函式接受三個引數。

- array：一個數組，成員可以是二進位制物件或字串，表示檔案的內容。
- name：字串，表示檔名或檔案路徑。
- options：配置物件，設定例項的屬性。該引數可選。

第三個引數配置物件，可以設定兩個屬性。

- type：字串，表示例項物件的 MIME 型別，預設值為空字串。
- lastModified：時間戳，表示上次修改的時間，預設為`Date.now()`。

下面是一個例子。

```javascript
var file = new File(
  ['foo'],
  'foo.txt',
  {
    type: 'text/plain',
  }
);
```

### 例項屬性和例項方法

File 物件有以下例項屬性。

- File.lastModified：最後修改時間
- File.name：檔名或檔案路徑
- File.size：檔案大小（單位位元組）
- File.type：檔案的 MIME 型別

```javascript
var myFile = new File([], 'file.bin', {
  lastModified: new Date(2018, 1, 1),
});
myFile.lastModified // 1517414400000
myFile.name // "file.bin"
myFile.size // 0
myFile.type // ""
```

上面程式碼中，由於`myFile`的內容為空，也沒有設定 MIME 型別，所以`size`屬性等於0，`type`屬性等於空字串。

File 物件沒有自己的例項方法，由於繼承了 Blob 物件，因此可以使用 Blob 的例項方法`slice()`。

## FileList 物件

`FileList`物件是一個類似陣列的物件，代表一組選中的檔案，每個成員都是一個 File 例項。它主要出現在兩個場合。

- 檔案控制元件節點（`<input type="file">`）的`files`屬性，返回一個 FileList 例項。
- 拖拉一組檔案時，目標區的`DataTransfer.files`屬性，返回一個 FileList 例項。

```javascript
// HTML 程式碼如下
// <input id="fileItem" type="file">
var files = document.getElementById('fileItem').files;
files instanceof FileList // true
```

上面程式碼中，檔案控制元件的`files`屬性是一個 FileList 例項。

FileList 的例項屬性主要是`length`，表示包含多少個檔案。

FileList 的例項方法主要是`item()`，用來返回指定位置的例項。它接受一個整數作為引數，表示位置的序號（從零開始）。但是，由於 FileList 的例項是一個類似陣列的物件，可以直接用方括號運算子，即`myFileList[0]`等同於`myFileList.item(0)`，所以一般用不到`item()`方法。

## FileReader 物件

FileReader 物件用於讀取 File 物件或 Blob 物件所包含的檔案內容。

瀏覽器原生提供一個`FileReader`建構函式，用來生成 FileReader 例項。

```javascript
var reader = new FileReader();
```

FileReader 有以下的例項屬性。

- FileReader.error：讀取檔案時產生的錯誤物件
- FileReader.readyState：整數，表示讀取檔案時的當前狀態。一共有三種可能的狀態，`0`表示尚未載入任何資料，`1`表示資料正在載入，`2`表示載入完成。
- FileReader.result：讀取完成後的檔案內容，有可能是字串，也可能是一個 ArrayBuffer 例項。
- FileReader.onabort：`abort`事件（使用者終止讀取操作）的監聽函式。
- FileReader.onerror：`error`事件（讀取錯誤）的監聽函式。
- FileReader.onload：`load`事件（讀取操作完成）的監聽函式，通常在這個函式裡面使用`result`屬性，拿到檔案內容。
- FileReader.onloadstart：`loadstart`事件（讀取操作開始）的監聽函式。
- FileReader.onloadend：`loadend`事件（讀取操作結束）的監聽函式。
- FileReader.onprogress：`progress`事件（讀取操作進行中）的監聽函式。

下面是監聽`load`事件的一個例子。

```javascript
// HTML 程式碼如下
// <input type="file" onchange="onChange(event)">

function onChange(event) {
  var file = event.target.files[0];
  var reader = new FileReader();
  reader.onload = function (event) {
    console.log(event.target.result)
  };

  reader.readAsText(file);
}
```

上面程式碼中，每當檔案控制元件發生變化，就嘗試讀取第一個檔案。如果讀取成功（`load`事件發生），就打印出檔案內容。

FileReader 有以下例項方法。

- FileReader.abort()：終止讀取操作，`readyState`屬性將變成`2`。
- FileReader.readAsArrayBuffer()：以 ArrayBuffer 的格式讀取檔案，讀取完成後`result`屬性將返回一個 ArrayBuffer 例項。
- FileReader.readAsBinaryString()：讀取完成後，`result`屬性將返回原始的二進位制字串。
- FileReader.readAsDataURL()：讀取完成後，`result`屬性將返回一個 Data URL 格式（Base64 編碼）的字串，代表檔案內容。對於圖片檔案，這個字串可以用於`<img>`元素的`src`屬性。注意，這個字串不能直接進行 Base64 解碼，必須把字首`data:*/*;base64,`從字串裡刪除以後，再進行解碼。
- FileReader.readAsText()：讀取完成後，`result`屬性將返回檔案內容的文字字串。該方法的第一個引數是代表檔案的 Blob 例項，第二個引數是可選的，表示文字編碼，預設為 UTF-8。

下面是一個例子。

```javascript
/* HTML 程式碼如下
  <input type="file" onchange="previewFile()">
  <img src="" height="200">
*/

function previewFile() {
  var preview = document.querySelector('img');
  var file    = document.querySelector('input[type=file]').files[0];
  var reader  = new FileReader();

  reader.addEventListener('load', function () {
    preview.src = reader.result;
  }, false);

  if (file) {
    reader.readAsDataURL(file);
  }
}
```

上面程式碼中，使用者選中圖片檔案以後，指令碼會自動讀取檔案內容，然後作為一個 Data URL 賦值給`<img>`元素的`src`屬性，從而把圖片展示出來。
