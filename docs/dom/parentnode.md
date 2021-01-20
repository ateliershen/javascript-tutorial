# ParentNode 介面，ChildNode 介面

節點物件除了繼承 Node 介面以外，還擁有其他介面。`ParentNode`介面表示當前節點是一個父節點，提供一些處理子節點的方法。`ChildNode`介面表示當前節點是一個子節點，提供一些相關方法。

## ParentNode 介面

如果當前節點是父節點，就會混入了（mixin）`ParentNode`介面。由於只有元素節點（element）、文件節點（document）和文件片段節點（documentFragment）擁有子節點，因此只有這三類節點會擁有`ParentNode`介面。

### ParentNode.children

`children`屬性返回一個`HTMLCollection`例項，成員是當前節點的所有元素子節點。該屬性只讀。

下面是遍歷某個節點的所有元素子節點的示例。

```javascript
for (var i = 0; i < el.children.length; i++) {
  // ...
}
```

注意，`children`屬性只包括元素子節點，不包括其他型別的子節點（比如文字子節點）。如果沒有元素型別的子節點，返回值`HTMLCollection`例項的`length`屬性為`0`。

另外，`HTMLCollection`是動態集合，會實時反映 DOM 的任何變化。

### ParentNode.firstElementChild

`firstElementChild`屬性返回當前節點的第一個元素子節點。如果沒有任何元素子節點，則返回`null`。

```javascript
document.firstElementChild.nodeName
// "HTML"
```

上面程式碼中，`document`節點的第一個元素子節點是`<HTML>`。

### ParentNode.lastElementChild

`lastElementChild`屬性返回當前節點的最後一個元素子節點，如果不存在任何元素子節點，則返回`null`。

```javascript
document.lastElementChild.nodeName
// "HTML"
```

上面程式碼中，`document`節點的最後一個元素子節點是`<HTML>`（因為`document`只包含這一個元素子節點）。

### ParentNode.childElementCount

`childElementCount`屬性返回一個整數，表示當前節點的所有元素子節點的數目。如果不包含任何元素子節點，則返回`0`。

```javascript
document.body.childElementCount // 13
```

### ParentNode.append()，ParentNode.prepend()

**（1）ParentNode.append()**

`append()`方法為當前節點追加一個或多個子節點，位置是最後一個元素子節點的後面。

該方法不僅可以新增元素子節點（引數為元素節點），還可以新增文字子節點（引數為字串）。

```javascript
var parent = document.body;

// 新增元素子節點
var p = document.createElement('p');
parent.append(p);

// 新增文字子節點
parent.append('Hello');

// 新增多個元素子節點
var p1 = document.createElement('p');
var p2 = document.createElement('p');
parent.append(p1, p2);

// 新增元素子節點和文字子節點
var p = document.createElement('p');
parent.append('Hello', p);
```

該方法沒有返回值。

注意，該方法與`Node.prototype.appendChild()`方法有三點不同。

- `append()`允許字串作為引數，`appendChild()`只允許子節點作為引數。
- `append()`沒有返回值，而`appendChild()`返回新增的子節點。
- `append()`可以新增多個子節點和字串（即允許多個引數），`appendChild()`只能新增一個節點（即只允許一個引數）。

**（2）ParentNode.prepend()**

`prepend()`方法為當前節點追加一個或多個子節點，位置是第一個元素子節點的前面。它的用法與`append()`方法完全一致，也是沒有返回值。

## ChildNode 介面

如果一個節點有父節點，那麼該節點就擁有了`ChildNode`介面。

### ChildNode.remove()

`remove()`方法用於從父節點移除當前節點。

```javascript
el.remove()
```

上面程式碼在 DOM 裡面移除了`el`節點。

### ChildNode.before()，ChildNode.after()

**（1）ChildNode.before()**

`before()`方法用於在當前節點的前面，插入一個或多個同級節點。兩者擁有相同的父節點。

注意，該方法不僅可以插入元素節點，還可以插入文字節點。

```javascript
var p = document.createElement('p');
var p1 = document.createElement('p');

// 插入元素節點
el.before(p);

// 插入文字節點
el.before('Hello');

// 插入多個元素節點
el.before(p, p1);

// 插入元素節點和文字節點
el.before(p, 'Hello');
```

**（2）ChildNode.after()**

`after()`方法用於在當前節點的後面，插入一個或多個同級節點，兩者擁有相同的父節點。用法與`before`方法完全相同。

### ChildNode.replaceWith()

`replaceWith()`方法使用引數節點，替換當前節點。引數可以是元素節點，也可以是文字節點。

```javascript
var span = document.createElement('span');
el.replaceWith(span);
```

上面程式碼中，`el`節點將被`span`節點替換。

