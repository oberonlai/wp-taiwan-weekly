# WP 台灣週報 · 第 8 期

**出刊日：** 2026-10-19

本週重點：**Rank Math SEO PRO 3.0.123 遭供應鏈攻擊**，10/16 白天更新過的站請視同已被入侵、立即處理。台灣金物流方面，**綠界官方 Woo 外掛**與 **Woomp** 都推出資安與結帳修正。WordPress 7.2 **Beta 1 時程不變，就在 10/20 台北時間 23:00**；Gutenberg 24.2 預設開啟 React 19。今晚台北彩虹小聚、明晚高雄小聚，10/30 台北再加開一場。

---

## 本週熱點

### WordPress 7.2 Beta 1 維持 10/20 推出
10/14 的核心開發會議議程確認，7.2 Beta 1 照原訂 **10/20 15:00 UTC（台北 10/20 23:00）** 釋出，後續 RC1 11/17、正式版 12/8 也沒有變動。WordPress 現行版本仍是 7.1.3。外掛與佈景主題開發者可以趁 Beta 期間在測試站跑一輪相容性測試。

→ [原文](https://make.wordpress.org/core/2026/10/14/dev-chat-agenda-oct-14-2026/)

### Gutenberg 24.2：註解移到邊欄、React 19 預設開啟
10/14 釋出。編輯器註解改放在內容旁的邊欄，可以和設定側欄同時開，刪除可復原、沒送出的內容不會遺失。導覽選單的連結搜尋一次涵蓋頁面、文章、分類與標籤；圖庫區塊手動挑選的圖片可以排序，也支援 duotone。開發者要注意：**React 19 改為預設開啟**，偵測到已知不相容的佈景或外掛時會自動留在 React 18，也可以在 Gutenberg > Experiments 關閉。Interactivity API 另新增 `data-wp-html`。

→ [原文](https://make.wordpress.org/core/2026/10/14/whats-new-in-gutenberg-24-2-14-october/)

### 工具列的「W」與網站名稱，核心團隊徵求意見
提案讓左上角的 W 圖示一律連到控制台，螢幕寬度小於 600px 時也會顯示；網站名稱則一律連到前台。官方附上 Playground 可以直接試用，對後台操作習慣有意見的人可以去留言。

→ [原文](https://make.wordpress.org/core/2026/10/12/call-for-feedback-the-w-and-the-site-name-in-the-toolbar/)

### 即時協作：三種同步引擎徵求測試
實驗外掛 gutenberg-sync-engines 內含三種同步引擎（yjs-server、DE-RTC、intent-log），官方請大家幫忙測試，選出伺服器端協作的基礎。協作編輯不會進 7.2，但方向正在定案。

→ [原文](https://make.wordpress.org/core/2026/10/14/real-time-collaboration-a-guide-to-sync-engines/)

---

## WooCommerce／電商

### 綠界官方 ECPay Ecommerce for WooCommerce：三項安全性修正
10/16 釋出 1.1.2610150，強化後台訂單 AJAX 驗證、為定期定額付款選項加上後端驗證，並限制信用卡分期的期數與金額，避免被竄改。隨後的 1.1.2610151 只調整版號，測試相容到 WordPress 7.1.1。使用綠界官方外掛收款的店家建議盡快更新。

→ [GitHub 釋出](https://github.com/ECPay/Woocommerce_ECPAY/releases/tag/1.1.2610150) · [外掛頁](https://wordpress.org/plugins/ecpay-ecommerce-for-woocommerce/)

### Woomp 3.5.23～3.5.25：超商取貨付款少了代收貨款，已修正
10/12 至 10/14 連出三版。**3.5.23** 修正開了 Woo 稅金又使用折價券時，綠界電子發票開立失敗（錯誤碼 1200031）。**3.5.24** 修正使用 Woomp 貨到付款搭配綠界超商物流時，物流單變成純配送、**沒有代收貨款**的問題，也修好 PHP 8.2 警告塞爆標頭導致後台 503，以及 PAYUNi 信用卡 V3 交易被拒卻顯示付款成功。**3.5.25** 讓 PAYUNi 信用卡 V2 除錯日誌遮罩卡號、CVC 與效期（舊日誌要手動刪除），付款回傳缺少 HashInfo 簽章時一律拒絕。有用超商取貨付款的店家，建議回頭核對近期訂單的代收金額。

→ [v3.5.25](https://github.com/zenbuapps/woomp/releases/tag/v3.5.25) · [v3.5.24](https://github.com/zenbuapps/woomp/releases/tag/v3.5.24) · [v3.5.23](https://github.com/zenbuapps/woomp/releases/tag/v3.5.23)

---

## 資安

### Rank Math SEO PRO 3.0.123 遭植入惡意程式，請立即處理
在**台北時間 10/16 04:02～15:12** 之間下載或更新的部分 3.0.123 檔案含有惡意程式（Wordfence 評 CVSS 9.8）。乾淨版本是 **3.0.124**，免費版不受影響。這段時間更新過 PRO 版的站要視同已被入侵：用更新前的備份還原，或依官方步驟手動清除；更換所有管理員密碼、資料庫帳密、salts 與 API key；再從 rankmath.com 帳號下載 3.0.124 重新安裝。入侵跡象包括 `wpsupport_` 開頭的管理員帳號、偽造的核心檔 `wp-includes/class-wp-cache-util.php`、對 rank1cdn.com 的連線，以及可疑的 wp_options 設定。

→ [Rank Math 公告](https://rankmath.com/kb/pro-3-0-123-security-incident/) · [Wordfence 分析](https://www.wordfence.com/blog/2026/10/rank-math-seo-pro-3-0-123-malware/)

### Backuply ≤1.5.8、UpdraftPlus：還原備份時可能執行惡意 SQL
Backuply（約 60 萬站）的 CVE-2026-93953（CVSS 8.0）讓訂閱者能把 SQL 藏在個人簡介裡，等管理員按下「還原備份」時執行，進而升級成管理員；請更新到 **1.5.9**。UpdraftPlus（約 400 萬站）在 1.26.9～1.26.11 也修了還原時可執行任意 SQL 的問題，現行版為 **1.26.11**。開放會員註冊的站尤其要留意。

→ [Wordfence：Backuply](https://www.wordfence.com/blog/2026/10/600000-wordpress-sites-affected-by-sql-injection-vulnerability-in-backuply-wordpress-plugin/) · [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-93953) · [UpdraftPlus 更新紀錄](https://wordpress.org/plugins/updraftplus/#developers)

### Ghostscript CVE-2026-39919：惡意 PDF 縮圖可攻擊主機
WordPress 會呼叫伺服器上的 Ghostscript 產生 PDF 縮圖，惡意 PDF 可造成記憶體溢位（CVSS 9.8），官方 WordPress Docker 映像也受影響。修補版為 Ghostscript **10.08.0**。這不是外掛更新能解決的問題，自架 VPS 或容器的站要更新系統套件或重建映像，使用代管主機的可以詢問主機商是否已修補。

→ [Wordfence 分析](https://www.wordfence.com/blog/2026/10/wordfence-argus-discovers-critical-vulnerability-in-ghostscript-the-program-that-turns-uploaded-pdfs-into-thumbnails-on-your-server/) · [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-39919)

### 本週其他高風險外掛漏洞
以下外掛都已有修補版，有安裝的站請更新到現行版本：

- **Click to Chat ≤4.44**（約 60 萬站）：未登入者可執行任意 shortcode，CVSS 9.1，現行版 4.45。→ [CVE-2026-92097](https://www.cve.org/CVERecord?id=CVE-2026-92097)
- **10Web Booster ≤2.34.10**：未登入者可上傳檔案，CVSS 9.8，現行版 2.34.11。→ [CVE-2026-108587](https://www.cve.org/CVERecord?id=CVE-2026-108587)
- **LearnPress ≤4.4.10**：未登入 PHP 物件注入，CVSS 9.8，現行版 4.4.11。→ [CVE-2026-104736](https://www.cve.org/CVERecord?id=CVE-2026-104736)
- **LatePoint ≤5.7.4**（約 10 萬站）：利用併發請求繞過 OTP 驗證，CVSS 9.1，現行版 5.7.5。→ [CVE-2026-110653](https://www.cve.org/CVERecord?id=CVE-2026-110653)
- **Theme My Login ≤7.2.1**：多站網路特定設定下可改他人密碼，CVSS 9.8，現行版 7.3.1。→ [CVE-2026-89079](https://www.cve.org/CVERecord?id=CVE-2026-89079)

---

## 外掛

### Elementor 4.3.5：修編輯失敗並強化安全檢查
10/13 釋出，修正開啟 Internal CSS Embedding 時無法編輯的問題，並加強 CSS、SVG 與元素 ID 的安全檢查。前兩版 4.3.3、4.3.4 則修了多項 MCP 相關問題。

→ [更新紀錄](https://wordpress.org/plugins/elementor/#developers)

### Yoast SEO 28.7：AI agent 可直接改 SEO 標題與描述
10/13 釋出，透過 Yoast Ability，AI agent 可以更新文章的 SEO 標題與描述。用 AI 助理管理內容的站，記得控管 agent 的權限範圍。

→ [更新紀錄](https://wordpress.org/plugins/wordpress-seo/#developers)

---

## 台灣站務與活動

### 【今晚 10/19】WordPress 彩虹小聚：當老闆本人就是 ERP
林馬具負責人林昀與工程師 Moksaweb 分享 WP 電商長大後如何整理營運流程、把 LINE 預約串進系統，另有閃電講。**2026-10-19（一）18:30–21:30**，台北言文字（重慶南路一段 11 號），入場低消一杯飲料。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316808850/)

### 【10/20】高雄 WordPress 小聚
講題「Google 商家經營：把陌生的客人變成熟客」。**2026-10-20（二）19:00–21:00**，高雄 Second Space RED（七賢一路 294 號），場地費 100 元，需先在 Miso 完成報名。

→ [Meetup](https://www.meetup.com/kaohsiung-wordpress-meetup/events/316748093/) · [報名](https://miso.tw/e/wordpress-202610)

### 【10/30】台北 WordPress 小聚：Vue SPA 官網為什麼搬回 WordPress
火曜領航 CTO 林詩凱分享「當 AI 爬蟲只讀到 77 個字」：單頁式 Vue 官網在 AI 爬蟲眼中幾乎沒有內容，最後決定搬回 WordPress。**2026-10-30（五）19:00–21:30**，可能存在的遊樂園 CollaPlay（萬華區武昌街二段 83 之 6 號，樓層請以活動頁說明為準）。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316945507/)

---

## 在地工作室部落格

### 網站搜尋小幫手：讓搜尋框主動出現
金城事務所的自製外掛，把藏在角落的搜尋框改成進首頁或讀到文末時跳出的搜尋卡片，附熱門關鍵字與最常被看的 5 篇文章；讀到文末才載入，不拖慢文章頁。

→ [原文](https://iseeu.tw/mr-search/)

### 影片輪播小幫手：首頁放 Shorts／Reels 疊卡輪播
同站另一款外掛，貼上 YouTube Shorts 或 IG Reels 網址就能做成疊卡輪播並在站內播放；使用 IG 音樂庫配樂的影片則只能跳轉到 IG 觀看。

→ [原文](https://iseeu.tw/mr-carousel/)

### 自動內部連結外掛該不該用？
作者認為把關鍵字自動連到標籤頁不算作弊，但幾乎沒有 SEO 效果，還會讓真正重要的文章分不到連結，建議把內部連結留給人工挑選。

→ [原文](https://iseeu.tw/auto-internal-links/)

---

## 主機商觀點

### Cloudways MCP 2.0：300 多個工具，舊 API key 已停用
新版 MCP 伺服器提供超過 300 個工具，新增讀取日誌與 WordPress 多站批次管理（一次最多 300 個應用）。要注意的是，舊 API key 與 API v1 網址**已在 10/15 停用**，要改用可分權限的 access token，官方建議先從唯讀權限開始。廠商立場：Cloudways（批次管理屬付費加購功能）。

→ [原文](https://www.cloudways.com/blog/cloudways-mcp-2-0_logs-wordpress-management/)

### Breeze 新功能：整頁快取下只重抓指定區塊
Breeze 快取外掛推出 beta 功能「Refresh Cached Page Parts」：頁面照常由快取送出，載入後只重新抓取你指定的區塊（例如即時庫存）。官方提醒登入、結帳與加入購物車的表單不要放進去。廠商立場：Cloudways。

→ [原文](https://www.cloudways.com/blog/breeze-refresh-cached-page-parts/)

### 每個網站都是 API 的使用者（Kinsta）
一次結帳背後可能要呼叫金流、庫存、運費、CRM 等多個外部 API，任何一個變慢都可能拖垮整站。文章說明怎麼設定逾時與監控，降低外部服務出問題時的衝擊。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/every-website-is-an-api-consumer/)

---

## 影音

### 犬哥網站：Elementor MCP 來了
介紹 Elementor 新加入的 MCP 功能，讓 AI 助理能操作 Elementor 頁面。實際版本與修正內容可對照 Elementor 4.3.3～4.3.5 的更新紀錄。

→ [YouTube](https://www.youtube.com/watch?v=1FrKZfOc97o) · [Elementor 更新紀錄](https://wordpress.org/plugins/elementor/#developers)

---

## 社群熱議

### Reddit｜保險公司要大家「立刻離開 WordPress」？
有人轉述澳洲 Coalition Insurance 講者在醫療業資安研討會上這樣建議。高讚留言反駁：近年哪個 CMS 沒出過 9 分以上的 CVE，問題多半出在老舊外掛與沒更新；也有人提醒可能是客戶聽錯重點。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1x3pqdb/coalition_insurance_advised_that_anyone_using/)

### Reddit｜被駭網站清完又一直復發
原因之一是管理員瀏覽器還開著被駭時期的分頁，一偵測到管理員登入就重新植入惡意程式；另一個是登入頁被側錄的密碼沒換。高讚建議用 WP-CLI 清掉所有登入工作階段（例如 `wp user session destroy --all`）、更換 salts 與密碼，並開啟兩步驟驗證。剛處理完 Rank Math 事件的站可以一併參考。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1x7mk9f/hacked_site_kept_reinfecting_itself_after_every/)

### Reddit｜明明用傳統結帳，還是被打 Store API
r/woocommerce 的店家發現掃描器改走 `?rest_route=` 與 `/wc/store/v1/batch`，繞過了結帳頁上的 Turnstile 與 Cloudflare 規則；偽造的 Stripe、PayPal webhook 則被簽章驗證擋下。沒用區塊結帳的商店，也別忘了替 Store API 加防護。

→ [Reddit](https://www.reddit.com/r/woocommerce/comments/1x4w299/classic_checkout_store_hit_through_the_blocks/)

---

## 職缺（近 7 日）

### 104｜品牌電商營運暨 AI 網站實作專員（旺得富家具）
新北樹林；待遇面議。操作 WordPress／WooCommerce 後台維護商品與網站內容，並用 AI Agent 搭配 GitHub 修改與測試前端頁面，兼做社群、客服與出貨。（頁面更新日期 10/19）

→ [職缺](https://www.104.com.tw/job/96qgs)

### 104｜品牌網站企劃主任（睡眠王國集團）
新北五股；月薪 40,000～50,000 元。負責雙品牌 WordPress 官網的日常維運、頁面更新與版位管理，並製作活動落地頁。（頁面更新日期 10/14）

→ [職缺](https://www.104.com.tw/job/8yr1p)

### 104｜業務行政暨行銷助理（匠優室內裝修）
台中西屯；月薪 29,500～38,000 元。負責 WordPress 官網內容更新、產品資料維護與圖片上架，另有美編、社群與行政客服工作。（頁面更新日期 10/19）

→ [職缺](https://www.104.com.tw/job/96r3d)

### 104｜網站、行銷、FB 文字編輯（愛露營）
台中北屯；月薪 29,500 元以上。需熟悉 WordPress 後台操作，負責露營相關內容、網站與社群文字編輯。（頁面更新日期 10/18）

→ [職缺](https://www.104.com.tw/job/7t6wy)

### 104｜網站專案顧問 Website Project Manager（奇策整合行銷）
台中西屯；月薪 38,000～48,000 元。負責企業官網、電商與 WordPress 網站從規劃、改版到上線的專案管理。（頁面更新日期 10/17）

→ [職缺](https://www.104.com.tw/job/94pnn)

### 1111｜網頁製作、架設與管理專員（鴻富工程科技／朵兒酷斯）
高雄新興；面議（經常性薪資 4 萬元以上）。負責網站規劃、前端製作與上線，需熟悉 WordPress、Shopify 或 Wix 其中一種，並處理購物車、金物流功能與主機、SSL、備份維運。（更新日期 10/19）

→ [職缺](https://www.1111.com.tw/job/133050236)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
