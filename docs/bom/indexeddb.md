# IndexedDB API

## 概述

隨著瀏覽器的功能不斷增強，越來越多的網站開始考慮，將大量資料儲存在客戶端，這樣可以減少從伺服器獲取資料，直接從本地獲取資料。

現有的瀏覽器資料儲存方案，都不適合儲存大量資料：Cookie 的大小不超過 4KB，且每次請求都會發送回伺服器；LocalStorage 在 2.5MB 到 10MB 之間（各家瀏覽器不同），而且不提供搜尋功能，不能建立自定義的索引。所以，需要一種新的解決方案，這就是 IndexedDB 誕生的背景。

通俗地說，IndexedDB 就是瀏覽器提供的本地資料庫，它可以被網頁尾本建立和操作。IndexedDB 允許儲存大量資料，提供查詢介面，還能建立索引。這些都是 LocalStorage 所不具備的。就資料庫型別而言，IndexedDB 不屬於關係型資料庫（不支援 SQL 查詢語句），更接近 NoSQL 資料庫。

IndexedDB 具有以下特點。

**（1）鍵值對儲存。** IndexedDB 內部採用物件倉庫（object store）存放資料。所有型別的資料都可以直接存入，包括 JavaScript 物件。物件倉庫中，資料以“鍵值對”的形式儲存，每一個數據記錄都有對應的主鍵，主鍵是獨一無二的，不能有重複，否則會丟擲一個錯誤。

**（2）非同步。**  IndexedDB 操作時不會鎖死瀏覽器，使用者依然可以進行其他操作，這與 LocalStorage 形成對比，後者的操作是同步的。非同步設計是為了防止大量資料的讀寫，拖慢網頁的表現。

**（3）支援事務。** IndexedDB 支援事務（transaction），這意味著一系列操作步驟之中，只要有一步失敗，整個事務就都取消，資料庫回滾到事務發生之前的狀態，不存在只改寫一部分資料的情況。

**（4）同源限制。** IndexedDB 受到同源限制，每一個數據庫對應建立它的域名。網頁只能訪問自身域名下的資料庫，而不能訪問跨域的資料庫。

**（5）儲存空間大。** IndexedDB 的儲存空間比 LocalStorage 大得多，一般來說不少於 250MB，甚至沒有上限。

**（6）支援二進位制儲存。** IndexedDB 不僅可以儲存字串，還可以儲存二進位制資料（ArrayBuffer 物件和 Blob 物件）。

## 基本概念

IndexedDB 是一個比較複雜的 API，涉及不少概念。它把不同的實體，抽象成一個個物件介面。學習這個 API，就是學習它的各種物件介面。

- 資料庫：IDBDatabase 物件
- 物件倉庫：IDBObjectStore 物件
- 索引： IDBIndex 物件
- 事務： IDBTransaction 物件
- 操作請求：IDBRequest 物件
- 指標： IDBCursor 物件
- 主鍵集合：IDBKeyRange 物件

下面是一些主要的概念。

**（1）資料庫**

資料庫是一系列相關資料的容器。每個域名（嚴格的說，是協議 + 域名 + 埠）都可以新建任意多個數據庫。

IndexedDB 資料庫有版本的概念。同一個時刻，只能有一個版本的資料庫存在。如果要修改資料庫結構（新增或刪除表、索引或者主鍵），只能透過升級資料庫版本完成。

**（2）物件倉庫**

每個資料庫包含若干個物件倉庫（object store）。它類似於關係型資料庫的表格。

**（3）資料記錄**

物件倉庫儲存的是資料記錄。每條記錄類似於關係型資料庫的行，但是隻有主鍵和資料體兩部分。主鍵用來建立預設的索引，必須是不同的，否則會報錯。主鍵可以是資料記錄裡面的一個屬性，也可以指定為一個遞增的整數編號。

```javascript
{ id: 1, text: 'foo' }
```

上面的物件中，`id`屬性可以當作主鍵。

資料體可以是任意資料型別，不限於物件。

**（4）索引**

為了加速資料的檢索，可以在物件倉庫裡面，為不同的屬性建立索引。

**（5）事務**

資料記錄的讀寫和刪改，都要透過事務完成。事務物件提供`error`、`abort`和`complete`三個事件，用來監聽操作結果。

## 操作流程

IndexedDB 資料庫的各種操作，一般是按照下面的流程進行的。這個部分只給出簡單的程式碼示例，用於快速上手，詳細的各個物件的 API 放在後文介紹。

### 開啟資料庫

使用 IndexedDB 的第一步是開啟資料庫，使用`indexedDB.open()`方法。

```javascript
var request = window.indexedDB.open(databaseName, version);
```

這個方法接受兩個引數，第一個引數是字串，表示資料庫的名字。如果指定的資料庫不存在，就會新建資料庫。第二個引數是整數，表示資料庫的版本。如果省略，開啟已有資料庫時，預設為當前版本；新建資料庫時，預設為`1`。

`indexedDB.open()`方法返回一個 IDBRequest 物件。這個物件透過三種事件`error`、`success`、`upgradeneeded`，處理開啟資料庫的操作結果。

**（1）error 事件**

`error`事件表示開啟資料庫失敗。

```javascript
request.onerror = function (event) {
  console.log('資料庫開啟報錯');
};
```

**（2）success 事件**

