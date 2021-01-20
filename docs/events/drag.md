# 拖拉事件

## 拖拉事件的種類

拖拉（drag）指的是，使用者在某個物件上按下滑鼠鍵不放，拖動它到另一個位置，然後釋放滑鼠鍵，將該物件放在那裡。

拖拉的物件有好幾種，包括元素節點、圖片、連結、選中的文字等等。在網頁中，除了元素節點預設不可以拖拉，其他（圖片、連結、選中的文字）都可以直接拖拉。為了讓元素節點可拖拉，可以將該節點的`draggable`屬性設為`true`。

```html
<div draggable="true">
  此區域可拖拉
</div>
```

上面程式碼的`div`區塊，在網頁中可以直接用滑鼠拖動。鬆開滑鼠鍵時，拖動效果就會消失，該區塊依然在原來的位置。

`draggable`屬性可用於任何元素節點，但是圖片（`<img>`）和連結（`<a>`）不加這個屬性，就可以拖拉。對於它們，用到這個屬性的時候，往往是將其設為`false`，防止拖拉這兩種元素。

注意，一旦某個元素節點的`draggable`屬性設為`true`，就無法再用滑鼠選中該節點內部的文字或子節點了。

當元素節點或選中的文字被拖拉時，就會持續觸發拖拉事件，包括以下一些事件。

- `drag`：拖拉過程中，在被拖拉的節點上持續觸發（相隔幾百毫秒）。
- `dragstart`：使用者開始拖拉時，在被拖拉的節點上觸發，該事件的`target`屬性是被拖拉的節點。通常應該在這個事件的監聽函式中，指定拖拉的資料。
- `dragend`：拖拉結束時（釋放滑鼠鍵或按下 ESC 鍵）在被拖拉的節點上觸發，該事件的`target`屬性是被拖拉的節點。它與`dragstart`事件，在同一個節點上觸發。不管拖拉是否跨視窗，或者中途被取消，`dragend`事件總是會觸發的。
- `dragenter`：拖拉進入當前節點時，在當前節點上觸發一次，該事件的`target`屬性是當前節點。通常應該在這個事件的監聽函式中，指定是否允許在當前節點放下（drop）拖拉的資料。如果當前節點沒有該事件的監聽函式，或者監聽函式不執行任何操作，就意味著不允許在當前節點放下資料。在視覺上顯示拖拉進入當前節點，也是在這個事件的監聽函式中設定。
- `dragover`：拖拉到當前節點上方時，在當前節點上持續觸發（相隔幾百毫秒），該事件的`target`屬性是當前節點。該事件與`dragenter`事件的區別是，`dragenter`事件在進入該節點時觸發，然後只要沒有離開這個節點，`dragover`事件會持續觸發。
- `dragleave`：拖拉操作離開當前節點範圍時，在當前節點上觸發，該事件的`target`屬性是當前節點。如果要在視覺上顯示拖拉離開操作當前節點，就在這個事件的監聽函式中設定。
- `drop`：被拖拉的節點或選中的文字，釋放到目標節點時，在目標節點上觸發。注意，如果當前節點不允許`drop`，即使在該節點上方鬆開滑鼠鍵，也不會觸發該事件。如果使用者按下 ESC 鍵，取消這個操作，也不會觸發該事件。該事件的監聽函式負責取出拖拉資料，並進行相關處理。

下面的例子展示，如何動態改變被拖動節點的背景色。

```javascript
div.addEventListener('dragstart', function (e) {
  this.style.backgroundColor = 'red';
}, false);

div.addEventListener('dragend', function (e) {
  this.style.backgroundColor = 'green';
}, false);
```

上面程式碼中，`div`節點被拖動時，背景色會變為紅色，拖動結束，又變回綠色。

下面是一個例子，展示如何實現將一個節點從當前父節點，拖拉到另一個父節點中。

