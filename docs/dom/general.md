# DOM 概述

## DOM

DOM 是 JavaScript 操作網頁的介面，全稱為“文件物件模型”（Document Object Model）。它的作用是將網頁轉為一個 JavaScript 物件，從而可以用指令碼進行各種操作（比如增刪內容）。

瀏覽器會根據 DOM 模型，將結構化文件（比如 HTML 和 XML）解析成一系列的節點，再由這些節點組成一個樹狀結構（DOM Tree）。所有的節點和最終的樹狀結構，都有規範的對外介面。

DOM 只是一個介面規範，可以用各種語言實現。所以嚴格地說，DOM 不是 JavaScript 語法的一部分，但是 DOM 操作是 JavaScript 最常見的任務，離開了 DOM，JavaScript 就無法控制網頁。另一方面，JavaScript 也是最常用於 DOM 操作的語言。後面介紹的就是 JavaScript 對 DOM 標準的實現和用法。

## 節點

DOM 的最小組成單位叫做節點（node）。文件的樹形結構（DOM 樹），就是由各種不同型別的節點組成。每個節點可以看作是文件樹的一片葉子。

節點的型別有七種。

- `Document`：整個文件樹的頂層節點
- `DocumentType`：`doctype`標籤（比如`<!DOCTYPE html>`）
- `Element`：網頁的各種HTML標籤（比如`<body>`、`<a>`等）
- `Attr`：網頁元素的屬性（比如`class="right"`）
- `Text`：標籤之間或標籤包含的文字
- `Comment`：註釋
- `DocumentFragment`：文件的片段

瀏覽器提供一個原生的節點物件`Node`，上面這七種節點都繼承了`Node`，因此具有一些共同的屬性和方法。

## 節點樹

一個文件的所有節點，按照所在的層級，可以抽象成一種樹狀結構。這種樹狀結構就是 DOM 樹。它有一個頂層節點，下一層都是頂層節點的子節點，然後子節點又有自己的子節點，就這樣層層衍生出一個金字塔結構，又像一棵樹。

瀏覽器原生提供`document`節點，代表整個文件。

```javascript
document
// 整個文件樹
```

文件的第一層有兩個節點，第一個是文件型別節點（`<!doctype html>`），第二個是 HTML 網頁的頂層容器標籤`<html>`。後者構成了樹結構的根節點（root node），其他 HTML 標籤節點都是它的下級節點。

除了根節點，其他節點都有三種層級關係。

- 父節點關係（parentNode）：直接的那個上級節點
- 子節點關係（childNodes）：直接的下級節點
- 同級節點關係（sibling）：擁有同一個父節點的節點

DOM 提供操作介面，用來獲取這三種關係的節點。比如，子節點介面包括`firstChild`（第一個子節點）和`lastChild`（最後一個子節點）等屬性，同級節點介面包括`nextSibling`（緊鄰在後的那個同級節點）和`previousSibling`（緊鄰在前的那個同級節點）屬性。
