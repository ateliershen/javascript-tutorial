# 導論

## 什麼是 JavaScript 語言？

JavaScript 是一種輕量級的指令碼語言。所謂“指令碼語言”（script language），指的是它不具備開發作業系統的能力，而是隻用來編寫控制其他大型應用程式（比如瀏覽器）的“指令碼”。

JavaScript 也是一種嵌入式（embedded）語言。它本身提供的核心語法不算很多，只能用來做一些數學和邏輯運算。JavaScript 本身不提供任何與 I/O（輸入/輸出）相關的 API，都要靠宿主環境（host）提供，所以 JavaScript 只合適嵌入更大型的應用程式環境，去呼叫宿主環境提供的底層 API。

目前，已經嵌入 JavaScript 的宿主環境有多種，最常見的環境就是瀏覽器，另外還有伺服器環境，也就是 Node 專案。

從語法角度看，JavaScript 語言是一種“物件模型”語言。各種宿主環境透過這個模型，描述自己的功能和操作介面，從而透過 JavaScript 控制這些功能。但是，JavaScript 並不是純粹的“面嚮物件語言”，還支援其他程式設計正規化（比如函數語言程式設計）。這導致幾乎任何一個問題，JavaScript 都有多種解決方法。閱讀本書的過程中，你會詫異於 JavaScript 語法的靈活性。

JavaScript 的核心語法部分相當精簡，只包括兩個部分：基本的語法構造（比如運算子、控制結構、語句）和標準庫（就是一系列具有各種功能的物件比如`Array`、`Date`、`Math`等）。除此之外，各種宿主環境提供額外的 API（即只能在該環境使用的介面），以便 JavaScript 呼叫。以瀏覽器為例，它提供的額外 API 可以分成三大類。

- 瀏覽器控制類：操作瀏覽器
- DOM 類：操作網頁的各種元素
- Web 類：實現網際網路的各種功能

如果宿主環境是伺服器，則會提供各種作業系統的 API，比如檔案操作 API、網路通訊 API等等。這些你都可以在 Node 環境中找到。

本書主要介紹 JavaScript 核心語法和瀏覽器網頁開發的基本知識，不涉及 Node。全書可以分成以下四大部分。

- 基本語法
- 標準庫
- 瀏覽器 API
- DOM

