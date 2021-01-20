# <button> 元素

`<button>`元素繼承了`HTMLButtonElement`介面。它有以下的例項屬性。

**（1）HTMLButtonElement.accessKey**

`HTMLButtonElement.accessKey`屬性返回一個字串，表示鍵盤上對應的鍵，透過`Alt + 這個鍵`可以讓按鈕獲得焦點。該屬性可讀寫。

**（2）HTMLButtonElement.autofocus**

`HTMLButtonElement.autofocus`屬性是一個布林值，表示頁面載入過程中，按鈕是否會自動獲得焦點。該屬性可讀寫。

**（3）HTMLButtonElement.disabled**

`HTMLButtonElement.disabled`屬性是一個布林值，表示該按鈕是否禁止點選。該屬性可讀寫。

**（4）HTMLButtonElement.form**

`HTMLButtonElement.form`屬性是一個表單元素，返回該按鈕所在的表單。該屬性只讀。如果按鈕不屬於任何表單，該屬性返回`null`。

**（5）HTMLButtonElement.formAction**

`HTMLButtonElement.formAction`返回一個字串，表示表單提交的 URL。該屬性可讀寫，一旦設定了值，點選按鈕就會提交到該屬性指定的 URL，而不是`<form>`元素指定的 URL。

**（6）HTMLButtonElement.formEnctype**

`HTMLButtonElement.formEnctype`屬性是一個字串，表示資料提交到伺服器的編碼型別。該屬性可讀寫，一旦設定了值，點選按鈕會按照該屬性指定的編碼方式，而不是`<form>`元素指定的編碼方式。

該屬性可以取以下的值。

- `application/x-www-form-urlencoded`（預設值）
- `multipart/form-data`（上傳檔案的編碼方式）
- `text/plain`

**（7）HTMLButtonElement.formMethod**

`HTMLButtonElement.formMethod`屬性是一個字串，表示瀏覽器提交表單的 HTTP 方法。該屬性可讀寫，一旦設定了值，點選後就會採用該屬性指定的 HTTP 方法，而不是`<form>`元素指定的編碼方法。

**（8）HTMLButtonElement.formNoValidate**

`HTMLButtonElement.formNoValidate`屬性是一個布林值，表示點選按鈕提交表單時，是否要跳過表單校驗的步驟。該屬性可讀寫，一旦設定會覆蓋`<form>`元素的`novalidate`屬性。

**（9）HTMLButtonElement.formTarget**

`HTMLButtonElement.formTarget`屬性是一個字串，指定了提交了表單以後，哪個視窗展示伺服器返回的內容。該屬性可讀寫，一旦設定會覆蓋`<form>`元素的`target`屬性。

**（10）HTMLButtonElement.labels**

`HTMLButtonElement.labels`返回`NodeList`例項，表示那些繫結按鈕的`<label>`元素。該屬性只讀。

```javascript
/* HTML 程式碼如下
  <label id="label1" for="test">Label 1</label>
  <button id="test">Button</button>
  <label id="label2" for="test">Label 2</label>
*/

const button = document.getElementById('test');

for(var i = 0; i < button.labels.length; i++) {
  console.log(button.labels[i].textContent);
}
// "Label 1"
// "Label 2"
```

上面程式碼中，兩個`<label>`元素繫結`<button>`元素。`button.labels`返回這兩個`<label>`元素。

**（11）HTMLButtonElement.name**

`HTMLButtonElement.name`屬性是一個字串，表示按鈕元素的`name`屬性。如果沒有設定`name`屬性，則返回空字串。該屬性可讀寫。

**（12）HTMLButtonElement.tabIndex**

`HTMLButtonElement.tabIndex`是一個整數，代表按鈕元素的 Tab 鍵順序。該屬性可讀寫。

**（13）HTMLButtonElement.type**

`HTMLButtonElement.type`屬性是一個字串，表示按鈕的行為。該屬性可讀寫，可能取以下的值。

- `submit`：預設值，表示提交表單。
- `reset`：重置表單。
- `button`：沒有任何預設行為。

**（14）HTMLButtonElement.validationMessage**

`HTMLButtonElement.validationMessage`屬性是一個字串，表示沒有透過校驗時顯示的提示資訊。該屬性只讀。

**（15）HTMLButtonElement.validity**

`HTMLButtonElement.validity`屬性返回該按鈕的校驗狀態（`ValidityState`）。該屬性只讀。

**（16）HTMLButtonElement.value**

`HTMLButtonElement.value`屬性返回該按鈕繫結的值。該屬性可讀寫。

**（17）HTMLButtonElement.willValidate**

`HTMLButtonElement.willValidate`屬性是一個布林值，表示該按鈕提交表單時是否將被校驗，預設為`false`。該屬性只讀。
