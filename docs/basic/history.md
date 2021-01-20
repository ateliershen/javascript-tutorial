# JavaScript 語言的歷史

## 誕生

JavaScript 因為網際網路而生，緊跟著瀏覽器的出現而問世。回顧它的歷史，就要從瀏覽器的歷史講起。

1990年底，歐洲核能研究組織（CERN）科學家 Tim Berners-Lee，在全世界最大的電腦網路——網際網路的基礎上，發明了全球資訊網（World Wide Web），從此可以在網上瀏覽網頁檔案。最早的網頁只能在作業系統的終端裡瀏覽，也就是說只能使用命令列操作，網頁都是在字元視窗中顯示，這當然非常不方便。

1992年底，美國國家超級電腦應用中心（NCSA）開始開發一個獨立的瀏覽器，叫做 Mosaic。這是人類歷史上第一個瀏覽器，從此網頁可以在圖形介面的視窗瀏覽。

1994年10月，NCSA 的一個主要程式設計師 Marc Andreessen 聯合風險投資家 Jim Clark，成立了 Mosaic 通訊公司（Mosaic Communications），不久後改名為 Netscape。這家公司的方向，就是在 Mosaic 的基礎上，開發面向普通使用者的新一代的瀏覽器 Netscape Navigator。

1994年12月，Navigator 釋出了1.0版，市場份額一舉超過90%。

Netscape 公司很快發現，Navigator 瀏覽器需要一種可以嵌入網頁的指令碼語言，用來控制瀏覽器行為。當時，網速很慢而且上網費很貴，有些操作不宜在伺服器端完成。比如，如果使用者忘記填寫“使用者名稱”，就點了“傳送”按鈕，到伺服器再發現這一點就有點太晚了，最好能在使用者發出資料之前，就告訴使用者“請填寫使用者名稱”。這就需要在網頁中嵌入小程式，讓瀏覽器檢查每一欄是否都填寫了。

管理層對這種瀏覽器指令碼語言的設想是：功能不需要太強，語法較為簡單，容易學習和部署。那一年，正逢 Sun 公司的 Java 語言問世，市場推廣活動非常成功。Netscape 公司決定與 Sun 公司合作，瀏覽器支援嵌入 Java 小程式（後來稱為 Java applet）。但是，瀏覽器指令碼語言是否就選用 Java，則存在爭論。後來，還是決定不使用 Java，因為網頁小程式不需要 Java 這麼“重”的語法。但是，同時也決定指令碼語言的語法要接近 Java，並且可以支援 Java 程式。這些設想直接排除了使用現存語言，比如 Perl、Python 和 TCL。

1995年，Netscape 公司僱傭了程式設計師 Brendan Eich 開發這種網頁尾本語言。Brendan Eich 有很強的函數語言程式設計背景，希望以 Scheme 語言（函式式語言鼻祖 LISP 語言的一種方言）為藍本，實現這種新語言。

1995年5月，Brendan Eich 只用了10天，就設計完成了這種語言的第一版。它是一個大雜燴，語法有多個來源。

- 基本語法：借鑑 C 語言和 Java 語言。
- 資料結構：借鑑 Java 語言，包括將值分成原始值和物件兩大類。
- 函式的用法：借鑑 Scheme 語言和 Awk 語言，將函式當作第一等公民，並引入閉包。
- 原型繼承模型：借鑑 Self 語言（Smalltalk 的一種變種）。
- 正則表示式：借鑑 Perl 語言。
- 字串和陣列處理：借鑑 Python 語言。

為了保持簡單，這種指令碼語言缺少一些關鍵的功能，比如塊級作用域、模組、子型別（subtyping）等等，但是可以利用現有功能找出解決辦法。這種功能的不足，直接導致了後來 JavaScript 的一個顯著特點：對於其他語言，你需要學習語言的各種功能，而對於 JavaScript，你常常需要學習各種解決問題的模式。而且由於來源多樣，從一開始就註定，JavaScript 的程式設計風格是函數語言程式設計和麵向物件程式設計的一種混合體。

Netscape 公司的這種瀏覽器指令碼語言，最初名字叫做 Mocha，1995年9月改為 LiveScript。12月，Netscape 公司與 Sun 公司（Java 語言的發明者和所有者）達成協議，後者允許將這種語言叫做 JavaScript。這樣一來，Netscape 公司可以藉助 Java 語言的聲勢，而 Sun 公司則將自己的影響力擴充套件到了瀏覽器。

