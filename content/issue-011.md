# WP 台灣週報 · 第 11 期

**出刊日：** 2026-11-09

本週重點：Contact Form 7 6.2.4 修補一個不需登入就能上傳任意檔案的漏洞，某些常見的 Apache 主機設定下還可能被遠端執行程式，這個外掛有上千萬個網站在用，請優先更新。上期報導的 WordPress 核心升級漏洞已被美國 CISA 列入「已遭利用」清單，攻擊嘗試超過 24 萬次。WooCommerce 推出 11.2.2 安全版，WooPayments 也有安全更新，官方另外說明 AI 讓漏洞回報暴增之後的因應方式；LiteSpeed Cache 被揭露在預設設定下可能快取到其他顧客的購物車與結帳資料。台灣這邊，Woomp 連發兩版補強 PayNow（立吉富）金流與物流的回呼驗證。7.2 Beta 4 推出，正式版仍是 12/8；11/24 台北彩虹小聚與台南小聚同晚舉行。

---

## 本週熱點

### Contact Form 7 6.2.4：免登入可上傳任意檔案，部分主機可被遠端執行

Contact Form 7 6.2.4 於 10/29 推出，11/8 公布漏洞編號 CVE-2026-114432（CVSS 9.8）。6.2～6.2.3 版的表單如果有「檔案上傳」欄位，攻擊者不需登入，就能組合多個缺陷上傳原本應該被擋下的檔案，而且檔案在送出後不會被清掉。若主機是 Debian／Ubuntu 預設的 Apache 設定（會公開列出目錄內容，且把 `.phar` 交給 PHP 執行），攻擊者可以找到這個檔案並執行程式；在 nginx 或允許 `.htaccess` 覆寫的 Apache 上，影響則限於上傳目錄被寫入檔案，但仍可能被當成網站自己的 HTML 或 SVG 內容提供出去。

修正後的副作用要留意：檔名處理改成「允許清單」，不在清單內的副檔名會自動被改成安全的副檔名。預設清單包含常見圖片、影音、PDF 與 TXT，如果你的表單需要收 ZIP 等其他格式，要用 `wpcf7_file_extension_safelist` 篩選器自行加入；有直接呼叫 `wpcf7_unship_uploaded_file()` 的程式碼，參數也有變動。

建議動作：確認外掛已更新到 6.2.4 以上（目前最新為 6.2.4.1）；有上傳欄位的表單，更新後實際測試一次能否正常收檔。

