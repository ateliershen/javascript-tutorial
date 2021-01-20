# <form> 元素

`<form>`元素代表了表單，繼承了 HTMLFormElement 介面。

## HTMLFormElement 的例項屬性

- `elements`：返回一個類似陣列的物件，成員是屬於該表單的所有控制元件元素。該屬性只讀。
- `length`：返回一個整數，表示屬於該表單的控制元件數量。該屬性只讀。
- `name`：字串，表示該表單的名稱。
- `method`：字串，表示提交給伺服器時所使用的 HTTP 方法。
- `target`：字串，表示表單提交後，伺服器返回的資料的展示位置。
- `action`：字串，表示表單提交資料的 URL。
- `enctype`（或`encoding`）：字串，表示表單提交資料的編碼方法，可能的值有`application/x-www-form-urlencoded`、`multipart/form-data`和`text/plain`。
- `acceptCharset`：字串，表示伺服器所能接受的字元編碼，多個編碼格式之間使用逗號或空格分隔。
- `autocomplete`：字串`on`或`off`，表示瀏覽器是否要對`<input>`控制元件提供自動補全。
- `noValidate`：布林值，表示是否關閉表單的自動校驗。

## HTMLFormElement 的例項方法

- `submit()`：提交表單，但是不會觸發`submit`事件和表單的自動校驗。
- `reset()`：重置表單控制元件的值為預設值。
- `checkValidity()`：如果控制元件能夠透過自動校驗，返回`true`，否則返回`false`，同時觸發`invalid`事件。

下面是一個建立表單並提交的例子。

```javascript
var f = document.createElement('form');
document.body.appendChild(f);
f.action = '/cgi-bin/some.cgi';
f.method = 'POST';
f.submit();
```