```javascript
/* HTML 程式碼如下
 <div class="dropzone">
   <div id="draggable" draggable="true">
     該節點可拖拉
   </div>
 </div>
 <div class="dropzone"></div>
 <div class="dropzone"></div>
 <div class="dropzone"></div>
*/

// 被拖拉節點
var dragged;

document.addEventListener('dragstart', function (event) {
  // 儲存被拖拉節點
  dragged = event.target;
  // 被拖拉節點的背景色變透明
  event.target.style.opacity = 0.5;
}, false);

document.addEventListener('dragend', function (event) {
  // 被拖拉節點的背景色恢復正常
  event.target.style.opacity = '';
}, false);

document.addEventListener('dragover', function (event) {
  // 防止拖拉效果被重置，允許被拖拉的節點放入目標節點
  event.preventDefault();
}, false);

document.addEventListener('dragenter', function (event) {
  // 目標節點的背景色變紫色
  // 由於該事件會冒泡，所以要過濾節點
  if (event.target.className === 'dropzone') {
    event.target.style.background = 'purple';
  }
}, false);

document.addEventListener('dragleave', function( event ) {
  // 目標節點的背景色恢復原樣
  if (event.target.className === 'dropzone') {
    event.target.style.background = '';
  }
}, false);

document.addEventListener('drop', function( event ) {
  // 防止事件預設行為（比如某些元素節點上可以開啟連結），
  event.preventDefault();
  if (event.target.className === 'dropzone') {
    // 恢復目標節點背景色
    event.target.style.background = '';
    // 將被拖拉節點插入目標節點
    dragged.parentNode.removeChild(dragged);
    event.target.appendChild( dragged );
  }
}, false);
```

關於拖拉事件，有以下幾個注意點。

- 拖拉過程只觸發以上這些拖拉事件，儘管滑鼠在移動，但是滑鼠事件不會觸發。
- 將檔案從作業系統拖拉進瀏覽器，不會觸發`dragstart`和`dragend`事件。
- `dragenter`和`dragover`事件的監聽函式，用來取出拖拉的資料（即允許放下被拖拉的元素）。由於網頁的大部分割槽域不適合作為放下拖拉元素的目標節點，所以這兩個事件的預設設定為當前節點不允許接受被拖拉的元素。如果想要在目標節點上放下的資料，首先必須阻止這兩個事件的預設行為。

```html
<div ondragover="return false">
<div ondragover="event.preventDefault()">
```

上面程式碼中，如果不取消拖拉事件或者阻止預設行為，就不能在`div`節點上放下被拖拉的節點。

## DragEvent 介面

拖拉事件都繼承了`DragEvent`介面，這個介面又繼承了`MouseEvent`介面和`Event`介面。

瀏覽器原生提供一個`DragEvent()`建構函式，用來生成拖拉事件的例項物件。

```javascript
new DragEvent(type, options)
```

`DragEvent()`建構函式接受兩個引數，第一個引數是字串，表示事件的型別，該引數必須；第二個引數是事件的配置物件，用來設定事件的屬性，該引數可選。配置物件除了接受`MouseEvent`介面和`Event`介面的配置屬性，還可以設定`dataTransfer`屬性要麼是`null`，要麼是一個`DataTransfer`介面的例項。

`DataTransfer`的例項物件用來讀寫拖拉事件中傳輸的資料，詳見下文《DataTransfer 介面》的部分。

## DataTransfer 介面概述

所有拖拉事件的例項都有一個`DragEvent.dataTransfer`屬性，用來讀寫需要傳遞的資料。這個屬性的值是一個`DataTransfer`介面的例項。

瀏覽器原生提供一個`DataTransfer()`建構函式，用來生成`DataTransfer`例項物件。

```javascript
var dataTrans = new DataTransfer();
```

`DataTransfer()`建構函式不接受引數。

拖拉的資料分成兩方面：資料的種類（又稱格式）和資料的值。資料的種類是一個 MIME 字串（比如`text/plain`、`image/jpeg`），資料的值是一個字串。一般來說，如果拖拉一段文字，則資料預設就是那段文字；如果拖拉一個連結，則資料預設就是連結的 URL。

拖拉事件開始時，開發者可以提供資料型別和資料值。拖拉過程中，開發者透過`dragenter`和`dragover`事件的監聽函式，檢查資料型別，以確定是否允許放下（drop）被拖拉的物件。比如，在只允許放下連結的區域，檢查拖拉的資料型別是否為`text/uri-list`。