之所以起這個名字，並不是因為 JavaScript 本身與 Java 語言有多麼深的關係（事實上，兩者關係並不深，詳見下節），而是因為 Netscape 公司已經決定，使用 Java 語言開發網路應用程式，JavaScript 可以像膠水一樣，將各個部分連線起來。當然，後來的歷史是 Java 語言的瀏覽器外掛失敗了，JavaScript 反而發揚光大。

1995年12月4日，Netscape 公司與 Sun 公司聯合釋出了 JavaScript 語言，對外宣傳 JavaScript 是 Java 的補充，屬於輕量級的 Java，專門用來操作網頁。

1996年3月，Navigator 2.0 瀏覽器正式內建了 JavaScript 指令碼語言。

## JavaScript 與 Java 的關係

這裡專門說一下 JavaScript 和 Java 的關係。它們是兩種不一樣的語言，但是彼此存在聯絡。

JavaScript 的基本語法和物件體系，是模仿 Java 而設計的。但是，JavaScript 沒有采用 Java 的靜態型別。正是因為 JavaScript 與 Java 有很大的相似性，所以這門語言才從一開始的 LiveScript 改名為 JavaScript。基本上，JavaScript 這個名字的原意是“很像Java的指令碼語言”。

JavaScript 語言的函式是一種獨立的資料型別，以及採用基於原型物件（prototype）的繼承鏈。這是它與 Java 語法最大的兩點區別。JavaScript 語法要比 Java 自由得多。

另外，Java 語言需要編譯，而 JavaScript 語言則是執行時由直譯器直接執行。

總之，JavaScript 的原始設計目標是一種小型的、簡單的動態語言，與 Java 有足夠的相似性，使得使用者（尤其是 Java 程式設計師）可以快速上手。

## JavaScript 與 ECMAScript 的關係

1996年8月，微軟模仿 JavaScript 開發了一種相近的語言，取名為JScript（JavaScript 是 Netscape 的註冊商標，微軟不能用），首先內置於IE 3.0。Netscape 公司面臨喪失瀏覽器指令碼語言的主導權的局面。

1996年11月，Netscape 公司決定將 JavaScript 提交給國際標準化組織 ECMA（European Computer Manufacturers Association），希望 JavaScript 能夠成為國際標準，以此抵抗微軟。ECMA 的39號技術委員會（Technical Committee 39）負責制定和稽核這個標準，成員由業內的大公司派出的工程師組成，目前共25個人。該委員會定期開會，所有的郵件討論和會議記錄，都是公開的。

1997年7月，ECMA 組織釋出262號標準檔案（ECMA-262）的第一版，規定了瀏覽器指令碼語言的標準，並將這種語言稱為 ECMAScript。這個版本就是 ECMAScript 1.0 版。之所以不叫 JavaScript，一方面是由於商標的關係，Java 是 Sun 公司的商標，根據一份授權協議，只有 Netscape 公司可以合法地使用 JavaScript 這個名字，且 JavaScript 已經被 Netscape 公司註冊為商標，另一方面也是想體現這門語言的制定者是 ECMA，不是 Netscape，這樣有利於保證這門語言的開放性和中立性。因此，ECMAScript 和 JavaScript 的關係是，前者是後者的規格，後者是前者的一種實現。在日常場合，這兩個詞是可以互換的。

ECMAScript 只用來標準化 JavaScript 這種語言的基本語法結構，與部署環境相關的標準都由其他標準規定，比如 DOM 的標準就是由 W3C組織（World Wide Web Consortium）制定的。

ECMA-262 標準後來也被另一個國際標準化組織 ISO（International Organization for Standardization）批准，標準號是 ISO-16262。

## JavaScript 的版本

1997年7月，ECMAScript 1.0釋出。

1998年6月，ECMAScript 2.0版釋出。

1999年12月，ECMAScript 3.0版釋出，成為 JavaScript 的通行標準，得到了廣泛支援。

2007年10月，ECMAScript 4.0版草案發布，對3.0版做了大幅升級，預計次年8月釋出正式版本。草案發布後，由於4.0版的目標過於激進，各方對於是否透過這個標準，發生了嚴重分歧。以 Yahoo、Microsoft、Google 為首的大公司，反對 JavaScript 的大幅升級，主張小幅改動；以 JavaScript 創造者 Brendan Eich 為首的 Mozilla 公司，則堅持當前的草案。

2008年7月，由於對於下一個版本應該包括哪些功能，各方分歧太大，爭論過於激進，ECMA 開會決定，中止 ECMAScript 4.0 的開發（即廢除了這個版本），將其中涉及現有功能改善的一小部分，釋出為 ECMAScript 3.1，而將其他激進的設想擴大範圍，放入以後的版本，由於會議的氣氛，該版本的專案代號起名為 Harmony（和諧）。會後不久，ECMAScript 3.1 就改名為 ECMAScript 5。

