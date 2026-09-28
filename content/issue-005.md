# WP 台灣週報 · 第 5 期

**出刊日：** 2026-09-28

本週重點：WordPress **7.1.2** 緊急資安釋出（**CVE-2026-87902** 條件式 RCE，釋出數小時內已探測、已入 CISA KEV）、**Trac MCP** 公開上線、**7.2** 起改 livestreamed Zoom 釋出；Woo 升至 **11.1.2** 並開放 **11.2** beta（正式週約 10/6），台灣 **RY Tools** 推至 2026.9.26；台北小聚 **9/29** WebMCP 再提醒。

---

## 本週熱點

### WordPress 7.1.2 資安釋出（CVE-2026-87902）
**7.1.2** 為純資安釋出（critical），建議立刻更新（有自動背景更新的站會自動開始）。未授權攻擊者可在特定條件下，讓頁面模板解析 include 作用中主題目錄外可讀的本機 PHP，進一步造成**條件式 RCE**。前置條件包括：作用中主題含名稱以 `page-` 開頭的頂層目錄（如 `page-templates`；影響舊預設主題 Twenty Twelve／Fourteen，以及 Neve、Hestia、Sydney 等），且伺服器上存在可讀的本機 `.php` 目標（常見鏈：`pearcmd.php`＋`register_argc_argv=On`）。官方 GHSA 標 **Critical、CVSS 4.0 = 9.2**；修補已回植至仍接受資安更新的分支（目前至 **4.7**）。Patchstack 觀察到釋出當日數小時內即有探測，翌日升級至 pearcmd 寫檔嘗試；OpenCVE 標 **CISA KEV（9/25 入庫）**。下載頁現行版本為 **7.1.2**。