`success`事件表示成功開啟資料庫。

```javascript
var db;

request.onsuccess = function (event) {
  db = request.result;
  console.log('資料庫開啟成功');
};
```

這時，透過`request`物件的`result`屬性拿到資料庫物件。

**（3）upgradeneeded 事件**

如果指定的版本號，大於資料庫的實際版本號，就會發生資料庫升級事件`upgradeneeded`。

```javascript
var db;

request.onupgradeneeded = function (event) {
  db = event.target.result;
}
```

這時透過事件物件的`target.result`屬性，拿到資料庫例項。

### 新建資料庫

新建資料庫與開啟資料庫是同一個操作。如果指定的資料庫不存在，就會新建。不同之處在於，後續的操作主要在`upgradeneeded`事件的監聽函式裡面完成，因為這時版本從無到有，所以會觸發這個事件。

通常，新建資料庫以後，第一件事是新建物件倉庫（即新建表）。

```javascript
request.onupgradeneeded = function(event) {
  db = event.target.result;
  var objectStore = db.createObjectStore('person', { keyPath: 'id' });
}
```

上面程式碼中，資料庫新建成功以後，新增一張叫做`person`的表格，主鍵是`id`。

更好的寫法是先判斷一下，這張表格是否存在，如果不存在再新建。

```javascript
request.onupgradeneeded = function (event) {
  db = event.target.result;
  var objectStore;
  if (!db.objectStoreNames.contains('person')) {
    objectStore = db.createObjectStore('person', { keyPath: 'id' });
  }
}
```

主鍵（key）是預設建立索引的屬性。比如，資料記錄是`{ id: 1, name: '張三' }`，那麼`id`屬性可以作為主鍵。主鍵也可以指定為下一層物件的屬性，比如`{ foo: { bar: 'baz' } }`的`foo.bar`也可以指定為主鍵。

如果資料記錄裡面沒有合適作為主鍵的屬性，那麼可以讓 IndexedDB 自動生成主鍵。

```javascript
var objectStore = db.createObjectStore(
  'person',
  { autoIncrement: true }
);
```

上面程式碼中，指定主鍵為一個遞增的整數。

新建物件倉庫以後，下一步可以新建索引。

```javascript
request.onupgradeneeded = function(event) {
  db = event.target.result;
  var objectStore = db.createObjectStore('person', { keyPath: 'id' });
  objectStore.createIndex('name', 'name', { unique: false });
  objectStore.createIndex('email', 'email', { unique: true });
}
```

上面程式碼中，`IDBObject.createIndex()`的三個引數分別為索引名稱、索引所在的屬性、配置物件（說明該屬性是否包含重複的值）。

### 新增資料

新增資料指的是向物件倉庫寫入資料記錄。這需要透過事務完成。

```javascript
function add() {
  var request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .add({ id: 1, name: '張三', age: 24, email: 'zhangsan@example.com' });

  request.onsuccess = function (event) {
    console.log('資料寫入成功');
  };

  request.onerror = function (event) {
    console.log('資料寫入失敗');
  }
}

add();
```

上面程式碼中，寫入資料需要新建一個事務。新建時必須指定表格名稱和操作模式（“只讀”或“讀寫”）。新建事務以後，透過`IDBTransaction.objectStore(name)`方法，拿到 IDBObjectStore 物件，再透過表格物件的`add()`方法，向表格寫入一條記錄。

寫入操作是一個非同步操作，透過監聽連線物件的`success`事件和`error`事件，瞭解是否寫入成功。

### 讀取資料

讀取資料也是透過事務完成。

```javascript
function read() {
   var transaction = db.transaction(['person']);
   var objectStore = transaction.objectStore('person');
   var request = objectStore.get(1);

   request.onerror = function(event) {
     console.log('事務失敗');
   };

   request.onsuccess = function( event) {
      if (request.result) {
        console.log('Name: ' + request.result.name);
        console.log('Age: ' + request.result.age);
        console.log('Email: ' + request.result.email);
      } else {
        console.log('未獲得資料記錄');
      }
   };
}

read();
```

上面程式碼中，`objectStore.get()`方法用於讀取資料，引數是主鍵的值。

### 遍歷資料

遍歷資料表格的所有記錄，要使用指標物件 IDBCursor。

```javascript
function readAll() {
  var objectStore = db.transaction('person').objectStore('person');

   objectStore.openCursor().onsuccess = function (event) {
     var cursor = event.target.result;

     if (cursor) {
       console.log('Id: ' + cursor.key);
       console.log('Name: ' + cursor.value.name);
       console.log('Age: ' + cursor.value.age);
       console.log('Email: ' + cursor.value.email);
       cursor.continue();
    } else {
      console.log('沒有更多資料了！');
    }
  };
}

readAll();
```

上面程式碼中，新建指標物件的`openCursor()`方法是一個非同步操作，所以要監聽`success`事件。

### 更新資料

更新資料要使用`IDBObject.put()`方法。

```javascript
function update() {
  var request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .put({ id: 1, name: '李四', age: 35, email: 'lisi@example.com' });

  request.onsuccess = function (event) {
    console.log('資料更新成功');
  };

  request.onerror = function (event) {
    console.log('資料更新失敗');
  }
}

update();
```

上面程式碼中，`put()`方法自動更新了主鍵為`1`的記錄。

### 刪除資料

