# Date 物件

`Date`物件是 JavaScript 原生的時間庫。它以國際標準時間（UTC）1970年1月1日00:00:00作為時間的零點，可以表示的時間範圍是前後各1億天（單位為毫秒）。

## 普通函式的用法

`Date`物件可以作為普通函式直接呼叫，返回一個代表當前時間的字串。

```javascript
Date()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

注意，即使帶有引數，`Date`作為普通函式使用時，返回的還是當前時間。

```javascript
Date(2000, 1, 1)
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

上面程式碼說明，無論有沒有引數，直接呼叫`Date`總是返回當前時間。

## 建構函式的用法

`Date`還可以當作建構函式使用。對它使用`new`命令，會返回一個`Date`物件的例項。如果不加引數，例項代表的就是當前時間。

```javascript
var today = new Date();
```

`Date`例項有一個獨特的地方。其他物件求值的時候，都是預設呼叫`.valueOf()`方法，但是`Date`例項求值的時候，預設呼叫的是`toString()`方法。這導致對`Date`例項求值，返回的是一個字串，代表該例項對應的時間。

```javascript
var today = new Date();

today
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"

// 等同於
today.toString()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

上面程式碼中，`today`是`Date`的例項，直接求值等同於呼叫`toString`方法。

作為建構函式時，`Date`物件可以接受多種格式的引數，返回一個該引數對應的時間例項。

```javascript
// 引數為時間零點開始計算的毫秒數
new Date(1378218728000)
// Tue Sep 03 2013 22:32:08 GMT+0800 (CST)

// 引數為日期字串
new Date('January 6, 2013');
// Sun Jan 06 2013 00:00:00 GMT+0800 (CST)

