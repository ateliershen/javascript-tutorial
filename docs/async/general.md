# 非同步操作概述

## 單執行緒模型

單執行緒模型指的是，JavaScript 只在一個執行緒上執行。也就是說，JavaScript 同時只能執行一個任務，其他任務都必須在後面排隊等待。

注意，JavaScript 只在一個執行緒上執行，不代表 JavaScript 引擎只有一個執行緒。事實上，JavaScript 引擎有多個執行緒，單個指令碼只能在一個執行緒上執行（稱為主執行緒），其他執行緒都是在後臺配合。

JavaScript 之所以採用單執行緒，而不是多執行緒，跟歷史有關係。JavaScript 從誕生起就是單執行緒，原因是不想讓瀏覽器變得太複雜，因為多執行緒需要共享資源、且有可能修改彼此的執行結果，對於一種網頁尾本語言來說，這就太複雜了。如果 JavaScript 同時有兩個執行緒，一個執行緒在網頁 DOM 節點上新增內容，另一個執行緒刪除了這個節點，這時瀏覽器應該以哪個執行緒為準？是不是還要有鎖機制？所以，為了避免複雜性，JavaScript 一開始就是單執行緒，這已經成了這門語言的核心特徵，將來也不會改變。

這種模式的好處是實現起來比較簡單，執行環境相對單純；壞處是隻要有一個任務耗時很長，後面的任務都必須排隊等著，會拖延整個程式的執行。常見的瀏覽器無響應（假死），往往就是因為某一段 JavaScript 程式碼長時間執行（比如死迴圈），導致整個頁面卡在這個地方，其他任務無法執行。JavaScript 語言本身並不慢，慢的是讀寫外部資料，比如等待 Ajax 請求返回結果。這個時候，如果對方伺服器遲遲沒有響應，或者網路不通暢，就會導致指令碼的長時間停滯。

如果排隊是因為計算量大，CPU 忙不過來，倒也算了，但是很多時候 CPU 是閒著的，因為 IO 操作（輸入輸出）很慢（比如 Ajax 操作從網路讀取資料），不得不等著結果出來，再往下執行。JavaScript 語言的設計者意識到，這時 CPU 完全可以不管 IO 操作，掛起處於等待中的任務，先執行排在後面的任務。等到 IO 操作返回了結果，再回過頭，把掛起的任務繼續執行下去。這種機制就是 JavaScript 內部採用的“事件迴圈”機制（Event Loop）。

單執行緒模型雖然對 JavaScript 構成了很大的限制，但也因此使它具備了其他語言不具備的優勢。如果用得好，JavaScript 程式是不會出現堵塞的，這就是為什麼 Node 可以用很少的資源，應付大流量訪問的原因。

為了利用多核 CPU 的計算能力，HTML5 提出 Web Worker 標準，允許 JavaScript 指令碼建立多個執行緒，但是子執行緒完全受主執行緒控制，且不得操作 DOM。所以，這個新標準並沒有改變 JavaScript 單執行緒的本質。

## 同步任務和非同步任務

程式裡面所有的任務，可以分成兩類：同步任務（synchronous）和非同步任務（asynchronous）。

同步任務是那些沒有被引擎掛起、在主執行緒上排隊執行的任務。只有前一個任務執行完畢，才能執行後一個任務。

非同步任務是那些被引擎放在一邊，不進入主執行緒、而進入任務佇列的任務。只有引擎認為某個非同步任務可以執行了（比如 Ajax 操作從伺服器得到了結果），該任務（採用回撥函式的形式）才會進入主執行緒執行。排在非同步任務後面的程式碼，不用等待非同步任務結束會馬上執行，也就是說，非同步任務不具有“堵塞”效應。

舉例來說，Ajax 操作可以當作同步任務處理，也可以當作非同步任務處理，由開發者決定。如果是同步任務，主執行緒就等著 Ajax 操作返回結果，再往下執行；如果是非同步任務，主執行緒在發出 Ajax 請求以後，就直接往下執行，等到 Ajax 操作有了結果，主執行緒再執行對應的回撥函式。

## 任務佇列和事件迴圈

JavaScript 執行時，除了一個正在執行的主執行緒，引擎還提供一個任務佇列（task queue），裡面是各種需要當前程式處理的非同步任務。（實際上，根據非同步任務的型別，存在多個任務佇列。為了方便理解，這裡假設只存在一個佇列。）

首先，主執行緒會去執行所有的同步任務。等到同步任務全部執行完，就會去看任務佇列裡面的非同步任務。如果滿足條件，那麼非同步任務就重新進入主執行緒開始執行，這時它就變成同步任務了。等到執行完，下一個非同步任務再進入主執行緒開始執行。一旦任務佇列清空，程式就結束執行。

