# coServ

coServ 是以 node.js 實作的一個非常精簡的 web server。與一般 web server 不同的地方是，coServ 可以用來讀取來自不同網站的內容，然後將結果整合到同一個網頁中。

不論是只有幾篇靜態網頁的小網站，或是結構複雜的網路應用（web app），coServ 都可勝任愉快。coServ 以類似 EJS 的樣板引擎，協助網站開發者將內容和視覺設計做有效的分離。很特別的是，coServ 的樣板引擎不僅可以用來合成 HTML 的內容，也可以用來合成 Javascript 和 CSS 。這給前端工程師更大的彈性，也使得程式碼重複使用變得更可行。

coServ 是基於 "internet of servers" 的理念所設計的。這意味不同網域的 server 可以互相交談，進而合作產出對使用者最佳的內容或服務。此外，coServ 也是為了協助開發者利用 COIMOTION API 這個後端服務而設計的。

##  功能特色

+ 比網站樣板更強大的「頁型」系統，使前端設計更為簡便。

+ 將網頁分解成區域和區塊。設計時只需專注於小規模的區塊，coServ 會自動將網頁組合好。每個區塊可以重複使用在不同的網頁中。

+ 使用類似 EJS 的樣板引擎 (template engine)來動態產生實際的 HTML, Javascript 與 CSS 的內容。是的，連 Javascript 和 CSS 也都可以用樣板引擎來生成。

+ 能自動從 COIMOTION API 引擎中讀取資料。

+ 如果需要從其他網站或資料源取得資料，可以自行撰寫模組來達成。

+ 利用語系資源套件來支援多國語系。支援多國語言只需單一的 HTML 原始檔。

+ 單一的 coServ 可以支援多個網站，而且非常簡單。只需二三行設定碼就可達成。

## 如何安裝
npm install coserv

## 如何使用
1. 安裝完成後到 coServ 目錄底下執行 'node coServ.js' 即可。Server 預設跑在 8080 port。

2. 如果你希望 server 跑在其他的埠號（例如 80），可以直接修改 coServ.js 的最後一行。

3. 如果你要指定網域給 server，那麼只需修改 'www/sites.json' 這個檔。把裡面的 IP 網址 127.0.0.1 改成你所要的網域即可（當然你要先取得網域，並將 DNS 指向 coServ 所在的位置）。

4. 試著在網路瀏覽器上輸入' http://127.0.0.1:808'0（如果你已指定了網域，那就改用 'http://www.yourDomain.com'）。如果設定都正確，你就會看到預設的首頁。

## 建立自己的網站
從範例開始改大概是最快的方法。假設你用下載內容中的 'demoApp'做基礎，以下說明所需的步驟：

1. 到 coServ/www/themes 目錄底下複製整個 'demoApp'的目錄，並為新的目錄取個名。假設這個新網站的代碼名稱為 'foo'。

2. 所有網站的一切（外觀、行為）都是由'foo'目錄底下的檔案所決定，沒其他地方了。其中 siteURI.json 列出網站所有的網址（類似 site map 的功能），而 layoutMap.json 檔案則用來決定什麼網址要使用哪個頁型來進行畫面的組合和輸出。至於 'layout' 這個目錄包含了網站所使用的所有頁型。在目前的範例網站上只有一個 'default' 的預設頁型。

3. 你還必須告訴 coServ 現在有了一個新網站。這件事是在 'www/sites.json' 檔案中敘明。這個檔案簡單易懂，直接看內容就知道如何修改。

### 樣板引擎
#### 1. 語法
要引用coServ指令或Javascript程式碼，可以用：

    <% your javascript code or coServ directives here %>

要取得變數的值，則使用：

    <%= variable %>

所以在語法上，基本上和 EJS 這個樣板引擎是相同的。

#### 2. 指令
除了嵌入 Javascript 程式碼到樣板中外，你也可以使用 coServ 的指令。coServ 的指令說明如下：

+ **includeCss()**: 這個指令會找出頁型、區域和區塊中所引用的 CSS 檔，並加以輸出。這個指令應該只用在頁型中，用來列出所有的 CSS 檔，如<link rel=...>

+ **includeJs()**: 這個指令會找出頁型、區域和區塊中所引用的 js 檔，並加以輸出。這個指令應該只用在頁型中，用來列出所有的 js 檔，如 <script type="text/javascript" source="..."></script>

