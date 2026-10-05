# WP 台灣週報 · 第 6 期

**出刊日：** 2026-10-05

本週重點：**Gutenberg 24.1** 把設計工具擴到幾乎所有核心區塊，**7.2 Beta 1** 訂在 10/20–22；CVE-2026-87902 五天內已有逾三萬個 IP 發動攻擊，還沒升 **7.1.2** 的站請立刻處理。Woo 宣布 **11.6 起要求 PHP 8.1+**，11.2 正式版預計本週釋出；台灣 **RY Tools**、**Woomp** 都有綠界物流相關修正。10 月台北彩虹小聚（10/19）與高雄小聚（10/20）已開放報名。

---

## 本週熱點

### Gutenberg 24.1：設計工具擴到幾乎所有核心區塊
9/30 釋出，約 140 個 PR，把背景、陰影、邊框、顏色與字型等設計工具擴到幾乎所有核心區塊（含 Accordion、Tabs、Comments 等），text-shadow 也能直接在 Global Styles 設定。外掛最低需求同步升到 **WordPress 7.0**，還在 6.x 的站裝最新版 Gutenberg 前要先升核心。

→ [原文](https://make.wordpress.org/core/2026/09/30/whats-new-in-gutenberg-24-1-30-september/)

### WordPress 7.2：Beta 1 訂在 10/20–22，正式版 12/8–10
7.2 時程頁已列出確切日期：**Beta 1 為 10/20–22**，**正式版為 12/8–10**（延續上期報導的「12 月初、改以 Zoom 直播釋出」）。測試團隊同時公布 7.2 專屬 Test Scrub，固定在每月第 2、4 個週四台北時間 23:00，想提早回報問題的開發者可以排進行事曆。

→ [7.2 時程](https://make.wordpress.org/core/7-2/) · [Test Scrub 時程](https://make.wordpress.org/test/2026/10/01/test-scrub-schedule-for-wordpress-7-2/)

### Presence API 0.14.0：看得到誰在線上，AI agent 也會標出來
這個功能外掛會顯示誰在線上、正在編輯哪一篇。現行 0.14.0 把編輯鎖改存在 presence 資料表，在線紀錄預設開啟、可在設定中關閉；透過 REST 或 MCP 編輯內容的 AI agent 會帶「Agent」徽章出現在名單上，多人或人機協作編輯時比較不會互相覆蓋。

→ [原文](https://make.wordpress.org/core/2026/10/02/presence-api-whats-new/)

### WordPress.org 支援信箱改用自架 FreeScout
WordPress.org 約 20 個團隊支援信箱將從 HelpScout 搬到自架的 FreeScout，改用 WordPress.org 帳號登入並強制兩步驟驗證；分階段進行，外掛審查團隊的收件匣排在最後、暫定 10/19 搬遷（官方表示時程可能調整）。近期有外掛送審或申訴往來的開發者，留意回信來源可能改變。

→ [原文](https://make.wordpress.org/meta/2026/10/02/heads-up-helpscout-to-freescout-migration/)

---

## WooCommerce／電商

### Woo 11.6 起要求 PHP 8.1+（2027 年 2 月）
官方定案：**WooCommerce 11.6**（預計 2027 年 2 月）起最低需求為 **PHP 8.1**，比提案時的 11.5 晚一版。**11.3** 起後台會開始提醒，**11.5** 是最後支援 PHP 7.4／8.0 的版本。台灣仍有不少共享主機預設舊版 PHP，現在就該排升級與相容性測試。

→ [原文](https://developer.woocommerce.com/2026/09/29/php-8-1-requirement/)

### Dual API 移出核心，改為獨立外掛
實驗性的 Dual API 自 **11.2** 起從核心移除、改成獨立外掛，內建的商品／優惠券測試用 API 也一併拿掉。只影響曾經啟用這項實驗功能的開發者；11.2 正式版預計本週（10/6 起）釋出，有用到的請先裝外掛版再升級。

→ [原文](https://developer.woocommerce.com/2026/09/28/experimental-dual-api-plugin/)

### RY Tools 2026.9.29：綠界物流單可沿用訂單編號
相對上期的 2026.9.26，本週再出兩版：**2026.9.28** 再強化金流付款通知的驗證，**2026.9.29** 讓綠界物流的第一張物流單可以使用與訂單相同的編號，方便對帳。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### Woomp v3.5.22：修綠界超取沒選門市也能下單
**v3.5.22**（10/1）修正綠界超商取貨「沒選門市也能結帳」，以及離島門市被誤擋的問題。前一版 **v3.5.21**（9/21）則修了 PayUni V2 信用卡「已扣款卻掉單」。用 Woomp 搭綠界或 PayUni 的店家建議升級。

→ [原文](https://github.com/zenbuapps/woomp/releases/tag/v3.5.22)

### Photo Reviews for WooCommerce ≤1.2.30：刪評論可能連帶刪商品（CVE-2026-101923）
攻擊者先埋一則特製評論，店家刪除那則評論時，就可能連帶永久刪除商品或頁面（CVSS **8.1**）。請升級到 **1.2.31**。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-101923)

---

## 資安

### 【更新】CVE-2026-87902 已被大規模利用
上期報導的核心頁面模板漏洞，CrowdSec 觀測到修補後 5 天內有 **30,813 個 IP** 發動攻擊，單日訊號最高 **124,154 筆**（9/27）；即時追蹤頁顯示近 30 天累計約 **14.4 萬個 IP**，本週量能略降但仍持續。請確認所有站都已在 **7.1.2**（或各分支對應資安版）。

→ [CrowdSec 報告](https://www.crowdsec.net/vulntracking-report/cve-2026-87902-wordpress-vulnerability) · [即時追蹤](https://tracker.crowdsec.net/cves/CVE-2026-87902)

### Super Forms ≤6.3.316：註冊表單可直接拿管理員（CVE-2026-15989）
未登入者在註冊表單加上 `role=administrator` 就能取得管理員權限（CVSS **9.8**）。同版另有可讀取伺服器任意檔案的路徑遍歷 **CVE-2026-15896**（9.1）。目前最新為 **6.3.320 LTS**，有用的站請立刻升級並檢查管理員名單。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-15989)

### WordPress File Upload ≤5.1.10：未授權 SQL Injection（CVE-2026-62071）
不需登入即可觸發 SQL Injection（CVSS **9.3**），修補版 **5.2.0**。

→ [Patchstack](https://patchstack.com/database/wordpress/plugin/wp-file-upload/vulnerability/wordpress-wordpress-file-upload-plugin-5-1-10-sql-injection-vulnerability)

### Beaver Builder ≤2.11.0.5：條件式任意 shortcode 執行（CVE-2026-92084）
在頁面使用 Sidebar 模組、且其中小工具會顯示攻擊者可控文字的情況下，未授權者可執行任意 shortcode（CVSS **9.1**）。外掛目錄現行版為 **2.11.0.6**。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-92084)

---

## 台灣站務與活動

### 【10/19】WordPress 彩虹小聚：當老闆本人就是 ERP
林馬具負責人林昀與工程師 Moksaweb 分享 WP 電商長大後怎麼整理營運流程、把 LINE 預約串進系統，另有閃電講。**2026-10-19（一）18:30–21:30**，台北言文字（重慶南路一段 11 號），入場低消一杯飲料。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316808850/)

### 【10/20】高雄 WordPress 小聚
講題「Google 商家經營：把陌生的客人變成熟客」（Allen 國安）。**2026-10-20（二）19:00–21:00**，高雄 Second Space RED（七賢一路 294 號），場地費 100 元，需先完成報名。

→ [Meetup](https://www.meetup.com/kaohsiung-wordpress-meetup/events/316748093/)

### WordCamp Asia 2027 徵 Contributor Day 工作坊引導者
WordCamp Asia 2027 將於 **2027-04-09** 在馬來西亞檳城舉行 Contributor Day，現正徵求工作坊引導者，申請至 **11/24**。

→ [原文](https://asia.wordcamp.org/2027/call-for-contributor-day-workshop-facilitators/)

---

## 在地工作室部落格

### 廠商審稿小幫手：業配草稿免登入標註
金城事務所自製外掛：合作廠商不用登入後台就能在業配草稿上直接標註，作者一鍵套用修改，審稿紀錄保存一年。為工作室自家客戶提供。

→ [原文](https://iseeu.tw/mr-review/)

### 網站數據小幫手：後台直接看每篇文章人氣與廣告收益
同站自製外掛，用 GA4 資料在 WordPress 後台列出每篇文章的人氣與廣告收益，文中也說明為什麼不直接用 GA4、Site Kit 或 Jetpack。

→ [原文](https://iseeu.tw/mr-analytics/)

### 痞客金賞小幫手：從痞客邦搬家後找回得獎貼紙
從痞客邦搬到 WordPress 的部落客，可用短代碼把 2017–2024 年的金點賞貼紙放回原本文章。

→ [原文](https://iseeu.tw/mr-pixstar/)

---

## 主機商觀點

### WordPress 先需要 API，再談更多 AI（Kinsta）
主張 AI agent 管站應該靠文件完整的 API，而不是模擬點擊後台；文中列了四個檢查主機是否「agent-ready」的問題，換成其他主機也能拿來問。另一篇延伸教學示範用 SSH＋WP-CLI 別名讓 CLI agent 操作遠端站，並提醒要設限與人工核准指令。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/wordpress-needs-apis/) · [CLI agent 教學](https://kinsta.com/blog/cli-agents-wordpress/)

### 好 bot／壞 bot 二分法已經不夠用（WP Engine）
同一個爬蟲可能同時做搜尋索引與 AI 訓練，建議改用「爬多少、帶回多少流量」來決定擋不擋，並談到 Cloudflare 9 月推出的新爬蟲控制項。廠商立場：WP Engine。

→ [原文](https://wpengine.com/blog/reframing-bot-binary/)

### 搬家 WordPress 實際會發生什麼（Pressable）
整理搬站四個常見地雷：Woo 訂單在切換期間留在舊主機、沒預覽就切 DNS、搬法選錯，以及上線後沒人可問。廠商立場：Pressable（Automattic 體系）。

→ [原文](https://pressable.com/blog/what-actually-happens-when-you-migrate-a-wordpress-site/)

---

## 影音精選

### WordPress：用 Abilities API 以自然語言操作 WooCommerce
官方頻道講解 Abilities API（WordPress 6.9 引入）如何讓 AI agent 看懂外掛能做什麼，並示範用自然語言操作 Woo 商店。

→ [影片](https://www.youtube.com/watch?v=DKqmowLO-Fs)

### 犬哥：Google 排名和 AI 引用越來越不一樣
引用 Ahrefs 研究：AI Overview 引用的頁面與 Google 前 10 名的重疊率，從約 76% 降到約 38%，談這對內容與 SEO 策略的意義。

→ [影片](https://www.youtube.com/watch?v=X9IL5t60mkA)

### WordPress：零預算的 WordPress 防護
從 wp-config 強化、權限控管、擋暴力登入到入侵後應變，整理不花錢就能做的防護清單。

→ [影片](https://www.youtube.com/watch?v=87D3RZNsy_8)

---

## 社群熱議

### Reddit｜網站被駭導向博弈站，駭客還留了「簽名檔」
多個按鈕被改成連到博弈站，作者清站時找到駭客留下的檔案與 Telegram 連結。高讚留言提醒那只是故意留的簽名，真正的後門常藏在隱藏管理員、沒有標頭的 mu-plugin、殘留的 application passwords，建議全部撤銷並跑 `wp core verify-checksums`。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wwa4lv/our_site_got_hacked_but_i_found_the_hackers/)

### Reddit｜你的站真的升到 7.1.2 了嗎？
wp-config 殘留 `WP_AUTO_UPDATE_CORE=false`，或關了 WP-Cron 卻沒設伺服器 cron，網站可能好幾年都沒更新、後台也不提醒。建議用 `wp core version` 逐站檢查；高讚留言推薦設成 `'minor'`，只自動套小版本資安更新。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wu0oxr/did_your_sites_actually_update_to_712_or_do_you/)

### Reddit｜每月 50 個訪客，卻因流量超量被共享主機停權
留言多半指向 AI 爬蟲，特別是 AJAX 多重篩選頁會產生大量網址組合；也提醒「每月訪客數」通常不含 bot，要看主機原始 log 才知道真相。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wu2ucg/shared_host_suspended_our_site_for_excessive/)

### Reddit｜接手客戶網站，卻沒人知道管理員帳號和主機在哪
接案者拿到的不是管理員帳號，客戶也不知道主機與 Cloudflare 帳號，最後從 DNS 一路追到 AWS 上的 Plesk，才找到原本的網頁公司，對方三年來把帳單寄到沒人收的信箱。很適合拿來提醒客戶自己保管網域、主機與 DNS 帳號。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wv7amz/client_lost_access_to_the_admin_account_for/)

### Reddit｜大流量 Woo：WP-Cron 改伺服器 cron、排程佇列怎麼修？
兩串討論同一件事。一家每天幾千次 wp-cron 請求、尖峰出現 499 錯誤的店，多數人支持改用伺服器 cron 每 1～5 分鐘跑一次，但也提醒要另查 PHP-FPM、Heartbeat、cart fragments 與 bot 流量。另一家月訂單 650 筆的店，留言建議先看真正逾期的排程任務、確認啟用 HPOS，佇列正常後再加 Redis，最後才考慮升級主機。

→ [Reddit：WP-Cron](https://www.reddit.com/r/woocommerce/comments/1wwn3bu/hightraffic_woocommerce_disable_wpcron_and_switch/) · [Reddit：650 筆訂單](https://www.reddit.com/r/woocommerce/comments/1wxgjvn/help_650_ordersmo_shop_host_says_upgrade_dev_says/)

### Reddit｜用 AI 把 2018 年的老主題改到相容 PHP 8.3？
高讚回覆認為 PHP 相容問題不難修，真正的風險是多年沒更新的 WPBakery。有人分享用 Claude 把 17 個舊站升到 8.3；共識是先在 staging 開 log 逐一修，看不懂的 AI 程式別上線，長期還是要換主題或改用區塊。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wx01xx/can_an_llm_refactor_a_wp_theme_for_php_8x/)

---

## 職缺（近 7 日）

### 104｜WordPress 網頁設計師（默聲創意）
台北內湖；月薪 45,000～52,000 元，部分遠端。負責網站架構與頁面設計，需熟悉 WordPress 主題與區塊編輯器、HTML/CSS 與 RWD，需附作品集。（頁面更新日期 10/02）

→ [職缺](https://www.104.com.tw/job/8l2oe)

### 104｜WordPress 網頁設計工程師（全崴科技）
新北三重；面議（經常性薪資達 4 萬元或以上）。以 WordPress 做網頁設計、開發與修正更新，需熟悉 WordPress 架構並能獨立作業，含上線測試與日常維護。（頁面更新日期 10/02）

→ [職缺](https://www.104.com.tw/job/8x6ue)

### 104｜SEO 數位行銷工程師・WordPress 專家（好樂購家具）
台中北屯；月薪 42,000～45,000 元。負責官網 WordPress 維護、外掛整合與 Core Web Vitals 效能，以及技術 SEO（Schema）與 GA4／GTM 追蹤；需 2 年以上 WordPress 實作經驗。（頁面更新日期 10/01）

→ [職缺](https://www.104.com.tw/job/8zn18)

### 104｜WordPress 網頁設計師／美編（弘琦）
台北內湖；月薪 45,000～48,000 元，歡迎 UI／視覺／平面設計轉職。用 WordPress＋Elementor 建置與維護網站、做 RWD，並兼做 Banner、CIS 等美編。（頁面更新日期 09/29）

→ [職缺](https://www.104.com.tw/job/9574l)

### 1111｜WordPress 網頁設計師（陽信開發）
新竹東區；月薪 35,000 元以上，不考慮外包接案。負責官網架站、版型、外掛設定除錯與速度／基本 SEO 優化；需有購物車、金流串接、會員系統經驗並附 WordPress 作品。（頁面日期 10/02）

→ [職缺](https://www.1111.com.tw/job/132835238)

### 1111｜AI 網站軟體開發工程師（燿華電子）
新北土城；月薪 45,000～55,000 元。以 PHP／JS／MySQL 建置維護網站並串接 AI 影像辨識，WordPress 建置與 Theme／Plugin 調整為職責之一。（頁面日期 10/01）

→ [職缺](https://www.1111.com.tw/job/132810815)

### 1111｜台中班網頁設計師（伊美美容教育機構）
台中中區；月薪 32,000～40,000 元。負責 WordPress 網站設計與維護、UI/UX，以 HTML/CSS 與 Photoshop／Illustrator 切版，並協助廣告文宣設計。（頁面日期 09/28）

→ [職缺](https://www.1111.com.tw/job/130449558)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