`IDBObjectStore.delete()`方法用於刪除記錄。

```javascript
function remove() {
  var request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .delete(1);

  request.onsuccess = function (event) {
    console.log('資料刪除成功');
  };
}

remove();
```

### 使用索引

索引的意義在於，可以讓你搜索任意欄位，也就是說從任意欄位拿到資料記錄。如果不建立索引，預設只能搜尋主鍵（即從主鍵取值）。

假定新建表格的時候，對`name`欄位建立了索引。

```javascript
objectStore.createIndex('name', 'name', { unique: false });
```

現在，就可以從`name`找到對應的資料記錄了。

```javascript
var transaction = db.transaction(['person'], 'readonly');
var store = transaction.objectStore('person');
var index = store.index('name');
var request = index.get('李四');

request.onsuccess = function (e) {
  var result = e.target.result;
  if (result) {
    // ...
  } else {
    // ...
  }
}
```

## indexedDB 物件

瀏覽器原生提供`indexedDB`物件，作為開發者的操作介面。

### indexedDB.open()

`indexedDB.open()`方法用於開啟資料庫。這是一個非同步操作，但是會立刻返回一個 IDBOpenDBRequest 物件。

```javascript
var openRequest = window.indexedDB.open('test', 1);
```

上面程式碼表示，開啟一個名為`test`、版本為`1`的資料庫。如果該資料庫不存在，則會新建該資料庫。

`open()`方法的第一個引數是資料庫名稱，格式為字串，不可省略；第二個引數是資料庫版本，是一個大於`0`的正整數（`0`將報錯），如果該引數大於當前版本，會觸發資料庫升級。第二個引數可省略，如果資料庫已存在，將開啟當前版本的資料庫；如果資料庫不存在，將建立該版本的資料庫，預設版本為`1`。

開啟資料庫是非同步操作，透過各種事件通知客戶端。下面是有可能觸發的4種事件。

- **success**：開啟成功。
- **error**：開啟失敗。
- **upgradeneeded**：第一次開啟該資料庫，或者資料庫版本發生變化。
- **blocked**：上一次的資料庫連線還未關閉。

第一次開啟資料庫時，會先觸發`upgradeneeded`事件，然後觸發`success`事件。

根據不同的需要，對上面4種事件監聽函式。

```javascript
var openRequest = indexedDB.open('test', 1);
var db;

openRequest.onupgradeneeded = function (e) {
  console.log('Upgrading...');
}

openRequest.onsuccess = function (e) {
  console.log('Success!');
  db = openRequest.result;
}

openRequest.onerror = function (e) {
  console.log('Error');
  console.log(e);
}
```

上面程式碼有兩個地方需要注意。首先，`open()`方法返回的是一個物件（IDBOpenDBRequest），監聽函式就定義在這個物件上面。其次，`success`事件發生後，從`openRequest.result`屬性可以拿到已經開啟的`IndexedDB`資料庫物件。

### indexedDB.deleteDatabase()

`indexedDB.deleteDatabase()`方法用於刪除一個數據庫，引數為資料庫的名字。它會立刻返回一個`IDBOpenDBRequest`物件，然後對資料庫執行非同步刪除。刪除操作的結果會透過事件通知，`IDBOpenDBRequest`物件可以監聽以下事件。

- `success`：刪除成功
- `error`：刪除報錯

```javascript
var DBDeleteRequest = window.indexedDB.deleteDatabase('demo');

DBDeleteRequest.onerror = function (event) {
  console.log('Error');
};

DBDeleteRequest.onsuccess = function (event) {
  console.log('success');
};
```

呼叫`deleteDatabase()`方法以後，當前資料庫的其他已經開啟的連線都會接收到`versionchange`事件。

注意，刪除不存在的資料庫並不會報錯。

### indexedDB.cmp()

`indexedDB.cmp()`方法比較兩個值是否為 indexedDB 的相同的主鍵。它返回一個整數，表示比較的結果：`0`表示相同，`1`表示第一個主鍵大於第二個主鍵，`-1`表示第一個主鍵小於第二個主鍵。

```javascript
window.indexedDB.cmp(1, 2) // -1
```

注意，這個方法不能用來比較任意的 JavaScript 值。如果引數是布林值或物件，它會報錯。

```javascript
window.indexedDB.cmp(1, true) // 報錯
window.indexedDB.cmp({}, {}) // 報錯
```

## IDBRequest 物件

IDBRequest 物件表示開啟的資料庫連線，`indexedDB.open()`方法和`indexedDB.deleteDatabase()`方法會返回這個物件。資料庫的操作都是透過這個物件完成的。

這個物件的所有操作都是非同步操作，要透過`readyState`屬性判斷是否完成，如果為`pending`就表示操作正在進行，如果為`done`就表示操作完成，可能成功也可能失敗。

操作完成以後，觸發`success`事件或`error`事件，這時可以透過`result`屬性和`error`屬性拿到操作結果。如果在`pending`階段，就去讀取這兩個屬性，是會報錯的。

IDBRequest 物件有以下屬性。