// 引數為多個整數，
// 代表年、月、日、小時、分鐘、秒、毫秒
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```

關於`Date`建構函式的引數，有幾點說明。

第一點，引數可以是負整數，代表1970年元旦之前的時間。

```javascript
new Date(-1378218728000)
// Fri Apr 30 1926 17:27:52 GMT+0800 (CST)
```

第二點，只要是能被`Date.parse()`方法解析的字串，都可以當作引數。

```javascript
new Date('2013-2-15')
new Date('2013/2/15')
new Date('02/15/2013')
new Date('2013-FEB-15')
new Date('FEB, 15, 2013')
new Date('FEB 15, 2013')
new Date('February, 15, 2013')
new Date('February 15, 2013')
new Date('15 Feb 2013')
new Date('15, February, 2013')
// Fri Feb 15 2013 00:00:00 GMT+0800 (CST)
```

上面多種日期字串的寫法，返回的都是同一個時間。

第三，引數為年、月、日等多個整數時，年和月是不能省略的，其他引數都可以省略的。也就是說，這時至少需要兩個引數，因為如果只使用“年”這一個引數，`Date`會將其解釋為毫秒數。

```javascript
new Date(2013)
// Thu Jan 01 1970 08:00:02 GMT+0800 (CST)
```

上面程式碼中，2013被解釋為毫秒數，而不是年份。

```javascript
new Date(2013, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```

上面程式碼中，不管有幾個引數，返回的都是2013年1月1日零點。

最後，各個引數的取值範圍如下。

- 年：使用四位數年份，比如`2000`。如果寫成兩位數或個位數，則加上`1900`，即`10`代表1910年。如果是負數，表示公元前。
- 月：`0`表示一月，依次類推，`11`表示12月。
- 日：`1`到`31`。
- 小時：`0`到`23`。
- 分鐘：`0`到`59`。
- 秒：`0`到`59`
- 毫秒：`0`到`999`。

注意，月份從`0`開始計算，但是，天數從`1`開始計算。另外，除了日期的預設值為`1`，小時、分鐘、秒鐘和毫秒的預設值都是`0`。

這些引數如果超出了正常範圍，會被自動折算。比如，如果月設為`15`，就折算為下一年的4月。

```javascript
new Date(2013, 15)
// Tue Apr 01 2014 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 0)
// Mon Dec 31 2012 00:00:00 GMT+0800 (CST)
```

上面程式碼的第二個例子，日期設為`0`，就代表上個月的最後一天。

引數還可以使用負數，表示扣去的時間。

```javascript
new Date(2013, -1)
// Sat Dec 01 2012 00:00:00 GMT+0800 (CST)
new Date(2013, 0, -1)
// Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
```

上面程式碼中，分別對月和日使用了負數，表示從基準日扣去相應的時間。

## 日期的運算

型別自動轉換時，`Date`例項如果轉為數值，則等於對應的毫秒數；如果轉為字串，則等於對應的日期字串。所以，兩個日期例項物件進行減法運算時，返回的是它們間隔的毫秒數；進行加法運算時，返回的是兩個字串連線而成的新字串。

```javascript
var d1 = new Date(2000, 2, 1);
var d2 = new Date(2000, 3, 1);

d2 - d1
// 2678400000
d2 + d1
// "Sat Apr 01 2000 00:00:00 GMT+0800 (CST)Wed Mar 01 2000 00:00:00 GMT+0800 (CST)"
```

## 靜態方法

### Date.now()

`Date.now`方法返回當前時間距離時間零點（1970年1月1日 00:00:00 UTC）的毫秒數，相當於 Unix 時間戳乘以1000。

```javascript
Date.now() // 1364026285194
```

### Date.parse()

`Date.parse`方法用來解析日期字串，返回該時間距離時間零點（1970年1月1日 00:00:00）的毫秒數。

日期字串應該符合 RFC 2822 和 ISO 8061 這兩個標準，即`YYYY-MM-DDTHH:mm:ss.sssZ`格式，其中最後的`Z`表示時區。但是，其他格式也可以被解析，請看下面的例子。

```javascript
Date.parse('Aug 9, 1995')
Date.parse('January 26, 2011 13:51:50')
Date.parse('Mon, 25 Dec 1995 13:30:00 GMT')
Date.parse('Mon, 25 Dec 1995 13:30:00 +0430')
Date.parse('2011-10-10')
Date.parse('2011-10-10T14:48:00')
```

上面的日期字串都可以解析。

如果解析失敗，返回`NaN`。

```javascript
Date.parse('xxx') // NaN
```

### Date.UTC()

`Date.UTC`方法接受年、月、日等變數作為引數，返回該時間距離時間零點（1970年1月1日 00:00:00 UTC）的毫秒數。

```javascript
// 格式
Date.UTC(year, month[, date[, hrs[, min[, sec[, ms]]]]])

// 用法
Date.UTC(2011, 0, 1, 2, 3, 4, 567)
// 1293847384567
```

該方法的引數用法與`Date`建構函式完全一致，比如月從`0`開始計算，日期從`1`開始計算。區別在於`Date.UTC`方法的引數，會被解釋為 UTC 時間（世界標準時間），`Date`建構函式的引數會被解釋為當前時區的時間。

## 例項方法

`Date`的例項物件，有幾十個自己的方法，除了`valueOf`和`toString`，可以分為以下三類。

- `to`類：從`Date`物件返回一個字串，表示指定的時間。
- `get`類：獲取`Date`物件的日期和時間。
- `set`類：設定`Date`物件的日期和時間。

### Date.prototype.valueOf()

`valueOf`方法返回例項物件距離時間零點（1970年1月1日00:00:00 UTC）對應的毫秒數，該方法等同於`getTime`方法。

```javascript
var d = new Date();

d.valueOf() // 1362790014817
d.getTime() // 1362790014817
```

預期為數值的場合，`Date`例項會自動呼叫該方法，所以可以用下面的方法計算時間的間隔。

```javascript
var start = new Date();
// ...
var end = new Date();
var elapsed = end - start;
```

### to 類方法

**（1）Date.prototype.toString()**

`toString`方法返回一個完整的日期字串。

```javascript
var d = new Date(2013, 0, 1);

d.toString()
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
d
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
```

因為`toString`是預設的呼叫方法，所以如果直接讀取`Date`例項，就相當於呼叫這個方法。

**（2）Date.prototype.toUTCString()**

`toUTCString`方法返回對應的 UTC 時間，也就是比北京時間晚8個小時。

```javascript
var d = new Date(2013, 0, 1);

d.toUTCString()
// "Mon, 31 Dec 2012 16:00:00 GMT"
```

**（3）Date.prototype.toISOString()**

`toISOString`方法返回對應時間的 ISO8601 寫法。

```javascript
var d = new Date(2013, 0, 1);

d.toISOString()
// "2012-12-31T16:00:00.000Z"
```

注意，`toISOString`方法返回的總是 UTC 時區的時間。

**（4）Date.prototype.toJSON()**

`toJSON`方法返回一個符合 JSON 格式的 ISO 日期字串，與`toISOString`方法的返回結果完全相同。

```javascript
var d = new Date(2013, 0, 1);

d.toJSON()
// "2012-12-31T16:00:00.000Z"
```

**（5）Date.prototype.toDateString()**

`toDateString`方法返回日期字串（不含小時、分和秒）。

```javascript
var d = new Date(2013, 0, 1);
d.toDateString() // "Tue Jan 01 2013"
```

**（6）Date.prototype.toTimeString()**

`toTimeString`方法返回時間字串（不含年月日）。

```javascript
var d = new Date(2013, 0, 1);
d.toTimeString() // "00:00:00 GMT+0800 (CST)"
```

**（7）本地時間**

以下三種方法，可以將 Date 例項轉為表示本地時間的字串。

- `Date.prototype.toLocaleString()`：完整的本地時間。
- `Date.prototype.toLocaleDateString()`：本地日期（不含小時、分和秒）。
- `Date.prototype.toLocaleTimeString()`：本地時間（不含年月日）。

下面是用法例項。

```javascript
var d = new Date(2013, 0, 1);

d.toLocaleString()
// 中文版瀏覽器為"2013年1月1日 上午12:00:00"
// 英文版瀏覽器為"1/1/2013 12:00:00 AM"

d.toLocaleDateString()
// 中文版瀏覽器為"2013年1月1日"
// 英文版瀏覽器為"1/1/2013"

d.toLocaleTimeString()
// 中文版瀏覽器為"上午12:00:00"
// 英文版瀏覽器為"12:00:00 AM"
```

這三個方法都有兩個可選的引數。

```javascript
dateObj.toLocaleString([locales[, options]])
dateObj.toLocaleDateString([locales[, options]])
dateObj.toLocaleTimeString([locales[, options]])
```

這兩個引數中，`locales`是一個指定所用語言的字串，`options`是一個配置物件。下面是`locales`的例子，分別採用`en-US`和`zh-CN`語言設定。

```javascript
var d = new Date(2013, 0, 1);

d.toLocaleString('en-US') // "1/1/2013, 12:00:00 AM"
d.toLocaleString('zh-CN') // "2013/1/1 上午12:00:00"

d.toLocaleDateString('en-US') // "1/1/2013"
d.toLocaleDateString('zh-CN') // "2013/1/1"

d.toLocaleTimeString('en-US') // "12:00:00 AM"
d.toLocaleTimeString('zh-CN') // "上午12:00:00"
```

`options`配置物件有以下屬性。

- `dateStyle`：可能的值為`full`、`long`、`medium`、`short`。
- `timeStyle`：可能的值為`full`、`long`、`medium`、`short`。
- `month`：可能的值為`numeric`、`2-digit`、`long`、`short`、`narrow`。
- `year`：可能的值為`numeric`、`2-digit`。
- `weekday`：可能的值為`long`、`short`、`narrow`。
- `day`、`hour`、`minute`、`second`：可能的值為`numeric`、`2-digit`。
- `timeZone`：可能的值為 IANA 的時區資料庫。
- `timeZooneName`：可能的值為`long`、`short`。
- `hour12`：24小時週期還是12小時週期，可能的值為`true`、`false`。

下面是用法例項。

```javascript
var d = new Date(2013, 0, 1);

d.toLocaleDateString('en-US', {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric'
})
// "Tuesday, January 1, 2013"

d.toLocaleDateString('en-US', {
  day: "2-digit",
  month: "long",
  year: "2-digit"
});
// "January 01, 13"

d.toLocaleTimeString('en-US', {
  timeZone: 'UTC',
  timeZoneName: 'short'
})
// "4:00:00 PM UTC"

d.toLocaleTimeString('en-US', {
  timeZone: 'Asia/Shanghai',
  timeZoneName: 'long'
})
// "12:00:00 AM China Standard Time"

d.toLocaleTimeString('en-US', {
  hour12: false
})
// "00:00:00"

d.toLocaleTimeString('en-US', {
  hour12: true
})
// "12:00:00 AM"
```

### get 類方法

`Date`物件提供了一系列`get*`方法，用來獲取例項物件某個方面的值。

- `getTime()`：返回例項距離1970年1月1日00:00:00的毫秒數，等同於`valueOf`方法。
- `getDate()`：返回例項物件對應每個月的幾號（從1開始）。
- `getDay()`：返回星期幾，星期日為0，星期一為1，以此類推。
- `getFullYear()`：返回四位的年份。
- `getMonth()`：返回月份（0表示1月，11表示12月）。
- `getHours()`：返回小時（0-23）。
- `getMilliseconds()`：返回毫秒（0-999）。
- `getMinutes()`：返回分鐘（0-59）。
- `getSeconds()`：返回秒（0-59）。
- `getTimezoneOffset()`：返回當前時間與 UTC 的時區差異，以分鐘表示，返回結果考慮到了夏令時因素。

所有這些`get*`方法返回的都是整數，不同方法返回值的範圍不一樣。

- 分鐘和秒：0 到 59
- 小時：0 到 23
- 星期：0（星期天）到 6（星期六）
- 日期：1 到 31
- 月份：0（一月）到 11（十二月）

```javascript
var d = new Date('January 6, 2013');

d.getDate() // 6
d.getMonth() // 0
d.getFullYear() // 2013
d.getTimezoneOffset() // -480
```

上面程式碼中，最後一行返回`-480`，即 UTC 時間減去當前時間，單位是分鐘。`-480`表示 UTC 比當前時間少480分鐘，即當前時區比 UTC 早8個小時。

下面是一個例子，計算本年度還剩下多少天。

```javascript
function leftDays() {
  var today = new Date();
  var endYear = new Date(today.getFullYear(), 11, 31, 23, 59, 59, 999);
  var msPerDay = 24 * 60 * 60 * 1000;
  return Math.round((endYear.getTime() - today.getTime()) / msPerDay);
}
```

上面這些`get*`方法返回的都是當前時區的時間，`Date`物件還提供了這些方法對應的 UTC 版本，用來返回 UTC 時間。

- `getUTCDate()`
- `getUTCFullYear()`
- `getUTCMonth()`
- `getUTCDay()`
- `getUTCHours()`
- `getUTCMinutes()`
- `getUTCSeconds()`
- `getUTCMilliseconds()`

```javascript
var d = new Date('January 6, 2013');

d.getDate() // 6
d.getUTCDate() // 5
```

上面程式碼中，例項物件`d`表示當前時區（東八時區）的1月6日0點0分0秒，這個時間對於當前時區來說是1月6日，所以`getDate`方法返回6，對於 UTC 時區來說是1月5日，所以`getUTCDate`方法返回5。

### set 類方法

`Date`物件提供了一系列`set*`方法，用來設定例項物件的各個方面。

- `setDate(date)`：設定例項物件對應的每個月的幾號（1-31），返回改變後毫秒時間戳。
- `setFullYear(year [, month, date])`：設定四位年份。
- `setHours(hour [, min, sec, ms])`：設定小時（0-23）。
- `setMilliseconds()`：設定毫秒（0-999）。
- `setMinutes(min [, sec, ms])`：設定分鐘（0-59）。
- `setMonth(month [, date])`：設定月份（0-11）。
- `setSeconds(sec [, ms])`：設定秒（0-59）。
- `setTime(milliseconds)`：設定毫秒時間戳。

這些方法基本是跟`get*`方法一一對應的，但是沒有`setDay`方法，因為星期幾是計算出來的，而不是設定的。另外，需要注意的是，凡是涉及到設定月份，都是從0開始算的，即`0`是1月，`11`是12月。

```javascript
var d = new Date ('January 6, 2013');

d // Sun Jan 06 2013 00:00:00 GMT+0800 (CST)
d.setDate(9) // 1357660800000
d // Wed Jan 09 2013 00:00:00 GMT+0800 (CST)
```

`set*`方法的引數都會自動折算。以`setDate()`為例，如果引數超過當月的最大天數，則向下一個月順延，如果引數是負數，表示從上個月的最後一天開始減去的天數。

```javascript
var d1 = new Date('January 6, 2013');

d1.setDate(32) // 1359648000000
d1 // Fri Feb 01 2013 00:00:00 GMT+0800 (CST)

var d2 = new Date ('January 6, 2013');

d2.setDate(-1) // 1356796800000
d2 // Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
```

上面程式碼中，`d1.setDate(32)`將日期設為1月份的32號，因為1月份只有31號，所以自動折算為2月1日。`d2.setDate(-1)`表示設為上個月的倒數第二天，即12月30日。

`set`類方法和`get`類方法，可以結合使用，得到相對時間。

```javascript
var d = new Date();

// 將日期向後推1000天
d.setDate(d.getDate() + 1000);
// 將時間設為6小時後
d.setHours(d.getHours() + 6);
// 將年份設為去年
d.setFullYear(d.getFullYear() - 1);
```

`set*`系列方法除了`setTime()`，都有對應的 UTC 版本，即設定 UTC 時區的時間。

- `setUTCDate()`
- `setUTCFullYear()`
- `setUTCHours()`
- `setUTCMilliseconds()`
- `setUTCMinutes()`
- `setUTCMonth()`
- `setUTCSeconds()`

```javascript
var d = new Date('January 6, 2013');
d.getUTCHours() // 16
d.setUTCHours(22) // 1357423200000
d // Sun Jan 06 2013 06:00:00 GMT+0800 (CST)
```

上面程式碼中，本地時區（東八時區）的1月6日0點0分，是 UTC 時區的前一天下午16點。設為 UTC 時區的22點以後，就變為本地時區的上午6點。

## 參考連結

- Rakhitha Nimesh，[Getting Started with the Date Object](http://jspro.com/raw-javascript/beginners-guide-to-javascript-date-and-time/)
- Ilya Kantor, [Date/Time functions](http://javascript.info/tutorial/datetime-functions)
