# 表單事件

## 表單事件的種類

### input 事件

`input`事件當`<input>`、`<select>`、`<textarea>`的值發生變化時觸發。對於複選框（`<input type=checkbox>`）或單選框（`<input type=radio>`），使用者改變選項時，也會觸發這個事件。另外，對於開啟`contenteditable`屬性的元素，只要值發生變化，也會觸發`input`事件。

`input`事件的一個特點，就是會連續觸發，比如使用者每按下一次按鍵，就會觸發一次`input`事件。

`input`事件物件繼承了`InputEvent`介面。

該事件跟`change`事件很像，不同之處在於`input`事件在元素的值發生變化後立即發生，而`change`在元素失去焦點時發生，而內容此時可能已經變化多次。也就是說，如果有連續變化，`input`事件會觸發多次，而`change`事件只在失去焦點時觸發一次。

下面是`<select>`元素的例子。

```javascript
/* HTML 程式碼如下
<select id="mySelect">
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>
*/

function inputHandler(e) {
  console.log(e.target.value)
}

var mySelect = document.querySelector('#mySelect');
mySelect.addEventListener('input', inputHandler);
```

上面程式碼中，改變下拉框選項時，會觸發`input`事件，從而執行回撥函式`inputHandler`。

### select 事件

`select`事件當在`<input>`、`<textarea>`裡面選中文字時觸發。

```javascript
// HTML 程式碼如下
// <input id="test" type="text" value="Select me!" />

var elem = document.getElementById('test');
elem.addEventListener('select', function (e) {
  console.log(e.type); // "select"
}, false);
```

選中的文字可以透過`event.target`元素的`selectionDirection`、`selectionEnd`、`selectionStart`和`value`屬性拿到。

### change 事件

`change`事件當`<input>`、`<select>`、`<textarea>`的值發生變化時觸發。它與`input`事件的最大不同，就是不會連續觸發，只有當全部修改完成時才會觸發，另一方面`input`事件必然伴隨`change`事件。具體來說，分成以下幾種情況。

- 啟用單選框（radio）或複選框（checkbox）時觸發。
- 使用者提交時觸發。比如，從下列列表（select）完成選擇，在日期或檔案輸入框完成選擇。
- 當文字框或`<textarea>`元素的值發生改變，並且喪失焦點時觸發。

下面是一個例子。

```javascript
// HTML 程式碼如下
// <select size="1" onchange="changeEventHandler(event);">
//   <option>chocolate</option>
//   <option>strawberry</option>
//   <option>vanilla</option>
// </select>

function changeEventHandler(event) {
  console.log(event.target.value);
}
```

如果比較一下上面`input`事件的例子，你會發現對於`<select>`元素來說，`input`和`change`事件基本是等價的。

### invalid 事件

使用者提交表單時，如果表單元素的值不滿足校驗條件，就會觸發`invalid`事件。

```html
<form>
  <input type="text" required oninvalid="console.log('invalid input')" />
  <button type="submit">提交</button>
</form>
```

上面程式碼中，輸入框是必填的。如果不填，使用者點選按鈕提交時，就會觸發輸入框的`invalid`事件，導致提交被取消。

### reset 事件，submit 事件

這兩個事件發生在表單物件`<form>`上，而不是發生在表單的成員上。

`reset`事件當表單重置（所有表單成員變回預設值）時觸發。

`submit`事件當表單資料向伺服器提交時觸發。注意，`submit`事件的發生物件是`<form>`元素，而不是`<button>`元素，因為提交的是表單，而不是按鈕。

## InputEvent 介面

`InputEvent`介面主要用來描述`input`事件的例項。該介面繼承了`Event`介面，還定義了一些自己的例項屬性和例項方法。

瀏覽器原生提供`InputEvent()`建構函式，用來生成例項物件。

```javascript
new InputEvent(type, options)
```

`InputEvent`建構函式可以接受兩個引數。第一個引數是字串，表示事件名稱，該引數是必需的。第二個引數是一個配置物件，用來設定事件例項的屬性，該引數是可選的。配置物件的欄位除了`Event`建構函式的配置屬性，還可以設定下面的欄位，這些欄位都是可選的。

- `inputType`：字串，表示發生變更的型別（詳見下文）。
- `data`：字串，表示插入的字串。如果沒有插入的字串（比如刪除操作），則返回`null`或空字串。
- `dataTransfer`：返回一個 DataTransfer 物件例項，該屬性通常只在輸入框接受富文字輸入時有效。

`InputEvent`的例項屬性主要就是上面三個屬性，這三個例項屬性都是隻讀的。

**（1）InputEvent.data**

`InputEvent.data`屬性返回一個字串，表示變動的內容。

```javascript
// HTML 程式碼如下
// <input type="text" id="myInput">
var input = document.getElementById('myInput');
input.addEventListener('input', myFunction, false);

function myFunction(e) {
  console.log(e.data);
}
```

上面程式碼中，如果手動在輸入框裡面輸入`abc`，控制檯會先輸出`a`，再在下一行輸出`b`，再在下一行輸出`c`。然後選中`abc`，一次性將它們刪除，控制檯會輸出`null`或一個空字串。

**（2）InputEvent.inputType**

`InputEvent.inputType`屬性返回一個字串，表示字串發生變更的型別。

對於常見情況，Chrome 瀏覽器的返回值如下。完整列表可以參考[文件](https://w3c.github.io/input-events/index.html#dom-inputevent-inputtype)。

- 手動插入文字：`insertText`
- 貼上插入文字：`insertFromPaste`
- 向後刪除：`deleteContentBackward`
- 向前刪除：`deleteContentForward`

**（3）InputEvent.dataTransfer**

`InputEvent.dataTransfer`屬性返回一個 DataTransfer 例項。該屬性只在文字框接受貼上內容（insertFromPaste）或拖拽內容（`insertFromDrop`）時才有效。