- `IDBRequest.readyState`：等於`pending`表示操作正在進行，等於`done`表示操作正在完成。
- `IDBRequest.result`：返回請求的結果。如果請求失敗、結果不可用，讀取該屬性會報錯。
- `IDBRequest.error`：請求失敗時，返回錯誤物件。
- `IDBRequest.source`：返回請求的來源（比如索引物件或 ObjectStore）。
- `IDBRequest.transaction`：返回當前請求正在進行的事務，如果不包含事務，返回`null`。
- `IDBRequest.onsuccess`：指定`success`事件的監聽函式。
- `IDBRequest.onerror`：指定`error`事件的監聽函式。

IDBOpenDBRequest 物件繼承了 IDBRequest 物件，提供了兩個額外的事件監聽屬性。

- `IDBOpenDBRequest.onblocked`：指定`blocked`事件（`upgradeneeded`事件觸發時，資料庫仍然在使用）的監聽函式。
- `IDBOpenDBRequest.onupgradeneeded`：`upgradeneeded`事件的監聽函式。

## IDBDatabase 物件

開啟資料成功以後，可以從`IDBOpenDBRequest`物件的`result`屬性上面，拿到一個`IDBDatabase`物件，它表示連線的資料庫。後面對資料庫的操作，都透過這個物件完成。

```javascript
var db;
var DBOpenRequest = window.indexedDB.open('demo', 1);

DBOpenRequest.onerror = function (event) {
  console.log('Error');
};

DBOpenRequest.onsuccess = function(event) {
  db = DBOpenRequest.result;
  // ...
};
```

### 屬性

IDBDatabase 物件有以下屬性。

- `IDBDatabase.name`：字串，資料庫名稱。
- `IDBDatabase.version`：整數，資料庫版本。資料庫第一次建立時，該屬性為空字串。
- `IDBDatabase.objectStoreNames`：DOMStringList 物件（字串的集合），包含當前資料的所有 object store 的名字。
- `IDBDatabase.onabort`：指定 abort 事件（事務中止）的監聽函式。
- `IDBDatabase.onclose`：指定 close 事件（資料庫意外關閉）的監聽函式。
- `IDBDatabase.onerror`：指定 error 事件（訪問資料庫失敗）的監聽函式。
- `IDBDatabase.onversionchange`：資料庫版本變化時觸發（發生`upgradeneeded`事件，或呼叫`indexedDB.deleteDatabase()`）。

下面是`objectStoreNames`屬性的例子。該屬性返回一個 DOMStringList 物件，包含了當前資料庫所有物件倉庫的名稱（即表名），可以使用 DOMStringList 物件的`contains`方法，檢查資料庫是否包含某個物件倉庫。

```javascript
if (!db.objectStoreNames.contains('firstOS')) {
  db.createObjectStore('firstOS');
}
```

上面程式碼先判斷某個物件倉庫是否存在，如果不存在就建立該物件倉庫。

### 方法

IDBDatabase 物件有以下方法。

- `IDBDatabase.close()`：關閉資料庫連線，實際會等所有事務完成後再關閉。
- `IDBDatabase.createObjectStore()`：建立存放資料的物件倉庫，類似於傳統關係型資料庫的表格，返回一個 IDBObjectStore 物件。該方法只能在`versionchange`事件監聽函式中呼叫。
- `IDBDatabase.deleteObjectStore()`：刪除指定的物件倉庫。該方法只能在`versionchange`事件監聽函式中呼叫。
- `IDBDatabase.transaction()`：返回一個 IDBTransaction 事務物件。

下面是`createObjectStore()`方法的例子。

```javascript
var request = window.indexedDB.open('demo', 2);

request.onupgradeneeded = function (event) {
  var db = event.target.result;

  db.onerror = function(event) {
    console.log('error');
  };

  var objectStore = db.createObjectStore('items');

  // ...
};
```

上面程式碼建立了一個名為`items`的物件倉庫，如果該物件倉庫已經存在，就會丟擲一個錯誤。為了避免出錯，需要用到下文的`objectStoreNames`屬性，檢查已有哪些物件倉庫。

`createObjectStore()`方法還可以接受第二個物件引數，用來設定物件倉庫的屬性。

```javascript
db.createObjectStore('test', { keyPath: 'email' });
db.createObjectStore('test2', { autoIncrement: true });
```

上面程式碼中，`keyPath`屬性表示主鍵（由於主鍵的值不能重複，所以上例存入之前，必須保證資料的`email`屬性值都是不一樣的），預設值為`null`；`autoIncrement`屬性表示，是否使用自動遞增的整數作為主鍵（第一個資料記錄為1，第二個資料記錄為2，以此類推），預設為`false`。一般來說，`keyPath`和`autoIncrement`屬性只要使用一個就夠了，如果兩個同時使用，表示主鍵為遞增的整數，且物件不得缺少`keyPath`指定的屬性。

下面是`deleteObjectStore()`方法的例子。

```javascript
var dbName = 'sampleDB';
var dbVersion = 2;
var request = indexedDB.open(dbName, dbVersion);

request.onupgradeneeded = function(e) {
  var db = request.result;
  if (e.oldVersion < 1) {
    db.createObjectStore('store1');
  }

  if (e.oldVersion < 2) {
    db.deleteObjectStore('store1');
    db.createObjectStore('store2');
  }

  // ...
};
```

下面是`transaction()`方法的例子，該方法用於建立一個數據庫事務，返回一個 IDBTransaction 物件。向資料庫新增資料之前，必須先建立資料庫事務。

```javascript
var t = db.transaction(['items'], 'readwrite');
```