2009年12月，ECMAScript 5.0版 正式釋出。Harmony 專案則一分為二，一些較為可行的設想定名為 JavaScript.next 繼續開發，後來演變成 ECMAScript 6；一些不是很成熟的設想，則被視為 JavaScript.next.next，在更遠的將來再考慮推出。TC39 的總體考慮是，ECMAScript 5 與 ECMAScript 3 基本保持相容，較大的語法修正和新功能加入，將由 JavaScript.next 完成。當時，JavaScript.next 指的是ECMAScript 6。第六版釋出以後，將指 ECMAScript 7。TC39 預計，ECMAScript 5 會在2013年的年中成為 JavaScript 開發的主流標準，並在此後五年中一直保持這個位置。

2011年6月，ECMAScript 5.1版釋出，並且成為 ISO 國際標準（ISO/IEC 16262:2011）。到了2012年底，所有主要瀏覽器都支援 ECMAScript 5.1版的全部功能。

2013年3月，ECMAScript 6 草案凍結，不再新增新功能。新的功能設想將被放到 ECMAScript 7。

2013年12月，ECMAScript 6 草案發布。然後是12個月的討論期，聽取各方反饋。

2015年6月，ECMAScript 6 正式釋出，並且更名為“ECMAScript 2015”。這是因為 TC39 委員會計劃，以後每年釋出一個 ECMAScript 的版本，下一個版本在2016年釋出，稱為“ECMAScript 2016”，2017年釋出“ECMAScript 2017”，以此類推。

## 周邊大事記

JavaScript 伴隨著網際網路的發展一起發展。網際網路周邊技術的快速發展，刺激和推動了 JavaScript 語言的發展。下面，回顧一下 JavaScript 的周邊應用發展。

1996年，樣式表標準 CSS 第一版釋出。

1997年，DHTML（Dynamic HTML，動態 HTML）釋出，允許動態改變網頁內容。這標誌著 DOM 模式（Document Object Model，文件物件模型）正式應用。

1998年，Netscape 公司開源了瀏覽器，這導致了 Mozilla 專案的誕生。幾個月後，美國線上（AOL）宣佈併購 Netscape。

1999年，IE 5部署了 XMLHttpRequest 介面，允許 JavaScript 發出 HTTP 請求，為後來大行其道的 Ajax 應用創造了條件。

2000年，KDE 專案重寫了瀏覽器引擎 KHTML，為後來的 WebKit 和 Blink 引擎打下基礎。這一年的10月23日，KDE 2.0釋出，第一次將 KHTML 瀏覽器包括其中。

2001年，微軟公司時隔5年之後，釋出了 IE 瀏覽器的下一個版本 Internet Explorer 6。這是當時最先進的瀏覽器，它後來統治了瀏覽器市場多年。

2001年，Douglas Crockford 提出了 JSON 格式，用於取代 XML 格式，進行伺服器和網頁之間的資料交換。JavaScript 可以原生支援這種格式，不需要額外部署程式碼。

2002年，Mozilla 專案釋出了它的瀏覽器的第一版，後來起名為 Firefox。

2003年，蘋果公司釋出了 Safari 瀏覽器的第一版。

2004年，Google 公司釋出了 Gmail，促成了網際網路應用程式（Web Application）這個概念的誕生。由於 Gmail 是在4月1日釋出的，很多人起初以為這只是一個玩笑。

2004年，Dojo 框架誕生，為不同瀏覽器提供了同一介面，併為主要功能提供了便利的呼叫方法。這標誌著 JavaScript 程式設計框架的時代開始來臨。

2004年，WHATWG 組織成立，致力於加速 HTML 語言的標準化程序。

2005年，蘋果公司在 KHTML 引擎基礎上，建立了 WebKit 引擎。

2005年，Ajax 方法（Asynchronous JavaScript and XML）正式誕生，Jesse James Garrett 發明了這個詞彙。它開始流行的標誌是，2月份釋出的 Google Maps 專案大量採用該方法。它幾乎成了新一代網站的標準做法，促成了 Web 2.0時代的來臨。

2005年，Apache 基金會發布了 CouchDB 資料庫。這是一個基於 JSON 格式的資料庫，可以用 JavaScript 函式定義檢視和索引。它在本質上有別於傳統的關係型資料庫，標識著 NoSQL 型別的資料庫誕生。

