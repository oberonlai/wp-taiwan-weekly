# WP 台灣週報 · 第 13 期

**出刊日：** 2026-11-23

本週重點：WordPress 7.1.5 一口氣修補 20 個資安問題，請確認網站已更新；同一天也推出 7.2 RC2。Wordfence 發現有一個 IP 從 10 月底開始集中掃描台灣 .tw 網站、試打已知漏洞，台灣站長值得順手做一次外掛健檢。伺服器這邊，PHP 與 NGINX 同週發布重大修補，其中 PHP-FPM 的 CVE-2026-129289 在特定設定下可透過一般 WordPress 請求遠端執行程式，這兩個都得靠主機商或自己更新伺服器。WooCommerce 11.3.1 是一次大範圍的安全性修補；台灣這邊，好用版 Woomp 連出四版修正 PayUni 定期定額換卡、ezPay 與 PayNow 電子發票，RY Tools 加入金流通知異常封鎖。7.2 方面，上期提到的 Secrets API 已確定撤出 7.2。活動有台北、台南、彰化、新竹四場小聚。

---

## 本週熱點

### WordPress 7.1.5：修補 20 個資安問題，請立刻確認更新

WordPress 7.1.5 於 11/19 推出（台北時間凌晨），官方要求**立即更新**。這次一共 20 項：18 個漏洞加 2 項強化。比較需要注意的幾個：

- **Template Part 區塊屬性**：官方描述為「未登入即可提權、可能導致遠端執行程式」。Wordfence 的分析是：攻擊者做好一條特製的網站編輯器連結，只要管理員點開，就會在背景替攻擊者建立一個管理員帳號（CVSS 8.8）。
- **多站台（Multisite）**：單一子站的管理員可以透過自訂器的選單預覽，接管整個網路的超級管理員帳號（CVSS 9.1）。
- **未登入就能利用的**：留言裡的短代碼屬性可以藏入惡意腳本、REST API 搜尋端點會洩漏私密分類、大量 XML-RPC pingback 可以把網站拖垮。
- 另外還有兩個 SQL 注入（WP_Meta_Query、自訂器首頁設定預覽）與多個 XSS；Imgur 嵌入也被移除。

修補已一路回補到 4.7，舊分支也有對應版本，例如 7.0.9、6.9.12，最舊到 4.7.40。有開自動背景更新的網站多數會自己更新，但還是建議到「控制台 → 更新」確認版本號；有關掉自動更新、或由代管商控管更新的網站，請主動確認。

同一天也推出了 **7.2 RC2**，已經包含這批修補；原本排在 11/24 的 RC2 因此提前。正式版仍預定 12/8。

