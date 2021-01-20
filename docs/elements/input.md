# <input> 元素

`<input>`元素主要用於表單元件，它繼承了 HTMLInputElement 介面。

## HTMLInputElement 的例項屬性

### 特徵屬性

- `name`：字串，表示`<input>`節點的名稱。該屬性可讀寫。
- `type`：字串，表示`<input>`節點的型別。該屬性可讀寫。
- `disabled`：布林值，表示`<input>`節點是否禁止使用。一旦被禁止使用，表單提交時不會包含該`<input>`節點。該屬性可讀寫。
- `autofocus`：布林值，表示頁面載入時，該元素是否會自動獲得焦點。該屬性可讀寫。
- `required`：布林值，表示表單提交時，該`<input>`元素是否必填。該屬性可讀寫。
- `value`：字串，表示該`<input>`節點的值。該屬性可讀寫。
- `validity`：返回一個`ValidityState`物件，表示`<input>`節點的校驗狀態。該屬性只讀。
- `validationMessage`：字串，表示該`<input>`節點的校驗失敗時，使用者看到的報錯資訊。如果該節點不需要校驗，或者透過校驗，該屬性為空字串。該屬性只讀。
- `willValidate`：布林值，表示表單提交時，該`<input>`元素是否會被校驗。該屬性只讀。

### 表單相關屬性

- `form`：返回`<input>`元素所在的表單（`<form>`）節點。該屬性只讀。
- `formAction`：字串，表示表單提交時的伺服器目標。該屬性可讀寫，一旦設定了這個屬性，會覆蓋表單元素的`action`屬性。
- `formEncType`：字串，表示表單提交時資料的編碼方式。該屬性可讀寫，一旦設定了這個屬性，會覆蓋表單元素的`enctype`的屬性。
- `formMethod`：字串，表示表單提交時的 HTTP 方法。該屬性可讀寫，一旦設定了這個屬性，會覆蓋表單元素的`method`屬性。
- `formNoValidate`：布林值，表示表單提交時，是否要跳過校驗。該屬性可讀寫，一旦設定了這個屬性，會覆蓋表單元素的`formNoValidate`屬性。
- `formTarget`：字串，表示表單提交後，伺服器返回資料的開啟位置。該屬性可讀寫，一旦設定了這個屬性，會覆蓋表單元素的`target`屬性。

### 文字輸入框的特有屬性

以下屬性只有在`<input>`元素可以輸入文字時才有效。

- `autocomplete`：字串`on`或`off`，表示該`<input>`節點的輸入內容可以被瀏覽器自動補全。該屬性可讀寫。
- `maxLength`：整數，表示可以輸入的字串最大長度。如果設為負整數，會報錯。該屬性可讀寫。
- `size`：整數，表示`<input>`節點的顯示長度。如果型別是`text`或`password`，該屬性的單位是字元個數，否則單位是畫素。該屬性可讀寫。
- `pattern`：字串，表示`<input>`節點的值應該滿足的正則表示式。該屬性可讀寫。
- `placeholder`：字串，表示該`<input>`節點的佔位符，作為對元素的提示。該字串不能包含回車或換行。該屬性可讀寫。
- `readOnly`：布林值，表示使用者是否可以修改該節點的值。該屬性可讀寫。
- `min`：字串，表示該節點的最小數值或日期，且不能大於`max`屬性。該屬性可讀寫。
- `max`：字串，表示該節點的最大數值或日期，且不能小於`min`屬性。該屬性可讀寫。
- `selectionStart`：整數，表示選中文字的起始位置。如果沒有選中文字，返回游標在`<input>`元素內部的位置。該屬性可讀寫。
- `selectionEnd`：整數，表示選中文字的結束位置。如果沒有選中文字，返回游標在`<input>`元素內部的位置。該屬性可讀寫。
- `selectionDirection`：字串，表示選中文字的方向。可能的值包括`forward`（與文字書寫方向一致）、`backward`（與文字書寫方向相反）和`none`（文字方向未知）。該屬性可讀寫。

### 複選框和單選框的特有屬性

如果`<input>`元素的型別是複選框（checkbox）或單選框（radio），會有下面的特有屬性。

- `checked`：布林值，表示該`<input>`元素是否選中。該屬性可讀寫。
- `defaultChecked`：布林值，表示該`<input>`元素預設是否選中。該屬性可讀寫。
- `indeterminate`：布林值，表示該`<input>`元素是否還沒有確定的狀態。一旦使用者點選過一次，該屬性就會變成`false`，表示使用者已經給出確定的狀態了。該屬性可讀寫。

### 影象按鈕的特有屬性

如果`<input>`元素的型別是`image`，就會變成一個影象按鈕，會有下面的特有屬性。

- `alt`：字串，影象無法顯示時的替代文字。該屬性可讀寫。
- `height`：字串，表示該元素的高度（單位畫素）。該屬性可讀寫。
- `src`：字串，表示該元素的圖片來源。該屬性可讀寫。
- `width`：字串，表示該元素的寬度（單位畫素）。該屬性可讀寫。

### 檔案上傳按鈕的特有屬性