`transaction()`方法接受兩個引數：第一個引數是一個數組，裡面是所涉及的物件倉庫，通常是隻有一個；第二個引數是一個表示操作型別的字串。目前，操作型別只有兩種：`readonly`（只讀）和`readwrite`（讀寫）。新增資料使用`readwrite`，讀取資料使用`readonly`。第二個引數是可選的，省略時預設為`readonly`模式。

## IDBObjectStore 物件

IDBObjectStore 物件對應一個物件倉庫（object store）。`IDBDatabase.createObjectStore()`方法返回的就是一個 IDBObjectStore 物件。

IDBDatabase 物件的`transaction()`返回一個事務物件，該物件的`objectStore()`方法返回 IDBObjectStore 物件，因此可以採用下面的鏈式寫法。

```javascript
db.transaction(['test'], 'readonly')
  .objectStore('test')
  .get(X)
  .onsuccess = function (e) {}
```

### 屬性

IDBObjectStore 物件有以下屬性。

- `IDBObjectStore.indexNames`：返回一個類似陣列的物件（DOMStringList），包含了當前物件倉庫的所有索引。
- `IDBObjectStore.keyPath`：返回當前物件倉庫的主鍵。
- `IDBObjectStore.name`：返回當前物件倉庫的名稱。
- `IDBObjectStore.transaction`：返回當前物件倉庫所屬的事務物件。
- `IDBObjectStore.autoIncrement`：布林值，表示主鍵是否會自動遞增。

### 方法

IDBObjectStore 物件有以下方法。

**（1）IDBObjectStore.add()**

`IDBObjectStore.add()`用於向物件倉庫新增資料，返回一個 IDBRequest 物件。該方法只用於新增資料，如果主鍵相同會報錯，因此更新資料必須使用`put()`方法。

```javascript
objectStore.add(value, key)
```

該方法接受兩個引數，第一個引數是鍵值，第二個引數是主鍵，該引數可選，如果省略預設為`null`。

建立事務以後，就可以獲取物件倉庫，然後使用`add()`方法往裡面新增資料了。

```javascript
var db;
var DBOpenRequest = window.indexedDB.open('demo', 1);

DBOpenRequest.onsuccess = function (event) {
  db = DBOpenRequest.result;
  var transaction = db.transaction(['items'], 'readwrite');

  transaction.oncomplete = function (event) {
    console.log('transaction success');
  };

  transaction.onerror = function (event) {
    console.log('transaction error: ' + transaction.error);
  };

  var objectStore = transaction.objectStore('items');
  var objectStoreRequest = objectStore.add({ foo: 1 });

  objectStoreRequest.onsuccess = function (event) {
    console.log('add data success');
  };

};
```

**（2）IDBObjectStore.put()**

`IDBObjectStore.put()`方法用於更新某個主鍵對應的資料記錄，如果對應的鍵值不存在，則插入一條新的記錄。該方法返回一個 IDBRequest 物件。

```javascript
objectStore.put(item, key)
```

該方法接受兩個引數，第一個引數為新資料，第二個引數為主鍵，該引數可選，且只在自動遞增時才有必要提供，因為那時主鍵不包含在資料值裡面。

**（3）IDBObjectStore.clear()**

`IDBObjectStore.clear()`刪除當前物件倉庫的所有記錄。該方法返回一個 IDBRequest 物件。

```javascript
objectStore.clear()
```

該方法不需要引數。

**（4）IDBObjectStore.delete()**

`IDBObjectStore.delete()`方法用於刪除指定主鍵的記錄。該方法返回一個 IDBRequest 物件。

```javascript
objectStore.delete(Key)
```

該方法的引數為主鍵的值。

**（5）IDBObjectStore.count()**

`IDBObjectStore.count()`方法用於計算記錄的數量。該方法返回一個 IDBRequest 物件。

```javascript
IDBObjectStore.count(key)
```

不帶引數時，該方法返回當前物件倉庫的所有記錄數量。如果主鍵或 IDBKeyRange 物件作為引數，則返回對應的記錄數量。

**（6）IDBObjectStore.getKey()**

`IDBObjectStore.getKey()`用於獲取主鍵。該方法返回一個 IDBRequest 物件。

```javascript
objectStore.getKey(key)
```

該方法的引數可以是主鍵值或 IDBKeyRange 物件。

**（7）IDBObjectStore.get()**

`IDBObjectStore.get()`用於獲取主鍵對應的資料記錄。該方法返回一個 IDBRequest 物件。

```javascript
objectStore.get(key)
```

**（8）IDBObjectStore.getAll()**

`DBObjectStore.getAll()`用於獲取物件倉庫的記錄。該方法返回一個 IDBRequest 物件。

```javascript
// 獲取所有記錄
objectStore.getAll()

// 獲取所有符合指定主鍵或 IDBKeyRange 的記錄
objectStore.getAll(query)

// 指定獲取記錄的數量
objectStore.getAll(query, count)
```

**（9）IDBObjectStore.getAllKeys()**

`IDBObjectStore.getAllKeys()`用於獲取所有符合條件的主鍵。該方法返回一個 IDBRequest 物件。

```javascript
// 獲取所有記錄的主鍵
objectStore.getAllKeys()

// 獲取所有符合條件的主鍵
objectStore.getAllKeys(query)

// 指定獲取主鍵的數量
objectStore.getAllKeys(query, count)
```

