# WP 台灣週報 · 第 10 期

**出刊日：** 2026-11-02

本週重點：WordPress 7.1.4 於 10/28 推出，一次修補 22 個漏洞，其中最嚴重的是核心升級過程中的「競爭條件」漏洞，不需登入就可能遠端執行程式碼，而且釋出後幾分鐘就有人開始試打，請確認網站已更新。官方安全計畫公布兩個月成績：漏洞回報量暴增到每月數百件，41 天內發了 4 次安全版。7.2 時程調整，Beta 3 提前推出，正式版仍是 12/8；AI 團隊也公布「代理式網路」路線圖，目標放在 7.3。WooCommerce 11.3 Beta 開放測試，Woomp 3.5.27 調整「未付款」顯示預設值。在地文章有設計湖分析 SHOPLINE 等平台該不該搬 WooCommerce，WordCamp Asia 2027 推薦講者 11/8 截止。

---

## 本週熱點

### WordPress 7.1.4 安全版：22 個漏洞，核心升級時可被免登入遠端執行

WordPress 7.1.4 於 10/28（台灣時間 10/29 凌晨）推出，Wordfence 統計共修補 22 個漏洞。最嚴重的是 CVE-2026-119977（CVSS 8.1）：WordPress 自動或手動升級核心時，會把新版檔案暫放在 `wp-content/upgrade` 底下一個可預測、可從網路存取的路徑，其中包含安裝精靈 `setup-config.php`。攻擊者只要在升級進行的那幾秒內搶先存取，就可能藉此取得網站控制權並執行程式碼，不需要登入。雖然時間窗口很短，但攻擊者可以不斷輪詢等待時機。修正方式是改用隨機命名的暫存目錄、加上防護頁，並阻擋暫存設定檔被不當套用。

這個修正一路回移到 4.7 版，各分支修補版本包含 6.9.11、7.0.8、7.1.4 等。Wordfence 在 10/29 清晨（台灣時間）推出防火牆規則，部署後 9 分鐘就攔到第一次攻擊，前 21 小時超過 18,500 次請求，其中兩個來源針對少數網站持續輪詢約 14.5 小時，顯然是在等升級時機。Wordfence 免費版要到 11/27 才會收到這條規則。

其他修補多為「投稿者」以上權限可觸發的儲存型 XSS（Interactivity API 綁定、區塊分隔標記、分類區塊、留言分頁／文章導覽標籤、自訂器即時預覽等），另有隱私資料清除請求的 XSS（編輯以上）、未登入可讀取尚未發布的自訂器變更、Notes 權限問題、Interactivity API 阻斷服務，以及更新 eMudhra 根憑證。致謝名單中有多筆來自 Anthropic 的回報。

建議動作：到「控制台 → 更新」確認版本已是 7.1.4（或你所在分支的最新修補版）；有關閉自動更新的網站請盡快手動更新。