如果`<input>`元素的型別是`file`，就會變成一個檔案上傳按鈕，會有下面的特有屬性。

- `accept`：字串，表示該元素可以接受的檔案型別，型別之間使用逗號分隔。該屬性可讀寫。
- `files`：返回一個`FileList`例項物件，包含了選中上傳的一組`File`例項物件。

### 其他屬性

- `defaultValue`：字串，表示該`<input>`節點的原始值。
- `dirName`：字串，表示文字方向。
- `accessKey`：字串，表示讓該`<input>`節點獲得焦點的某個字母鍵。
- `list`：返回一個`<datalist>`節點，該節點必須繫結`<input>`元素，且`<input>`元素的型別必須可以輸入文字，否則無效。該屬性只讀。
- `multiple`：布林值，表示是否可以選擇多個值。
- `labels`：返回一個`NodeList`例項，代表綁定當前`<input>`節點的`<label>`元素。該屬性只讀。
- `step`：字串，表示在`min`屬性到`max`屬性之間，每次遞增或遞減時的數值或時間。
- `valueAsDate`：`Date`例項，一旦設定，該`<input>`元素的值會被解釋為指定的日期。如果無法解析該屬性的值，`<input>`節點的值將是`null`。
- `valueAsNumber`：浮點數，當前`<input>`元素的值會被解析為這個數值。

## HTMLInputElement 的例項方法

- `focus()`：當前`<input>`元素獲得焦點。
- `blur()`：移除`<input>`元素的焦點。
- `select()`：選中`<input>`元素內部的所有文字。該方法不能保證`<input>`獲得焦點，最好先用`focus()`方法，再用這個方法。
- `click()`：模擬滑鼠點選當前的`<input>`元素。
- `setSelectionRange()`：選中`<input>`元素內部的一段文字，但不會將焦點轉移到選中的文字。該方法接受三個引數，第一個引數是開始的位置（從0開始），第二個引數是結束的位置（不包括該位置），第三個引數是可選的，表示選擇的方向，有三個可能的值（`forward`、`backward`和預設值`none`）。
- `setRangeText()`：新文字替換選中的文字。該方法接受四個引數，第一個引數是新文字，第二個引數是替換的開始位置(從`0`開始計算)，第三個引數是結束位置（該位置不包括在內），第四個引數表示替換後的行為（可選），有四個可能的值：`select`（選中新插入的文字）、`start`（游標位置移到插入的文字之前）、`end`（游標位置移到插入的文字之後）、`preserve`（預設值，如果原先就有文字被選中且本次替換位置與原先選中位置有交集，則替換後同時選中新插入的文字與原先選中的文字，否則保持原先選中的文字）。
- `setCustomValidity()`：該方法用於自定義校驗失敗時的報錯資訊。它的引數就是報錯的提示資訊。注意，一旦設定了自定義報錯資訊，該欄位就不會校驗通過了，因此使用者重新輸入時，必須將自定義報錯資訊設為空字串，請看下面的例子。
- `checkValidity()`：返回一個布林值，表示當前節點的校驗結果。如果返回`false`，表示不滿足校驗要求，否則就是校驗成功或不必校驗。
- `stepDown()`：將當前`<input>`節點的值減少一個步長。該方法可以接受一個整數`n`作為引數，表示一次性減少`n`個步長，預設是`1`。有幾種情況會拋錯：當前`<input>`節點不適合遞減或遞增、當前節點沒有`step`屬性、`<input>`節點的值不能轉為數字、遞減之後的值小於`min`屬性或大於`max`屬性。
- `stepUp()`：將當前`<input>`節點的值增加一個步長。其他與`stepDown()`方法相同。

下面是`setSelectionRange()`方法的一個例子。

```javascript
/* HTML 程式碼如下
  <p><input type="text" id="mytextbox" size="20" value="HelloWorld"/></p>
  <p><button onclick="SelectText()">選擇文字</button></p>
*/

function SelectText() {
  var input = document.getElementById('mytextbox');
  input.focus();
  input.setSelectionRange(2, 5);
}
```

上面程式碼中，點選按鈕以後，會選中`llo`三個字元。

下面是`setCustomValidity()`的例子。

```javascript
/* HTML 程式碼如下
  <form id="form">
    <input id="field" type="text" pattern="[a-f,0-9]{4}" autocomplete=off>
  </form>
*/

const form   = document.querySelector('#form');
const field  = document.querySelector('#field');

form.addEventListener('submit', (e) => {
  e.preventDefault(); // 防止這個例子發出 POST 請求
});

field.oninvalid = (event) => {
  event.target.setCustomValidity('必須是一個 4 位十六進位制數');
}

field.oninput = (event) => {
  event.target.setCustomValidity('');
}
```

上面程式碼中，輸入框必須輸入一個4位的十六進位制數。如果不滿足條件（比如輸入`xxx`），按下回車鍵以後，就會提示自定義的報錯資訊。一旦自定義了報錯資訊，輸入框就會一直處於校驗失敗狀態，因此重新輸入時，必須把自定義報錯資訊設為空字串。另外，為了避免自動補全提示框遮住報錯資訊，必須將輸入框的`autocomplete`屬性關閉。