**（10）IDBObjectStore.index()**

`IDBObjectStore.index()`方法返回指定名稱的索引物件 IDBIndex。

```javascript
objectStore.index(name)
```

有了索引以後，就可以針對索引所在的屬性讀取資料。

```javascript
var t = db.transaction(['people'], 'readonly');
var store = t.objectStore('people');
var index = store.index('name');

var request = index.get('foo');
```

上面程式碼開啟物件倉庫以後，先用`index()`方法指定獲取`name`屬性的索引，然後用`get()`方法讀取某個`name`屬性(`foo`)對應的資料。如果`name`屬性不是對應唯一值，這時`get()`方法有可能取回多個數據物件。另外，`get()`是非同步方法，讀取成功以後，只能在`success`事件的監聽函式中處理資料。

**（11）IDBObjectStore.createIndex()**

`IDBObjectStore.createIndex()`方法用於新建當前資料庫的一個索引。該方法只能在`VersionChange`監聽函式裡面呼叫。

```javascript
objectStore.createIndex(indexName, keyPath, objectParameters)
```

該方法可以接受三個引數。

- indexName：索引名
- keyPath：主鍵
- objectParameters：配置物件（可選）

第三個引數可以配置以下屬性。

- unique：如果設為`true`，將不允許重複的值
- multiEntry：如果設為`true`，對於有多個值的主鍵陣列，每個值將在索引裡面新建一個條目，否則主鍵陣列對應一個條目。

假定物件倉庫中的資料記錄都是如下的`person`型別。

```javascript
var person = {
  name: name,
  email: email,
  created: new Date()
};
```

可以指定這個物件的某個屬性來建立索引。

```javascript
var store = db.createObjectStore('people', { autoIncrement: true });

store.createIndex('name', 'name', { unique: false });
store.createIndex('email', 'email', { unique: true });
```

上面程式碼告訴索引物件，`name`屬性不是唯一值，`email`屬性是唯一值。

**（12）IDBObjectStore.deleteIndex()**

`IDBObjectStore.deleteIndex()`方法用於刪除指定的索引。該方法只能在`VersionChange`監聽函式裡面呼叫。

```javascript
objectStore.deleteIndex(indexName)
```

**（13）IDBObjectStore.openCursor()**

`IDBObjectStore.openCursor()`用於獲取一個指標物件。

```javascript
IDBObjectStore.openCursor()
```

指標物件可以用來遍歷資料。該物件也是非同步的，有自己的`success`和`error`事件，可以對它們指定監聽函式。

```javascript
var t = db.transaction(['test'], 'readonly');
var store = t.objectStore('test');

var cursor = store.openCursor();

cursor.onsuccess = function (event) {
  var res = event.target.result;
  if (res) {
    console.log('Key', res.key);
    console.dir('Data', res.value);
    res.continue();
  }
}
```

監聽函式接受一個事件物件作為引數，該物件的`target.result`屬性指向當前資料記錄。該記錄的`key`和`value`分別返回主鍵和鍵值（即實際存入的資料）。`continue()`方法將游標移到下一個資料物件，如果當前資料物件已經是最後一個數據了，則游標指向`null`。

`openCursor()`方法的第一個引數是主鍵值，或者一個 IDBKeyRange 物件。如果指定該引數，將只處理包含指定主鍵的記錄；如果省略，將處理所有的記錄。該方法還可以接受第二個引數，表示遍歷方向，預設值為`next`，其他可能的值為`prev`、`nextunique`和`prevunique`。後兩個值表示如果遇到重複值，會自動跳過。

**（14）IDBObjectStore.openKeyCursor()**

`IDBObjectStore.openKeyCursor()`用於獲取一個主鍵指標物件。

```javascript
IDBObjectStore.openKeyCursor()
```

## IDBTransaction 物件

IDBTransaction 物件用來非同步操作資料庫事務，所有的讀寫操作都要透過這個物件進行。

`IDBDatabase.transaction()`方法返回的就是一個 IDBTransaction 物件。

```javascript
var db;
var DBOpenRequest = window.indexedDB.open('demo', 1);

DBOpenRequest.onsuccess = function(event) {
  db = DBOpenRequest.result;
  var transaction = db.transaction(['demo'], 'readwrite');

  transaction.oncomplete = function (event) {
    console.log('transaction success');
  };

  transaction.onerror = function (event) {
    console.log('transaction error: ' + transaction.error);
  };

  var objectStore = transaction.objectStore('demo');
  var objectStoreRequest = objectStore.add({ foo: 1 });

  objectStoreRequest.onsuccess = function (event) {
    console.log('add data success');
  };

};
```

事務的執行順序是按照建立的順序，而不是發出請求的順序。

```javascript
var trans1 = db.transaction('foo', 'readwrite');
var trans2 = db.transaction('foo', 'readwrite');
var objectStore2 = trans2.objectStore('foo')
var objectStore1 = trans1.objectStore('foo')
objectStore2.put('2', 'key');
objectStore1.put('1', 'key');
```

上面程式碼中，`key`對應的鍵值最終是`2`，而不是`1`。因為事務`trans1`先於`trans2`建立，所以首先執行。

注意，事務有可能失敗，只有監聽到事務的`complete`事件，才能保證事務操作成功。

IDBTransaction 物件有以下屬性。