→ [原文（WordPress 7.1.5）](https://wordpress.org/news/2026/11/wordpress-7-1-5-release/)
→ [原文（Wordfence 逐項分析）](https://www.wordfence.com/blog/2026/11/wordpress-7-1-5-security-release-fixes-19-vulnerabilities/)

### 台灣 .tw 網站被集中掃描：一個 IP 試打已知漏洞

Wordfence 11/20 發表分析：從 10 月 26 日開始，被攻擊的 .tw 網站數量大約變成原來的三倍，日本、韓國、香港、新加坡、中國的網域都沒有類似變化。追查下來，多數流量來自同一個 IP **45.38.60.254**。它幾乎只打台灣網站，Wordfence 防火牆已經擋下或記錄超過 24.6 萬次請求；在「惡意檔案上傳」規則擋下的請求裡，有 82.7% 打的是 .tw 網站。

這些請求都是在試打 2017～2024 年已經公開的舊漏洞，例如 jQuery File Upload 的上傳漏洞。它上傳的只是用來確認「能不能傳上去」的測試檔，沒有發現後門程式。11/14 之後，這個 IP 每天碰到的網站已經降到 15 個以下，但這類掃描的目的就是找出還沒修補的網站，被掃到的漏洞之後仍可能被拿來攻擊。

建議動作：把停用或不再使用的外掛、佈景主題直接刪除（停用不等於移除）；更新所有還在用的外掛；檢查 `wp-content/uploads` 等上傳目錄裡有沒有不該出現的 `.php` 檔；有防火牆或主機 WAF 的話，可以查一下記錄裡有沒有這個 IP。

→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/11/45-38-60-254-the-scanner-behind-the-november-2026-rise-in-attacks-on-tw-websites/)

### PHP 與 NGINX 重大修補：更新 WordPress 修不到，要靠主機

**PHP**：11/19 晚間（台北時間）推出 8.2.36、8.3.37、8.4.28、8.5.13，這次共有 7 個重大等級的問題。跟 WordPress 最相關的是 **CVE-2026-129289**（CVSS v4 9.2）：網頁伺服器把請求交給 PHP-FPM 時，如果會重複使用同一條連線（例如 NGINX 開了 `fastcgi_keep_conn on`，或 Apache `mod_proxy_fcgi` 設了 `enablereuse=on`），攻擊者可以讓 PHP 把請求裡沒讀完的資料當成一個新請求來執行，繞過伺服器原本的檢查，最後在沒登入的情況下執行 PHP 程式。Wordfence 已在測試環境用一般的 WordPress REST API 請求重現，不需要任何有漏洞的外掛。

這兩種連線重複使用的設定都不是預設值，但為了效能刻意打開的伺服器並不少，例如 WordOps 的 NGINX 範本預設就是開的。PHP 官方說明，用 LiteSpeed（LSAPI）的環境影響是同一條連線上的後續請求可能被干擾。目前還沒列入 CISA 已遭利用清單，但技術細節已經公開。PHP 官方不再替 8.1 以前的版本出修補（部分主機商會透過 CloudLinux 等套件回補），另外 **PHP 8.2 的安全支援只到 2026/12/31**。PHP 8.6.0 也在同一天正式推出。

**NGINX**：11/18 推出 1.30.7（穩定版）與 1.31.9（主線版），一次修補 10 個問題，其中兩個 HTTP/2 漏洞 CVE-2026-123007、CVE-2026-123633 有研究人員示範遠端執行程式。前提是 NGINX 有開啟 HTTP/2；截至 11/18，歐盟 CERT 表示尚未看到實際利用。

建議動作：用共享或代管主機的，看主機商公告，或直接問「PHP 更新到 8.x 的哪一版了？PHP-FPM 有沒有重啟？NGINX 修好了嗎？」。自己管主機的，安裝上述版本或作業系統廠商的修補套件，重啟 PHP-FPM 和網頁伺服器，容器要用新映像檔重建；要確認的是 PHP-FPM 的版本，不是命令列 `php -v` 的版本。一時無法更新的話，可以先關掉 PHP-FPM 連線重複使用，以及 NGINX 的 HTTP/2。

→ [原文（PHP 官方）](https://www.php.net/)
→ [原文（PHP 安全公告 CVE-2026-129289）](https://github.com/php/php-src/security/advisories/GHSA-qc49-hpvq-g2w6)
→ [原文（NGINX 安全公告）](https://nginx.org/en/security_advisories.html)
→ [原文（Wordfence：PHP 漏洞與 WordPress）](https://www.wordfence.com/blog/2026/11/cve-2026-129289-wordpress-php-vulnerabilities/)
→ [原文（Wordfence：NGINX HTTP/2）](https://www.wordfence.com/blog/2026/11/nginx-http2-vulnerabilities-wordpress/)

### WooCommerce 11.3.1：結帳、優惠券、顧客資料的大範圍安全修補

WooCommerce 11.3.1 於 11/20 推出，屬於安全性更新，包含資料庫更新。這版修了 60 多項問題，店家比較有感的有這些：

- **結帳與訂單**：結帳時會檢查帳單國家是否在可銷售範圍內；同時有多個結帳請求時，不會再把沒付款的商品加進已完成的訂單；「保留庫存」的數量計算更準確。
- **優惠券**：每位顧客的使用次數限制計算更準；訂單取消、失敗或移到垃圾桶時，使用次數會正確扣回；折扣金額不會超過商品本身的價格；「允許的 Email」改成精確比對，只有 `*` 可以當萬用字元。
- **顧客資料**：分享出去的購物車連結只會複製購物車和優惠券，不會連同分享者的結帳資料一起帶過去；在「我的帳號」確認 Email 時要先輸入密碼；訂單追蹤頁的存取檢查更嚴格。

開發者要注意三件事：佈景主題多了 `myaccount/form-confirm-email.php` 範本，有覆寫範本的要檢查；舊版 wc-admin options REST 端點改為需要 `manage_woocommerce` 權限；用不同網域呼叫 Store API、而且會帶登入 Cookie 的無頭（headless）前台，必須用 `allowed_http_origins` 把前台網域加入允許清單，否則更新後會出現 CORS 錯誤。11.3.1 需要 WordPress 7.0 以上。

建議動作：先備份（確認備份裡有當天訂單），更新後完成資料庫更新，再實際下一筆會套用優惠券、修改運送地址的測試訂單。

→ [原文（11.3.1 發布說明）](https://developer.woocommerce.com/2026/11/19/woocommerce-11-3-1/)
→ [原文（完整更新紀錄）](https://wordpress.org/plugins/woocommerce/#developers)

### Woomp 連出四版：PayUni 定期定額換卡、ezPay 與 PayNow 電子發票修正

好用版 Woomp 11/19～11/21 連續推出 3.5.31～3.5.34：

- **3.5.31**（11/19）：從 PayUni V1 搬到 V3 定期定額的商店，顧客終於能在「我的帳號」自行換卡。換卡時只授權 5 元綁定新卡，約 2 分鐘後自動取消；續扣會優先用訂閱綁定的卡。
- **3.5.32**（同日稍晚）：**有更新到 3.5.31 的站台請立即更新。** 3.5.31 在商店設定「自動請款」時取消授權一律失敗，顧客會被實扣 5 元；新版會先查請款狀態，沒請款就取消授權、已請款就退 5 元。另外修正 3D 驗證後回不到商店、3D 失敗仍顯示已更換，以及記錄檔裡留有卡片雜湊與卡號前六碼的問題。3.5.31 期間已經被請款的 5 元不會自動補退，要到 PayUni 後台搜尋金額 5 元、訂單編號格式為「訂閱編號-C序號」的交易手動退款。行為改變：PayUni V3 一般訂單在後台退款失敗時，現在會正確顯示失敗與原因（舊版會顯示成功）。
- **3.5.33**（11/21）：ezPay 電子發票的作廢模式設為「自動」時，自動作廢其實從來沒有觸發過，訂單轉成「已退款」等狀態時不會作廢發票。新版修好了，但**只處理更新之後**才轉狀態的訂單。更新前已退款、發票卻還有效的訂單，要到「WooCommerce → 訂單」篩選出來，發票號碼欄還有號碼的手動按「作廢發票」；另外，手動作廢失敗時現在也會正確顯示錯誤原因。
- **3.5.34**（11/21）：PayNow 電子發票過去每一張都會把買家完整帳單地址傳給 PayNow，新版改成送空值；另外修正地址或姓名含逗號時欄位錯位。

→ [原文（Woomp 3.5.32）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.32)
→ [原文（Woomp 3.5.33）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.33)
→ [原文（Woomp 3.5.34）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.34)
→ [原文（Woomp 3.5.31）](https://github.com/zenbuapps/woomp/releases/tag/v3.5.31)

### WordPress 7.2 RC1、RC2：Secrets API 撤出，開發者手冊出爐

7.2 RC1 於 11/18 推出，RC2 隨 7.1.5 在 11/19 提前推出，正式版仍是 12/8。這兩週的重點：

- **Secrets API 不隨 7.2 推出。** 上期介紹的加密憑證 API 已在 11/17 從 7.2 撤除。官方說明，除了開發者對設計的疑慮（寫入密鑰和讀取主機提供的密鑰該不該放在同一套 API），複查也發現設定密鑰時沒有檢查 `manage_secrets` 權限，與其在發布前夕大改，不如撤回重新設計。上期提到要主機商準備的 `WP_SECRETS_KEY` 等設定，7.2 暫時用不到。
- **7.2 開發者手冊（Field Guide）**：這版處理了超過 440 張核心工單，並合併 Gutenberg 23.7～24.3 的成果。和外掛、佈景主題比較相關的有：媒體庫、新增媒體畫面與媒體視窗上傳的圖片，也改成在瀏覽器端處理（Chromium 系列 137 版以上、裝置記憶體大於 2GB；Firefox、Safari 自動改回伺服器處理）；動態 GIF、WebP 也會產生各尺寸縮圖；後台選單與工具列圖示從 Dashicons 字型改成 SVG，有自訂後台配色或用 CSS 改圖示的要檢查；資料庫連線會自動關掉 `NO_BACKSLASH_ESCAPES` 模式；新的 `/random/` 隨機文章網址可以用 `wp_enable_random_content_redirect` 篩選器關閉。
- **為 jQuery 4 做準備**：7.2 先把 jQuery Migrate 升到 3.6.0，用來提早顯示即將失效的寫法；jQuery 4 本身暫定 7.3 才換，預設停用 jQuery Migrate 則要等到 7.5 或 7.6 左右。有自己寫 jQuery 的外掛與佈景主題，現在就可以開著除錯模式看看有沒有棄用警告。
- 其他小改動：新增應用程式密碼時會寄信通知使用者；`WP_ENVIRONMENT_TYPE` 設成 `local` 而沒定義 `WP_DEBUG` 時，會預設開啟除錯；工具列的「Howdy」移進使用者選單。

正體中文翻譯方面，7.2 已在 11/22 開放翻譯。截至本期出刊，正體中文（zh_TW）的 7.2 前台字串完成約 93%，後台約 98%，還有 500 多條待翻，有興趣的人可以到翻譯平台幫忙。

→ [原文（RC1）](https://wordpress.org/news/2026/11/wordpress-7-2-release-candidate-1/)
→ [原文（Secrets API 撤除）](https://github.com/WordPress/wordpress-develop/commit/53ea8006d1)
→ [原文（7.2 Field Guide）](https://make.wordpress.org/core/2026/11/20/wordpress-7-2-field-guide/)
→ [原文（jQuery 4 準備）](https://make.wordpress.org/core/2026/11/17/preparing-for-jquery-4-with-wordpress-7-2/)
→ [原文（7.2 開放翻譯）](https://make.wordpress.org/polyglots/2026/11/22/wordpress-7-2-ready-to-be-translated/)
→ [正體中文翻譯進度](https://translate.wordpress.org/locale/zh-tw/default/wp/dev/)

---

## WooCommerce／電商

### RY Tools 2026.11.20：金流通知驗證失敗太多次會自動封鎖

RY Tools for WooCommerce 11/20 推出 2026.11.20，新增「第三方主動通知的異常偵測封鎖機制」，涵蓋綠界、藍新、PAYUNi、速買配的付款通知，以及綠界、速買配的物流通知。從程式碼看，同一家金流的通知在一段時間內驗證失敗達 10 次，就會在預設的 1 小時內擋下這家的**所有**通知，並寄信通知管理員（開發者可以用 `ry_callback_site_limit_seconds` 調整時間）。

建議動作：收到這封通知信時，代表有人在亂打你的金流通知網址，封鎖期間正常付款的通知也會被擋下。請到金流後台核對這段時間的交易，把已經付款、卻還停在「待付款」的訂單手動改成「處理中」。

→ [原文（RY Tools 外掛頁）](https://wordpress.org/plugins/ry-woocommerce-tools/#developers)

### 電商相關外掛漏洞

- **Simple Shopping Cart**（約 1 萬個安裝）5.3.3 以前（CVE-2026-131782，CVSS 9.8）：反序列化物件注入，請更新到 5.3.4。
- **Unzer Payments** 2.2.1 以前（CVE-2026-110009，CVSS 9.8）：Google Pay 快速結帳端點只要送出某個使用者的 Email，就會直接登入成那個人，包括管理員；請更新到 2.2.2。

→ [原文（Simple Shopping Cart）](https://www.cve.org/CVERecord?id=CVE-2026-131782)
→ [原文（Unzer Payments）](https://www.cve.org/CVERecord?id=CVE-2026-110009)

### WooCommerce 線上座談：用 AI 對抗詐欺與資安（12/11 台北時間凌晨）

WooCommerce 開發者部落格將在 12/10（美西時間）早上 9 點，也就是台北時間 12/11 凌晨 1 點，舉辦線上座談，由 Automattic 的金流風控、WooCommerce 工程主管與代理商代表，談目前打 WooCommerce 商店的盜刷、測卡、帳號接管手法，AI 在偵測詐欺上哪裡比規則好用、哪裡還需要人工，以及 AI 代理幫消費者下單之後怎麼分辨正常代理與機器人。報名後就算沒看直播也會收到錄影連結。

→ [原文](https://developer.woocommerce.com/2026/11/19/fraud-security-ai-live-event/)

---

## 資安

### 其他高風險漏洞

- **WPvivid 備份與搬家**（約 90 萬個安裝）0.9.134 以前（CVE-2026-84693，CVSS 8.1）：在管理員產生了還沒過期的搬家接收金鑰時，未登入者可以讓之後的排程清理刪掉網站上的任意檔案；請更新到最新版（目前 0.9.140）。
- **Otter Blocks**（約 30 萬個安裝）3.2.10 以前（CVE-2026-123132，CVSS 8.8）：未登入者可以在留言裡放入連結，管理員點了就會執行惡意腳本；請更新到 3.2.10 以上。
- **GiveWP**（約 10 萬個安裝）4.17.0 以前（CVE-2026-82675，CVSS 8.1）：未登入者可以重設任何綁定捐款人紀錄的 WordPress 帳號密碼，如果綁的是管理員就能接管網站；請更新到最新版。
- **WP STAGING**（約 10 萬個安裝）4.17.0 以前、Pro 6.17.0 以前（CVE-2026-109555，CVSS 8.0）：訂閱者可以在自己的個人資料裡埋入惡意資料，等管理員之後執行含資料庫的還原或重設測試站時，就會在伺服器上執行程式；請更新到最新版（目前 4.22.0）。
- **Soledad 佈景主題** 8.7.7 以前（CVE-2026-121684，CVSS 8.1）：未登入者可以透過公開的 AJAX 動作引入伺服器上的檔案並執行程式；這是熱門的付費佈景主題，請到購買平台下載新版更新。
- **WP Import Export Lite**（約 4 萬個安裝）3.9.32 以前（CVE-2026-19793，CVSS 8.8）：未登入者只要能送出一則待審留言，等管理員之後做一次「匯出設定再匯入」的搬移流程時，就能注入程式；請更新到最新版（目前 3.9.35）。
- **Everest Forms**（約 9 萬個安裝）3.6.4 以前（CVE-2026-129220，CVSS 8.8）：如果管理員曾把「建立表單」權限下放給其他角色，該角色可以啟用網站上任何已安裝但停用的外掛，進而提權；請更新到 3.6.5。上期刊過的是另一個 CVE。

→ [原文（WPvivid）](https://www.cve.org/CVERecord?id=CVE-2026-84693)
→ [原文（Otter Blocks）](https://www.cve.org/CVERecord?id=CVE-2026-123132)
→ [原文（GiveWP）](https://www.cve.org/CVERecord?id=CVE-2026-82675)
→ [原文（WP STAGING）](https://www.cve.org/CVERecord?id=CVE-2026-109555)
→ [原文（Soledad）](https://www.cve.org/CVERecord?id=CVE-2026-121684)
→ [原文（WP Import Export Lite）](https://www.cve.org/CVERecord?id=CVE-2026-19793)
→ [原文（Everest Forms）](https://www.cve.org/CVERecord?id=CVE-2026-129220)

### UpdraftPlus 1.26.16：再修一批還原相關的問題

UpdraftPlus 1.26.16 補上與 1.26.9 同類型的問題：低權限帳號在資料庫裡塞入特製的長字串，就可能在之後還原時動手腳；同時修正還原時對 JSON 內容做搜尋取代可能造成非預期修改。這版也開始支援 `mariadb-dump`。用 UpdraftPlus 備份的網站請更新。

→ [原文（UpdraftPlus 更新紀錄）](https://wordpress.org/plugins/updraftplus/#developers)

---

## 外掛動態

### 外掛後台廣告：WordPress 提案要求外掛標明每則通知的「用途」

Make Core 11/16 發表提案，起因是 Matt Mullenweg 認為眼前最急的問題是目錄裡的外掛把後台塞滿促銷橫幅。做法是：外掛發送的每一類通知都要先宣告用途，從五種裡選一種：營運狀態、待完成設定、他人動態、操作回饋、促銷。其中「促銷」包括升級付費、折扣、使用小技巧、新功能介紹、請你評分、問卷、要求開啟使用量追蹤；這類通知只能是一般資訊等級、一定可以關閉，而且不會出現在 WordPress 核心的頁面上。提案特別不設「資訊」這一類，以免變成漏洞；之後外掛審查工具會依宣告的用途檢查有沒有違規。WordPress 核心自己的通知不受限制。目前是提案階段，正在收集意見。

→ [原文](https://make.wordpress.org/core/2026/11/16/wordpress-notifications-and-declared-intent-proposal-for-the-path-forward/)

### 官方社群版 SMTP 外掛上架

WordPress 外掛團隊 11/17 推出由社群維護的「SMTP」外掛。很多主機會關閉 PHP 的 `mail()` 或封鎖 25 埠，這時網站寄不出密碼重設信、表單通知和訂單信，而且不會有任何錯誤提示。這個外掛只做一件事：讓 WordPress 改走你指定的 SMTP 伺服器寄信。正式站建議把主機、帳號、密碼寫成 `wp-config.php` 常數（例如 `SMTP_HOST`、`SMTP_PASSWORD`），寫了就會鎖住後台欄位。另外會保留 30 天的寄送紀錄（只記收件人、主旨、狀態，不存內文），寄信失敗可以通知 Slack，也支援 WP-CLI。外掛原本規劃在 7.2 改用 Secrets API 加密保存密碼，但該 API 已撤出 7.2。需要 WordPress 6.3、PHP 7.4 以上。

→ [原文（外掛團隊）](https://make.wordpress.org/plugins/2026/11/16/introducing-smtp-a-community-maintained-way-to-send-wordpress-email-reliably/)
→ [外掛頁](https://wordpress.org/plugins/smtp/)

### Yoast SEO 28.9：圖片替代文字檢查變細，順手修一個資料外洩

Yoast SEO 28.9 於 11/18 推出：原本「替代文字是否含焦點關鍵字」的檢查，換成五項新檢查（長度、重複、贅字、沒描述內容、關鍵字堆砌），大量編輯器也能依替代文字篩選。這版另外修正一個安全問題：`head` REST 端點會把私密文章、頁面的內容露給沒有權限的訪客。XML 網站地圖也拿掉了樣式表，因為瀏覽器正在停止支援 XSLT。

→ [原文（Yoast 更新紀錄）](https://wordpress.org/plugins/wordpress-seo/#developers)

### Elementor 4.4.1：安全性修正與 7.2 相容

Elementor 4.4.1 於 11/16 推出，標示已相容 WordPress 7.2，並加強屬性、權限與資料處理的安全檢查；也修正了手機版頁面剛載入時錨點連結捲到錯誤段落，以及 MCP 手動設定說明在某些環境失效的問題。

→ [原文（Elementor 更新紀錄）](https://wordpress.org/plugins/elementor/#developers)

### Gutenberg 24.5 RC1

Gutenberg 24.5 RC1 於 11/19 推出：格線（Grid）區塊可以把區塊放到指定欄列、從角落拖曳調整大小；樣式修訂版可以並排比較或看程式差異；可以一次替多個選取的區塊加同一則 Notes 註記；文字格式的「醒目提示」拆成「文字顏色」與「醒目提示顏色」兩個控制項。

→ [原文（Gutenberg 24.5.0-rc.1）](https://github.com/WordPress/gutenberg/releases/tag/v24.5.0-rc.1)

---

## 台灣站務與活動

### 台北彩虹小聚：用 AI 做小遊戲，讓人人都能參與社會議題（11/24 週二）

11/24（二）18:30–21:30，台灣性別平等教育協會（台北市中正區羅斯福路三段 218-2 號 3 樓，公館），免費入場，現場提供點心。Saori 與 Qmoa 分享用 AI 做出《大樹神獸》小遊戲的完整過程，之後有 6 位、每人 5 分鐘的閃電講。歡迎所有人參加。

→ [活動頁](https://www.meetup.com/taipei-wordpress/events/317270590/)

### 台南小聚 #11：我用 AI 讓 LINE 變成自動化助理（11/24 週二）

11/24（二）19:00–21:00，三皇三家歸仁店（臺南市歸仁區中山路三段 28 號），18:00 起可以先到現場用餐，低消一份點心或飲料。講者是余啟彰 Fish。外縣市朋友可以搭高鐵到台南站，報名時告知主辦，18:30 可以一起接駁到會場。

→ [活動頁](https://www.meetup.com/kaohsiung-wordpress-meetup/events/317339884/)

### 彰化小聚親子場 #48：組盆多肉好過年（11/29 週日）

11/29（日）14:00–16:00，田尾「多肉好好買」（彰化縣田尾鄉柳鳳村民族路二段 143 巷 12 號），每組 350 元，以組為單位報名，親子一起做直徑約 20 公分的多肉組盆。

→ [活動頁](https://www.meetup.com/changhua-wordpress-meetup-group/events/316974028/)

### 台北小聚：你的瀏覽器就是 AI，Chrome 內建 AI 實戰（11/30 週一）

11/30（一）19:00 起，可能存在的遊樂園 CollaPlay（台北市萬華區武昌街二段 83 之 6 號）。講者李冠緯（李小胖），介紹 Chrome 內建 AI 的 Prompt、Translator、Summarizer 等 API，現場示範用 JavaScript 直接呼叫瀏覽器裡的 AI 做翻譯、內容生成與結構化資料處理，也會談硬體限制、繁體中文支援，以及怎麼搭配雲端 AI 控制成本。之後有閃電講與交流。

→ [活動頁](https://www.meetup.com/taipei-wordpress/events/317535370/)

### 新竹小聚：一人公司如何建立失聯判斷與交接機制（12/19 週六）

12/19（六）14:00–17:00，FOXLOVE COFFEE（新竹市林森路 281 號，近新竹火車站），免費參加，低消一杯飲料。講者大邵分享一人公司、一人工作室萬一突然失聯，客戶的網站、帳號和流程要怎麼交接，接著有自我介紹與 5 分鐘閃電講。

→ [活動頁](https://www.meetup.com/hsinchu-wordpress-meetup/events/317495214/)

### 官方開始把聚會從 Meetup.com 搬到 WordPress.org

WordPress 社群團隊 11/20 宣布，第一批試辦的聚會群組開始從 Meetup.com 搬到 events.wordpress.org，使用開源的 GatherPress。搬過去之後，成員用 WordPress.org 帳號登入，活動會顯示在 events.wordpress.org，也會出現在所有 WordPress 網站的控制台裡。只有 Email 對得上既有 WordPress.org 帳號的成員會被匯入，而且會提前兩週通知、可以選擇退出；舊的 Meetup.com 頁面會等群組在新家穩定後再討論何時關閉。台灣各地小聚目前仍在 Meetup.com 報名；有固定參加的人，可以先申請一個 WordPress.org 帳號，之後搬家時才能被帶過去。

→ [原文](https://make.wordpress.org/community/2026/11/19/the-move-is-on-gatherpress-migration-faqs/)

### WordCamp Asia 2027 招募貢獻者日桌長

WordCamp Asia 2027 正在招募 4/9 貢獻者日（馬來西亞檳城）各 Make 團隊的桌長。今年要求桌長事前準備：替自己的團隊寫一篇貢獻者日說明、做一份新手第一次貢獻的步驟指南，並準備新手、中階、進階三種難度的任務。不在檳城也可以申請遠端桌長，透過 Slack 線上協助。申請者需要目前在該團隊有實際貢獻紀錄。

→ [原文](https://asia.wordcamp.org/2027/call-for-table-leads/)

---

## 在地工作室與教學部落格

### 鵠崙設計：接手別人做的 WordPress 網站，先確認這幾件事

針對「原廠商找不到、版本太舊不敢更新」的網站，先確認網域、主機、管理員帳號、一份能還原的完整備份這四樣在自己手上，也要問清楚付費外掛授權掛在誰名下、公司信箱是否跟網站在同一台主機。拿到權限後先別動，檢查核心與 PHP 版本、外掛是否還有人維護、客製程式改在哪裡、有沒有來路不明的管理員帳號；升級一律先在測試環境做，先升核心與外掛、再升 PHP，逐頁實際操作表單、會員、結帳。文末附一份向前廠商要資料的清單，金流、物流、電子發票帳號的歸屬也在裡面。

→ [原文](https://www.design-hu.com/web-news/wordpress-website-takeover-maintenance.html)

### 傑哥架站教室：Bricks 的 Single 與 Archive 模板，交給 AI 透過 MCP 建立

接續上期的 Bricks 原生 MCP 示範站，這篇請 AI 建立兩個模板：Single 管單篇文章內頁（分類、標題、日期、特色圖、內文、上下篇導覽，全部用動態資料標籤），Archive 管分類、標籤、作者等列表頁。容易出錯的地方是查詢迴圈要設在「卡片」上，不是外層的格線容器；卡片格線用一個全域類別處理電腦三欄、平板兩欄、手機一欄。作者提醒，細節可以交給 AI 執行，但自己要有能力檢查，例如手機、平板有沒有跑版或橫向溢出。

→ [原文](https://jclassroom.net/bricks-single-archive-template/)

### 犬哥網站：網站改版會不會掉排名、影響 AI 引用？

重點是：會掉排名的不是改版本身，而是網址、內容、標題、內部連結和結構化資料的變動沒有銜接好。文章依改版前盤點、新站測試、上線當天、上線後追蹤整理檢查項目：能不換網址就不換，非換不可的話做好新舊網址對照表，用 301 轉到內容對應的新頁（Google 表示 301 不會造成 PageRank 流失）；服務說明、案例、FAQ 要完整搬過去，不要改版後只剩圖片和標語；換模板時要確認結構化資料有帶過去；換網域、換 CMS、改版最好分開做。對 AI 引用（GEO）來說，最大的風險是曾被引用的網址變成 404。

→ [原文](https://frankknow.com/how-redesign-affect-seo-geo/)

---

## 主機商觀點

### Pressable：11 月 PHP 與 NGINX 修補已完成（廠商立場）

Automattic 旗下的 Pressable 表示，NGINX 修補在 11/18 一推出就套用完成，11/19 的 PHP 安全更新也已套用到所有環境，客戶不必做任何事，需要給資安稽核或保險問卷的，可以請客服書面確認。文中也提醒非 Pressable 用戶要確認的版本：PHP 8.2.36、8.3.37、8.4.28、8.5.13，NGINX 1.30.7／1.31.9 以上，並提醒 PHP 8.2 在 2026 年底停止安全修補。

→ [原文](https://pressable.com/blog/your-pressable-sites-are-protected-from-the-november-php-and-nginx-security-issues/)

### Hostinger：PHP 修補要等 CloudLinux 套件，先公布時程（廠商立場）

Hostinger 在 PHP 修補發布當天先公布預估時程：它的 PHP 套件來自 CloudLinux，最常用的版本約在 PHP 官方發布後 1.5～2 小時取得，8.1 以前需要回補的舊版本約要 6 小時；文中也強調這是 PHP 本身的問題，更新 CMS、佈景主題或外掛都無法修補。11/20 更新說代管方案的所有伺服器（含舊版 PHP）都已裝好修補；自己管 PHP 的 VPS 則要自行更新。這也說明了一般主機商的修補流程：多數要等上游套件，不一定在 PHP 官方發布當下就修好。

→ [原文](https://www.hostinger.com/blog/php-fixes-november-2026/)

---

## 影音

### 犬哥網站：20 分鐘學會 WordPress 架站（Astra＋Elementor）

約 21 分鐘的新手影片（11/19 上架），從主機與網域、WordPress 基本設定開始，示範安裝 Astra 主題、匯入版型、統一全站配色與字體，再用 Elementor 拖曳修改首頁、調整選單與頁首，最後用 WPForms 做聯絡表單。影片說明欄有主機推薦連結。

→ [影片](https://www.youtube.com/watch?v=dIUy07XSveI)

---

## 社群熱議

### r/WordPress：一個月有 7 萬個網站離開 WordPress，只有 13% 換到別的 CMS

一個追蹤網站技術的資料服務分享：同一批網域逐月比對，上個月有 70,603 個網站不再使用 WordPress，但只有 9,481 個（13%）換到其他 CMS。其餘的去向：1.3 萬個轉址到別處、8,500 多個變成伺服器預設頁、3,600 多個停放，還有約 3.4 萬個無法辨識；抽樣這一批，扣掉主機佔位頁、隱藏特徵的 WordPress 和少數新平台，其餘看起來多是用 AI 重做的網站，有些甚至還留著 WordPress 的 `uploads/年/月` 圖片路徑。作者也提醒，市占率要看怎麼算，同一個月的數字可以說成上升也可以說成下降，追蹤同一批網域才看得出真正的流向。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1y07t01/70603_sites_left_wordpressorg_a_month_only_9481/)

### r/WordPress：WordPress 不會倒，那頁面編輯器和外掛呢？

一位 2013 年開始做代理商、手上有幾百個 WordPress 客戶網站的發文者說，他現在改用 Claude 做網站：客戶只需改文字圖片的，用 PagesCMS；要完整控制權的才給 WordPress。他已經不用頁面編輯器和外掛，聯絡表單、預約表單都請 AI 搭配 Supabase 寫。他擔心的是，以前用頁面編輯器（WPBakery）做的幾百個網站，等熱門外掛因為需求變少而沒人維護時，會不會全部變成資安隱患。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xzehba/wordpress_will_be_fine_what_happens_to_builders/)

### r/WordPress：提醒大家 PHP 緊急安全更新

本週 PHP 一發布安全更新，就有用戶在 r/WordPress 發文提醒，列出要升到的 8.2.36、8.3.37、8.4.28、8.5.13，並建議當作緊急維護，所有環境都要排進去；文中也提到重大等級的問題需要非預設設定或較少見的架構才會受影響。完整說明見本期「本週熱點」的 PHP 與 NGINX 一則。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1y0c8i8/critical_php_security_release_19112026/)

---

## 職缺（近 7 日）

本期職缺來自 104 與 1111，分布在雙北、新竹與台中，有 WordPress 開發與網頁設計職，也有不少把 WordPress 官網維運併入行銷工作的職缺。

### 1111｜PHP 程式開發工程師（獨家報導）
台北大安；月薪 29,500 元起。以 PHP 與 JavaScript 開發客製 WordPress 佈景主題與外掛。（更新日期 11/19）

→ [職缺](https://www.1111.com.tw/job/80221905)

### 1111｜WordPress 網頁設計師（陽信開發）
新竹東區；月薪 35,000 元以上。負責官網 WordPress 架站、版型與外掛設定、除錯與 SEO 優化；不考慮外包或接案。（更新日期 11/21）

→ [職缺](https://www.1111.com.tw/job/132835238)

### 1111｜台中班－網頁設計師（伊美教育機構）
台中中區；月薪 32,000 元起。負責公司 WordPress 網站的設計與維護，並兼顧 UI/UX。（更新日期 11/20）

→ [職缺](https://www.1111.com.tw/job/130449558)

### 104｜行銷企劃（健立生醫）
台北松山；月薪 45,000～65,000 元。負責品牌官網營運維護與 SEO／GEO，需熟悉 WordPress 後台與 Elementor，另兼行銷企劃與廣告。（更新日期 11/23）

→ [職缺](https://www.104.com.tw/job/8x74p)

### 104｜網站、行銷、FB 文字編輯（台灣愛露營科技）
台中北屯；月薪 29,500 元以上。露營主題的文字編輯與網站、社群經營，需熟悉 WordPress 網站後台操作。（更新日期 11/23）

→ [職缺](https://www.104.com.tw/job/7t6wy)

### 104｜網站與社群經營專員（寢之堡）
台北；經營品牌網站與社群，需熟悉電商後台（Shopline、91APP、Cyberbiz 或 WordPress 擇一）。（更新日期 11/22）

→ [職缺](https://www.104.com.tw/job/8pqav)

### 1111｜數位行銷專員（奇異鳥集團 KIWI Group）
新北林口；月薪 40,000 元起。留遊學產業的數位行銷，需熟悉 WordPress 平台架構與管理。（更新日期 11/16）

→ [職缺](https://www.1111.com.tw/job/130353088)

### 1111｜行銷企劃（噶薩爾生物科技）
台北內湖；月薪 32,000～45,000 元。經營社群與撰寫文案，需能操作 WordPress 等網站後台。（更新日期 11/23）

→ [職缺](https://www.1111.com.tw/job/132017905)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
