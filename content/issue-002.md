# WP 台灣週報 · 第 2 期

**出刊日：** 2026-09-07

本週重點：WCUS 公開 PHP／相容座談筆記、WooCommerce 11.1 工程 advisory 補齊、Super Forms 未授權上傳漏洞仍在野外利用討論中，並收錄台灣工作室近文與主機商 MCP／bot 流量觀點。

---

## 本週熱點

### WCUS 2026：WordPress 與 PHP 社群怎麼談下去
Make/Core 整理 WordCamp US 一場非正式 PHP 座談（Chatham House Rule）。痛點包含：PHP 社群常不把 WP 開發者當「PHP 開發者」、長期向下相容造成外掛與套件摩擦、Gutenberg 與 Core 對 PHP 7.4 的工具鏈不一致。筆記引用約 **18%** 網站仍跑 PHP 7.4；並討論主機升級誘因、polyfill／依 PHP 版本閘控功能，以及以 WASM 作為 PHP extension、沙箱載入原生能力等後續方向。

→ [原文](https://make.wordpress.org/core/2026/09/03/wordcamp-us-2026-php-conversation/)

### 7.1 Icon Registration API：註冊自訂圖示集合
Developer Blog 教學如何用 `wp_register_icon_collection()`／`wp_register_icon()` 註冊集合與圖示，並以完整外掛範例示範。文末標明 7.1 限制：SVG 僅允許部分元素、`stroke` 會被剝除、尚無可重用編輯器 IconPicker（部分預計 7.2+）。可與上期「Dashicons 改走 SVG」提案對讀。

→ [原文](https://developer.wordpress.org/news/2026/08/18/hands-on-with-the-wordpress-7-1-icon-registration-api/)

### Accessibility Lab 原型外掛：實驗與實用工具分流
無障礙團隊推出 **Accessibility Lab**（對齊 Performance Lab／AI plugin 的 canonical 實驗外掛模式），強調**不是**把 Core 無障礙修補移出 Core。首版模組含：媒體庫檢視選項、區塊無障礙檢查（整合 Troy Chaplin 驗證框架）、標題層級即時警告。目前託管於個人 GitHub，之後擬遷至 WordPress.org。

→ [原文](https://make.wordpress.org/accessibility/2026/08/21/help-shape-the-accessibility-lab-plugin/)

### WordPress 7.1.1：時程未變，下週 RC1
維護版仍預計 **9/10 RC1**、**9/17 正式版**；範圍僅限 7.1 週期引入或刻意延後的 bug。無改期或並列安全釋出。

→ [原文](https://make.wordpress.org/core/2026/09/02/wordpress-7-1-1-release-schedule/)

---

## WooCommerce／電商

### 變體圖庫正式進核心
11.1 起變體圖庫全面啟用，實驗開關移除；原 Additional Variation Images 外掛升級時會自動停用。圖庫改存變體上的 `_product_image_gallery`；REST `gallery_image_ids` 可讀寫。舊 meta `_wc_additional_variation_images` 會以 Action Scheduler 批次遷移（每次最多 250）。開發者應改用 `get_gallery_image_ids()`／`set_gallery_image_ids()`。

→ [原文](https://developer.woocommerce.com/2026/09/01/additional-variation-images/)

### Store API／REST 略過區塊註冊：約快 30–42%
`BlockRegistrationContext` 在非渲染請求上略過區塊／樣式註冊。若外掛在被略過的請求裡依賴 Woo 已註冊區塊，需用 `woocommerce_should_register_blocks` 在該情境回傳 `true`。官方不建議再繼承內部 `AbstractBlock`，應走 `block.json`＋`register_block_type()`。

→ [原文](https://developer.woocommerce.com/2026/08/31/block-registration-skips-11-1/)

### 退役穩定版 Admin feature flags
一批已穩定的 WooCommerce Admin 功能旗標改由核心直接載入；`Features::is_enabled()`、`window.wcAdminFeatures` 等仍有相容 shim，但會發 deprecation。擴充應拿掉對已退役旗標的閘門。

→ [原文](https://developer.woocommerce.com/2026/08/31/retiring-stable-feature-flags/)

### 訂單項目刪除：只刪「當下要刪」的 ID
接續 11.0 延遲刪除。11.1 起核心會記住要求刪除當下的項目 ID，避免擴充在 `save()` 前寫入的替換項目被誤刪。自訂 order data store 可覆寫 `delete_items_by_ids()` 以選擇延遲刪除。

→ [原文](https://developer.woocommerce.com/2026/08/31/changes-to-order-item-deletion/)

### 預告 11.2：商品儲存與排序 hooks 調整
商品儲存路徑減少無變更的 term／meta 寫入（單次 save SQL 可降約 45%）；掛在 `set_object_terms` 且假設「每次存商品都會觸發」的程式可能受影響。後台拖曳排序改為範圍型演算法：`woocommerce_after_product_ordering` 等標記棄用，新 hooks 為 `woocommerce_product_ordering_process_reindexed_products`、`woocommerce_product_ordering_process_moved_products`。

→ [原文](https://developer.woocommerce.com/2026/08/25/product-lifecycle-hooks/)

### 藍新智慧 ATM 2.0：ATM 退款 API 踩坑
Oberon Lai 實作「僅 ATM 虛擬帳號＋需 API 退款」時的實務筆記：後台需申請開通、須收單凱基並帶 `SourceType`／`SourceBankId`／`SourceAccountNo`、退款要自存匯款人帳號、虛擬帳號最短可設 1 小時等。參數與開通規則仍以藍新客服／手冊為準。

→ [原文](https://oberonlai.blog/newebpay-smart-atm-2-refund-api/)

---

## 資安

### Super Forms：未授權任意檔案上傳（CVE-2026-14894）
NVD／Wordfence：**CVE-2026-14894**（CVSS 9.8）。Super Forms ≤ **6.3.313** 的 `submit_form` 缺少檔案類型驗證與權限檢查；未登入者可先透過公開 AJAX 取得 nonce，再上傳可執行檔達成 RCE。請升級至 **6.3.314+**，並檢查 `uploads` 目錄是否有異常 PHP。社群亦在討論相關示警。

→ [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-14894) · [Wordfence](https://www.wordfence.com/threat-intel/vulnerabilities/id/e9c7fb16-efbb-41e9-be13-98e96c1e9100?source=cve) · [Reddit 討論](https://www.reddit.com/r/Wordpress/comments/1w6k4sy/super_forms_plugin_has_critical_vulnerability/)

### Elementor Pro：未授權檔案上傳（CVE-2026-32475）
NVD／Patchstack：**CVE-2026-32475**（CNA 標 CVSS 9.0）。Elementor Pro ≤ **4.2.1** 存在危險類型檔案上傳風險。請升級至修補版本（業界報導指 **4.2.2+**），並掃描異常上傳檔。

→ [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-32475) · [Patchstack](https://patchstack.com/articles/critical-unauthenticated-file-upload-to-rce-in-elementor-pro-plugin?_s_id=cve)

---

## 在地工作室部落格

### AI 寫部落格懶人包：口述到發布
金城事務所整理「口述心得 → AI 整理架構／套 SEO・AEO → 事實校對後發布」流程，並串站上實戰文當檢查清單。

→ [原文](https://iseeu.tw/ai-writing-guide/)

### 聯盟折扣碼怎麼寫才容易被 AI 收錄
同站說明折扣碼要被 AI 摘要收錄，一行宜寫齊代碼、折幅、條件、期限；並談專屬碼、過期擺放與合規紅線。

→ [原文](https://iseeu.tw/affiliate-coupon-code/)

### Google 我的商家：本地 SEO 操作指南
鵠崙設計整理 GBP 基本資料、圖片、評論、動態與洞察數據，強調本地搜尋與轉換意圖。

→ [原文](https://www.design-hu.com/web-news/google-my-business-local-seo-guide.html)

### 2026 網頁設計範例：30 個台灣企業案例分類
同站依製造業 B2B、專業服務、品牌形象、電商、活動單頁分類談設計邏輯與趨勢（大字級、克制動效、模組化、AI 搜尋友善結構）。

→ [原文](https://www.design-hu.com/web-news/web-design-examples-2026.html)

### 品牌官網設計流程：七步驟到上線
哈拉設計拆解目標受眾、關鍵字、介面體驗、WordPress 建置、測試上線與持續優化，偏中小企業官網實務。

→ [原文](https://dianroot.com/brand-official-website-design-guide-seo-wordpress/)

---

## 主機商觀點

### 在 Kinsta 站上把 WordPress 接到 MCP／Claude（Kinsta）
逐步：安裝 MCP Adapter、用 Abilities API 註冊草稿能力、Application Password、Postman 握手，再以 Claude Desktop＋`@automattic/mcp-wordpress-remote` 連線。文末導向其主機與 APM，請標廠商立場。

→ [原文](https://kinsta.com/blog/wordpress-mcp/)

### Bot 流量的隱形成本：頻寬報告看不見的地方（Kinsta）
分析基礎設施上大量 bot 打購物車／篩選 URL 等動態路徑，強制跑 PHP 與資料庫卻無轉換。並介紹 MyKinsta Bot Protection 分級與「封鎖 AI 爬蟲」開關。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/bot-traffic-hosting-cost-wordpress/)

### 用 MCP 批次安全滾動 WP／PHP 更新（Pressable）
說明以 Pressable MCP 盤點版本、先測代表性站、查 PHP log、分批上線與必要時回滾。廠商立場：Pressable。

→ [原文](https://pressable.com/blog/how-to-safely-roll-out-wordpress-and-php-updates-at-scale/)

### WooCommerce Analytics 為何「說謊」（Rocket.net）
談歸因缺口（社群／即時通訊常變 direct），建議結帳後、離開意圖、出貨信等回饋觸點。廠商立場：Rocket.net。

→ [原文](https://rocket.net/blog/your-woocommerce-analytics-are-lying/)

---

## 影音精選

### The Agentic Web Is Coming – Is WordPress Ready?
官方頻道演講：Agentic Web 與 WordPress 準備度（Miriam Schwab）。

→ [影片](https://www.youtube.com/watch?v=35gk5eWui9E)

### Your Client Wants AI. Their Lawyer Just Said No.
客戶要上 AI、法務擋下的實務張力（@WordPress）。

→ [影片](https://www.youtube.com/watch?v=YXTKv5rhcDM)

### Beyond Plugins: Real Accessibility for WordPress Websites
官方頻道談真正可及性，而不只是「裝無障礙外掛」。

→ [影片](https://www.youtube.com/watch?v=jibx_v5MP4Y)

### WordPress 7.1 新功能示範（WPBeginner）
示範向教學；細節仍以官方釋出與 Dev Notes 為準。

→ [影片](https://www.youtube.com/watch?v=DxLidMp1At4)

### 犬哥：Google Ads 串接 Claude 分析燒錢點
行銷工具向實作；非 WordPress 核心釋出。

→ [影片](https://www.youtube.com/watch?v=GrKRhlljLF8)

---

## 社群熱議

### Reddit：什麼會讓你相信一份外掛評測？
討論認為可信評測需要 staging 實測、對應當前版本、清楚排除項與利益揭露，而非 SEO／聯盟導向的「最佳外掛」清單。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1w8rby5/what_actually_makes_you_trust_a_wordpress_plugin/)

### Reddit：區塊主題如何管可重用設計元件？
代理商討論 patterns、template parts、自訂區塊、`theme.json` 與鎖定策略，在全域一致性與單次編輯彈性之間找平衡。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1w7549h/how_do_you_handle_reusable_design_components_in_block_themes/)

### Reddit：Claude Design＋Claude Code 做電商站？
詢問以 Claude 設計／寫碼建新 Woo 站、既有 Elementor＋Woo 專案如何銜接；留言偏 AI 輔助流程與遷移限制。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1w7yo8a/claude_design_claude_code/)

### Reddit：Woo 運送區域改了運費卻不動
懷疑 Checkout Block／快取導致國家變更後運費未重算；建議查地理定位、必填欄位、CDN／頁面快取與 zone 順序。

→ [Reddit](https://www.reddit.com/r/woocommerce/comments/1w8lzm6/shipping_cost_doesnt_change_according_to_the_different_shipping_zone/)

---

*《WP 台灣週報》第 2 期 · 獨立研究一手來源，欄位架構借鏡 The WP Weekly。*
