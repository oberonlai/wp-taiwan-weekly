# WP 台灣週報 · 第 3 期

**出刊日：** 2026-09-14

本週重點：WordPress **7.1.1 RC1** 如期釋出（正式版仍訂 9/17）、**7.2 Release Squad** 成軍、Core／Gutenberg 開發環境升上 **Node.js 24**；Woo 提案 11.5 起要求 **PHP 8.1+**，台灣 **RY Tools** 推至 2026.9.10，並收錄 Checkout Block 金流／發票實務與目錄 AI 掃毒等社群熱議。

---

## 本週熱點

### WordPress 7.1.1 RC1 已開放測試
維護版 **7.1.1 RC1** 於 9/10 如期釋出（bug-fix only）。可用 Beta Tester（Point Release＋Nightlies）、`wp core update` 指向 RC zip，或直接下載測試包。公告列出多則 7.1 週期回歸修補（含 Multisite 刪使用者重新指派、sitemap 無文章時回 404、tooltip／媒體 PNG、blockGap／Site Icon 等）以及一批 Gutenberg PR。正式版仍預計 **2026-09-17 15:00 UTC**（約台北當日 23:00）；若 RC 發現問題可改期。協調在 Slack `#7-1-release-leads`。

→ [原文](https://make.wordpress.org/core/2026/09/10/wordpress-7-1-1-rc1-is-now-available/) · [時程](https://make.wordpress.org/core/2026/09/02/wordpress-7-1-1-release-schedule/)

### 7.2 Release Squad 組成，Beta 約十月下旬
7.2 釋出小隊正式公布：Release Lead **Matt Mullenweg**；Release Coordination David Baumwald；Tech Leads George Mamadashvili、Peter Wilson；預設主題 Twenty Twenty-Seven 由 Henrique Iamarino（Design）與 Maggie Cabrera、Carolina Nymark（Dev）領軍。官方 7.2 頁時程：**Beta 1 約 10/20–22**，**正式版約 12/8–10**。

→ [原文](https://make.wordpress.org/core/2026/09/09/announcing-the-wordpress-7-2-release-squad/) · [7.2 頁](https://make.wordpress.org/core/7-2/)

### Core／Gutenberg 開發環境改為 Node.js 24、npm 11
自 **2026-09-08** 起，貢獻 Core／Gutenberg 需 **Node.js ≥24.18.0**、**npm ≥11.16.0**（trunk、`wp/7.1`、wordpress-develop 的 7.1 branch）。貢獻者應 `nvm install && nvm use`（或 fnm）後 `npm ci`，並 rebase 開著的 PR。升級解鎖隔離相依、OIDC 發佈、`minimumReleaseAge`、Node 原生 TypeScript type-stripping 等。

→ [原文](https://make.wordpress.org/core/2026/09/09/updating-wordpress-to-use-node-js-24-and-npm-11/)

### 開發者月報（2026 年 9 月）
Developer Blog 整理 Gutenberg 23.8／23.9 與 7.2 展望，並串起可執行 Code Reference 範例、區塊 variation／transform 鍵盤快捷鍵、可擴充 Site Editor、DataViews 公開化、PHP-only block schema、theme.json states 修正等。適合外掛／主題開發者一站掃描本月變化。

→ [原文](https://developer.wordpress.org/news/2026/09/whats-new-for-developers-september-2026/)

---

## WooCommerce／電商

### 提案：WooCommerce 11.5 起最低 PHP 改為 8.1+
官方徵求意見：目標在 **11.5**（暫訂 **2027-01**）把最低 PHP 從 7.4 拉到 **8.1+**，結束對 7.4／8.0 的支援。未達標商店不會被強制升級；WordPress 預設不會對不符 PHP 的站提供該版更新。內部 opt-in 數據指 PHP 7.4 約 7%、8.0 約 2%。此為提案、尚未定案。

→ [原文](https://developer.woocommerce.com/2026/09/08/from-php-7-4-to-8-1/)

### RY Tools 更新至 2026.9.10：物流費用公式修正
台灣常用金物流整合外掛自 2026.8.30 再推一版：**2026.9.10** 修正「物流費用計算公式特定情況下失效」。目錄需求 PHP **8.2+**、WP **6.8+**、Woo **9.0+**；已測至 WP 7.1。本週 Woo 穩定版仍為 **11.1.0**；綠界／藍新官方購物車外掛目錄無新版號。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### 藍新金流串接到 Checkout Block（鐵人賽實務）
Oberon Lai 以藍新 MPG 走完台灣接案金流：AES／TradeSha／CheckCode、中繼頁 POST、**訂單狀態只信 NotifyURL**、金額比對與冪等；並示範區塊結帳需另寫 `AbstractPaymentMethodType`＋`registerPaymentMethod`（`$name` 須等於 gateway `$id`），建置需 `@woocommerce/dependency-extraction-webpack-plugin`。屬開發者實務教學，參數仍以藍新手冊為準。

→ [金流篇](https://oberonlai.blog/woocommerce-newebpay-payment-gateway/) · [Checkout Block 篇](https://oberonlai.blog/woocommerce-checkout-block-custom-payment-gateway/)

### 區塊結帳：台灣電子發票條件欄位（零 JS）
同系列示範用 `woocommerce_register_additional_checkout_field()` 做出個人／公司／捐贈連動欄位，以 JSON Schema 控制 `hidden`／`required`，並含統編檢查碼與載具格式正規化；提醒勿再依賴舊版 `woocommerce_checkout_fields`＋jQuery 套路。

→ [原文](https://oberonlai.blog/woocommerce-checkout-block-invoice/)

### Subscriptions 9.2.0：設定與開發變更
付費擴充 Subscriptions **9.2.0**：實物商品也可按比例計費、手動續訂旗標收斂、移除訂閱項目權限改查 `edit_shop_subscription_line_items`、送禮改每商品 meta、設定頁改掛 `woocommerce_get_settings_pages` 等。有做訂閱／自訂 gateway 者應對照完整 changelog。

→ [原文](https://developer.woocommerce.com/2026/09/08/wc-subscriptions-9-2-0/)

---

## 資安

### WooCommerce < 11.1.0：未授權 HTTP DoS（CVE-2026-48888）
NVD／Patchstack：**CVE-2026-48888**（CNA 標 CVSS 7.5）。WooCommerce **< 11.1.0** 資源分配無節流（CWE-770），可造成未授權 HTTP DoS。請升級至 **11.1.0+**。

→ [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-48888) · [Patchstack](https://patchstack.com/database/wordpress/plugin/woocommerce/vulnerability/wordpress-woocommerce-plugin-11-1-0-denial-of-service-attack-vulnerability)

### All-in-One WP Migration ≤7.109：二次 SQLi 可至 RCE（CVE-2026-19949）
NVD／Patchstack：**CVE-2026-19949**（Wordfence CNA 標 CVSS 8.8）。還原封存流程存在二次 SQL 注入；未授權者可附加查詢，若管理員執行還原可能洩漏密鑰並進一步達成 RCE。請升級至 **7.110+**，勿只依賴防火牆規則。

→ [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-19949)

---

## 在地工作室部落格

### Weebly 搬家到 WordPress：六步驟流程
鵠崙設計整理備份匯出、建 WP 環境、內容重建、301／網域、追蹤重設等步驟，強調趁搬家升級內容與效能，並提醒 SEO 不掉排名要點。

→ [原文](https://www.design-hu.com/web-news/weebly-to-wordpress-migration-guide.html)

### 網站多久維護一次？頻率對照表
同站拆每日備份、即時監控、每週核心／外掛更新、每月連結表單 SSL、每季速度與資料庫、每年健檢；強調安全更新應盡快處理。

→ [原文](https://www.design-hu.com/web-news/website-maintenance-frequency.html)

### AI 流量要拆兩帳：機器讀取 vs 真人點入
金城事務所主張用 Cloudflare 看機器人讀取、GA4 看真人從 ChatGPT 等點入；實例顯示引用次數與真人點擊可差上百倍，混算會誤判成效。

→ [原文](https://iseeu.tw/ai-traffic-analysis/)

---

## 主機商觀點

### AI workflow 何時該升級給人決定（Kinsta）
談 AI agent 已能跑部署、更新、資安反應，但哪些高風險步驟仍需人工 escalation。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/wordpress-ai-workflow-escalation/)

### Agentic commerce：為「第四類訪客」準備結帳（Kinsta）
區分訓練用爬蟲與代使用者比價／結帳的 AI agent；主張 WP／Woo 站要讓 agent 讀得懂結構、走得完結帳。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/agentic-commerce-wordpress/)

### 官方 AI Plugin 與 Agentic Web（WP Engine）
介紹 WP 7.0 後 AI Client／Connectors 與官方 AI plugin 方向（provider-agnostic、Settings → Connectors），以及結構化內容、Abilities API、權限與稽核日誌等準備清單。廠商解讀，細節仍回官方一手。廠商立場：WP Engine。

→ [AI Plugin](https://wpengine.com/blog/ai-plugin/) · [Agentic Web 需求](https://wpengine.com/blog/what-wordpress-needs-for-the-agentic-web/)

### 促銷檔期前用一則 prompt 預跑檢查（Pressable）
建議大檔前用單一 prompt 批次檢查快取、PHP、外掛相容與結帳路徑。廠商立場：Pressable。

→ [原文](https://pressable.com/blog/the-one-prompt-to-run-before-your-next-woocommerce-sale/)

---

## 影音精選

### 犬哥：GEO／AI 引用偏好與內容格式
談 GEO 與 AI 引用率、內容格式選擇；可與站上 SEO／GEO 文互相參照。

→ [影片](https://www.youtube.com/watch?v=SZ3TRIBYBLs)

### Keynote：Matt Mullenweg × Robert Jacobi
官方頻道 WCUS Keynote 對談；具體路線圖仍以 Make／News 為準。

→ [影片](https://www.youtube.com/watch?v=ss7ngc1VU-Q)

### The Update Day：代理商維運更新日流程
Mike Miler 談 WordPress 代理商的更新日檢查與維運節奏。

→ [影片](https://www.youtube.com/watch?v=GEV7p84xgM0)

### WPVibe vs WordPress AI Connectors（WPBeginner）
比較第三方 WPVibe 與核心／官方 AI Connectors；產品能力請回文字一手確認。

→ [影片](https://www.youtube.com/watch?v=NlN6SzD3Ons)

---

## 社群熱議

### Reddit：目錄用 AI 掃外掛／主題更新並自動擋
討論指出 WordPress.org 目錄對更新做 AI 資安掃描，惡意或嚴重問題會自動擋下；留言多半支持並認為在 AI 產碼時代必要。政策細節仍應回官方一手。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wc6jpj/wordpress_is_now_scanning_all_plugin_and_theme/)

### Reddit：為什麼公司關掉自動更新？
高互動串共識偏：自動更新可能一次炸多站、外掛衝突、帶 bug 的版本直上線；維運者多主張 staging／手動定期更新，Elementor 站尤不宜盲開。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wd7ac6/why_companies_turn_off_wordpress_and_plugin/)

### Reddit：「自我修復」型惡意程式愈來愈難清
整理短生命週期 payload、站外常駐、Service Worker 回種與 timestomping；留言案例多指向寧可乾淨重裝＋只還原內容。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wbqz5w/the_new_wave_of_selfhealing_wordpress_malware_is/)

### Reddit：Woo 擬最低 PHP 8.1+（官方工程師轉貼）
與本週 Woo 提案同題；留言一面倒支持棄 7.4／8.0，部分主張直接跳到 8.3／8.4。

→ [Reddit](https://www.reddit.com/r/woocommerce/comments/1wbmzs6/woocommerce_is_proposing_a_minimum_required_php/)

### Reddit：維護難的是蓋壞的站，不是 WordPress
主張痛點來自主題＋外掛堆疊；自訂主題、原生區塊、極短外掛清單，並稱 AI 降低自寫成本。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1weyf2s/maintaining_wordpress_isnt_hard_maintaining_a/)

### Reddit：8.0 要把留言搬成外掛？
提案把留言移出 Core；開發者歡迎縮小攻擊面，部落客則擔心多裝一個外掛。屬社群溫度調查，非正式路線圖。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wamixv/move_comments_to_a_plugin_in_80/)

---

## 職缺（近 7 日）

### 104｜WORDPRESS網站管理行銷人員（炫煬國際行銷）
新北中和；月薪 36,000 元以上。要求 WordPress 架站／套版／外掛（含 WooCommerce）、官網與 LiteShop 維護、LINE Pay／街口／綠界等金物流與社群維運。（頁面更新日期 09/08）

→ [職缺](https://www.104.com.tw/job/879me)

### yes123｜後端工程師（千陽號旅行社）
台北中山；月薪 40,000～55,000 元。列 WordPress／WooCommerce／金物流、Divi／Elementor，並強調 AIGC／LLM.txt 等 AI 友善站與客服工具。（職缺更新：2026-09-14）

→ [職缺](https://www.yes123.com.tw/wk_index/job.asp?job_id=20250829112827_20553648&p_id=c20250828165840_7341729)

### 104｜數位行銷專員（玩構網路科技）
高雄楠梓；月薪 35,000～45,000 元。SEO／GEO／AI 內容為主，工作內容含透過 WordPress 協助客戶調整版型、內容發布與 Landing Page。（頁面更新日期 09/14）

→ [職缺](https://www.104.com.tw/job/8kom7)

---

*《WP 台灣週報》第 3 期 · 獨立研究一手來源，欄位架構借鏡 The WP Weekly。*
