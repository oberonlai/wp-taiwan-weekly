# WP 台灣週報 · 第 12 期

**出刊日：** 2026-11-16

本週重點：台灣這邊，好用版 Woomp 3.5.30 一次補強所有金流的付款通知驗證。舊版有好幾家金流可以在沒付款的情況下把訂單偽造成「已付款」，LINE Pay 甚至能被別人觸發退款，有用 Woomp 的店家請立刻更新。WooCommerce Stripe Gateway 也推出安全更新，其中 10.9.0 曾在特定條件下把顧客密碼明文存進訂單資料；WooCommerce 11.3 正式版上線。Apache 的 HTTP/2 模組出現可遠端執行程式的嚴重漏洞，這個得靠主機商修補，更新 WordPress 沒有用。WordPress 7.2 Beta 5 推出，RC1 就在 11/17，新的 Secrets API 要求主機商配合設定金鑰；官方另外推出搬站工具 Reprint 和旗艦主題 Mētis。活動方面，這兩週有高雄、桃園、台北、台南、彰化五場小聚。

---

## 本週熱點

### Woomp 3.5.30：所有金流的付款通知都加上驗證，請立刻更新

Woomp 3.5.30 於 11/13 推出，官方要求**所有站台盡快更新**。舊版有多家金流的付款通知網址沒有確認請求是不是真的來自金流商，攻擊者可以在完全沒付款的情況下，把訂單偽造成「已付款／處理中」：

- **風險最高**：PChomePay 支付連的付款通知完全不驗證；PayUni 統一金流舊版 ATM／超商代碼的繳費通知不驗簽章；速買配 SmilePay 的驗證碼可被猜中，物流通知也不驗證。
- **高風險**：綠界、藍新在金鑰沒填或開著測試模式時可被偽造；LINE Pay 任何人都能對別人的訂單觸發退款與取消。
- **中風險**：PayUni、PayNow、綠界、藍新已取消或已退款的訂單，可能被重送的舊通知改回「處理中」。

新版規定，金流回呼在改訂單狀態前必須通過四關：簽章正確且金鑰不可為空（沒有簽章機制的 PChomePay 改由網站主動向金流商查詢）、付款方式與交易編號要對得上這筆訂單、金額與商店代號要相符、只有「待付款／保留／失敗」的訂單能被標成已付款。被拒絕的請求一律寫入日誌。另外也修了 PayUni 可以用別人已儲存的卡片、PChomePay 的 API token 存成可下載檔案、LINE Pay 顧客自行退款其實沒有真的退錢等問題。

兩個行為改變要知道：綠界測試模式下，在綠界後台按「模擬付款」不會再把訂單改成已付款，測試請改用綠界測試信用卡實際刷卡；更新當下正在速買配選門市的顧客需要重選門市。

建議動作：更新後，每個啟用中的金流各完成一筆真實付款，確認訂單會自動變成「處理中」；再到「WooCommerce → 狀態 → 日誌」選來源 `woomp-payment-callback`，確認正常付款沒有出現 `callback rejected`。如果正常付款被拒，訂單會停在待付款（不會被誤標成已付款），先手動改成「處理中」並把日誌回報給開發者。

→ [原文（Woomp 3.5.30）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.30)

### WooCommerce Stripe Gateway 安全更新：請更新到 11.2.0（或 11.1.2）

WooCommerce 官方 11/12 發文，請所有使用 WooCommerce Stripe Gateway 的商店立即更新到 11.2.0；必須留在 11.1.x 的商店請更新到 11.1.2。這次修補的問題來自 Automattic 的主動掃描與外部回報，最嚴重的可能讓人完成詐欺購買，目前沒有遭利用的證據。

另一個要檢查的是舊問題：**10.9.0** 在以下條件同時成立時，會把顧客在結帳時設定的新帳號密碼以明文存在訂單資料裡——使用傳統（非區塊）結帳、結帳頁顯示 Stripe 快速結帳按鈕、顧客在結帳時建立帳號並用 Apple Pay 或 Google Pay 付款。這些密碼管理員與商店經理在訂單頁就看得到，也能透過 WP-CLI、REST API 讀到。10.9.1 已停止這樣存，11.1.0 起更新後會在背景清除舊資料，清到資料時會在後台顯示通知並寄信給網站管理員。

