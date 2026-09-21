# WP 台灣週報 · 第 4 期

**出刊日：** 2026-09-21

本週重點：WordPress **7.1.1** 正式釋出（維護＋資安，含 Core `wpautop()` XSS 等）、**7.2 Roadmap** 與新預設主題 **Ipsum**、Gutenberg **24.0**；Woo 升至 **11.1.1** 並開放區塊主題 **Purple** beta，台灣 **RY Tools** 推至 2026.9.16；另有 Gravity Forms 關鍵檔案上傳漏洞與社群熱議的購物車濫用／快取踩雷。

---

## 本週熱點

### WordPress 7.1.1 正式釋出（維護＋資安）
上期報的 RC1 已於 **9/17** 落地為正式版。短週期**維護＋資安**釋出：約 **17** 則 Core bugfix、**19** 則區塊編輯器修補、**11** 則資安修補。官方建議立即更新（有自動背景更新的站會自動開始）。下載頁現行版本為 **7.1.1**；未另出 RC2。下一主版本為 **7.2**（規劃 12 月初）。資安修補會視需要回移植至仍接受資安更新的分支；僅最新版獲積極支援。

→ [原文](https://wordpress.org/news/2026/09/wordpress-7-1-1-maintenance-and-security-release/) · [下載](https://wordpress.org/download/)

### Roadmap to 7.2：Notes、Secrets API、Ipsum
Make/Core 公布 **7.2** 產品方向（約 **2026 年 12 月初**）：Notes 建議模式與 emoji 反應、sudo mode／**Secrets API**／Application Passwords 強化、Global Styles 與 Description List／ToC 區塊、Omnibar 與可擴充 Site Editor、DataViews／DataForms 等。預設主題改為 **Ipsum**。AI 持續在官方 AI plugin 推進，**不保證進 Core**；即時協作（RTC）**刻意不列** 7.2 roadmap。項目為積極推進目標，不保證全部進最終版。

→ [原文](https://make.wordpress.org/core/2026/09/18/roadmap-to-7-2/)

### 新預設主題 Ipsum：告別年份命名
**Ipsum** 擬隨 **7.2** 出貨：刻意極簡的部落格「空白畫布」，命名取自 lorem ipsum。依社群方向**告別年份命名**（不再 Twenty Twenty-X）。先前較表達性的 Mētis 不會隨本版出貨。設計可測、開發審查進行中；Design Lead Henrique Iamarino。

→ [原文](https://make.wordpress.org/core/2026/09/16/introducing-ipsum-the-new-default-theme/)

### Gutenberg 24.0：視覺修訂含標題、Gallery Grid、Site Title fit-text
雙週版 **24.0**（9/16）：視覺修訂涵蓋文章標題（含 diff）、Gallery 新增 Grid variation（欄數／裁切可依 viewport）、Site Title 支援 fit-text。另有圖示 stroke 風格、List Tab 縮排、背景圖自 URL 設定等。後續 DataForm 編輯器 inspector 測試需 Gutenberg **24.0+**。

→ [原文](https://make.wordpress.org/core/2026/09/16/whats-new-in-gutenberg-24-0/)

---

## WooCommerce／電商

### WooCommerce 11.1.1：資安強化＋Mini-Cart 樣式修正
點修版已上架（**11.1.1**，約 9/18）。官方標 Security update；無需資料庫升級。重點含 REST API 金鑰驗證加嚴、舊版 options／行動 App 登入與訪客 session 驗證強化，以及 Mini-Cart 被可見性規則隱藏時不再以未樣式狀態出現在 footer。上期已報需 ≥11.1.0 的 DoS 修補；本週建議再升到 **11.1.1**。

→ [原文](https://developer.woocommerce.com/2026/09/18/woocommerce-11-1-1/)

### 徵測 Purple：Woo 第一套正式區塊主題（beta）
官方重啟並開放 **Purple** beta：可從 GitHub `woocommerce/woo-themes` 安裝，或看預覽站。內建多數 Woo 區塊頁模板、色盤／字型搭配與電商 pattern。初輪回饋後計畫上主題目錄與 Marketplace。測試請用最新 WP＋Woo、暫存／本機；GitHub 直裝不會自動更新。

→ [原文](https://developer.woocommerce.com/2026/09/15/purple/) · [區塊主題直播（9/30 PDT）](https://developer.woocommerce.com/2026/09/17/block-theme-event/)

### RY Tools 更新至 2026.9.16：綠界宅配物流單
台灣常用金物流整合外掛自 2026.9.10 再推：**2026.9.16** 修正「綠界物流宅配物流單無法順利產生」。需求仍為 PHP **8.2+**、WP **6.8+**、Woo **9.0+**；已測至 WP **7.1.1**。使用綠界宅配列印／產單的站應優先更新。藍新／綠界官方購物車外掛目錄本週無新版號。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### 實驗：在 Woo 上跑 Claude Commerce Agent
Woo 團隊在 Agentic Tools repo 放上 Claude Commerce Agent 參考實作（**非正式維護**）。買家端可搜尋／比價／組車再交回商店原本 Checkout；商家端建議需人工 Approve，不會自行下單收款。對象為評估 AI 工作流的開發者；須自架服務並負擔 Claude 費用。

→ [原文](https://developer.woocommerce.com/2026/09/16/wc-claude-commerce-agent/)

---

## 資安

### Core：`wpautop()` 未授權 Stored XSS（CVE-2026-93485）
隨 **7.1.1** 修補。OpenCVE／Patchstack：**CVE-2026-93485**（CVSS 約 **7.1 High**）。`wpautop()` 正則未正確處理屬性內引號／`>`，可經留言造成未授權 Stored XSS（預設首次留言需審核，但審核非安全控制）。影響 7.1（&lt;7.1.1）並回溯多個分支。請升級至 **7.1.1**（或各分支對應資安版）。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-93485) · [Patchstack 分析](https://patchstack.com/articles/wordpress-7-1-1-maintenance-and-security-release/)

### Gravity Forms ≤3.1.0.4：未授權任意檔案上傳可至 RCE（CVE-2026-84434）
**CVE-2026-84434**（CVSS **9.8 Critical**）。公開表單若含 Visibility 設為 **Hidden** 的 File Upload 欄位，未登入攻擊者可繞過副檔名驗證上傳可執行檔，進一步達成 RCE。請升級至 **3.1.0.5+**；若暫無法升級，先移除或停用隱藏的上傳欄位。

→ [OpenCVE](https://app.opencve.io/cve/CVE-2026-84434) · [changelog](https://docs.gravityforms.com/gravityforms-change-log/)

### Forminator ≤1.57.2：未授權 Broken Access Control（CVE-2026-92229）
Patchstack：**CVE-2026-92229**（CVSS 約 **9.1**）。請升級至 **1.57.3+**。

→ [Patchstack](https://patchstack.com/database/wordpress/plugin/forminator/vulnerability/wordpress-forminator-forms-plugin-1-57-2-unauthenticated-arbitrary-shortcode-execution-via-current-url-parameter-vulnerability)

---

## 台灣站務與活動

### 【9/29】WordPress 台北小聚 @ CollaPlay
講題「WebMCP：讓 AI 知道你的網站可以怎麼操作」（李小胖／哈拉設計）。**2026-09-29（二）19:00–22:00**，台北萬華 CollaPlay。含閃電講與交流。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316309009/)

---

## 在地工作室部落格

### 舊文章整理：從盤點到驗收
金城事務所把舊文整理拆成盤點數據 → 判斷優先序 → 改寫／合併／刪除 → 驗收，強調先用數據篩篇，而不是整站重寫。

→ [原文](https://iseeu.tw/content-audit-guide/)

### 舊文改寫六模組模板
同站把改寫模板化（開頭結論、痛點問句、中段問答、適合誰等），目標短時間完成且利於 AI 摘要引用。

→ [原文](https://iseeu.tw/rewrite-template/)

---

## 主機商觀點

### 為 AI agent 設計站，不只給真人看（Kinsta）
談 Abilities API／MCP Adapter、權限最小化、可預期回應與觀測性，以及主機層 API／MCP。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/wordpress-for-ai-agents/)

### AI 搜尋可見度 vs 爬蟲打掛主機（Kinsta）
區分搜尋／Agent／訓練爬蟲，主張依「回報價值」與「昂貴路徑」（搜尋結果頁、加購 URL 等）做分層防護，而非一刀切封鎖。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/ai-search-bot-traffic/)

---

## 影音精選

### WPBeginner：請立刻更新到 7.1.1
短片提醒安全更新；版本與修補清單仍以官方 News 為準。

→ [影片](https://www.youtube.com/watch?v=0yJbEI3g--Y)

### WPBeginner：AI 能否在 WP 站建表單
示範／討論 AI 建表單流程；實作細節請自行驗證外掛與權限。

→ [影片](https://www.youtube.com/watch?v=UOcM7v34MlQ)

---

## 社群熱議

### Reddit｜Gravity Forms 嚴重漏洞，請立刻更新
社群轉貼 CVSS 9.8 任意上傳風險，留言多催有裝就更新；細節見上方資安欄一手 CVE。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wln8p5/major_vulnerability_98_cvss_in_gravity_forms/)

### Reddit｜裝了資安外掛仍被駭：可疑帳號＋本機惡意擴充
站長清站後追查指向本機 Chrome 惡意擴充可能竊登入態；提醒端點與瀏覽器擴充也是攻擊面。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wj04yy/wordpress_hacked/)

### Reddit｜用 AI 消化 Wordfence 報告、自動向主機商檢舉機器人
把攻擊報告丟給 AI 起草 abuse 檢舉；討論 Cloudflare 擋不住時的維運自動化。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wixvmb/wordfence_ai_and_the_bot_fight_back/)

### Reddit｜Woo 購物車濫用：一小時七千次 add-to-cart
分散近七千 IP 狂打 `?add-to-cart=`，導致逾時；台灣促銷檔期／共用主機店家相關。

→ [Reddit](https://www.reddit.com/r/woocommerce/comments/1wlkxdo/have_you_seen_this_specific_woocommerce_cartabuse/)

### Reddit｜快取外掛反送 `Cache-Control: no-cache`，Meta 爬蟲吃掉六成流量
NitroPack 無條件送 no-cache，邊緣快取失效；提醒查 response header 與爬蟲流量。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wjs61o/my_caching_plugin_was_sending_cachecontrol/)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