→ [原文（Contact Form 7 6.2.4）](https://contactform7.com/2026/10/29/contact-form-7-624/)
→ [原文（CVE-2026-114432）](https://www.cve.org/CVERecord?id=CVE-2026-114432)

### WordPress 核心升級漏洞已遭實際攻擊，列入 CISA 已遭利用清單

上期報導的 CVE-2026-119977（WordPress 4.7～7.1.3 核心升級期間的競爭條件，可免登入遠端執行程式）有新進展：美國網路安全暨基礎設施安全局（CISA）已將它列入「已知遭利用漏洞（KEV）」清單。Wordfence 11/5 更新，自漏洞公開以來已攔下超過 24 萬次攻擊，10/30 單日超過 7.4 萬次。攻擊手法是不斷請求 `/wp-content/upgrade/wordpress-版本號/setup-config.php` 這類可預測路徑，賭網站剛好在升級中。

Wordfence 也提醒：從舊版升級到修補版的那一次，跑的仍是舊的升級流程，但時間很短，所以還是應該盡快更新、不要拖。若想多一層保險，可以在升級前先於伺服器層封鎖 `/wp-content/upgrade/` 底下的 PHP 檔案。Wordfence 免費版的防火牆規則要到 11/27 才會生效。

建議動作：到「控制台 → 更新」確認已是 7.1.4，或你所在分支的修補版（例如 6.9.11、7.0.8）。

→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/11/attackers-actively-probing-for-upgrade-window-rce-vulnerability-in-wordpress-core/)
→ [原文（CISA KEV）](https://www.cisa.gov/known-exploited-vulnerabilities-catalog?field_cve=CVE-2026-119977)

### WordPress 7.2 Beta 4：翻譯更新可逐一挑選，正式版仍為 12/8

7.2 Beta 4 於 11/3 推出（Beta 3 已在上期隨 7.1.4 提前釋出）。這版主要是修正與打磨：Windows 高對比模式下的鍵盤焦點更清楚、右至左語系的區塊對齊修正，Abilities API、DataViews 與 REST API 也持續調整，新預設主題 Ipsum 補強了無障礙與樣式。站長比較有感的是「更新」頁面會逐一列出可用的翻譯更新，可以像外掛、佈景一樣挑選要安裝哪些。

時程沒有變：Beta 5 11/10、RC1 11/17，正式版 12/8。測試可用 WP-CLI `wp core update --version=7.2-beta4`、Beta Tester 外掛或 WordPress Playground，請勿在正式站測試。

本週也有兩篇 7.2 開發者說明值得外掛作者注意：

- **新增 `/random/` 網址**：造訪 `example.com/random/` 會隨機導向一篇已發布文章，也支援分類、作者、自訂文章類型（例如 `/random/category/news/`）。功能預設開啟，不想要可用 `wp_enable_random_content_redirect` 篩選器關閉；自訂文章類型可在 `register_post_type()` 用新的 `randomable` 參數排除。
- **後台選單與工具列圖示改用 SVG**：圖示從 Dashicons 字型改成內嵌 SVG。一般註冊選單的外掛不用改，但自訂後台配色方案，或用 CSS `::before` 隱藏、替換核心圖示的外掛要更新寫法。新 API 讓 `add_menu_page()` 可直接用圖示名稱（例如 `core-admin/pin`），工具列節點也多了 `icon` 參數。

另外，Matt Mullenweg 11/9 在 Make Core 發文，指出非多站模式的新會員註冊沒有套用較嚴格的使用者名稱過濾，提議讓新註冊規則更嚴格，並檢討系統寄出的通知信（例如留言通知是否不要預設附上內容），減少被垃圾訊息利用的機會。目前還是討論階段。

→ [原文（Beta 4）](https://wordpress.org/news/2026/11/wordpress-7-2-beta-4/)
→ [原文（/random/ 網址）](https://make.wordpress.org/core/2026/11/05/your-content-is-going-to-get-more-random/)
→ [原文（後台 SVG 圖示）](https://make.wordpress.org/core/2026/11/05/svg-icons-in-the-admin-bar-and-menu-in-wordpress-7-2/)
→ [原文（Conservative in What We Accept）](https://make.wordpress.org/core/2026/11/08/conservative-in-what-we-accept/)

### Gutenberg 24.4 RC：wp-env 可不用 Docker、Notes 能搜尋和按表情

Gutenberg 24.4 RC1 於 11/4 推出（正式版尚未釋出），24.3.1 也修正了 Notes 的錯誤。24.4 的亮點：`wp-env` 新增實驗性的執行方式，直接用本機安裝的 PHP 搭配 SQLite，不必再跑 Docker；Notes（協作備註）的總覽側欄可以搜尋、依狀態篩選，還能按讚與加上表情回應；社群圖示區塊可以在側欄直接改連結；文章與留言分頁數字區塊可設框線；群組區塊多了實驗性的「Canvas」版面。

→ [原文（24.4 RC1）](https://github.com/WordPress/gutenberg/releases/tag/v24.4.0-rc.1)

---

## WooCommerce／電商

### WooCommerce 11.2.2 安全版：Store API、REST API 多處強化

11.2.2 於 11/4（台灣時間 11/5 清晨）推出，官方建議盡快更新，這版包含資料庫更新。修補內容包括：Store API 的購物車數量、REST API 金鑰驗證、到店取貨的稅金計算；全域屬性改名後商品預設屬性的處理；訂單存檔失敗時不再繼續進入付款；商品 CSV 匯入的欄位對應驗證；後台連線端點也更嚴格。

開發者要注意兩個行為改變：Store API 結帳時，如果外掛掛在 `woocommerce_before_order_object_save`、`woocommerce_new_order` 或 `woocommerce_update_order` 的程式丟出例外，結帳會直接回傳 500 錯誤，不再帶著存一半的訂單繼續；另外兩個 Jetpack 連線端點改為必須是已登入的管理員瀏覽器工作階段，用 REST API 金鑰、WP-CLI 或應用程式密碼呼叫會得到 403。

11.3 Beta 2 於 11/3 推出，正式版預定本週。

→ [原文](https://developer.woocommerce.com/2026/11/04/woocommerce-11-2-2-release-notes/)

### WooPayments 安全更新：訂閱功能，各版本線都有修補版

WooPayments 11/4（台灣時間）發布安全更新，修補內建訂閱功能的問題（延續 8 月的修補）。每條版本線都有對應修補版，例如 11.2.0 → 11.2.1、11.1.0 → 11.1.1；9.0 以前的舊版請直接升到 11.2.1。官方特別提醒：就算開了自動更新或主機代管更新，也請親自到「外掛 → 已安裝的外掛」核對版本，對外公開的測試站也要檢查。

→ [原文](https://developer.woocommerce.com/2026/11/03/security-update-for-woopayments/)

### WooCommerce 說明安全策略：主動掃描、必要安全更新會自動安裝

WooCommerce 團隊 11/6 發文說明近期安全更新變多的原因：AI 工具讓找漏洞變容易，透過 HackerOne 收到的 Woo 回報一年內增加約 9 倍，其中約四分之三是中低風險。團隊自己也在找：8 月深度掃描 Woo 主程式與自家擴充，標出約 700 個潛在問題；8 月起每次合併程式碼都會自動跑 AI 安全掃描；10 月加入 Anthropic 的開源掃描計畫，相關修補放在 11.2.2 與 11.3.1。

對店家最重要的改變：從 Woo 11.2 起，WooCommerce.com 可以把擴充的安全版標為「必要更新」，有連線 WooCommerce.com 且訂閱有效的商店，會在下次檢查時（通常一天內）自動安裝，就算關閉自動更新也一樣（用程式明確阻擋自動更新的網站除外）。PHP 方面，11.3 會對仍用 PHP 7.4、8.0 的商店顯示提醒，11.5 是最後支援的版本。

→ [原文](https://developer.woocommerce.com/2026/11/06/woo-latest-security/)

### Woomp 3.5.28、3.5.29：PayNow 金流與物流回呼加上來源驗證

有用好用版 Woomp 內建 PayNow（立吉富）模組的店家請盡快更新。

- **3.5.28（11/4）金流**：舊版沒有確認付款通知真的來自 PayNow，偽造通知可能把未付款訂單標成已付款，或把已付款訂單改回保留中。新版會核對付款方式、金額與驗證碼（PassCode）才處理。更新後請到 PayNow 金流設定確認「Transaction Password」填的是 PayNow 的**賣場網站交易密碼**，填錯或留空的話付款通知會全部被拒；接著跑一筆真實交易，並到「WooCommerce → 狀態 → 日誌」選 `paynow-payment` 確認沒有驗證失敗紀錄。
- **3.5.29（11/5）物流**：選門市與貨態更新的回呼也加上驗證，避免被偽造請求改掉取貨門市或取消物流單。後台改門市改用 12 小時有效的一次性驗證碼；貨態回傳網址可改成每站獨立、帶 token 的新網址（到「WooCommerce → 設定 → 物流設定 → 立吉富」複製），舊網址也能繼續用，外掛會先向 PayNow 查詢確認。日誌來源為 `paynow-shipping`。

注意：另一個獨立外掛「Payment via PayNow for WooCommerce」不在這次修正範圍。另外 RY Tools 也推出 2026.11.4，微調結帳表單的 JavaScript。

→ [原文（3.5.28）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.28)
→ [原文（3.5.29）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.29)

### LiteSpeed Cache：預設設定可能把顧客購物車與結帳資料快取給別人看

LiteSpeed Cache（約 700 萬個安裝）7.9.1 以前有 CVE-2026-93389（CVSS 8.2）：在預設開啟的「快取 REST API」設定下，外掛會把 WooCommerce Store API 的購物車（`/wc/store/v1/cart`）與結帳（`/wc/store/v1/checkout`）回應當成公開內容快取，而且沒有依顧客工作階段區分。結果是後來的訪客可能直接拿到前一位顧客的姓名、地址、Email、電話、購物車內容，甚至購物車工作階段權杖與訂單金鑰。

建議動作：WooCommerce 網站請更新到最新版（目前為 7.9.3），並確認購物車、結帳頁沒有被任何一層快取（見本期社群熱議的檢查方式）。

→ [原文](https://www.cve.org/CVERecord?id=CVE-2026-93389)

### 其他電商相關外掛漏洞

- **Registration Form for WooCommerce** 1.1.4 以前（CVE-2026-119075，CVSS 9.8）：結帳時建立帳號可自選角色，未登入者能直接註冊成管理員，請更新到 1.1.5。
- **payever – WooCommerce Gateway** 4.8.0 以前（CVE-2026-93754，CVSS 9.8）：未經驗證的回呼可寫入 PHP 檔案進而遠端執行，目前為 4.9.0。
- **Printcart Store（Web to Print）** 2.13.2 以前（CVE-2026-19803，CVSS 9.8）：特定上傳設定開啟時，未登入者可寫入 PHP 檔，目前為 2.13.6。
- **Checkout Field Editor for WooCommerce（Pro）** 3.8.0 以前（CVE-2026-122041，CVSS 9.1）：結帳頁有檔案上傳欄位時，未登入者可刪除任意媒體檔，付費版請更新到最新版。
- **SureCart** 4.9.5 以前（CVE-2026-120574）：免密碼登入的 6 位數驗證碼沒有次數限制，可被暴力破解接管帳號，約 8 萬個安裝，目前為 4.12.1。
- **Easy Digital Downloads** 3.7.2 以前（CVE-2026-120538）：PayPal 訪客結帳填入管理員 Email 可能取得登入狀態，請更新到 3.7.3。
- **FluentCart** 1.6.2 以前（CVE-2026-120577）：特定角色可刪除伺服器任意檔案，目前為 2.3.0。

→ [原文（Registration Form）](https://www.cve.org/CVERecord?id=CVE-2026-119075)
→ [原文（payever）](https://www.cve.org/CVERecord?id=CVE-2026-93754)
→ [原文（Printcart）](https://www.cve.org/CVERecord?id=CVE-2026-19803)
→ [原文（Checkout Field Editor Pro）](https://www.cve.org/CVERecord?id=CVE-2026-122041)
→ [原文（SureCart）](https://www.cve.org/CVERecord?id=CVE-2026-120574)
→ [原文（Easy Digital Downloads）](https://www.cve.org/CVERecord?id=CVE-2026-120538)
→ [原文（FluentCart）](https://www.cve.org/CVERecord?id=CVE-2026-120577)

### Woo 擴充開發者：官方推出自動產生的 PHP stubs

WooCommerce 推出 `woocommerce/php-stubs` 套件，自動為 Marketplace 與 WordPress.org 上的 Woo 擴充產生 PHP stubs，讓 PHPStan 能檢查你的外掛對其他擴充的呼叫，而不必在分析環境裝齊所有外掛，也可以指定要對照的最低支援版本。Marketplace 擴充作者在主檔案標頭加上 `Woo Stubs: public`，下次發版就會產生公開 stubs。

→ [原文](https://developer.woocommerce.com/2026/11/03/php-stubs-for-extensions/)

---

## 資安

### HTTP2Shell：Brotli 壓縮函式庫漏洞，可經 WordPress pingback 觸發

CVE-2026-111497 是 Google Brotli 壓縮函式庫（1.0.3～1.2.0）的記憶體寫入漏洞，網路上被稱為 HTTP2Shell（其實和 HTTP/2 無關），CISA 已列入已遭利用清單。這不是 WordPress 或外掛的漏洞，而是伺服器層的函式庫，但公開的概念驗證利用 WordPress 未登入就能呼叫的 XML-RPC `pingback.ping`：讓網站去抓攻擊者準備的網址，伺服器在解壓惡意 Brotli 回應時觸發漏洞。Wordfence 在與概念驗證相同的環境（Debian 13、Apache、PHP 8.3.36）重現了遠端執行程式，換個 PHP 版本就沒成功，代表攻擊需要貼合伺服器環境，但仍不能掉以輕心。

要注意的是：更新 WordPress 不會修到這個函式庫；Wordfence 的「停用 XML-RPC 驗證」選項也不會關掉 pingback。

建議動作：託管主機用戶請直接問主機商「網站使用的 Brotli 是否已修補 CVE-2026-111497，相關服務是否已重啟」；自管主機請安裝作業系統的安全更新（例如 Debian 13 的 `libbrotli1 1.1.0-2+deb13u1`，版本號開頭仍是 1.1.0），並重啟 Apache／PHP-FPM；用容器的要以新的基礎映像重建，單純重啟容器沒有用。

→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/11/http2shell-wordpress-brotli/)
→ [原文（CVE-2026-111497）](https://www.cve.org/CVERecord?id=CVE-2026-111497)

### 其他高風險漏洞

- **LatePoint** 預約外掛 5.7.6 以前（CVE-2026-118066，CVSS 9.1）：未登入者可重設顧客帳號密碼，約 10 萬個安裝，目前為 5.7.8。
- **GiveWP** 4.19.1 以前（CVE-2026-118193，CVSS 8.8）：擁有 GiveWP 管理員角色者可讓捐款人註冊成高權限帳號，約 10 萬個安裝，請更新到 4.19.1.1。
- **Import and export users and customers** 2.5.11 以前（CVE-2026-120554，CVSS 8.8）：具建立使用者權限的自訂角色可提權為管理員，約 7 萬個安裝，目前為 2.5.13。
- **s2Member** 260927 以前（CVE-2026-86596，CVSS 9.8）：特定權限帳號可遠端執行程式，目前為 261106。
- **Advanced Custom Fields: Extended PRO** 0.9.2.6 以前（CVE-2026-120534，CVSS 9.8）：在開啟非預設的短代碼預覽，或伺服器缺少 OpenSSL 時，未登入者可呼叫任意 WordPress 函式，請更新付費版。
- **BuddyBoss App** 3.2.1 以前（CVE-2026-76124，CVSS 8.8）：訂閱者可上傳可執行檔案，請更新到 3.2.2。

→ [原文（LatePoint）](https://www.cve.org/CVERecord?id=CVE-2026-118066)
→ [原文（GiveWP）](https://www.cve.org/CVERecord?id=CVE-2026-118193)
→ [原文（Import and export users）](https://www.cve.org/CVERecord?id=CVE-2026-120554)
→ [原文（s2Member）](https://www.cve.org/CVERecord?id=CVE-2026-86596)
→ [原文（ACF Extended PRO）](https://www.cve.org/CVERecord?id=CVE-2026-120534)
→ [原文（BuddyBoss App）](https://www.cve.org/CVERecord?id=CVE-2026-76124)

---

## 台灣站務與活動

### 台北彩虹小聚：用 AI 做小遊戲，讓人人都能參與社會議題（11/24 週二）

11/24（二）18:30–21:30，在公館的台灣性別平等教育協會（台北市中正區羅斯福路三段 218-2 號 3 樓），免費參加，現場有點心。講者 Saori（系統分析師）與 Qmoa（AI 方舟計畫創辦人）分享以「種樹」為主題的遊戲《大樹神獸》：從靈感、遊戲設計、怎麼跟 AI 溝通、快速做出可玩版本，到在 Threads 上擴散、拿下黑客松冠軍的過程。後半場有 6 個各 5 分鐘的閃電講，可現場報名。彩虹小聚歡迎所有人參加，名額有限，報名後請務必出席或提前取消；鼓勵自備水杯。

→ [活動頁](https://www.meetup.com/taipei-wordpress/events/317270590/)

### 台南小聚 #11：我用 AI 讓 LINE 變成自動化助理（11/24 週二）

11/24（二）19:00 開始，在三皇三家歸仁店（臺南市歸仁區中山路三段 28 號），18:00 起就可以先到現場吃晚餐。講者是網路行銷講師余啟彰（Fish），分享如何用 AI 把 LINE 變成自動化助理；分享後可交流、接案工商，寫網站卡關也能找人問方向。現場低消一人一份點心或飲料，店家後方有收費停車場；外縣市朋友搭高鐵到台南站，報名時告知主辦，18:30 可一起接駁到現場。

→ [活動頁](https://www.meetup.com/kaohsiung-wordpress-meetup/events/317339884/)

### 彰化小聚親子場 #48：組盆多肉好過年（11/29 週日）

上期介紹過的親子交流場：11/29（日）14:00–16:00 在田尾「多肉好好買」組一盆多肉盆栽，每組 350 元，適合帶家人一起參加。

→ [活動頁](https://www.meetup.com/changhua-wordpress-meetup-group/events/316974028/)

---

## 在地工作室部落格

### iseeu：部落格寫不出題材？去社團找讀者一直在問的問題

iseeu 建議別再憑空想題材，改去臉書社團、Dcard、LINE 群組看大家反覆在問什麼：同一個問題底下很多人留「+1」「跪求」，就代表有搜尋需求。做法是每週固定逛 2～3 個社團把問題抄下來，累積後分類，同類問題整理成一篇、每段回答一個，控制在 1,500 字內講清楚。文中案例的旅遊部落客改用這種寫法後，每日流量從約 100 人成長到 2,000 人。作者也認為，用口語問句當標題、第一段就給答案的寫法，在 AI 搜尋時代更容易被引用。

→ [原文](https://iseeu.tw/blog-topic-ideas/)

### 鵠崙設計：B2B 外銷網站，簽約前要問清楚的七件事

鵠崙整理找外銷網站廠商時可以問的七個問題，並先說明自己也是網站公司：有沒有同產業外銷案例、產品型錄要依買家找產品的方式分類（系列、規格、料號搜尋、規格書下載）、多語系要有各自網址並成對設定 hreflang（只靠瀏覽器語言或 IP 自動切換，搜尋引擎可能只收錄一種語言）、翻譯由誰負責與校對、詢價資料除了寄信還要在後台留紀錄並測試會不會進垃圾信匣、海外速度要從目標市場實測，以及上線後主機、備份、資安由誰負責，網域與主機登記在誰名下。適合轉給正在找外銷官網廠商的製造業客戶。

→ [原文](https://www.design-hu.com/web-news/how-to-choose-b2b-export-website-company.html)

---

## 主機商觀點

### WP Engine：機器人最多只能用 80% 的 PHP worker（廠商立場）

WP Engine 說明，共享主機上許多網站出現莫名的 500 錯誤，原因是 AI 爬蟲大量讀取舊文章、分頁、篩選頁這些沒有快取的網址，把 PHP worker 全部占滿，連帶拖垮同一台主機上的其他網站。他們的做法是把流量分成人類與機器人：人類依網站各自排隊，機器人共用一條佇列輪流處理，並設下硬性上限，機器人最多只能用 80% 的 worker（10 個裡至少保留 2 個給真人）。內容是說明自家平台，但「別全擋爬蟲，而是限制它能吃掉的資源」這個思路，自管主機也可以用 PHP-FPM 池或速率限制來實作。

→ [原文](https://wpengine.com/blog/php-worker-cap-on-bots/)

### Kinsta：7.2 新預設主題 Ipsum 實作導覽（廠商立場）

7.2 正式版 12/8 推出前，Kinsta 先帶讀者拆解新預設主題 Ipsum：極簡的區塊主題，預設背景與內文對比達 10.26:1（WCAG AAA），每頁有「跳到內容」連結；範本透過隱藏的區塊樣式組成，內建約 60 個區塊樣式，文中也示範如何用 Block Bindings API 加入動態內容。想在 7.2 推出後用 Ipsum 做客戶站，可以先看這篇熟悉結構。

→ [原文](https://kinsta.com/blog/ipsum-wordpress-theme/)

---

## 影音

### WordPress 官方頻道：WordPress Hooks 入門工作坊

約 75 分鐘的新手工作坊（WordCamp Bengaluru 2024 錄影，11/6 上架），從頭解釋 action 與 filter 是什麼、WordPress 為什麼這麼依賴 hooks、怎麼在自己的程式裡使用，並導覽常用的 hooks。適合剛開始改佈景主題或寫小外掛的人。

→ [影片](https://www.youtube.com/watch?v=wIStT5sPQBw)

### 犬哥網站：ChatGPT＋Claude＋WordPress 做作品集網站

約 19 分鐘的實作影片，示範不寫程式，用 ChatGPT 與 Claude 輔助規劃與產出內容，在 WordPress 上做出可以自己編輯、自己上架作品的作品集網站，並分享幾個設定小技巧。

→ [影片](https://www.youtube.com/watch?v=VNtknZUX3N0)

---

## 社群熱議

### r/WordPress：客戶給的網站備份裡，藏著會執行 .exe 的 mu-plugin

一位接案者收到「客戶」寄來的 WP Migrate 匯出檔，被要求先在自己電腦的 LocalWP 或 XAMPP 跑起來。他沒有直接解壓，而是先用程式檢查：WordPress 核心與幾個外掛的檔案校驗碼全部正確，但 `wp-content/mu-plugins` 裡多了一個 `custom-fields-handler.php`，會在 WordPress 啟動時把上傳目錄裡一張假的 `.jpeg`（實際是 Windows 執行檔）改名成 `.exe` 並嘗試執行，失敗還會換八種方法再試。這段程式在一般 Linux 主機上跑不起來，目標顯然是在 Windows 電腦上打開備份的人。留言中作者補充，發案的 Upwork 帳號才兩天大、同時邀請了 30 位接案者，已向平台檢舉。

作者現在匯入別人給的備份前會做的事：先列出壓縮檔內容不解壓、檢查上傳目錄每個檔案的開頭位元組（看是不是偽裝的執行檔）、核對核心與外掛校驗碼但別以為核心乾淨就沒事、逐一讀 `mu-plugins` 與直接放在 `wp-content` 下的 PHP 檔、比對資料庫裡啟用的外掛和實際存在的資料夾，最後才在隔離的 Linux 容器裡匯入。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xr6qgf/found_a_muplugin_in_a_wp_migrate_export_that/)

### r/WordPress：Nexcess 不再單賣 LearnDash、Give、The Events Calendar、Kadence

Nexcess 官方頁面證實，LearnDash、The Events Calendar、Give 與 Kadence 改為只賣四合一方案，1 個網站每年 350 美元起，依網站數分級；既有的單一產品訂閱會在 11/16 以後的續約日轉成新方案，續約前 30 天會寄信通知，不能再只買其中一個。選擇不續約的話，外掛還能繼續運作，但不會再有支援、自動更新與安全修補。討論串裡不少代理商表示只用其中一套的客戶成本大增，打算評估替代方案。

有客戶網站用到這四套外掛的人，建議先查清楚續約日，再決定要接受新方案，還是趁續約前安排替代方案並規劃搬移。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xodgi1/nexcess_stops_selling_learndash_give_the_events/)
→ [Nexcess 方案說明](https://explore.nexcess.com/software-packages)

### r/woocommerce：大促前，怎麼確認購物車與結帳沒被快取

發文者每年協助小店準備黑色星期五，發現最常出事的不是主機掛掉，而是購物車或結帳頁被某一層快取住：主機的伺服器快取、加速外掛或 CDN。症狀包括購物車顯示別人的商品數量、折價券套不上、出現莫名的「工作階段過期」，而店主用自己電腦測試時一切正常。

留言補充了容易漏掉的地方：除了頁面本身，`wc-ajax` 請求與 `/wp-json/wc/` 底下的 Store API 也可能被快取（本期 LiteSpeed Cache 漏洞正是這種情況）；CDN 要另外設定「有 `woocommerce_items_in_cart` 或 `wp_woocommerce_session_` cookie 時略過快取」，外掛的排除設定不一定會套用到 CDN；Cloudflare「快取所有內容」這類規則會蓋過 WooCommerce 送出的不快取標頭；頁面快取時間要短於 nonce 有效期（12～24 小時）；Redis 物件快取也可能留著舊的購物車片段。檢查方法：用 `curl -I` 連續請求購物車頁兩次，看 `cf-cache-status`、`age` 等標頭；在完全登出的狀態，用兩個獨立裝置或網路各放不同商品、一個套折價券，確認金額互不干擾，最後完整下一筆測試訂單。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xqtz37/before_a_big_sale_how_do_you_doublecheck_cart_and/)
→ [WooCommerce 快取設定文件](https://developer.woocommerce.com/docs/best-practices/performance/configuring-caching-plugins/)

### r/woocommerce：排程動作一直「逾期」，Action Scheduler 是怎麼跑的

WooCommerce 的 webhook 發送、訂閱續訂與許多外掛的背景工作都靠 Action Scheduler。官方文件說明，預設每次只認領 25 個動作、處理約 30 秒，而觸發它的 WP-Cron 只在有人瀏覽網頁時才會跑，所以流量低的商店，排程可能整段時間都沒動。可以在「WooCommerce → 狀態 → 排程動作」依狀態篩選，打開失敗動作的紀錄看原因。

討論中的建議：在 `wp-config.php` 加 `define( 'DISABLE_WP_CRON', true );`，改用系統排程定時觸發（系統排程與 WP-CLI 擇一，不要兩個都跑）；積壓時可用 `wp action-scheduler run`，但先用 `--batch-size=25 --batches=1` 小量試跑、確認結果，再考慮加大；調整批次量或並行數之前，先找出最舊的逾期項目是哪個 hook，很多時候是某個逾時的 webhook 或寄信卡住整批，加大並行只會讓更多執行緒卡在同一個慢工作上。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xq1wlv/scheduled_actions_piling_up_as_pastdue_what/)
→ [Action Scheduler 效能文件](https://actionscheduler.org/perf/)

---

## 職缺（近 7 日）

本期職缺集中在雙北與台中，以網站管理、SEO 與內容編輯兼顧 WordPress 的職位為主。

### 1111｜網站管理／行銷（駿業國際精密）
新北汐止；月薪 34,000～45,000 元。以 WordPress 等 CMS 維護官網與產品內容，並負責前端調整、SEO、流量分析與數位行銷。（更新日期 11/05）

→ [職缺](https://www.1111.com.tw/job/132803015)

### 1111｜網站文案編輯與設計實習生（兼職）（上伊美）
新北新店；時薪 196 元以上。協助新網站規劃與 WordPress 架設、撰寫網站文案與部落格內容、版面設計及網站行銷，有經驗者優先。（更新日期 11/08）

→ [職缺](https://www.1111.com.tw/job/132529883)

### 1111｜SEO 行銷助理（政久興業）
台北內湖；面議（經常性薪資達 4 萬元以上）。在 Wix、Shopify、WordPress 等平台執行網站 SEO、關鍵字規劃、頁面結構調整與分析工具設定。（更新日期 11/03）

→ [職缺](https://www.1111.com.tw/job/133049286)

### 1111｜網頁設計師（三司達企業）
台中西屯；月薪 32,000～39,000 元。負責網站視覺、網頁製作、程式調整與網站更新管理，WordPress 列為必要技能。（更新日期 11/08）

→ [職缺](https://www.1111.com.tw/job/78600439)

### 1111｜新媒體網站資深編輯（循環管理事業）（雙橡園開發）
台中西屯；月薪 29,500～45,000 元。編輯 WordPress 網站，並規劃數位內容、社群主題、網路活動與品牌傳播。（更新日期 11/05）

→ [職缺](https://www.1111.com.tw/job/98689133)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