+ **css()**: 這個指令會把頁型、區域和區塊中所有的 CSS 規則整合起來，並加以輸出。這個指令應該只用在頁型中用來輸 出<head><style>...</style></head>的內容。

+ **js()**: 這個指令會把頁型、區域和區塊中所有的 js 程式碼整合起來，並加以輸出。這個指令應該只用在頁型中用來輸出<head><script>...</script></head>的內容。

+ **region(region_path)**: 嵌入一個區域。區域像是一個容器，除了可以有自己的樣式設定外，區域主要的功能是用來包含其他的區域和區塊，方便做小範圍的排版。'region_path' 是用來指向定義區域的目錄。這個路徑的內容若是相對路徑（以 ./ 開頭），是相對於目前呼叫的區域所在目錄。如不是相對路徑，則表示要是用共用區域。共用區域一律存放在 'www/themes/share/views' 這個目錄底下。

+ **block(blockName, option)**: 嵌入一個區塊。區塊不項區域是個容器，而是會產生實際的內容。區塊的顯示方式會定義在 'www/themes/[Your_Website_Code]/blocks' 這個目錄下。'blockName' 這個參數表示區塊的名稱。

#### 3. 變數
樣板引擎提供了各種變數以便存取在網頁生成時所需的資料和輔助資訊。變數大致上分成以下幾類：回傳變數、區塊變數、環境變數和片語。

##### i. 回傳變數
回傳變數是在執行一個區塊時所產生的資料。這可能是呼叫一個遠端的 API 所回傳的結果（主要是呼叫COIMOTION API），或是執行一個本地端的區塊模組的傳回值。以下是一些範例：

    <%= value.title %>
    

可用來取得 title 的欄位，

    <% value.list.forEach(function(item) { %>
        <li><%= item.title %></li>
    <% }); %>
    
上面這個範例則是將傳回值中的 list 陣列，內容值的標題欄位一一取出。

##### ii. 區塊變數
每個區塊有自己的區塊變數。這些變數在內容生成時有時會用到：

+ uri: 區塊的引用網址
+ query: 引用區塊時所輸入的參數
+ intpath: 與 uri 類似，但是將識別碼的部份移除
+ service: 如果區塊是呼叫 COIMOTION API 來取的資料或內容，這個變數顯示 API 的呼叫網址。
+ viewPath: 這個區塊的顯示檔（包含HTML, Javascript, CSS, 包含檔和多語資源套件）所在的檔案目錄路徑。

##### iii. 環境變數
環境變數包含了與整個網頁（不只是個別區塊）相關的資訊：

+ locID: 網頁所使用的語系識別碼。
+ title: 網頁的標題文字。
+ description: 網頁的說明文字。可以用在 HTML 描述網頁的 meta tag 中。

##### iv. 片語
如果你的網站需要支援多國語系，只需在網頁上使用片語，並在多語資源套件檔中加上該片語的各國語言翻譯即可。例如：

    <%= ph.addr %>
    
會把 'addr' 這個片語轉成使用者所虛妄的語系文字。

#### 4. 範例
利用樣板來產生 HTML 的內容是最常見的用法。下列的例子可以用來設定網頁的標題：

    <title><%=ctx.title%></title>
    
你也可以將一個 Javascript 程式改用樣板的方式來撰寫：

    if (<%= value.isValid %>)
        // 執行結果是有效的，顯示結果
    else
        // 對使用者提出警告
        
甚至 CSS 也可以用樣板來生成：

    ul.news {
        <% if (value.isImportant) { %>
        color: #red;
        <% } else { %>
        color: #black;
        <% } %>
    }
    
（你也許不再需要SASS, SCSS 或是LESS了）

### 區塊模組
區塊模組是用來供給區塊資料的（如果你不是用 COIMOTON API 來取的資料）。它讓開發者可以提供客製的功能。區塊模組基本上就是一個 nodejs 的模組，但是必須實作 run() 這個函數。以下是個簡單的範例：

    var  EventEmitter = require('events').EventEmitter;
    
    var  hotMod = new EventEmitter();
    
    hotMod.run = function run(inData)  {
        var  result = {
    					  "value": {
    						    "list": [{"title": "COIMOTION Is An Amazing API Service Engine."},
    								         {"title": "coServ Help You Manage A Big WebSite With Ease."}
    								        ]
    					   }
    				 };
        hotMod.emit('done', result);
    };
    
    module.exports = hotMod;