2006年，jQuery 函式庫誕生，作者為John Resig。jQuery 為操作網頁 DOM 結構提供了非常強大易用的介面，成為了使用最廣泛的函式庫，並且讓 JavaScript 語言的應用難度大大降低，推動了這種語言的流行。

2006年，微軟公司釋出 IE 7，標誌重新開始啟動瀏覽器的開發。

2006年，Google推出 Google Web Toolkit 專案（縮寫為 GWT），提供 Java 編譯成 JavaScript 的功能，開創了將其他語言轉為 JavaScript 的先河。

2007年，Webkit 引擎在 iPhone 手機中得到部署。它最初基於 KDE 專案，2003年蘋果公司首先採用，2005年開源。這標誌著 JavaScript 語言開始能在手機中使用了，意味著有可能寫出在桌面電腦和手機中都能使用的程式。

2007年，Douglas Crockford 發表了名為《JavaScript: The good parts》的演講，次年由 O'Reilly 出版社出版。這標誌著軟體行業開始嚴肅對待 JavaScript 語言，對它的語法開始重新認識，

2008年，V8 編譯器誕生。這是 Google 公司為 Chrome 瀏覽器而開發的，它的特點是讓 JavaScript 的執行變得非常快。它提高了 JavaScript 的效能，推動了語法的改進和標準化，改變外界對 JavaScript 的不佳印象。同時，V8 是開源的，任何人想要一種快速的嵌入式指令碼語言，都可以採用 V8，這拓展了 JavaScript 的應用領域。

2009年，Node.js 專案誕生，創始人為 Ryan Dahl，它標誌著 JavaScript 可以用於伺服器端程式設計，從此網站的前端和後端可以使用同一種語言開發。並且，Node.js 可以承受很大的併發流量，使得開發某些網際網路大規模的實時應用變得容易。

2009年，Jeremy Ashkenas 釋出了 CoffeeScript 的最初版本。CoffeeScript 可以被轉換為 JavaScript 執行，但是語法要比 JavaScript 簡潔。這開啟了其他語言轉為 JavaScript 的風潮。

2009年，PhoneGap 專案誕生，它將 HTML5 和 JavaScript 引入移動裝置的應用程式開發，主要針對 iOS 和 Android 平臺，使得 JavaScript 可以用於跨平臺的應用程式開發。

2009，Google 釋出 Chrome OS，號稱是以瀏覽器為基礎發展成的作業系統，允許直接使用 JavaScript 編寫應用程式。類似的專案還有 Mozilla 的 Firefox OS。

2010年，三個重要的專案誕生，分別是 NPM、BackboneJS 和 RequireJS，標誌著 JavaScript 進入模組化開發的時代。

2011年，微軟公司釋出 Windows 8作業系統，將 JavaScript 作為應用程式的開發語言之一，直接提供系統支援。

2011年，Google 釋出了 Dart 語言，目的是為了結束 JavaScript 語言在瀏覽器中的壟斷，提供更合理、更強大的語法和功能。Chromium瀏覽器有內建的 Dart 虛擬機器，可以執行 Dart 程式，但 Dart 程式也可以被編譯成 JavaScript 程式執行。