- `IDBTransaction.db`：返回當前事務所在的資料庫物件 IDBDatabase。
- `IDBTransaction.error`：返回當前事務的錯誤。如果事務沒有結束，或者事務成功結束，或者被手動終止，該方法返回`null`。
- `IDBTransaction.mode`：返回當前事務的模式，預設是`readonly`（只讀），另一個值是`readwrite`。
- `IDBTransaction.objectStoreNames`：返回一個類似陣列的物件 DOMStringList，成員是當前事務涉及的物件倉庫的名字。
- `IDBTransaction.onabort`：指定`abort`事件（事務中斷）的監聽函式。
- `IDBTransaction.oncomplete`：指定`complete`事件（事務成功）的監聽函式。
- `IDBTransaction.onerror`：指定`error`事件（事務失敗）的監聽函式。

IDBTransaction 物件有以下方法。

- `IDBTransaction.abort()`：終止當前事務，回滾所有已經進行的變更。
- `IDBTransaction.objectStore(name)`：返回指定名稱的物件倉庫 IDBObjectStore。

## IDBIndex 物件

IDBIndex 物件代表資料庫的索引，透過這個物件可以獲取資料庫裡面的記錄。資料記錄的主鍵預設就是帶有索引，IDBIndex 物件主要用於透過除主鍵以外的其他鍵，建立索引獲取物件。

IDBIndex 是永續性的鍵值對儲存。只要插入、更新或刪除資料記錄，引用的物件庫中的記錄，索引就會自動更新。

`IDBObjectStore.index()`方法可以獲取 IDBIndex 物件。

```javascript
var transaction = db.transaction(['contactsList'], 'readonly');
var objectStore = transaction.objectStore('contactsList');
var myIndex = objectStore.index('lName');

myIndex.openCursor().onsuccess = function (event) {
  var cursor = event.target.result;
  if (cursor) {
    var tableRow = document.createElement('tr');
    tableRow.innerHTML =   '<td>' + cursor.value.id + '</td>'
                         + '<td>' + cursor.value.lName + '</td>'
                         + '<td>' + cursor.value.fName + '</td>'
                         + '<td>' + cursor.value.jTitle + '</td>'
                         + '<td>' + cursor.value.company + '</td>'
                         + '<td>' + cursor.value.eMail + '</td>'
                         + '<td>' + cursor.value.phone + '</td>'
                         + '<td>' + cursor.value.age + '</td>';
    tableEntry.appendChild(tableRow);

    cursor.continue();
  } else {
    console.log('Entries all displayed.');
  }
};
```

IDBIndex 物件有以下屬性。

- `IDBIndex.name`：字串，索引的名稱。
- `IDBIndex.objectStore`：索引所在的物件倉庫。
- `IDBIndex.keyPath`：索引的主鍵。
- `IDBIndex.multiEntry`：布林值，針對`keyPath`為陣列的情況，如果設為`true`，建立陣列時，每個陣列成員都會有一個條目，否則每個陣列都只有一個條目。
- `IDBIndex.unique`：布林值，表示建立索引時是否允許相同的主鍵。

IDBIndex 物件有以下方法，它們都是非同步的，立即返回的都是一個 IDBRequest 物件。

- `IDBIndex.count()`：用來獲取記錄的數量。它可以接受主鍵或 IDBKeyRange 物件作為引數，這時只返回符合主鍵的記錄數量，否則返回所有記錄的數量。
- `IDBIndex.get(key)`：用來獲取符合指定主鍵的資料記錄。
- `IDBIndex.getKey(key)`：用來獲取指定的主鍵。
- `IDBIndex.getAll()`：用來獲取所有的資料記錄。它可以接受兩個引數，都是可選的，第一個引數用來指定主鍵，第二個引數用來指定返回記錄的數量。如果省略這兩個引數，則返回所有記錄。由於獲取成功時，瀏覽器必鬚生成所有物件，所以對效能有影響。如果資料集比較大，建議使用 IDBCursor 物件。
- `IDBIndex.getAllKeys()`：該方法與`IDBIndex.getAll()`方法相似，區別是獲取所有主鍵。
- `IDBIndex.openCursor()`：用來獲取一個 IDBCursor 物件，用來遍歷索引裡面的所有條目。
- `IDBIndex.openKeyCursor()`：該方法與`IDBIndex.openCursor()`方法相似，區別是遍歷所有條目的主鍵。

## IDBCursor 物件

IDBCursor 物件代表指標物件，用來遍歷資料倉庫（IDBObjectStore）或索引（IDBIndex）的記錄。

IDBCursor 物件一般透過`IDBObjectStore.openCursor()`方法獲得。

```javascript
var transaction = db.transaction(['rushAlbumList'], 'readonly');
var objectStore = transaction.objectStore('rushAlbumList');

objectStore.openCursor(null, 'next').onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    var listItem = document.createElement('li');
      listItem.innerHTML = cursor.value.albumTitle + ', ' + cursor.value.year;
      list.appendChild(listItem);

      console.log(cursor.source);
      cursor.continue();
    } else {
      console.log('Entries all displayed.');
    }
  };
};
```

IDBCursor 物件的屬性。