發生`drop`事件時，監聽函式取出拖拉的資料，對其進行處理。

## DataTransfer 的例項屬性

### DataTransfer.dropEffect

`DataTransfer.dropEffect`屬性用來設定放下（drop）被拖拉節點時的效果，會影響到拖拉經過相關區域時滑鼠的形狀。它可能取下面的值。

- copy：複製被拖拉的節點
- move：移動被拖拉的節點
- link：建立指向被拖拉的節點的連結
- none：無法放下被拖拉的節點

除了上面這些值，設定其他的值都是無效的。

```javascript
target.addEventListener('dragover', function (e) {
  e.preventDefault();
  e.stopPropagation();
  e.dataTransfer.dropEffect = 'copy';
});
```

上面程式碼中，被拖拉元素一旦`drop`，接受的區域會複製該節點。

`dropEffect`屬性一般在`dragenter`和`dragover`事件的監聽函式中設定，對於`dragstart`、`drag`、`dragleave`這三個事件，該屬性不起作用。因為該屬性只對接受被拖拉的節點的區域有效，對被拖拉的節點本身是無效的。進入目標區域後，拖拉行為會初始化成設定的效果。

### DataTransfer.effectAllowed

`DataTransfer.effectAllowed`屬性設定本次拖拉中允許的效果。它可能取下面的值。

- copy：複製被拖拉的節點
- move：移動被拖拉的節點
- link：建立指向被拖拉節點的連結
- copyLink：允許`copy`或`link`
- copyMove：允許`copy`或`move`
- linkMove：允許`link`或`move`
- all：允許所有效果
- none：無法放下被拖拉的節點
- uninitialized：預設值，等同於`all`

如果某種效果是不允許的，使用者就無法在目標節點中達成這種效果。

這個屬性與`dropEffect`屬性是同一件事的兩個方面。前者設定被拖拉的節點允許的效果，後者設定接受拖拉的區域的效果，它們往往配合使用。

`dragstart`事件的監聽函式，可以用來設定這個屬性。其他事件的監聽函式裡面設定這個屬性是無效的。

```javascript
source.addEventListener('dragstart', function (e) {
  e.dataTransfer.effectAllowed = 'move';
});

target.addEventListener('dragover', function (e) {
  ev.dataTransfer.dropEffect = 'move';
});
```

只要`dropEffect`屬性和`effectAllowed`屬性之中，有一個為`none`，就無法在目標節點上完成`drop`操作。

### DataTransfer.files

`DataTransfer.files`屬性是一個 FileList 物件，包含一組本地檔案，可以用來在拖拉操作中傳送。如果本次拖拉不涉及檔案，則該屬性為空的 FileList 物件。

下面就是一個接收拖拉檔案的例子。

```javascript
// HTML 程式碼如下
// <div id="output" style="min-height: 200px;border: 1px solid black;">
//   檔案拖拉到這裡
// </div>

var div = document.getElementById('output');

div.addEventListener("dragenter", function( event ) {
  div.textContent = '';
  event.stopPropagation();
  event.preventDefault();
}, false);

div.addEventListener("dragover", function( event ) {
  event.stopPropagation();
  event.preventDefault();
}, false);

div.addEventListener("drop", function( event ) {
  event.stopPropagation();
  event.preventDefault();
  var files = event.dataTransfer.files;
  for (var i = 0; i < files.length; i++) {
    div.textContent += files[i].name + ' ' + files[i].size + '位元組\n';
  }
}, false);
```

上面程式碼中，透過`dataTransfer.files`屬性讀取被拖拉的檔案的資訊。如果想要讀取檔案內容，就要使用`FileReader`物件。

```javascript
div.addEventListener('drop', function(e) {
  e.preventDefault();
  e.stopPropagation();

  var fileList = e.dataTransfer.files;
  if (fileList.length > 0) {
    var file = fileList[0];
    var reader = new FileReader();
    reader.onloadend = function(e) {
      if (e.target.readyState === FileReader.DONE) {
        var content = reader.result;
        div.innerHTML = 'File: ' + file.name + '\n\n' + content;
      }
    }
    reader.readAsBinaryString(file);
  }
});
```

