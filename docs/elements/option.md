# <option> 元素

`<option>`元素表示下拉框（`<select>`，`<optgroup>`或`<datalist>`）裡面的一個選項。它是 HTMLOptionElement 介面的例項。

## 屬性

除了繼承 HTMLElement 介面的屬性和方法，HTMLOptionElement 介面具有下面的屬性。

- `disabled`：布林值，表示該項是否可選擇。
- `defaultSelected`：布林值，表示該項是否預設選中。一旦設為`true`，該項的值就是`<select>`的預設值。
- `form`：返回`<option>`所在的表單元素。如果不屬於任何表單，則返回`null`。該屬性只讀。
- `index`：整數，表示該選項在整個下拉列表裡面的位置。該屬性只讀。
- `label`：字串，表示對該選項的說明。如果該屬性未設定，則返回該選項的文字內容。
- `selected`：布林值，表示該選項是否選中。
- `text`：字串，該選項的文字內容。
- `value`：字串，該選項的值。表單提交時，上傳的就是選中項的這個屬性。

## Option() 建構函式

瀏覽器原生提供`Option()`建構函式，用來生成 HTMLOptionElement 例項。

```javascript
new Option(text, value, defaultSelected, selected)
```

它接受四個引數，都是可選的。

- text：字串，表示該選項的文字內容。如果省略，返回空字串。
- value：字串，表示該選項的值。如果省略，預設返回`text`屬性的值。
- defaultSelected：布林值，表示該項是否預設選中，預設為`false`。注意，即使設為`true`，也不代表該項的`selected`屬性為`true`。
- selected：布林值，表示該項是否選中，預設為`false`。

```javascript
var newOption = new Option('hello', 'world', true);

newOption.text // "hello"
newOption.value // "world"
newOption.defaultSelected // true
newOption.selected // false
```

上面程式碼中，`newOption`的`defaultSelected`屬性為`true`，但是它沒有被選中（即`selected`屬性為`false`）。