→ [原文](https://wordpress.org/news/2026/09/wordpress-7-1-2-release/) · [GHSA](https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-7hp8-65ch-5whp) · [Patchstack 探測追蹤](https://patchstack.com/articles/cve-2026-87902-attackers-started-probing-wordpress-sites-hours-after-the-patch/)

### WordPress Trac MCP server 公開上線
官方宣布公開、免費的 **WordPress Trac MCP server**（無需帳號／API key），可接 Claude、ChatGPT 或其他 MCP client，直接讀 tickets、changesets、timeline。工具含 `searchTickets`、`getTicket`、`getChangeset`、`getTimeline`、`getTracInfo`；預設 Core Trac，亦支援 Meta／Themes／Plugins 等。程式碼在 GitHub `WordPress/trac-mcp`。

→ [原文](https://make.wordpress.org/core/2026/09/24/wordpress-trac-mcp-server/)

### 7.2 起改採 livestreamed Zoom 釋出
經歷 State of the Word、WCUS 上現場直播釋出等實驗後，專案決定改為 **livestreamed Zoom webinar**：release squad 可遠端參與、觀眾可提問。此模式將用於接下來幾次釋出，**從 WordPress 7.2（early December 2026）開始**；報名細節接近釋出日再公布。

→ [原文](https://make.wordpress.org/core/2026/09/23/iterating-from-live-events-to-livestreamed-releases/)

### Contributor Toolkit 1.2：支援 Gutenberg 貢獻流程
**v1.2.0** 在既有 Core／Trac 工作流外，新增完整 **Gutenberg** 貢獻流程——建立 Gutenberg site、連結 GitHub issue、Apply 既有 PR、裝置碼登入後開 PR。適合 Contributor Day／新手與進階貢獻者。

→ [原文](https://make.wordpress.org/core/2026/09/25/wordpress-contributor-toolkit-1-2-one-app-for-your-first-core-or-gutenberg-contribution/)

---

## WooCommerce／電商

### WooCommerce 11.1.2：資安＋變體相簿修正
點修版已上架（**11.1.2**，約 9/22）。官方標 Security update；無需資料庫升級。重點含以 email 為基礎的商品評論驗證加嚴，以及主題在 template 呼叫 `get_available_variations` 時的無限遞迴回歸修正。所有 Woo 站建議再升到 **11.1.2**。

→ [原文](https://developer.woocommerce.com/2026/09/22/woocommerce-11-1-2/)

### WooCommerce 11.2.0 Pre-release（正式週約 10/6）
**11.2.0-beta.1** 開放測試；**Release week: 2026-10-06**。亮點含 Product CSV 可依 GTIN／UPC／EAN／ISBN 比對更新、Additional Checkout Fields 支援 date 欄、可設定的撤回通知信，以及多則區塊樣式與 Developer Advisories（含 PhotoSwipe、Store API cart-session、實驗性 API 移除等）。開發者請讀預釋出文並用 Beta Tester 測暫存站。

→ [原文](https://developer.woocommerce.com/2026/09/21/woocommerce-11-2-0-pre-release-notes/)

### Store API 新增購物車數量驗證 filter（11.2）
自 **11.2** 起 Store API（及 Cart／Checkout 區塊）新增 `woocommerce_store_api_cart_item_quantity_validation`，對應經典購物車的 `woocommerce_update_cart_validation`。須回傳 **`WP_Error`** 才會在區塊 UI 顯示錯誤；回傳 `false` 或 `wc_add_notice()` 無效。台灣客製限購／數量外掛若只掛舊 hook，區塊結帳可能被繞過。

→ [原文](https://developer.woocommerce.com/2026/09/24/new-cart-validation-filter/)

### RY Tools 更新至 2026.9.26：金流通知驗證
相對上期 **2026.9.16**，本週又兩版：**2026.9.22** 新增「已付款訂單付款失敗通知信」；**2026.9.26** 強化金流發送付款通知時的內容驗證。現行目錄版號 **2026.9.26**；使用 RY＋綠界／速買配等站建議升級。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

---

## 資安

### Core：頁面模板路徑遍歷 → 條件式 RCE（CVE-2026-87902）
細節見上方熱點。OpenCVE：CVSS v3.1 約 **8.1**、**KEV yes**。請升級至 **7.1.2**（或各分支對應資安版）；日誌可搜 `%2e%2e`／`pearcmd`／`pagename`+`page_id` 同現。

→ [OpenCVE](https://www.opencve.io/cve/CVE-2026-87902)

### Addify Request a Quote ≤2.9.2：未授權任意檔案上傳（CVE-2026-18143）
Addify **Request a Quote for WooCommerce** ≤**2.9.2**，公開報價 popup 流程缺少副檔名／MIME 驗證，未授權者可上傳可執行檔（CVSS **9.8 Critical**）。OpenCVE（9/26）稱暫無廠商修補；若有使用請停用該公開 popup、禁止上傳目錄執行 PHP，並追蹤＞2.9.2 修補版。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-18143)

---

## 台灣站務與活動

### 【9/29】WordPress 台北小聚 @ CollaPlay
講題「WebMCP：讓 AI 知道你的網站可以怎麼操作」（李小胖／哈拉設計）。**2026-09-29（二）19:00–22:00**，台北萬華 CollaPlay。含閃電講與交流。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316309009/)

---

## 在地工作室部落格

### 社群按鈕小幫手：LINE、蝦皮、街口都能放
金城事務所自製「社群按鈕小幫手」：內建 35 種按鈕（含 LINE／蝦皮／街口），手機可逐顆顯示或收合；並對照 My Sticky Elements、Chaty 等國外外掛在台灣情境的痛點。

→ [原文](https://iseeu.tw/mr-social/)

### 嵌入媒體小幫手：貼網址就變卡片
同站自製嵌入外掛：貼網址即可把 YouTube／Instagram／臉書／TikTok／Threads 轉成統一卡片，點了才載入播放器、封面存本站；並可背景改寫舊文既有嵌入碼。

→ [原文](https://iseeu.tw/mr-embed/)

---

## 主機商觀點

### AI agent 當網站運維操作者（Kinsta）
談 AI agent 如何接手 WP 維運（更新、staging、部署、除錯），並對照 Abilities API／MCP／主機層 API；強調分級權限與人工核准，非全面自治。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/ai-wordpress-operators/)

### 發現 AI bot 流量後的七個常見錯誤（Kinsta）
七個常見錯誤：把比例當診斷、一刀切擋所有 bot、把爬蟲尖峰當資安事件、只看量不看路徑、擋了 crawler 卻留 crawl trap、以為 robots.txt 會強制、抄別人防火牆規則等。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/managing-ai-bot-traffic/)

### GEO for agencies：把 AI 搜尋可見度產品化（WP Engine）
面向 agency 談 GEO／AEO 與 FAME 框架，以及如何把 AI 可見度做成稽核、retainers、內容服務；文中推自家生態 SiteSonar。廠商立場：WP Engine。

→ [原文](https://wpengine.com/blog/geo-for-agencies/)

---

## 影音精選

### WPBeginner：交給免費 ChatGPT 的 15 項 WP 任務
示範把常見 WordPress 工作交給免費 ChatGPT 帳號處理的實務清單；實作細節請自行驗證權限與外掛。

→ [影片](https://www.youtube.com/watch?v=y91cJwqIIxs)

### 犬哥：ChatGPT＋Codex＋WordPress 五點流程
談 ChatGPT／Codex 與 WordPress 協作的五點流程，偏 AI 建站與維護實作。

→ [影片](https://www.youtube.com/watch?v=1tHk9DMxrPk)

---

## 社群熱議

### Reddit｜Elementor 4.3.0／4.3.1 CSRF，請升 4.3.2
社群轉貼 Patchstack：Elementor Website Builder 4.3.0／4.3.1 有 CSRF；留言補 CVE-2026-62062，修補版 **4.3.2** 已出。台灣 Elementor 案量高，建議批次更新並檢查 WAF。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wpqz4s/csrf_vulnerability_in_elementor_430_and_431/)

### Reddit｜中國流量暴增：該不該擋？AI 爬蟲與主機負載
站長發現中國成最大流量來源、懷疑是 AI 抓站機器人，問怎麼擋、擋了會不會傷 SEO。留言熱議地理封鎖 vs 依 UA／行為擋爬蟲，以及整國封鎖的副作用。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wpp6p9/high_traffic_coming_from_china_should_i_block/)

### Reddit｜WordPress 可能是 AI 最難取代的平台之一
作者主張 AI 會寫程式，但 WP 既有外掛／主題／文件／除錯生態仍難一次取代；AI 更像加速「用 WP 建站」。本週最高互動串，留言兩極。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wpaggq/wordpress_may_be_one_of_the_hardest_platforms_for/)

### Reddit｜LiteSpeed Cache 加速 Woo 很猛，也是更新踩雷來源
維護者肯定 LSC 可把 Woo 手機 PageSpeed 推高，但錯設不會大喊失敗，而是購物車不刷新、訪客卡舊頁。舉例 staging 匯入後舊 transient 誤判、Git 合併 LSC 檔致整站 500。

→ [Reddit](https://www.reddit.com/r/woocommerce/comments/1wovcsx/litespeed_cache_is_the_best_thing_that_happened/)

### Reddit｜小功能該寫程式還是裝外掛？
作者近期砍外掛：導向、CPT、追蹤碼、小幅 Woo 調整能幾行搞定就不裝；複雜／常更新才選口碑外掛。留言圍繞維護債、資安面與客戶可自助程度。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wp6n9s/wordpress_developers_relying_on_plugins_for/)

### Reddit｜用 Claude 從零做主題／mu-plugins：該注意什麼資安？
作者改用 Claude 做自訂主題與 mu-plugins，效能自評優於 Elementor，但擔心 AI 產出程式的資安底線（權限、nonce、逃逸、檔案寫入等）。屬「AI 寫 WP 程式」實務提問串。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wrpy7e/safety_mesures_for_creating_websites_with_claude/)

---

## 職缺（近 7 日）

### 104｜WordPress 專員（赫爾墨思行銷）
台中北屯；月薪 40,000～43,500 元。負責外語專案 WordPress 內容維護、版面與外掛／佈景（Elementor、Bricks、Divi、Gutenberg），並配合 SEO（Meta、Schema、URL）與備份維運。（頁面更新日期 09/24）

→ [職缺](https://www.104.com.tw/job/93ati)

### 104｜WORDPRESS 網站管理行銷人員（炫煬國際）
新北中和；月薪 36,000 元以上。以 WordPress 架站、套版與 WooCommerce 等外掛維護官網及 LiteShop 商品上下架，並處理金物流串接與社群維運；註明無 WordPress 操作經驗勿應徵。（頁面更新日期 09/24）

→ [職缺](https://www.104.com.tw/job/879me)

### 104｜資深 WordPress 專員（赫爾墨思行銷）
台中北屯；月薪 42,000～46,000 元。負責外語專案 WordPress 建置與日常管理、速度與資安、SEO 架構，並加分 WooCommerce、PHP／JavaScript 與主機／DNS 經驗。（頁面更新日期 09/24）

→ [職缺](https://www.104.com.tw/job/93ass)

### 104｜網站暨數位影音設計師（歐尼克斯廣告）
台北信義；月薪 50,000 元以上。需能獨立用 WordPress 建置網站、產品頁與 Landing Page 並日常維護，同時用 AI 做圖文與短影音；面試要提供網站作品。（頁面更新日期 09/21）

→ [職缺](https://www.104.com.tw/job/8rs06)

### 1111｜WordPress 網站工程師・約聘 6 個月（美加文化）
台北中正；面議（經常性薪資達 4 萬元或以上）。約聘六個月，負責 WordPress 建置、維運與改版，需 HTML／CSS／JavaScript、Divi 或 Elementor、SEO 與主機除錯。（頁面日期 09/22）

→ [職缺](https://www.1111.com.tw/job/130456287)

### 1111｜網路行銷企劃及網站操作人員（川本國際包裝）
台中烏日；月薪 38,000～42,000 元。職稱要求熟悉 Wordpress，工作為產品／活動企劃、銷售頁、社群經營與基本美工、影音剪輯。（頁面日期 09/27）

→ [職缺](https://www.1111.com.tw/job/113017136)

### 1111｜電商網站設計師（唯高盛國際）
台北內湖；面議（經常性薪資達 4 萬元或以上）。以 Magento 為主做電商介面、Wireframe 與轉換優化；職位要求熟悉 Magento 或類似平台（含 WooCommerce），以及 HTML／CSS／JavaScript、Git。（頁面日期 09/25）

→ [職缺](https://www.1111.com.tw/job/132774909)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