- `IDBCursor.source`：返回正在遍歷的物件倉庫或索引。
- `IDBCursor.direction`：字串，表示指標遍歷的方向。共有四個可能的值：next（從頭開始向後遍歷）、nextunique（從頭開始向後遍歷，重複的值只遍歷一次）、prev（從尾部開始向前遍歷）、prevunique（從尾部開始向前遍歷，重複的值只遍歷一次）。該屬性透過`IDBObjectStore.openCursor()`方法的第二個引數指定，一旦指定就不能改變了。
- `IDBCursor.key`：返回當前記錄的主鍵。
- `IDBCursor.value`：返回當前記錄的資料值。
- `IDBCursor.primaryKey`：返回當前記錄的主鍵。對於資料倉庫（objectStore）來說，這個屬性等同於 IDBCursor.key；對於索引，IDBCursor.key 返回索引的位置值，該屬性返回資料記錄的主鍵。

IDBCursor 物件有如下方法。

- `IDBCursor.advance(n)`：指標向前移動 n 個位置。
- `IDBCursor.continue()`：指標向前移動一個位置。它可以接受一個主鍵作為引數，這時會跳轉到這個主鍵。
- `IDBCursor.continuePrimaryKey()`：該方法需要兩個引數，第一個是`key`，第二個是`primaryKey`，將指標移到符合這兩個引數的位置。
- `IDBCursor.delete()`：用來刪除當前位置的記錄，返回一個 IDBRequest 物件。該方法不會改變指標的位置。
- `IDBCursor.update()`：用來更新當前位置的記錄，返回一個 IDBRequest 物件。它的引數是要寫入資料庫的新的值。

## IDBKeyRange 物件

IDBKeyRange 物件代表資料倉庫（object store）裡面的一組主鍵。根據這組主鍵，可以獲取資料倉庫或索引裡面的一組記錄。

IDBKeyRange 可以只包含一個值，也可以指定上限和下限。它有四個靜態方法，用來指定主鍵的範圍。

- `IDBKeyRange.lowerBound()`：指定下限。
- `IDBKeyRange.upperBound()`：指定上限。
- `IDBKeyRange.bound()`：同時指定上下限。
- `IDBKeyRange.only()`：指定只包含一個值。

下面是一些程式碼例項。

```javascript
// All keys ≤ x
var r1 = IDBKeyRange.upperBound(x);

// All keys < x
var r2 = IDBKeyRange.upperBound(x, true);

// All keys ≥ y
var r3 = IDBKeyRange.lowerBound(y);

// All keys > y
var r4 = IDBKeyRange.lowerBound(y, true);

// All keys ≥ x && ≤ y
var r5 = IDBKeyRange.bound(x, y);

// All keys > x &&< y
var r6 = IDBKeyRange.bound(x, y, true, true);

// All keys > x && ≤ y
var r7 = IDBKeyRange.bound(x, y, true, false);

// All keys ≥ x &&< y
var r8 = IDBKeyRange.bound(x, y, false, true);

// The key = z
var r9 = IDBKeyRange.only(z);
```

`IDBKeyRange.lowerBound()`、`IDBKeyRange.upperBound()`、`IDBKeyRange.bound()`這三個方法預設包括端點值，可以傳入一個布林值，修改這個屬性。

與之對應，IDBKeyRange 物件有四個只讀屬性。

- `IDBKeyRange.lower`：返回下限
- `IDBKeyRange.lowerOpen`：布林值，表示下限是否為開區間（即下限是否排除在範圍之外）
- `IDBKeyRange.upper`：返回上限
- `IDBKeyRange.upperOpen`：布林值，表示上限是否為開區間（即上限是否排除在範圍之外）

IDBKeyRange 例項物件生成以後，將它作為引數輸入 IDBObjectStore 或 IDBIndex 物件的`openCursor()`方法，就可以在所設定的範圍內讀取資料。

```javascript
var t = db.transaction(['people'], 'readonly');
var store = t.objectStore('people');
var index = store.index('name');

var range = IDBKeyRange.bound('B', 'D');

index.openCursor(range).onsuccess = function (e) {
  var cursor = e.target.result;
  if (cursor) {
    console.log(cursor.key + ':');

    for (var field in cursor.value) {
      console.log(cursor.value[field]);
    }
    cursor.continue();
  }
}
```

IDBKeyRange 有一個例項方法`includes(key)`，返回一個布林值，表示某個主鍵是否包含在當前這個主鍵組之內。

```javascript
var keyRangeValue = IDBKeyRange.bound('A', 'K', false, false);

keyRangeValue.includes('F') // true
keyRangeValue.includes('W') // false
```

## 參考連結

- Raymond Camden, [Working With IndexedDB – Part 1](http://net.tutsplus.com/tutorials/javascript-ajax/working-with-indexeddb/)
- Raymond Camden, [Working With IndexedDB – Part 2](http://net.tutsplus.com/tutorials/javascript-ajax/working-with-indexeddb-part-2/)
- Raymond Camden, [Working With IndexedDB - Part 3](https://code.tutsplus.com/tutorials/working-with-indexeddb-part-3--net-36220)
- Tiffany Brown, [An Introduction to IndexedDB](http://dev.opera.com/articles/introduction-to-indexeddb/)
- David Fahlander, [Breaking the Borders of IndexedDB](https://hacks.mozilla.org/2014/06/breaking-the-borders-of-indexeddb/)
- TutorialsPoint, [HTML5 - IndexedDB](https://www.tutorialspoint.com/html5/html5_indexeddb.htm)