### DataTransfer.types

`DataTransfer.types`屬性是一個只讀的陣列，每個成員是一個字串，裡面是拖拉的資料格式（通常是 MIME 值）。比如，如果拖拉的是文字，對應的成員就是`text/plain`。

下面是一個例子，透過檢查`dataTransfer`屬性的型別，決定是否允許在當前節點執行`drop`操作。

```javascript
function contains(list, value){
  for (var i = 0; i < list.length; ++i) {
    if(list[i] === value) return true;
  }
  return false;
}

function doDragOver(event) {
  var isLink = contains(event.dataTransfer.types, 'text/uri-list');
  if (isLink) event.preventDefault();
}
```

上面程式碼中，只有當被拖拉的節點有一個是連結時，才允許在當前節點放下。

### DataTransfer.items

`DataTransfer.items`屬性返回一個類似陣列的只讀物件（DataTransferItemList 例項），每個成員就是本次拖拉的一個物件（DataTransferItem 例項）。如果本次拖拉不包含物件，則返回一個空物件。

DataTransferItemList 例項具有以下的屬性和方法。

- `length`：返回成員的數量
- `add(data, type)`：增加一個指定內容和型別（比如`text/html`和`text/plain`）的字串作為成員
- `add(file)`：`add`方法的另一種用法，增加一個檔案作為成員
- `remove(index)`：移除指定位置的成員
- `clear()`：移除所有的成員

DataTransferItem 例項具有以下的屬性和方法。

- `kind`：返回成員的種類（`string`還是`file`）。
- `type`：返回成員的型別（通常是 MIME 值）。
- `getAsFile()`：如果被拖拉是檔案，返回該檔案，否則返回`null`。
- `getAsString(callback)`：如果被拖拉的是字串，將該字元傳入指定的回撥函式處理。該方法是非同步的，所以需要傳入回撥函式。

下面是一個例子。

```javascript
div.addEventListener('drop', function (e) {
  e.preventDefault();
  if (e.dataTransfer.items != null) {
    for (var i = 0; i < e.dataTransfer.items.length; i++) {
      console.log(e.dataTransfer.items[i].kind + ': ' + e.dataTransfer.items[i].type);
    }
  }
});
```

## DataTransfer 的例項方法

### DataTransfer.setData()

`DataTransfer.setData()`方法用來設定拖拉事件所帶有的資料。該方法沒有返回值。

```javascript
event.dataTransfer.setData('text/plain', 'Text to drag');
```

上面程式碼為當前的拖拉事件加入純文字資料。

該方法接受兩個引數，都是字串。第一個引數表示資料型別（比如`text/plain`），第二個引數是具體資料。如果指定型別的資料在`dataTransfer`屬性不存在，那麼這些資料將被加入，否則原有的資料將被新資料替換。

如果是拖拉文字框或者拖拉選中的文字，會預設將對應的文字資料，新增到`dataTransfer`屬性，不用手動指定。

```html
<div draggable="true">
  aaa
</div>
```

上面程式碼中，拖拉這個`<div>`元素會自動帶上文字資料`aaa`。

使用`setData`方法，可以替換到原有資料。

```html
<div
  draggable="true"
  ondragstart="event.dataTransfer.setData('text/plain', 'bbb')"
>
  aaa
</div>
```

上面程式碼中，拖拉資料實際上是`bbb`，而不是`aaa`。

下面是新增其他型別的資料。由於`text/plain`是最普遍支援的格式，為了保證相容性，建議最後總是儲存一份純文字格式的資料。

```javascript
var dt = event.dataTransfer;

// 新增連結
dt.setData('text/uri-list', 'http://www.example.com');
dt.setData('text/plain', 'http://www.example.com');

// 新增 HTML 程式碼
dt.setData('text/html', 'Hello there, <strong>stranger</strong>');
dt.setData('text/plain', 'Hello there, <strong>stranger</strong>');

// 新增影象的 URL
dt.setData('text/uri-list', imageurl);
dt.setData('text/plain', imageurl);
```