非同步任務的寫法通常是回撥函式。一旦非同步任務重新進入主執行緒，就會執行對應的回撥函式。如果一個非同步任務沒有回撥函式，就不會進入任務佇列，也就是說，不會重新進入主執行緒，因為沒有用回撥函式指定下一步的操作。

JavaScript 引擎怎麼知道非同步任務有沒有結果，能不能進入主執行緒呢？答案就是引擎在不停地檢查，一遍又一遍，只要同步任務執行完了，引擎就會去檢查那些掛起來的非同步任務，是不是可以進入主執行緒了。這種迴圈檢查的機制，就叫做事件迴圈（Event Loop）。[維基百科](http://en.wikipedia.org/wiki/Event_loop)的定義是：“事件迴圈是一個程式結構，用於等待和傳送訊息和事件（a programming construct that waits for and dispatches events or messages in a program）”。

## 非同步操作的模式

下面總結一下非同步操作的幾種模式。

### 回撥函式

回撥函式是非同步操作最基本的方法。

下面是兩個函式`f1`和`f2`，程式設計的意圖是`f2`必須等到`f1`執行完成，才能執行。

```javascript
function f1() {
  // ...
}

function f2() {
  // ...
}

f1();
f2();
```

上面程式碼的問題在於，如果`f1`是非同步操作，`f2`會立即執行，不會等到`f1`結束再執行。

這時，可以考慮改寫`f1`，把`f2`寫成`f1`的回撥函式。

```javascript
function f1(callback) {
  // ...
  callback();
}

function f2() {
  // ...
}

f1(f2);
```

回撥函式的優點是簡單、容易理解和實現，缺點是不利於程式碼的閱讀和維護，各個部分之間高度[耦合](http://en.wikipedia.org/wiki/Coupling_(computer_programming))（coupling），使得程式結構混亂、流程難以追蹤（尤其是多個回撥函式巢狀的情況），而且每個任務只能指定一個回撥函式。

### 事件監聽

另一種思路是採用事件驅動模式。非同步任務的執行不取決於程式碼的順序，而取決於某個事件是否發生。

還是以`f1`和`f2`為例。首先，為`f1`繫結一個事件（這裡採用的 jQuery 的[寫法](http://api.jquery.com/on/)）。

```javascript
f1.on('done', f2);
```

上面這行程式碼的意思是，當`f1`發生`done`事件，就執行`f2`。然後，對`f1`進行改寫：

```javascript
function f1() {
  setTimeout(function () {
    // ...
    f1.trigger('done');
  }, 1000);
}
```

上面程式碼中，`f1.trigger('done')`表示，執行完成後，立即觸發`done`事件，從而開始執行`f2`。

這種方法的優點是比較容易理解，可以繫結多個事件，每個事件可以指定多個回撥函式，而且可以“[去耦合](http://en.wikipedia.org/wiki/Decoupling)”（decoupling），有利於實現模組化。缺點是整個程式都要變成事件驅動型，執行流程會變得很不清晰。閱讀程式碼的時候，很難看出主流程。

### 釋出/訂閱

事件完全可以理解成“訊號”，如果存在一個“訊號中心”，某個任務執行完成，就向訊號中心“釋出”（publish）一個訊號，其他任務可以向訊號中心“訂閱”（subscribe）這個訊號，從而知道什麼時候自己可以開始執行。這就叫做”[釋出/訂閱模式](http://en.wikipedia.org/wiki/Publish-subscribe_pattern)”（publish-subscribe pattern），又稱“[觀察者模式](http://en.wikipedia.org/wiki/Observer_pattern)”（observer pattern）。

這個模式有多種[實現](http://msdn.microsoft.com/en-us/magazine/hh201955.aspx)，下面採用的是 Ben Alman 的 [Tiny Pub/Sub](https://gist.github.com/661855)，這是 jQuery 的一個外掛。

首先，`f2`向訊號中心`jQuery`訂閱`done`訊號。

```javascript
jQuery.subscribe('done', f2);
```

然後，`f1`進行如下改寫。

```javascript
function f1() {
  setTimeout(function () {
    // ...
    jQuery.publish('done');
  }, 1000);
}
```

上面程式碼中，`jQuery.publish('done')`的意思是，`f1`執行完成後，向訊號中心`jQuery`釋出`done`訊號，從而引發`f2`的執行。

`f2`完成執行後，可以取消訂閱（unsubscribe）。

```javascript
jQuery.unsubscribe('done', f2);
```

這種方法的性質與“事件監聽”類似，但是明顯優於後者。因為可以透過檢視“訊息中心”，瞭解存在多少訊號、每個訊號有多少訂閱者，從而監控程式的執行。

## 非同步操作的流程控制

如果有多個非同步操作，就存在一個流程控制的問題：如何確定非同步操作執行的順序，以及如何保證遵守這種順序。

```javascript
function async(arg, callback) {
  console.log('引數為 ' + arg +' , 1秒後返回結果');
  setTimeout(function () { callback(arg * 2); }, 1000);
}
```

上面程式碼的`async`函式是一個非同步任務，非常耗時，每次執行需要1秒才能完成，然後再呼叫回撥函式。

如果有六個這樣的非同步任務，需要全部完成後，才能執行最後的`final`函式。請問應該如何安排操作流程？

```javascript
function final(value) {
  console.log('完成: ', value);
}

async(1, function (value) {
  async(2, function (value) {
    async(3, function (value) {
      async(4, function (value) {
        async(5, function (value) {
          async(6, final);
        });
      });
    });
  });
});
// 引數為 1 , 1秒後返回結果
// 引數為 2 , 1秒後返回結果
// 引數為 3 , 1秒後返回結果
// 引數為 4 , 1秒後返回結果
// 引數為 5 , 1秒後返回結果
// 引數為 6 , 1秒後返回結果
// 完成:  12
```

上面程式碼中，六個回撥函式的巢狀，不僅寫起來麻煩，容易出錯，而且難以維護。

### 序列執行

我們可以編寫一個流程控制函式，讓它來控制非同步任務，一個任務完成以後，再執行另一個。這就叫序列執行。

```javascript
var items = [ 1, 2, 3, 4, 5, 6 ];
var results = [];

function async(arg, callback) {
  console.log('引數為 ' + arg +' , 1秒後返回結果');
  setTimeout(function () { callback(arg * 2); }, 1000);
}

function final(value) {
  console.log('完成: ', value);
}

function series(item) {
  if(item) {
    async( item, function(result) {
      results.push(result);
      return series(items.shift());
    });
  } else {
    return final(results[results.length - 1]);
  }
}

series(items.shift());
```

上面程式碼中，函式`series`就是序列函式，它會依次執行非同步任務，所有任務都完成後，才會執行`final`函式。`items`陣列儲存每一個非同步任務的引數，`results`陣列儲存每一個非同步任務的執行結果。

注意，上面的寫法需要六秒，才能完成整個指令碼。

### 並行執行

流程控制函式也可以是並行執行，即所有非同步任務同時執行，等到全部完成以後，才執行`final`函式。

```javascript
var items = [ 1, 2, 3, 4, 5, 6 ];
var results = [];

function async(arg, callback) {
  console.log('引數為 ' + arg +' , 1秒後返回結果');
  setTimeout(function () { callback(arg * 2); }, 1000);
}

function final(value) {
  console.log('完成: ', value);
}

items.forEach(function(item) {
  async(item, function(result){
    results.push(result);
    if(results.length === items.length) {
      final(results[results.length - 1]);
    }
  })
});
```

上面程式碼中，`forEach`方法會同時發起六個非同步任務，等到它們全部完成以後，才會執行`final`函式。

相比而言，上面的寫法只要一秒，就能完成整個指令碼。這就是說，並行執行的效率較高，比起序列執行一次只能執行一個任務，較為節約時間。但是問題在於如果並行的任務較多，很容易耗盡系統資源，拖慢執行速度。因此有了第三種流程控制方式。

### 並行與序列的結合

所謂並行與序列的結合，就是設定一個門檻，每次最多隻能並行執行`n`個非同步任務，這樣就避免了過分佔用系統資源。

```javascript
var items = [ 1, 2, 3, 4, 5, 6 ];
var results = [];
var running = 0;
var limit = 2;

function async(arg, callback) {
  console.log('引數為 ' + arg +' , 1秒後返回結果');
  setTimeout(function () { callback(arg * 2); }, 1000);
}

function final(value) {
  console.log('完成: ', value);
}

function launcher() {
  while(running < limit && items.length > 0) {
    var item = items.shift();
    async(item, function(result) {
      results.push(result);
      running--;
      if(items.length > 0) {
        launcher();
      } else if(running == 0) {
        final(results);
      }
    });
    running++;
  }
}

launcher();
```

上面程式碼中，最多隻能同時執行兩個非同步任務。變數`running`記錄當前正在執行的任務數，只要低於門檻值，就再啟動一個新的任務，如果等於`0`，就表示所有任務都執行完了，這時就執行`final`函式。

這段程式碼需要三秒完成整個指令碼，處在序列執行和並行執行之間。透過調節`limit`變數，達到效率和資源的最佳平衡。
