# 鍵盤事件

## 鍵盤事件的種類

鍵盤事件由使用者擊打鍵盤觸發，主要有`keydown`、`keypress`、`keyup`三個事件，它們都繼承了`KeyboardEvent`介面。

- `keydown`：按下鍵盤時觸發。
- `keypress`：按下有值的鍵時觸發，即按下 Ctrl、Alt、Shift、Meta 這樣無值的鍵，這個事件不會觸發。對於有值的鍵，按下時先觸發`keydown`事件，再觸發這個事件。
- `keyup`：鬆開鍵盤時觸發該事件。

如果使用者一直按鍵不鬆開，就會連續觸發鍵盤事件，觸發的順序如下。

1. keydown
1. keypress
1. keydown
1. keypress
1. ...（重複以上過程）
1. keyup

## KeyboardEvent 介面概述

`KeyboardEvent`介面用來描述使用者與鍵盤的互動。這個介面繼承了`Event`介面，並且定義了自己的例項屬性和例項方法。

瀏覽器原生提供`KeyboardEvent`建構函式，用來新建鍵盤事件的例項。

```javascript
new KeyboardEvent(type, options)
```

`KeyboardEvent`建構函式接受兩個引數。第一個引數是字串，表示事件型別；第二個引數是一個事件配置物件，該引數可選。除了`Event`介面提供的屬性，還可以配置以下欄位，它們都是可選。

- `key`：字串，當前按下的鍵，預設為空字串。
- `code`：字串，表示當前按下的鍵的字串形式，預設為空字串。
- `location`：整數，當前按下的鍵的位置，預設為`0`。
- `ctrlKey`：布林值，是否按下 Ctrl 鍵，預設為`false`。
- `shiftKey`：布林值，是否按下 Shift 鍵，預設為`false`。
- `altKey`：布林值，是否按下 Alt 鍵，預設為`false`。
- `metaKey`：布林值，是否按下 Meta 鍵，預設為`false`。
- `repeat`：布林值，是否重複按鍵，預設為`false`。

## KeyboardEvent 的例項屬性

### KeyboardEvent.altKey，KeyboardEvent.ctrlKey，KeyboardEvent.metaKey，KeyboardEvent.shiftKey

以下屬性都是隻讀屬性，返回一個布林值，表示是否按下對應的鍵。

- `KeyboardEvent.altKey`：是否按下 Alt 鍵
- `KeyboardEvent.ctrlKey`：是否按下 Ctrl 鍵
- `KeyboardEvent.metaKey`：是否按下 meta 鍵（Mac 系統是一個四瓣的小花，Windows 系統是 windows 鍵）
- `KeyboardEvent.shiftKey`：是否按下 Shift 鍵

下面是一個示例。

```javascript
function showChar(e) {
  console.log('ALT: ' + e.altKey);
  console.log('CTRL: ' + e.ctrlKey);
  console.log('Meta: ' + e.metaKey);
  console.log('Shift: ' + e.shiftKey);
}

document.body.addEventListener('keydown', showChar, false);
```

### KeyboardEvent.code

`KeyboardEvent.code`屬性返回一個字串，表示當前按下的鍵的字串形式。該屬性只讀。

下面是一些常用鍵的字串形式，其他鍵請查[文件](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code/code_values)。

- 數字鍵0 - 9：返回`digit0` - `digit9`
- 字母鍵A - z：返回`KeyA` - `KeyZ`
- 功能鍵F1 - F12：返回 `F1` - `F12`
- 方向鍵：返回`ArrowDown`、`ArrowUp`、`ArrowLeft`、`ArrowRight`
- Alt 鍵：返回`AltLeft`或`AltRight`
- Shift 鍵：返回`ShiftLeft`或`ShiftRight`
- Ctrl 鍵：返回`ControlLeft`或`ControlRight`

### KeyboardEvent.key

`KeyboardEvent.key`屬性返回一個字串，表示按下的鍵名。該屬性只讀。

如果按下的鍵代表可列印字元，則返回這個字元，比如數字、字母。

如果按下的鍵代表不可列印的特殊字元，則返回預定義的鍵值，比如 Backspace，Tab，Enter，Shift，Control，Alt，CapsLock，Esc，Spacebar，PageUp，PageDown，End，Home，Left，Right，Up，Down，PrintScreen，Insert，Del，Win，F1～F12，NumLock，Scroll 等。

如果同時按下一個控制鍵和一個符號鍵，則返回符號鍵的鍵名。比如，按下 Ctrl + a，則返回`a`；按下 Shift + a，則返回大寫的`A`。

如果無法識別鍵名，返回字串`Unidentified`。

### KeyboardEvent.location

`KeyboardEvent.location`屬性返回一個整數，表示按下的鍵處在鍵盤的哪一個區域。它可能取以下值。

- 0：處在鍵盤的主區域，或者無法判斷處於哪一個區域。
- 1：處在鍵盤的左側，只適用那些有兩個位置的鍵（比如 Ctrl 和 Shift 鍵）。
- 2：處在鍵盤的右側，只適用那些有兩個位置的鍵（比如 Ctrl 和 Shift 鍵）。
- 3：處在數字小鍵盤。

### KeyboardEvent.repeat

`KeyboardEvent.repeat`返回一個布林值，代表該鍵是否被按著不放，以便判斷是否重複這個鍵，即瀏覽器會持續觸發`keydown`和`keypress`事件，直到使用者鬆開手為止。

## KeyboardEvent 的例項方法

### KeyboardEvent.getModifierState()

`KeyboardEvent.getModifierState()`方法返回一個布林值，表示是否按下或啟用指定的功能鍵。它的常用引數如下。

- `Alt`：Alt 鍵
- `CapsLock`：大寫鎖定鍵
- `Control`：Ctrl 鍵
- `Meta`：Meta 鍵
- `NumLock`：數字鍵盤開關鍵
- `Shift`：Shift 鍵

```javascript
if (
  event.getModifierState('Control') +
  event.getModifierState('Alt') +
  event.getModifierState('Meta') > 1
) {
  return;
}
```

上面程式碼表示，只要`Control`、`Alt`、`Meta`裡面，同時按下任意兩個或兩個以上的鍵就返回。