可以一次提供多種格式的資料。

```javascript
var dt = event.dataTransfer;
dt.setData('application/x-bookmark', bookmarkString);
dt.setData('text/uri-list', 'http://www.example.com');
dt.setData('text/plain', 'http://www.example.com');
```

上面程式碼中，透過在同一個事件上面，存放三種類型的資料，使得拖拉事件可以在不同的物件上面，`drop`不同的值。注意，第一種格式是一個自定義格式，瀏覽器預設無法讀取，這意味著，只有某個部署了特定程式碼的節點，才可能`drop`（讀取到）這個資料。

### DataTransfer.getData()

`DataTransfer.getData()`方法接受一個字串（表示資料型別）作為引數，返回事件所帶的指定型別的資料（通常是用`setData`方法新增的資料）。如果指定型別的資料不存在，則返回空字串。通常只有`drop`事件觸發後，才能取出資料。

下面是一個`drop`事件的監聽函式，用來取出指定型別的資料。

```javascript
function onDrop(event) {
  var data = event.dataTransfer.getData('text/plain');
  event.target.textContent = data;
  event.preventDefault();
}
```

上面程式碼取出拖拉事件的文字資料，將其替換成當前節點的文字內容。注意，這時還必須取消瀏覽器的預設行為，因為假如使用者拖拉的是一個連結，瀏覽器預設會在當前視窗開啟這個連結。

`getData`方法返回的是一個字串，如果其中包含多項資料，就必須手動解析。

```javascript
function doDrop(event) {
  var lines = event.dataTransfer.getData('text/uri-list').split('\n');
  for (let line of lines) {
    let link = document.createElement('a');
    link.href = line;
    link.textContent = line;
    event.target.appendChild(link);
  }
  event.preventDefault();
}
```

上面程式碼中，`getData`方法返回的是一組連結，就必須自行解析。

型別值指定為`URL`，可以取出第一個有效連結。

```javascript
var link = event.dataTransfer.getData('URL');
```

下面的例子是從多種型別的資料裡面取出資料。

```javascript
function doDrop(event) {
  var types = event.dataTransfer.types;
  var supportedTypes = ['text/uri-list', 'text/plain'];
  types = supportedTypes.filter(function (value) { types.includes(value) });
  if (types.length) {
    var data = event.dataTransfer.getData(types[0]);
  }
  event.preventDefault();
}
```

### DataTransfer.clearData()

`DataTransfer.clearData()`方法接受一個字串（表示資料型別）作為引數，刪除事件所帶的指定型別的資料。如果沒有指定型別，則刪除所有資料。如果指定型別不存在，則呼叫該方法不會產生任何效果。

```javascript
event.dataTransfer.clearData('text/uri-list');
```

上面程式碼清除事件所帶的`text/uri-list`型別的資料。

該方法不會移除拖拉的檔案，因此呼叫該方法後，`DataTransfer.types`屬性可能依然會返回`Files`型別（前提是存在檔案拖拉）。

注意，該方法只能在`dragstart`事件的監聽函式之中使用，因為這是拖拉操作的資料唯一可寫的時機。

### DataTransfer.setDragImage()

拖動過程中（`dragstart`事件觸發後），瀏覽器會顯示一張圖片跟隨滑鼠一起移動，表示被拖動的節點。這張圖片是自動創造的，通常顯示為被拖動節點的外觀，不需要自己動手設定。

`DataTransfer.setDragImage()`方法可以自定義這張圖片。它接受三個引數。第一個是`<img>`節點或者`<canvas>`節點，如果省略或為`null`，則使用被拖動的節點的外觀；第二個和第三個引數為滑鼠相對於該圖片左上角的橫座標和縱座標。

下面是一個例子。

```javascript
/* HTML 程式碼如下
 <div id="drag-with-image" class="dragdemo" draggable="true">
   drag me
 </div>
*/

var div = document.getElementById('drag-with-image');
div.addEventListener('dragstart', function (e) {
  var img = document.createElement('img');
  img.src = 'http://path/to/img';
  e.dataTransfer.setDragImage(img, 0, 0);
}, false);
```