→ [原文（WordPress.org）](https://wordpress.org/news/2026/10/wordpress-7-1-4/)
→ [原文（7.1.4 版本說明）](https://wordpress.org/documentation/wordpress-version/version-7-1-4/)
→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/10/wordpress-7-1-4-security-release-rce/)
→ [原文（CVE-2026-119977）](https://www.cve.org/CVERecord?id=CVE-2026-119977)

### 核心安全計畫兩個月：漏洞回報暴增 30 倍，41 天發 4 次安全版

WordPress 安全團隊 10/29 公布「核心安全計畫」上路兩個月的成果。HackerOne 上的回報量從過去每月 20～30 件，暴增到 7 月 450 件、8 月 773 件，近 90 天累計 2,306 件，今年已超過 3,000 件。41 天內連發 4 次安全版（7.1.1 9/17、7.1.2 9/22、7.1.3 10/6、7.1.4 10/28），7 月中以來共 7 次，關掉 50 多個嚴重／高風險問題。

團隊的因應方式：安全版改為分兩波釋出；發版流程 109 個步驟中已有 73% 自動化；導入 AI 分流系統，待處理佇列從 359 件降到兩位數；也開始用開放權重模型主動找漏洞，並加入 Anthropic 的開源掃描計畫。文末呼籲企業贊助分流與發版人力。對站長來說，意思很簡單：未來安全版只會更頻繁，自動更新與快速部署流程要先準備好。

→ [原文](https://make.wordpress.org/security/2026/10/29/the-core-security-initiative-two-months-in/)

### WordPress 7.2：Beta 2、Beta 3 接連推出，時程調整、正式版仍為 12/8

Beta 2 於 10/27 推出，重點包括 Abilities API 支援內容查詢並強化權限需求與結構描述、DataViews／DataForm 可在伺服器端註冊、REST API 彈性提升、後台資源載入調整，以及 SVG 圖示。Beta 3 原定 11/3，因配合 7.1.4 安全版提前在 10/28 一起推出。

調整後的時程：Beta 4 11/3、Beta 5 11/10、RC1 11/17、RC2 11/24、RC3 12/1，正式版 12/8 不變。測試可用 WP-CLI `wp core update --version=7.2-beta3`、Beta Tester 外掛或 WordPress Playground，請勿在正式站測試。

→ [原文（Beta 2）](https://wordpress.org/news/2026/10/wordpress-7-2-beta-2/)
→ [原文（7.2 開發時程）](https://make.wordpress.org/core/7-2/)

### AI 團隊公布「代理式網路」路線圖，AI 外掛 1.5.0 推出

WordPress AI 團隊 10/28 發文說明讓 WordPress 能被 AI 代理安全操作的四條工作線：MCP Adapter（提升穩定性，提議 7.3 併入核心或隨附）、Abilities（部分能力可能在 7.2 Beta 期間進核心）、OAuth（7.2 週期設計、7.3 在核心實作，搭配 Secrets API 保存憑證），以及端到端的使用體驗，整體目標鎖定 7.3。

官方 AI 外掛 10/30 推出 1.5.0（約 6 萬個安裝），新增多個實驗功能：C2PA 內容來源驗證、文字轉語音、MCP Access（自動安裝 MCP Adapter，並在「工具 → MCP Access」管理）、WebMCP（讓瀏覽器端的 AI 代理在編輯器裡呼叫功能）、修訂版變更摘要、區塊容器標籤自動產生；Markdown 輸出也會包含留言，並新增 Mistral 與 Vercel AI Gateway 連接器。實驗功能請先在測試站試用。

→ [原文（代理式網路路線圖）](https://make.wordpress.org/ai/2026/10/28/wordpress-for-the-agentic-web/)
→ [原文（AI 外掛 1.5.0）](https://github.com/WordPress/ai/releases/tag/1.5.0)

### Gutenberg 24.3：跨區塊套用文字格式、更多區塊樣式選項

Gutenberg 24.3 於 10/28 推出。可以一次選取多個區塊套用粗體、連結等文字格式；新增大頭貼（Avatar）元件；列出與 React 19 不相容的外掛清單，方便開發者提前修正。區塊樣式方面，段落與圖示區塊可設背景圖與漸層、文章標題支援「填滿寬度」文字、封面區塊可設連結顏色與最小寬度、表格可設文字對齊、圖庫燈箱在手機上可滑動切換，焦點外框樣式也統一了。

→ [原文](https://github.com/WordPress/gutenberg/releases/tag/v24.3.0)

---

## WooCommerce／電商

### WooCommerce 11.3 Beta 1：特價標籤顯示折扣、退貨政策頁設定

11.3 Beta 1 於 10/26 推出，正式版預定 11/9 那週，目前穩定版仍是 11.2.1。這版約 1,310 個 commit，店家看得到的改變：特價標籤可以顯示折扣金額或百分比，並加上前後綴文字，可變商品會顯示「最高折扣」；區塊樣式控制更多；精選分類區塊可放進分類查詢；手機上的商品圖庫滑動更順；「設定 → 進階 → 頁面設定」可指定退款／退貨政策頁，並輸出結構化資料；分析報表的訂單可依付款方式篩選。

開發者要注意兩個行為改變：商品 API 同時用 `on_sale` 與 `include`／`exclude` 時，結果改為兩個條件的交集（例如 `on_sale=true&include=10,13` 以前會多回傳其他特價商品，現在只回傳 13）；退款 REST API 會驗證 `line_items`，不合法時回傳 400／422 錯誤。上期提過的 MCP 連線方式調整也在這版。測試可用 WooCommerce Beta Tester 4.0.0。

→ [原文](https://developer.woocommerce.com/2026/10/26/woocommerce-11-3-pre-release/)

### 組合商品的子項目終於能被正確辨識

WooCommerce 開發者部落格 10/27 說明，新增 `woocommerce_store_api_cart_item_parent_item_key` 篩選器，Store API 的購物車項目也多了 `parent_item_key` 欄位，讓外掛標示哪些項目是另一個商品的子項目。這解決了組合商品（bundle）、組裝商品、任選組合等外掛常見的「購物車已有 N 件」數量算錯問題。有開發這類擴充的人，建議依文中範例接上新欄位。

→ [原文](https://developer.woocommerce.com/2026/10/27/cart-item-children/)

### Woomp 3.5.27：「訂單明細顯示未付款」預設改為關閉

好用版 Woomp 3.5.27 於 10/30 推出，把「於訂單明細中顯示未付款」改為預設關閉，既有網站更新後也會被強制關閉一次。原因是部分非同步回呼的金流（例如 Shopline Payment），顧客回到感謝頁時付款通知還沒送達，畫面就會顯示「(尚未付款)」，讓顧客誤以為付款失敗。若仍需要這個顯示，可到「WooCommerce → 設定 → 好用版擴充」重新開啟。

→ [原文](https://github.com/zenbuapps/woomp/releases/tag/v3.5.27)

### 電商相關外掛漏洞

- **PDF Invoice for WooCommerce** 7.1.1 以前（CVE-2026-109220，CVSS 9.8）：未登入者可在結帳時透過 Store API 的訂單備註注入惡意序列化資料，請更新到 7.2.0。
- **Checkout Files Upload for WooCommerce** 2.4.0 以前（CVE-2026-103688，CVSS 9.8）：「允許的檔案類型」設定留空時，未登入者可上傳任意檔案，約 7,000 個安裝，請更新並確認已填寫允許的副檔名。
- **Export and Import Users and Customers** 2.7.5 以前（CVE-2026-104368，CVSS 8.8）：訂閱者可利用 CSV 跳脫處理不一致提權為管理員，約 5 萬個安裝，目前為 2.7.7。
- **Menu Cart for WooCommerce** 3.2.1 以前（CVE-2026-105972）：未登入者可植入儲存型 XSS。
- **Bricks** 佈景 2.4.4 以前（CVE-2026-118804）：可外洩草稿、私人文章與優惠券（shop_coupon）資料，請更新到 2.4.4。

→ [原文（PDF Invoice）](https://www.cve.org/CVERecord?id=CVE-2026-109220)
→ [原文（Checkout Files Upload）](https://www.cve.org/CVERecord?id=CVE-2026-103688)
→ [原文（Export and Import Users）](https://www.cve.org/CVERecord?id=CVE-2026-104368)
→ [原文（Menu Cart）](https://www.cve.org/CVERecord?id=CVE-2026-105972)
→ [原文（Bricks）](https://www.cve.org/CVERecord?id=CVE-2026-118804)

---

## 資安

### PHP2Shell 後續：仍無確認的 WordPress 入侵案例

上期報導的 PHP 重大漏洞，Wordfence 10/28 更新：10/23–27 期間觀察到約 77,600 次攻擊請求、來自 2,000 多個 IP，但截至 10/28 仍沒有確認的 WordPress 網站因此被入侵；已知的實際入侵案例都在 WordPress 以外（例如防火牆設備、IIS 搭配 PHP-CGI 的環境）。Wordfence 也再次提醒：更新 WordPress 不會更新 PHP，PHP 要另外升級或請主機商確認。

→ [原文](https://www.wordfence.com/blog/2026/10/php2shell-update-still-no-confirmed-wordpress-compromises-and-answers-to-the-questions-were-hearing-most/)

### Gravity Forms：免登入物件注入，可能進一步遠端執行

Gravity Forms 3.1.2 以前有 CVE-2026-103039（CVSS 9.8）：表單若使用「清單（List）」類型的文章自訂欄位，未登入者可注入惡意序列化資料，在特定條件下可遠端執行程式碼。3.1.3 已修正，目前最新為 3.2.1（10/28）。這是付費外掛，不會經由 WordPress.org 自動更新，請到後台或官網確認版本。

→ [原文（CVE）](https://www.cve.org/CVERecord?id=CVE-2026-103039)
→ [原文（Gravity Forms 更新紀錄）](https://docs.gravityforms.com/gravityforms-change-log/)

### Rank Math SEO 免費版：投稿者可遠端執行程式

和先前的 PRO 版供應鏈事件無關，這次是免費版（約 400 萬個安裝）1.0.276 以前的 CVE-2026-1928（CVSS 8.8）：投稿者以上帳號可透過 `updateMeta` REST 端點注入物件，進而遠端執行程式碼。目前版本為 1.0.280，有開放多人投稿的網站請優先更新。

→ [原文](https://www.cve.org/CVERecord?id=CVE-2026-1928)

### 其他高風險漏洞

- **Formidable Forms** 6.35 以前（CVE-2026-89435，CVSS 8.8，僅免費版）：訂閱者可安裝任意外掛，約 30 萬個安裝，目前為 7.0。
- **NextGEN Gallery** 4.4.1 以前（CVE-2026-76010，CVSS 8.8）：已登入者可遠端執行程式，約 30 萬個安裝，目前為 4.5.5。
- **Kirki** 6.2.5 以前（CVE-2026-75891，CVSS 8.8）：訂閱者可接管管理員帳號，約 40 萬個安裝，目前為 6.4.1。
- **Enable Media Replace** 4.1.4～4.2.2（CVE-2026-114422）：未登入物件注入，是 2023 年舊漏洞的回歸，約 60 萬個安裝，請更新到 4.2.3。
- **Popup Builder** 4.4.5 以前（CVE-2026-89270，CVSS 9.1）：未登入者可把所有彈出視窗丟進垃圾桶，約 20 萬個安裝，請更新到 4.4.6。
- **Jupiter X Core** 4.61.0 以前（CVE-2026-106013，CVSS 9.8）：X（Twitter）社群登入可繞過驗證，約 7 萬個安裝，請更新到 4.62.0。
- **MemberPress** 1.14.1 以前（CVE-2026-92797）：CSRF 可導致帳號被接管，請更新到 1.14.2。

→ [原文（Formidable Forms）](https://www.cve.org/CVERecord?id=CVE-2026-89435)
→ [原文（NextGEN Gallery）](https://www.cve.org/CVERecord?id=CVE-2026-76010)
→ [原文（Kirki）](https://www.cve.org/CVERecord?id=CVE-2026-75891)
→ [原文（Enable Media Replace）](https://www.cve.org/CVERecord?id=CVE-2026-114422)
→ [原文（Popup Builder）](https://www.cve.org/CVERecord?id=CVE-2026-89270)
→ [原文（Jupiter X Core）](https://www.cve.org/CVERecord?id=CVE-2026-106013)
→ [原文（MemberPress）](https://www.cve.org/CVERecord?id=CVE-2026-92797)

### UpdraftPlus 1.26.14：UpdraftCentral 自動加站改為預設關閉

接續上期的 UpdraftCentral 金鑰外洩事件，UpdraftPlus 1.26.14（10/26）把「連線時自動將網站加入 UpdraftCentral」改為預設關閉；有設定 `DISALLOW_FILE_MODS` 的網站，也會一併禁止執行還原。還沒處理上期建議動作的網站，請一起更新。

→ [原文](https://wordpress.org/plugins/updraftplus/#developers)

---

## 台灣站務與活動

### WordCamp Asia 2027 推薦講者與主題：11/8 截止

WordCamp Asia 2027 將於 2027 年 4 月 9～11 日在馬來西亞檳城舉行，主辦團隊正在徵求「邀請講者」與主題建議，表單 11/8 截止。心中有想聽的講者或議題，可以趁這週推薦。

→ [原文](https://asia.wordcamp.org/2027/suggest-speaker-or-topic/)

### WordCamp 購票頁隱私問題：選擇不公開的參加者曾被列出

社群團隊 10/28 說明，2025/5/20～2026/10/28 期間，購票編輯頁會把「是否列在公開參加者頁面」顯示為「是」，就算你當初選「否」，只要回去存檔（例如改名字）就會被改成公開。公開頁面會顯示姓名、Gravatar，部分活動還有網站或社群連結、公司與職稱；Email、電話、地址、飲食與無障礙需求沒有外露。估計約 168 場活動、1,280 張票受影響，**WordCamp Asia 2026 是受影響最多的活動**（估計 149 張）。

官方已在發現當天修正，並把所有可能受影響的約 8,580 張票先隱藏。想重新公開的人，找出主旨為「Your Ticket」的票券信，點連結把選項改回「是」並儲存；有疑問可寫信到 dpo@wordpress.org。

→ [原文](https://make.wordpress.org/community/2026/10/28/attendee-privacy-issue-on-ticket-pages-what-happened-and-what-we-did/)

### 彰化小聚親子場 #48：組盆多肉好過年（11/29 週日）

11/29（日）14:00–16:00，在田尾的「多肉好好買」（彰化縣田尾鄉柳鳳村民族路二段 143 巷 12 號）一起組一盆約 20 公分的多肉盆栽，每組 350 元。這是親子交流場，不是技術講座，適合帶家人一起參加；現場有蘇卡達象龜。交通可搭火車到員林或高鐵到彰化，再轉高鐵快捷公車到田尾站，接著騎共享電動車或 MOOVO 單車前往。

→ [活動頁](https://www.meetup.com/changhua-wordpress-meetup-group/events/316974028/)

---

## 在地工作室部落格

### 設計湖：SHOPLINE、CYBERBIZ、91APP 要不要搬到 WooCommerce？

設計湖整理開店平台搬家的判斷方式：先把「平台月費＋抽成＋付費 App」和「主機與維護費＋授權費」放在一起算，再用「建置費 ÷ 每年省下的費用」估回本時間。文中列出四種適合搬的情況，並提醒搬家前要清點的資料：會員密碼無法匯出（顧客要重設）、金流、物流與電子發票帳號都得重新申請、舊網址要做 301 轉址；建置期一般約 1～3 個月。適合轉給正在猶豫要不要離開開店平台的客戶。

→ [原文](https://www.design-hu.com/web-news/ecommerce-platform-to-woocommerce.html)

### iseeu：部落格標籤怎麼命名才有用

iseeu 分享標籤整理方法：把「地點＋類別＋特色」組成一個標籤，刪掉單純地名、店名和只用一次的標籤，每篇文章掛 2～3 個就好。文中案例把 80 篇文章重新整理成 6 個主題標籤，再搭配 6 篇彙整文，3 個月內每日瀏覽量從約 800 成長到超過 1 萬。作者也認為在 AI 搜尋時代，標籤結構清楚更容易讓內容被理解與引用。

→ [原文](https://iseeu.tw/tag-naming/)

---

## 主機商觀點

### Kinsta：PHP2Shell 修補全紀錄（廠商立場）

Kinsta 10/29 公開這次 PHP 漏洞的處理過程：到 10/23 為止 Cloudflare WAF 攔下超過 55 萬次請求；修補前先全面關閉 `session.upload_progress` 作為緩解；PHP 7.4、8.0、8.1 這些官方已不支援的舊版，修補套件來自 LTS 廠商 Freexian。數十萬個容器分批更新，10/22 中午（台灣時間）還剩 1,888 個（約 0.5%），當天下午全部完成。內容是 Kinsta 說明自家服務，但「WAF 先擋、設定先緩解、舊版靠 LTS 廠商補」的順序，自管主機的人也可以參考。

→ [原文](https://kinsta.com/blog/php2shell-patch-rollout/)

### Kinsta：AI 不會讓網站專案變簡單（廠商立場）

Kinsta 從代理商角度指出，AI 讓產出程式碼變快，但工作量轉移到審查、整合與長期維護：以「表單外掛串 CRM」為例，權限檢查、資料流向、出問題時誰負責，都還是要有人想清楚。文中引用 DORA 2025 報告佐證。對接案者來說，可以拿來跟客戶溝通「為什麼用了 AI 報價不會砍半」。

→ [原文](https://kinsta.com/blog/ai-complexity/)

---

## 社群熱議

### r/WordPress：爬了 1,400 萬個網站，快取比佈景主題更影響速度

一位開發者用自己的爬蟲掃過 Chrome UX Report 名單中約 1,480 萬個網域首頁，發現 34.4% 是自架 WordPress，並對照真實使用者的 Core Web Vitals 資料。幾個發現：有快取時，前 10 大佈景主題的回應時間都落在 267～355 毫秒，沒快取時差距才拉開（GeneratePress 約 0.96 秒、Woodmart 約 1.9 秒）；但有 38.3% 的 WordPress 網站偵測不到任何快取。快取外掛要配合主機環境，LiteSpeed Cache 在 LiteSpeed 主機上表現很好，在其他主機上反而可能比不裝更差。以 LCP 合格率來看，ACF 自訂網站 72%、傳統編輯器 68.8%、Gutenberg 64.7%、Elementor 44.5%。CDN 直接從快取回應時，99.5% 的網站回應夠快。

留言提醒，這份資料主要量的是首頁，購物車、結帳、搜尋這些無法快取的動態頁面才是效能瓶頸，作者也承認這是限制。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xhv1se/wordpress_insights_from_crawling_14_million/)
→ [完整報告](https://theweb.report/#wordpress)

### r/WordPress、r/woocommerce：機器人流量把網站拖垮怎麼辦

兩則討論都在談同一件事。一位站長每天只有約 1,000 次真實瀏覽，Cloudflare 卻顯示每天 6.6 萬名訪客、32 萬次請求、快取率只有 7%，不開「Under Attack」模式網站就每隔一小時當機。另一則問 WooCommerce 站內搜尋是否會被機器人濫用，回覆一致說會：搜尋結果無法快取，每一次都直接查資料庫，有人遇過每天數千次空白搜尋。

留言給的方向大致相同：用 Cloudflare 快取規則把 HTML 也快取起來（登入或有購物車 cookie 時略過），把快取率拉高；只對被狂打的路徑（例如 `?s=` 搜尋、wp-login.php、xmlrpc.php）設定 WAF 規則或速率限制，而不是全站開 Under Attack；空白搜尋直接不處理；也可以先到 Cloudflare 的安全事件記錄看看是哪些網址被打最多。

→ [討論串（Under Attack 才能運作）](https://www.reddit.com/r/Wordpress/comments/1xgtvc6/website_crashes_all_the_time_only_works_when/)
→ [討論串（站內搜尋被機器人濫用）](https://www.reddit.com/r/woocommerce/comments/1xhk9ru/do_you_protect_the_search_function_of_you/)

### r/woocommerce：Webhook 失敗不會重送，連續失敗還會被自動停用

發文者提醒，如果串接 WooCommerce webhook 的系統（例如倉儲、ERP）某天起突然收不到訂單，先去「WooCommerce → 設定 → 進階 → Webhooks」看狀態是不是變成「已停用」。WooCommerce 官方文件與原始碼都證實：回應不是 2xx、301、302 就算失敗，失敗的內容不會重送；連續失敗超過 5 次，webhook 就會自動停用，必須手動重新啟用，而且不會寄信通知管理員。接收端維護個 20 分鐘，就可能讓 webhook 停掉、期間的訂單漏接。

留言建議：接收端收到就先回 200 再排進自己的佇列處理；定期用 REST API 依修改時間撈訂單做對帳；用 `woocommerce_webhook_disabled_due_delivery_failures` 這個 hook 或定時檢查 webhook 狀態，一被停用就發通知。發送紀錄可在「WooCommerce → 狀態 → 記錄」的 webhooks-delivery 查到。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xk4smq/woocommerce_webhooks_do_not_retry_after_more_than/)
→ [WooCommerce 官方文件](https://woocommerce.com/document/webhooks/)

### r/woocommerce：SumUp 官方金流外掛在 WordPress.org 被暫時下架

SumUp Payment Gateway for WooCommerce 的 WordPress.org 頁面顯示，外掛自 10/28 起暫時關閉、等待完整審查，無法下載，官方沒有說明原因。發文店家表示已安裝的外掛目前仍可收款，但不會再收到更新。

這則雖然是國外金流，處理原則對任何金流外掛都適用：外掛被下架時，已安裝的版本通常還能運作，但收不到安全更新；先備份目前的外掛檔案，比對近期金流後台的交易和 WooCommerce 訂單有沒有對不上，並在測試站準備好第二套金流，確認可正常下單後再決定是否切換。也可以到外掛的 SVN 提交紀錄觀察作者是否正在修正。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xk21sn/sumup_payment_gateway_for_woocommerce_closed_on/)
→ [WordPress.org 外掛頁](https://wordpress.org/plugins/sumup-payment-gateway-for-woocommerce/)

---

## 職缺（近 7 日）

本期職缺集中在台中與雙北，多數是網站管理或行銷兼顧 WordPress 的職位。

### 104｜SEO 數位行銷工程師（WordPress 專家）（好樂購家具）
台中北屯；月薪 42,000～45,000 元。以 WordPress 優化官網、整合外掛並改善 Core Web Vitals，透過 SEO 帶動導購。（頁面更新日期 11/02）

→ [職缺](https://www.104.com.tw/job/8zn18)

### 104｜資深 WordPress 專員（赫爾墨思行銷）
台中北屯；月薪 42,000～46,000 元。負責 WordPress 網站相關工作的資深專員職缺。（頁面更新日期 10/27）

→ [職缺](https://www.104.com.tw/job/93ass)

### 104｜WORDPRESS 網站管理行銷人員（炫煬國際行銷）
新北中和；月薪 36,000 元以上。管理與維護 WordPress 網站，並負責網站行銷。（頁面更新日期 10/29）

→ [職缺](https://www.104.com.tw/job/879me)

### 104｜品牌官方網站建置專員／電商架站顧問（兼職／專案外包）（竣天企業）
新北土城；時薪制（頁面未列金額）。以 WordPress／WooCommerce 架設品牌官網與電商網站，適合接案者。（頁面更新日期 10/29）

→ [職缺](https://www.104.com.tw/job/9751o)

### 104｜品牌電商營運暨 AI 網站實作專員（旺得富家具）
新北樹林；月薪 37,000 元，試用期後 40,000 元。負責電商營運，並實作 WordPress 與 AI 相關網站功能。（頁面更新日期 10/26）

→ [職缺](https://www.104.com.tw/job/96qgs)

### 1111｜WordPress 網頁設計師（陽信開發）
新竹東區；月薪 35,000 元以上。以 WordPress 進行網頁設計、內容更新與修改。（更新日期 11/01）

→ [職缺](https://www.1111.com.tw/job/132835238)

### 1111｜WordPress 網站工程師（約聘 6 個月）（美加文化實業／大美加行銷部）
台北中正；月薪 40,000～60,000 元。使用 Divi、Elementor 與區塊編輯器維護網站。（更新日期 10/30）

→ [職缺](https://www.1111.com.tw/job/130456287)

### 1111｜網站前端設計師（政久興業）
台北內湖；月薪 38,000 元以上。以前端與網站製作為主，職缺內容多次提到 WordPress。（更新日期 10/28）

→ [職缺](https://www.1111.com.tw/job/133049293)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