2011年，微軟工程師[Scott Hanselman](http://www.hanselman.com/blog/JavaScriptIsAssemblyLanguageForTheWebSematicMarkupIsDeadCleanVsMachinecodedHTML.aspx)提出，JavaScript 將是網際網路的組合語言。因為它無所不在，而且正在變得越來越快。其他語言的程式可以被轉成 JavaScript 語言，然後在瀏覽器中執行。

2012年，單頁面應用程式框架（single-page app framework）開始崛起，AngularJS 專案和 Ember 專案都發布了1.0版本。

2012年，微軟釋出 TypeScript 語言。該語言被設計成 JavaScript 的超集，這意味著所有 JavaScript 程式，都可以不經修改地在 TypeScript 中執行。同時，TypeScript 添加了很多新的語法特性，主要目的是為了開發大型程式，然後還可以被編譯成 JavaScript 執行。

2012年，Mozilla 基金會提出 [asm.js](http://asmjs.org/) 規格。asm.js 是 JavaScript 的一個子集，所有符合 asm.js 的程式都可以在瀏覽器中執行，它的特殊之處在於語法有嚴格限定，可以被快速編譯成效能良好的機器碼。這樣做的目的，是為了給其他語言提供一個編譯規範，使其可以被編譯成高效的 JavaScript 程式碼。同時，Mozilla 基金會還發起了 [Emscripten](https://github.com/kripken/emscripten/wiki) 專案，目標就是提供一個跨語言的編譯器，能夠將 LLVM 的位程式碼（bitcode）轉為 JavaScript 程式碼，在瀏覽器中執行。因為大部分 LLVM 位程式碼都是從 C / C++ 語言生成的，這意味著 C / C++ 將可以在瀏覽器中執行。此外，Mozilla 旗下還有 [LLJS](http://mbebenita.github.io/LLJS/) （將 JavaScript 轉為 C 程式碼）專案和 [River Trail](https://github.com/RiverTrail/RiverTrail/wiki) （一個用於多核心處理器的 ECMAScript 擴充套件）專案。目前，可以被編譯成 JavaScript 的[語言列表](https://github.com/jashkenas/coffee-script/wiki/List-of-languages-that-compile-to-JS)，共有將近40種語言。

2013年，Mozilla 基金會發布手機作業系統 Firefox OS，該作業系統的整個使用者介面都使用 JavaScript。

2013年，ECMA 正式推出 JSON 的[國際標準](http://www.ecma-international.org/publications/standards/Ecma-404.htm)，這意味著 JSON 格式已經變得與 XML 格式一樣重要和正式了。

2013年5月，Facebook 釋出 UI 框架庫 React，引入了新的 JSX 語法，使得 UI 層可以用元件開發，同時引入了網頁應用是狀態機的概念。

2014年，微軟推出 JavaScript 的 Windows 庫 WinJS，標誌微軟公司全面支援 JavaScript 與 Windows 作業系統的融合。

2014年11月，由於對 Joyent 公司壟斷 Node 專案、以及該專案進展緩慢的不滿，一部分核心開發者離開了 Node.js，創造了 io.js 專案，這是一個更開放、更新更頻繁的 Node.js 版本，很短時間內就釋出到了2.0版。三個月後，Joyent 公司宣佈放棄對 Node 專案的控制，將其轉交給新成立的開放性質的 Node 基金會。隨後，io.js 專案宣佈迴歸 Node，兩個版本將合併。

2015年3月，Facebook 公司釋出了 React Native 專案，將 React 框架移植到了手機端，可以用來開發手機 App。它會將 JavaScript 程式碼轉為 iOS 平臺的 Objective-C 程式碼，或者 Android 平臺的 Java 程式碼，從而為 JavaScript 語言開發高效能的原生 App 打開了一條道路。

2015年4月，Angular 框架宣佈，2.0 版將基於微軟公司的TypeScript語言開發，這等於為 JavaScript 語言引入了強型別。

2015年5月，Node 模組管理器 NPM 超越 CPAN，標誌著 JavaScript 成為世界上軟體模組最多的語言。

2015年5月，Google 公司的 Polymer 框架釋出1.0版。該專案的目標是生產環境可以使用 WebComponent 元件，如果能夠達到目標，Web 開發將進入一個全新的以元件為開發基礎的階段。

2015年6月，ECMA 標準化組織正式批准了 ECMAScript 6 語言標準，定名為《ECMAScript 2015 標準》。JavaScript 語言正式進入了下一個階段，成為一種企業級的、開發大規模應用的語言。這個標準從提出到批准，歷時10年，而 JavaScript 語言從誕生至今也已經20年了。

2015年6月，Mozilla 在 asm.js 的基礎上釋出 WebAssembly 專案。這是一種 JavaScript 引擎的中間碼格式，全部都是二進位制，類似於 Java 的位元組碼，有利於移動裝置載入 JavaScript 指令碼，執行速度提高了 20+ 倍。這意味著將來的軟體，會發布 JavaScript 二進位制包。

2016年6月，《ECMAScript 2016 標準》釋出。與前一年釋出的版本相比，它只增加了兩個較小的特性。

2017年6月，《ECMAScript 2017 標準》釋出，正式引入了 async 函式，使得非同步操作的寫法出現了根本的變化。

2017年11月，所有主流瀏覽器全部支援 WebAssembly，這意味著任何語言都可以編譯成 JavaScript，在瀏覽器執行。

## 參考連結

- Axel Rauschmayer, [The Past, Present, and Future of JavaScript](http://oreilly.com/javascript/radarreports/past-present-future-javascript.csp)
- John Dalziel, [The race for speed part 4: The future for JavaScript](http://creativejs.com/2013/06/the-race-for-speed-part-4-the-future-for-javascript/)
- Axel Rauschmayer, [Basic JavaScript for the impatient programmer](http://www.2ality.com/2013/06/basic-javascript.html)
- resin.io, [Happy 18th Birthday JavaScript! A look at an unlikely past and bright future](http://resin.io/happy-18th-birthday-javascript/)