建議動作：到「外掛 → 已安裝的外掛」確認版本，開了自動更新或主機代管也要親自確認；有 staging、開發站的也要逐一檢查。如果你的商店曾符合上述 10.9.0 條件，即使沒收到信，也請檢查備份、訂單匯出檔與會同步訂單資料的外部服務，並考慮請受影響的顧客重設密碼。注意 11.1.2／11.2.0 需要 WordPress 6.9 與 WooCommerce 11.0 以上。

11.2.0 本身也修了幾個電商常見狀況：已取消的訂單又付款成功時會自動退款或作廢、中斷後重送結帳或兩個分頁同時送出不會重複扣款、從正式站複製出來的 staging 站連接或中斷 Stripe 時不會再刪掉正式站的 webhook。

→ [原文（WooCommerce 開發者部落格）](https://developer.woocommerce.com/2026/11/11/stripe-gateway-security-update/)

### Apache HTTP/2 嚴重漏洞：要靠主機修補，更新 WordPress 沒有用

Apache 11/11 釋出 2.4.71，修補 CVE-2026-119618：負責 HTTP/2 的 mod_http2 模組有記憶體損毀問題，可能導致遠端執行程式。受影響的是 Apache 2.4.55～2.4.70（以及獨立的 mod_h2 2.0.8～2.0.42），而且伺服器要有啟用 HTTP/2（h2 或 h2c）才會中招；Apache 預設是 HTTP/1.1。目前公開資料能讓伺服器當機，但還沒有公開的遠端執行示範，也沒有已知的實際攻擊。

重點是：這是網頁伺服器層的漏洞，更新 WordPress、外掛或安全外掛都修不到。網站前面有 CDN 或負載平衡器提供 HTTP/2，也不代表後端的 Apache 有開，要看 Apache 本身的設定。

建議動作：用共享或代管主機的，先看主機商公告，找不到就直接問「你們的 Apache 有沒有接受 h2／h2c？CVE-2026-119618 修好了嗎？更新後有重啟嗎？」自己管主機的，安裝 2.4.71 或作業系統、控制台廠商的修補套件並重啟（套件版本號可能不同）；暫時無法更新的話，可以先從 Apache 設定移除 h2、h2c。

→ [原文（Apache 安全公告）](https://httpd.apache.org/security/vulnerabilities_24.html)
→ [原文（Wordfence 說明）](https://www.wordfence.com/blog/2026/11/cve-2026-119618-apache-http2/)

### WordPress 7.2 Beta 5：RC1 在 11/17，Secrets API 要主機商配合

7.2 Beta 5 於 11/11 推出，RC1 預定 11/17、正式版 12/8 不變。這版的修正包括：建立應用程式密碼時會寄通知信、後台工具列鍵盤導覽改善、Notes 表情回應的權限加強、MP3 上傳相容性；新預設主題 Ipsum 的焦點樣式與動畫也會尊重「減少動態效果」設定。PHP 8.6 預計 11/19 釋出，官方表示 WordPress 已知的 PHP 8.6 相容問題全部解決，但外掛與佈景主題要自己確認。

本週最值得注意的是 **Secrets API**：7.2 新增一套加密儲存 API 金鑰、OAuth 密鑰等憑證的機制，外掛可以用 `wp_set_secret()`／`wp_get_secret()` 存取，印出或寫進日誌時會自動遮罩，並保留前一個版本方便輪替金鑰。Beta 5 起，網站的 `wp-config.php` 沒有設定 `WP_SECRETS_KEY` 時這個 API 會整個關閉，而且只有用瀏覽器安裝 WordPress 時才會自動寫入這把金鑰——升級上來的舊站，以及用 `wp config create`、一鍵安裝、自動化腳本建立的新站都不會有。官方因此請主機商在 RC1 前測試：替新站自動寫入金鑰、規劃既有站的金鑰從哪裡來。開發者留言中也有人要求把這個 API 移出 7.2，最後是否隨 7.2 推出仍有變數。

主機商公告另外提醒：升級到 7.2 後要清除各站的 rewrite 規則與頁面快取，新的 `/random/` 隨機文章網址才會正常。

本週其他 7.2 開發者說明：

- **文字陰影**：段落與標題區塊在「排版」面板可以選文字陰影預設值，佈景主題可定義預設組合，其他區塊可在 `block.json` 用 `supports.typography.textShadow` 加入。
- **編輯器「選項」與「檢視」選單改用新元件**：外掛加的選單項目移到新的「Panels」子選單；未公開的 `as` 屬性會被忽略並顯示棄用警告，依舊 CSS class 名稱寫的樣式或自動化測試需要改。

→ [原文（Beta 5）](https://wordpress.org/news/2026/11/wordpress-7-2-beta-5/)
→ [原文（Secrets API 開發者說明）](https://make.wordpress.org/core/2026/11/11/the-secrets-api-in-wordpress-7-2/)
→ [原文（給主機商的測試呼籲）](https://make.wordpress.org/hosting/2026/11/12/call-to-test-wordpress-7-2-secrets-api/)
→ [原文（文字陰影）](https://make.wordpress.org/core/2026/11/11/new-block-support-in-wordpress-7-2-text-shadow/)
→ [原文（編輯器選單）](https://make.wordpress.org/core/2026/11/11/editor-the-options-and-view-menus-now-use-the-new-menu-component/)

### Reprint：官方推出開源搬站工具

WordPress.org 11/13 介紹 Reprint，一個免費、開源的整站複製工具，包含外掛與命令列程式，可以把網站的檔案和資料庫搬到新主機，或複製一份到自己電腦當測試站。它會把資料一段一段直接傳到目的地，舊主機不需要先騰出空間打包整站；中途斷線可以從上次的進度續傳；換網址時會連同設定、區塊、短代碼、序列化資料裡的連結一起改；重複複製時只傳有變動的檔案。

搬家建議流程：先複製、在新主機上檢查頁面、圖片、表單都正常，正式切換前暫停舊站的修改、再複製最後一次，然後把網域指到新主機。不想動自己的網站，也可以先在 WordPress Playground 的瀏覽器版試用。

→ [原文](https://wordpress.org/news/2026/11/reprint-your-site/)

### Mētis：WordPress 的新「旗艦主題」

Make Core 11/13 發表 Mētis。它原本是下一個預設主題的第一個設計方向，後來預設主題選了 Ipsum，Mētis 則應 Matt Mullenweg 要求獨立發展成「旗艦主題」——預設主題要適用所有網站，旗艦主題則可以放手展示編輯器目前能做到什麼。內容包括商業、新聞、雜誌、作品集、作家、個人六種入門網站範本，50 個區塊樣式，6 種樣式變化（其中一種是深色版，並會依訪客裝置的深色模式自動切換配色），以及 9 套開源字體。目前是設計完成的版本，接下來進入正式開發審查，可以先看示範站。

→ [原文](https://make.wordpress.org/core/2026/11/13/introducing-metis-our-new-flagship-theme/)

### Gutenberg 24.4 正式版

上期介紹的 24.4 已於 11/13 正式推出，隔天再出 24.4.1。除了 RC 階段就有的 Notes 搜尋、篩選與表情回應，正式版也讓「全域樣式」會列出哪些區塊的設定蓋過了全域設定，點一下就能跳到該區塊調整；實驗中的媒體編輯器可預覽 PDF；從瀏覽器備份還原草稿時多一層保護。

→ [原文](https://make.wordpress.org/core/2026/11/13/whats-new-in-gutenberg-24-4-12-november/)

---

## WooCommerce／電商

### WooCommerce 11.3：特價標籤可顯示折扣、可設定退換貨政策頁

WooCommerce 11.3.0 於 11/12 推出，向下相容，含資料庫更新。店家比較有感的改動：

- **特價標籤區塊**可以顯示折扣金額、折扣百分比或自訂文字，不用再寫程式。
- 商品、精選商品、精選分類、商品分類清單等區塊多了顏色、間距、框線控制。
- **商品圖庫區塊**在手機上改用原生捲動，滑動手感更自然。
- 可以指定**退換貨政策頁**，WooCommerce 會在頁面清單標示，並加上結構化資料，方便搜尋引擎與 AI 代理找到。

開發者面向的 REST API 退款分配驗證、商品篩選與 MCP 連線變更，請看官方發布前說明。更新前建議先在測試站跑過。

→ [原文（11.3.0 發布說明）](https://developer.woocommerce.com/2026/11/11/woocommerce-11-3-0-release-notes/)

### WooPayments 11.3.0：修正訂閱續訂沒扣款卻被標成已付款

同一天推出的 WooPayments 11.3.0 修了幾個會影響收款的問題：訂閱續訂失敗後，顧客改用需要 3D 驗證的卡時，續訂訂單可能沒扣到款就被標成已付款；Apple Pay、Google Pay、Amazon Pay 付款不會再因為 Level 3 資料的金額四捨五入而失敗；零元訂單的導回也會確認付款意圖屬於這筆訂單。另外新增「疑似詐欺預警」付款的待辦提醒。

→ [原文（WooPayments 更新紀錄）](https://wordpress.org/plugins/woocommerce-payments/#developers)

### RY Tools 2026.11.7：排程改用 Action Scheduler

RY Tools for WooCommerce 11/10 推出 2026.11.7，把外掛的排程事件改由 Action Scheduler 執行。之後要查排程是否卡住，可以到「WooCommerce → 狀態 → 排程動作」看。

→ [原文（RY Tools 外掛頁）](https://wordpress.org/plugins/ry-woocommerce-tools/#developers)

### 其他電商相關外掛漏洞

- **Woocommerce Wishlist**（非 TI 那款）1.1.0 以前（CVE-2026-124763，CVSS 9.1）：未登入就能覆寫網站任意設定，可讓網站壞掉；目前沒有修補版，建議停用移除。
- **Printaura-Woocommerce** 4.0.2 以前（CVE-2026-115733，CVSS 9.1）：讓所有 WooCommerce REST API 路由都不需驗證，未登入者可讀寫商店資料。
- **Security For WooCommerce** 1.0.16 以前（CVE-2026-123695，CVSS 9.8）：未登入者可替任何使用者產生登入權杖，直接登入成管理員。
- **Smoove abandoned cart trigger for WooCommerce** 4.3.6 以前（CVE-2026-123134，CVSS 8.8）：訪客送出的棄單資料沒過濾，會在後台執行惡意腳本，請更新到 4.3.6 以上。

→ [原文（Woocommerce Wishlist）](https://www.cve.org/CVERecord?id=CVE-2026-124763)
→ [原文（Printaura）](https://www.cve.org/CVERecord?id=CVE-2026-115733)
→ [原文（Security For WooCommerce）](https://www.cve.org/CVERecord?id=CVE-2026-123695)
→ [原文（Smoove）](https://www.cve.org/CVERecord?id=CVE-2026-123134)

---

## 資安

### Contact Form 7 6.2.5：修補兩個資料外洩問題

上期 6.2.4 修完嚴重的檔案上傳漏洞後，Contact Form 7 11/10 再推 6.2.5，修補兩個資訊外洩：郵件標籤 `[_post_*]` 可能洩漏非公開文章的資料，表單欄位的 `default:user_*` 選項可能洩漏使用者的其他資料（現在改用允許清單限制）。官方說風險不高，但仍建議盡快更新。6.2.5 需要 WordPress 7.1 以上。

→ [原文](https://contactform7.com/2026/11/10/contact-form-7-625/)

### 其他高風險漏洞

- **NextGEN Gallery**（約 30 萬個安裝）4.5.5 以前（CVE-2026-118191，CVSS 8.1）：有「管理相簿」權限的人可以刪除伺服器任意檔案（例如 wp-config.php），預設只有管理員有此權限，但若曾把權限下放給其他角色就有風險；請更新到 4.5.6。
- **Forminator**（約 60 萬個安裝）1.58.4 以前（CVE-2026-111579，CVSS 8.1）：付費註冊表單在金流曾連接後又中斷時，訪客可以不付款就完成註冊並取得表單設定的角色（可能到管理員）；請更新到 1.58.4。
- **Widget Options**（約 10 萬個安裝）4.2.6.1 以前（CVE-2026-116615，CVSS 8.8）：可經由文章內容注入並執行程式碼；目前為 4.2.7。
- **Everest Forms**（約 9 萬個安裝）3.6.2 以前（CVE-2026-120513，CVSS 8.2）：啟用「儲存並繼續」附加元件時，未登入者可刪除上傳目錄裡的檔案；請更新到最新版（目前 3.6.4）。
- **User Registration & Membership**（約 5 萬個安裝）5.3.2 以前（CVE-2026-109870，CVSS 8.1）：付費會員方案套用 100% 折扣券時沒有檢查折扣券是否適用，可免費取得付費方案的角色；請更新到 5.3.2。
- **WPML**（付費）4.9.7 以前（CVE-2026-76877，CVSS 8.1）：「每個語言一個網域」的跨網域登入功能有競爭條件，可能被冒用登入；請更新到最新版。
- **SureDash** 1.13.0 以前（CVE-2026-123769，CVSS 8.8）：入口網站管理員角色可以改預設註冊角色，進而提權；請更新到 1.13.1。

→ [原文（NextGEN Gallery）](https://www.cve.org/CVERecord?id=CVE-2026-118191)
→ [原文（Forminator）](https://www.cve.org/CVERecord?id=CVE-2026-111579)
→ [原文（Widget Options）](https://www.cve.org/CVERecord?id=CVE-2026-116615)
→ [原文（Everest Forms）](https://www.cve.org/CVERecord?id=CVE-2026-120513)
→ [原文（User Registration & Membership）](https://www.cve.org/CVERecord?id=CVE-2026-109870)
→ [原文（WPML）](https://www.cve.org/CVERecord?id=CVE-2026-76877)
→ [原文（SureDash）](https://www.cve.org/CVERecord?id=CVE-2026-123769)

---

## 外掛動態

### Elementor 4.4：AI 代理就緒檢查、更多 MCP 功能

Elementor 4.4.0 於 11/9 推出。新功能「Agent Ready」會替網站打分數，看網站對 AI 代理是否友善，並提供內容探索工具與機器人存取控制；另外新增 SVG 圖示庫（支援 Font Awesome 7）、清單元件、Google 地圖元件，手風琴元件可以分別設定展開與收合圖示，也加入「只能編輯內容」的權限。Elementor MCP 這版大幅擴充：AI 代理可以操作更多舊版小工具、清除快取、讀取系統資訊、上傳圖片到媒體庫，還能用修訂版本復原 AI 做的變更。

→ [原文（Elementor 更新紀錄）](https://wordpress.org/plugins/elementor/#developers)

### Wordfence 9.1：防火牆規則更新更快、看得到應用程式密碼登入失敗

Wordfence 9.1.0 於 11/9 推出（隔天 9.1.1 更新憑證檔）：防火牆規則檢查更新的頻率提高；「即時流量」會記錄透過應用程式密碼與 REST API 的登入失敗；攻擊警示信可以自訂門檻與計算時間，管理很多網站時可減少通知轟炸；也加入 PHP 8.6 相容修正。所有使用者（含免費版）都適用。

→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/11/wordfence-9-1-0-whats-new/)

---

## 台灣站務與活動

### 高雄小聚：你的網站在 AI 時代如何建立數位軌跡（11/17 週二）

11/17（二）19:00–21:00，Second Space RED（高雄市新興區七賢一路 294 號），場地費 100 元。由 KAIROS ENGINE 分享網站在 AI 時代如何留下數位軌跡。入場需先到 Miso 報名，憑報名成功信裡的簽到憑證開門（18:30–21:00 有效）。

→ [活動頁](https://www.meetup.com/kaohsiung-wordpress-meetup/events/317336764/)

### 桃園小聚 #63：有了 AI，大家駭起來（11/21 週六）

11/21（六）14:00–17:00，喜憨兒桃園南門公園庇護商店（桃園市桃園區三民路三段 264 號），低消一杯飲品。主講是 Tonny Chiu，之後有自我介紹與閃電講（歡迎現場報名）。場地只開放提早十分鐘入場；從桃園火車站騎 YouBike 約 4 分鐘。

→ [活動頁](https://www.meetup.com/taoyuan-wordpress-meetup/events/317297824/)

### 台北彩虹小聚：用 AI 做小遊戲，讓人人都能參與社會議題（11/24 週二）

11/24（二）18:30–21:30，台灣性別平等教育協會（台北市中正區羅斯福路三段 218-2 號 3 樓，公館），免費入場。Saori 與 Qmoa 分享用 AI 做出爆紅小遊戲《大樹神獸》的完整過程，之後有 6 位各 5 分鐘的閃電講。歡迎所有人參加。

→ [活動頁](https://www.meetup.com/taipei-wordpress/events/317270590/)

### 台南小聚 #11：我用 AI 讓 LINE 變成自動化助理（11/24 週二）

11/24（二）19:00–21:00，三皇三家歸仁店（臺南市歸仁區中山路三段 28 號），18:00 起可先到現場用餐，低消一份點心或飲料。講者余啟彰 Fish 分享用 AI 把 LINE 變成自動化助理。外縣市朋友可搭高鐵到台南站，報名時告知主辦，18:30 可一起接駁到會場。

→ [活動頁](https://www.meetup.com/kaohsiung-wordpress-meetup/events/317339884/)

### 彰化小聚親子場 #48：組盆多肉好過年（11/29 週日）

11/29（日）14:00–16:00，田尾「多肉好好買」，每組 350 元，以組為單位報名，親子一起做直徑約 20 公分的多肉組盆。

→ [活動頁](https://www.meetup.com/changhua-wordpress-meetup-group/events/316974028/)

### WordCamp Asia 2027 開賣：4/9–11 馬來西亞檳城

WordCamp Asia 2027 將於 2027 年 4 月 9–11 日在檳城 Penang Waterfront Convention Centre 舉行，一般票 35 美元（含兩天議程、午餐與會後派對），分批釋出。4/9 的貢獻者日要另外報名，2027 年 1 月 27 日開放，限 600 人；贊助方案也已開放申請。

→ [原文](https://wordpress.org/news/2026/11/wc-asia-2027-penang/)

---

## 在地工作室與教學部落格

### 犬哥網站：Claude Code＋Elementor MCP 做咖啡廳網站

搭配影片示範從本機 WordPress 環境開始，設定 Elementor MCP 並連上 Claude Code，用提示詞讓 AI 做出精品咖啡廳首頁，再調整全站頁首、頁尾。重點是 AI 做完的頁面仍是 Elementor 版面，之後可以直接在 Elementor 裡改文字、換圖、調排版，不用碰 HTML、CSS。文中附上影片使用的提示詞。

→ [原文](https://frankknow.com/claude-code-with-elementor-mcp/)

### 傑哥架站教室：Bricks 原生 MCP，讓 AI 協作建站

以虛構的「慢焙咖啡」示範 Bricks 內建的 MCP：在「Bricks → AI → 設定」啟用能力、確認 WordPress 核心提供的 MCP 入口已連線、用應用程式密碼建立認證，再把設定交給 Claude 或 Codex。作者的心得是先由人決定頁面、配色變數這些大方向，再請 AI 照表建立頁面與頁首頁尾範本，最後人工檢查手機與平板版面，比一開口就叫 AI「做一個網站」有效得多。

→ [原文](https://jclassroom.net/bricks-native-mcp-build-site-from-scratch/)

---

## 主機商觀點

### Kinsta：所有方案免費加上 Patchstack 漏洞防護（廠商立場）

Kinsta 宣布與 Patchstack 合作推出「Vulnerability Protection」，所有 WordPress 方案不加價。它會持續比對站上的外掛、佈景主題與 WordPress 版本是否有已知漏洞，有的話就在應用層自動套用針對性的防火牆規則，連官方還沒出修補的漏洞也能先擋，直到網站更新為止。Kinsta 表示上線頭幾週已在 6.8 萬個網站擋下超過 150 萬次攻擊嘗試。這類「虛擬修補」能爭取時間，但仍不能取代實際更新外掛。

→ [原文](https://kinsta.com/blog/kinsta-launches-vulnerability-protection-powered-by-patchstack-to-protect-wordpress-sites-at-no-extra-cost/)

### WP Engine：給 AI 代理權限，分讀取、草稿、執行三層（廠商立場）

越來越多 AI 代理想讀取、撰寫甚至直接修改網站，WP Engine 建議把它當成權限問題處理：「讀取」只開放已公開內容並限速；「草稿」只在測試站進行、需人工核准並保留版本紀錄；「執行」（發佈、刪除、改設定、裝程式）預設不給，真的要給就縮小範圍、先備份、全程留紀錄。核心原則是最小權限，避免代理拿到超出任務所需的能力。

→ [原文](https://wpengine.com/blog/ai-agent-access-control/)

---

## 影音

### WordPress 官方頻道：你的 WooCommerce 商店錢漏在哪裡（西班牙語）

約 77 分鐘的西班牙語講座（11/11 上架），以一間正在合作的 WooCommerce 商店為例，把 WordPress 裡現成的訂單資料和 Analytics 交叉比對，找出哪些商品會讓顧客回購、沒結帳的購物車最後去了哪裡。聽得懂西語或願意開自動字幕的電商經營者可以參考分析思路。

→ [影片](https://www.youtube.com/watch?v=d2rNv-PJvMY)

### 犬哥網站：18 分鐘學會 SEO 基本觀念

約 19 分鐘的新手影片，說明搜尋引擎收錄與排名的運作方式、三個核心觀念、搜尋意圖、站內／站外／技術 SEO 的分工與反向連結，也談到 AI 搜尋時代的做法。適合剛接手網站經營的人建立基本觀念。

→ [影片](https://www.youtube.com/watch?v=MU9l8r4wHSc)

---

## 社群熱議

### r/WordPress：後台被外掛廣告塞滿，網友受夠了

一位接案者登入客戶網站只想用 UpdraftPlus 備份，結果備份按鈕被其他外掛的廣告擠到畫面下方，發文抱怨外掛在後台不斷推銷付費版。留言一面倒附和：有人乾脆裝外掛來隱藏其他外掛的通知，也有人說外掛的推銷程度已經成為他選外掛的考量之一，已經付費還跳出購買廣告的最惹人厭。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xsmn61/note_to_all_those_who_sell_wordpress_plugins_no/)

### r/WordPress：交站多年，還一直收到舊客戶網站的通知信

做了上百個網站的開發者抱怨：客戶接手後沒改管理員 Email，WordPress 又要求新信箱點確認才生效，結果他一直收到不再負責的網站寄來的通知。留言整理出幾個做法：還能登入的網站，用 WP-CLI `wp option update admin_email 客戶信箱` 可以跳過確認直接改，或到 `wp-admin/options.php` 直接修改；有些外掛通知是寄到你個人帳號的 Email，交站時也要改掉或刪除自己的帳號；最根本的是交站時開視訊，請客戶當場完成信箱確認。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xsq184/stop_email_notifications_from_wordpress_sites_you/)

### r/woocommerce：沒付款的訂單一直占著最後幾件庫存

如果商品明明沒人付錢卻顯示缺貨，請檢查「WooCommerce → 設定 → 商品 → 庫存 → 保留庫存（分鐘）」。官方文件說明，這個設定只會自動取消「等待付款中」的訂單並釋出庫存，**不包含「保留」狀態的訂單**，這類訂單會一直占著庫存直到有人手動處理。建議：確認各付款方式建立的訂單是什麼狀態；限量商品設短一點的保留時間；每週篩出「保留」與「等待付款中」的訂單清掉；會跳轉到外部付款頁的金流，保留時間要比金流的付款時限長，免得顧客晚一點付款時訂單已經被取消。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xspfqt/unpaid_orders_keep_selling_your_last_items_what/)
→ [WooCommerce 商品設定文件](https://woocommerce.com/document/configuring-woocommerce-settings/products/)

### r/woocommerce：年底檔期前，先清點還有效的優惠券

發文者以一間開了三年、有約 400 張優惠券的商店為例：WooCommerce 優惠券沒有預設到期日，多數會永久有效；兩年前的黑五代碼還能用，被瀏覽器比價外掛找到就會不斷被套用；網紅代碼沒限制商品、也沒勾「排除特價商品」；「僅限單獨使用」預設沒勾，所以多張券能疊加，一筆訂單的毛利可能從 31 美元掉到 7 美元。後台的優惠券報表只看得到折扣金額，看不到成本。雙 11、黑五檔期前，值得花點時間清一次。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xu83a9/psa_count_your_live_coupons_heres_what_three/)
→ [WooCommerce 優惠券管理文件](https://woocommerce.com/document/coupon-management/)

### r/WordPress：對 Elementor 一直加 MCP 不滿

呼應本期 Elementor 4.4，一位用戶抱怨每次更新的更新紀錄都在講 MCP，還常跳出相容性警告，他只想要頁面編輯器，不需要網站裡內建 AI 層；他認為 Claude Code、Codex 已經能直接操作內容，建議 Elementor 至少把 MCP 拆成可選的獨立外掛。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xtofyq/woo_more_mcp_i_am_so_over_elementor/)

---

## 職缺（近 7 日）

本期職缺都來自 104，分布在雙北、台中與雲林，多數是兼顧 WordPress 官網維運、SEO 與內容的行銷或設計職。

### 104｜資深 WordPress 專員（赫爾墨思行銷）
台中北屯；月薪 42,000～46,000 元。負責外語專案的 WordPress 網站建置、維護、SEO 與功能開發。（更新日期 11/13）

→ [職缺](https://www.104.com.tw/job/93ass)

### 104｜WordPress 網站管理行銷人員（炫煬國際行銷）
新北中和；月薪 36,000 元以上。用 WordPress 架站、套版、安裝 WooCommerce 等外掛，維護官網、上下架商品並維運金物流串接。（更新日期 11/12）

→ [職缺](https://www.104.com.tw/job/879me)

### 104｜品牌網站企劃主任（睡眠王國實業）
新北五股；月薪 40,000～50,000 元。負責兩個品牌 WordPress 官網的日常維運，並製作活動頁與規劃 SEO 內容。（更新日期 11/10）

→ [職缺](https://www.104.com.tw/job/8yr1p)

### 104｜網頁編輯（環球生技多媒體）
台北信義；月薪 35,000 元以上。負責網站上稿、排版與 SEO，需懂基礎 HTML/CSS 或熟悉 WordPress 等 CMS。（更新日期 11/15）

→ [職缺](https://www.104.com.tw/job/8hat5)

### 104｜網頁設計師（禾橙視覺設計）
雲林北港；月薪 40,000～55,000 元。負責品牌官網與電商頁的 UI 設計與切版，熟悉 WordPress 加分。（更新日期 11/15）

→ [職缺](https://www.104.com.tw/job/8z450)

### 104｜數位行銷營運專員（晴好）
台中南屯；月薪 40,000～60,000 元。經營生鮮零售品牌的社群與官網，負責 WordPress 官網商品上架。（更新日期 11/15）

→ [職缺](https://www.104.com.tw/job/8t11b)

### 104｜整合行銷企劃專員（台芝國際）
台北中山；面議（經常性薪資達 4 萬元以上）。B2B 行銷，負責官網建置與內容更新，操作過 WordPress 或其他 CMS 加分。（更新日期 11/16）

→ [職缺](https://www.104.com.tw/job/93h04)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