JavaScript 語言有多個版本。本書的內容主要基於 ECMAScript 5.1 版本，這是學習 JavaScript 語法的基礎。ES6 和更新的語法請參考我寫的[《ECMAScript 6入門》](http://es6.ruanyifeng.com/)。

## 為什麼學習 JavaScript？

JavaScript 語言有一些顯著特點，使得它非常值得學習。它既適合作為學習程式設計的入門語言，也適合當作日常開發的工作語言。它是目前最有希望、前途最光明的計算機語言之一。

### 操控瀏覽器的能力

JavaScript 的發明目的，就是作為瀏覽器的內建指令碼語言，為網頁開發者提供操控瀏覽器的能力。它是目前唯一一種通用的瀏覽器指令碼語言，所有瀏覽器都支援。它可以讓網頁呈現各種特殊效果，為使用者提供良好的互動體驗。

目前，全世界幾乎所有網頁都使用 JavaScript。如果不用，網站的易用性和使用效率將大打折扣，無法成為操作便利、對使用者友好的網站。

對於一個網際網路開發者來說，如果你想提供漂亮的網頁、令使用者滿意的上網體驗、各種基於瀏覽器的便捷功能、前後端之間緊密高效的聯絡，JavaScript 是必不可少的工具。

### 廣泛的使用領域

近年來，JavaScript 的使用範圍，慢慢超越了瀏覽器，正在向通用的系統語言發展。

**（1）瀏覽器的平臺化**

隨著 HTML5 的出現，瀏覽器本身的功能越來越強，不再僅僅能瀏覽網頁，而是越來越像一個平臺，JavaScript 因此得以呼叫許多系統功能，比如操作本地檔案、操作圖片、呼叫攝像頭和麥克風等等。這使得 JavaScript 可以完成許多以前無法想象的事情。

**（2）Node**

Node 專案使得 JavaScript 可以用於開發伺服器端的大型專案，網站的前後端都用 JavaScript 開發已經成為了現實。有些嵌入式平臺（Raspberry Pi）能夠安裝 Node，於是 JavaScript 就能為這些平臺開發應用程式。

**（3）資料庫操作**

JavaScript 甚至也可以用來操作資料庫。NoSQL 資料庫這個概念，本身就是在 JSON（JavaScript Object Notation）格式的基礎上誕生的，大部分 NoSQL 資料庫允許 JavaScript 直接操作。基於 SQL 語言的開源資料庫 PostgreSQL 支援 JavaScript 作為操作語言，可以部分取代 SQL 查詢語言。

**（4）移動平臺開發**

JavaScript 也正在成為手機應用的開發語言。一般來說，安卓平臺使用 Java 語言開發，iOS 平臺使用 Objective-C 或 Swift 語言開發。許多人正在努力，讓 JavaScript 成為各個平臺的通用開發語言。

PhoneGap 專案就是將 JavaScript 和 HTML5 打包在一個容器之中，使得它能同時在 iOS 和安卓上執行。Facebook 公司的 React Native 專案則是將 JavaScript 寫的元件，編譯成原生元件，從而使它們具備優秀的效能。

Mozilla 基金會的手機作業系統 Firefox OS，更是直接將 JavaScript 作為作業系統的平臺語言，但是很可惜這個專案沒有成功。

**（5）內嵌指令碼語言**

越來越多的應用程式，將 JavaScript 作為內嵌的指令碼語言，比如 Adobe 公司的著名 PDF 閱讀器 Acrobat、Linux 桌面環境 GNOME 3。

**（6）跨平臺的桌面應用程式**

Chromium OS、Windows 8 等作業系統直接支援 JavaScript 編寫應用程式。Mozilla 的 Open Web Apps 專案、Google 的 [Chrome App 專案](http://developer.chrome.com/apps/about_apps)、GitHub 的 [Electron 專案](http://electron.atom.io/)、以及 [TideSDK 專案](http://tidesdk.multipart.net/docs/user-dev/generated/)，都可以用來編寫運行於 Windows、Mac OS 和 Android 等多個桌面平臺的程式，不依賴瀏覽器。

**（7）小結**

可以預期，JavaScript 最終將能讓你只用一種語言，就開發出適應不同平臺（包括桌面端、伺服器端、手機端）的程式。早在2013年9月的[統計](http://adambard.com/blog/top-github-languages-for-2013-so-far/)之中，JavaScript 就是當年 GitHub 上使用量排名第一的語言。

著名程式設計師 Jeff Atwood 甚至提出了一條 [“Atwood 定律”](http://www.codinghorror.com/blog/2007/07/the-principle-of-least-power.html)：

> “所有可以用 JavaScript 編寫的程式，最終都會出現 JavaScript 的版本。”(Any application that can be written in JavaScript will eventually be written in JavaScript.)

### 易學性

相比學習其他語言，學習 JavaScript 有一些有利條件。

**（1）學習環境無處不在**

只要有瀏覽器，就能執行 JavaScript 程式；只要有文字編輯器，就能編寫 JavaScript 程式。這意味著，幾乎所有電腦都原生提供 JavaScript 學習環境，不用另行安裝複雜的 IDE（整合開發環境）和編譯器。

**（2）簡單性**

相比其他指令碼語言（比如 Python 或 Ruby），JavaScript 的語法相對簡單一些，本身的語法特性並不是特別多。而且，那些語法中的複雜部分，也不是必需要學會。你完全可以只用簡單命令，完成大部分的操作。

**（3）與主流語言的相似性**

JavaScript 的語法很類似 C/C++ 和 Java，如果學過這些語言（事實上大多數學校都教），JavaScript 的入門會非常容易。

必須說明的是，雖然核心語法不難，但是 JavaScript 的複雜性體現在另外兩個方面。

首先，它涉及大量的外部 API。JavaScript 要發揮作用，必須與其他元件配合，這些外部元件五花八門，數量極其龐大，幾乎涉及網路應用的各個方面，掌握它們絕非易事。

其次，JavaScript 語言有一些設計缺陷。某些地方相當不合理，另一些地方則會出現怪異的執行結果。學習 JavaScript，很大一部分時間是用來搞清楚哪些地方有陷阱。Douglas Crockford 寫過一本有名的書，名字就叫[《JavaScript: The Good Parts》](http://javascript.crockford.com/)，言下之意就是這門語言不好的地方很多，必須寫一本書才能講清楚。另外一些程式設計師則感到，為了更合理地編寫 JavaScript 程式，就不能用 JavaScript 來寫，而必鬚髮明新的語言，比如 CoffeeScript、TypeScript、Dart 這些新語言的發明目的，多多少少都有這個因素。

儘管如此，目前看來，JavaScript 的地位還是無法動搖。加之，語言標準的快速進化，使得 JavaScript 功能日益增強，而語法缺陷和怪異之處得到了彌補。所以，JavaScript 還是值得學習，況且它的入門真的不難。

### 強大的效能

JavaScript 的效能優勢體現在以下方面。

**（1）靈活的語法，表達力強。**

JavaScript 既支援類似 C 語言清晰的程序式程式設計，也支援靈活的函數語言程式設計，可以用來寫併發處理（concurrent）。這些語法特性已經被證明非常強大，可以用於許多場合，尤其適用非同步程式設計。

JavaScript 的所有值都是物件，這為程式設計師提供了靈活性和便利性。因為你可以很方便地、按照需要隨時創造資料結構，不用進行麻煩的預定義。

JavaScript 的標準還在快速進化中，並不斷合理化，新增更適用的語法特性。

**（2）支援編譯執行。**

JavaScript 語言本身，雖然是一種解釋型語言，但是在現代瀏覽器中，JavaScript 都是編譯後執行。程式會被高度最佳化，執行效率接近二進位制程式。而且，JavaScript 引擎正在快速發展，效能將越來越好。

此外，還有一種 WebAssembly 格式，它是 JavaScript 引擎的中間碼格式，全部都是二進位制程式碼。由於跳過了編譯步驟，可以達到接近原生二進位制程式碼的執行速度。各種語言（主要是 C 和 C++）透過編譯成 WebAssembly，就可以在瀏覽器裡面執行。

**（3）事件驅動和非阻塞式設計。**

JavaScript 程式可以採用事件驅動（event-driven）和非阻塞式（non-blocking）設計，在伺服器端適合高併發環境，普通的硬體就可以承受很大的訪問量。

### 開放性

JavaScript 是一種開放的語言。它的標準 ECMA-262 是 ISO 國際標準，寫得非常詳盡明確；該標準的主要實現（比如 V8 和 SpiderMonkey 引擎）都是開放的，而且質量很高。這保證了這門語言不屬於任何公司或個人，不存在版權和專利的問題。

語言標準由 TC39 委員會負責制定，該委員會的運作是透明的，所有討論都是開放的，會議記錄都會對外公佈。

不同公司的 JavaScript 執行環境，相容性很好，程式不做調整或只做很小的調整，就能在所有瀏覽器上執行。

### 社群支援和就業機會

全世界程式設計師都在使用 JavaScript，它有著極大的社群、廣泛的文獻和圖書、豐富的程式碼資源。絕大部分你需要用到的功能，都有多個開源函式庫可供選用。

作為專案負責人，你不難招聘到數量眾多的 JavaScript 程式設計師；作為開發者，你也不難找到一份 JavaScript 的工作。

## 實驗環境

本教程包含大量的示例程式碼，只要電腦安裝了瀏覽器，就可以用來實驗了。讀者可以一邊讀一邊執行示例，加深理解。

推薦安裝 Chrome 瀏覽器，它的“開發者工具”（Developer Tools）裡面的“控制檯”（console），就是執行 JavaScript 程式碼的理想環境。

進入 Chrome 瀏覽器的“控制檯”，有兩種方法。

- 直接進入：按下`Option + Command + J`（Mac）或者`Ctrl + Shift + J`（Windows / Linux）
- 開發者工具進入：開發者工具的快捷鍵是 F12，或者`Option + Command + I`（Mac）以及`Ctrl + Shift + I`（Windows / Linux），然後選擇 Console 面板

進入控制檯以後，就可以在提示符後輸入程式碼，然後按`Enter`鍵，程式碼就會執行。如果按`Shift + Enter`鍵，就是程式碼換行，不會觸發執行。建議閱讀本教程時，將程式碼複製到控制檯進行實驗。

作為嘗試，你可以將下面的程式複製到“控制檯”，按下回車後，就可以看到執行結果。

```javascript
function greetMe(yourName) {
  console.log('Hello ' + yourName);
}

greetMe('World')
// Hello World
```
