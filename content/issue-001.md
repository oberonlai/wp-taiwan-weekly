# WP 台灣週報 · 第 1 期

**出刊日：** 2026-09-06

本週重點：WordPress 7.1.1 維護版時程、WooCommerce 11.1、後台圖示現代化提案，台灣金物流與區塊結帳實務，並新增主機商觀點與影音精選。

---

## 本週熱點

### WordPress 7.1.1 維護版時程公布
7.1 釋出後回報量與嚴重度足以開一輪維護版：預計 9/10 RC1、**9/17 正式版**，範圍僅限 7.1 週期引入或刻意延後的 bug。三位 release lead：@adamsilverstein、@adrianduffell、@andraganescu。

→ [原文](https://make.wordpress.org/core/2026/09/02/wordpress-7-1-1-release-schedule/)

### Code Reference 可執行範例上線
文件頁上的程式片段可直接按 Run，在瀏覽器靠 Playground 跑起來。DocBlock 用 `php interactive` 標記；7.1 先上兩則，7.2 會再補。

→ [原文](https://make.wordpress.org/core/2026/09/04/runnable-code-examples-are-now-live-in-the-code-reference/)

### 提案：後台 Dashicons 改走 SVG
Make/Core 提出以 `wp_get_icon()` 與 SVG 取代 admin bar、側欄的 Dashicons 字型圖示，改善無障礙、高對比與字型載入失敗時的顯示。主題／外掛若硬依賴 Dashicons 字型，宜提早關注討論進度。

→ [原文](https://make.wordpress.org/core/2026/09/04/replacing-dashicons-in-the-admin-bar-and-menu/)

### Gutenberg 23.9：工具列可插入區塊、自訂樣式更好找
區塊工具列直接插新區塊；Global Styles 對有自訂覆寫的區塊加圓點標示，也可只篩自訂樣式。另有 Group 軸向 gap、更多元素可在 Global Styles 調色等。

→ [原文](https://make.wordpress.org/core/2026/09/02/whats-new-in-gutenberg-23-9/)

### 官方瀏覽器擴充功能
Chrome／Chromium 與 Safari（macOS）可裝：隱藏 admin bar、保留常用捷徑、辨識 WP 站、區塊外框／手機預覽／清快取等開發輔助。資料留在本機，無追蹤。

→ [原文](https://wordpress.org/news/2026/08/browser-extension/)

### Secrets API 提案瞄準 7.2
Eric Mann 提案：Core 要有一等公民的密鑰儲存（加密、WP-CLI、drop-in），UI 留到 7.3。背景是外掛長期把 API key 明文寫進 options，AI 整合讓外洩成本更高。

→ [原文](https://make.wordpress.org/core/2026/08/25/proposal-a-secrets-api-for-wordpress-7-2/)

---

## WooCommerce／電商

### WooCommerce 11.1.0 正式釋出
重點：變體圖庫變核心功能（Additional Variation Images 退役）、歐盟訂單撤回權（預設關閉）、虛擬商品後台不再顯示無用運送地址、Store API／REST 約快 30–42%、產品圖庫影片實驗功能。有資料庫更新，上線前請讀完整 changelog。

→ [原文](https://developer.woocommerce.com/2026/09/03/wc-11-1-release-notes/)

### 訂單撤回（Order Withdrawal）怎麼用
啟用後顧客可於下單 14 日內自助送出撤回申請；商家收 email／inbox，**不會自動取消或退款**，流程仍由店家人工處理。有助歐盟相關需求，但不保證合規。

→ [原文](https://developer.woocommerce.com/2026/08/27/order-withdrawal-woocommerce-11-1/)

### 告別 magic string：Woo 核心 Enum 常數類
訂單狀態、產品類型等改以 `Automattic\WooCommerce\Enums\*` 公開常數收斂；因 PHP 7.4 下限與既有字串契約，不用 PHP native enum。擴充請對齊最低 Woo 版本，並留意 `completed` 與 `wc-completed` 差異。

→ [原文](https://developer.woocommerce.com/2026/09/02/from-magic-strings-to-enum/)

### Meta Box 5.15：正式支援 Woo HPOS
訂單自訂欄位可寫進 `wc_orders_meta`，與 High-Performance Order Storage 對齊。台灣電商若已開 HPOS、又用 Meta Box 擴訂單欄位，這版值得測。

→ [外掛目錄](https://wordpress.org/plugins/meta-box/) · [官方說明](https://metabox.io/hpos-support/)

### RY Tools 更新至 2026.8.30
台灣站常用免費金物流外掛（綠界／藍新／速買配等）。最低需求 PHP 8.2、WP 6.8、Woo 9.0；已測 WP 至 7.1。近期 changelog 含強化 SmilePay 物流狀態安全性、修綠界 ATM 無法結帳與區塊結帳狀態辨識、不再支援內建測試 API 金鑰等。啟用安裝數 5,000+。

→ [外掛目錄](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### 區塊結帳時代：舊 filter 可能整段失效
Oberon Lai（WP 開發日常）說明：Woo 前台短代碼／PHP 範本與區塊＋Store API 兩套並存，新開店多走區塊；`woocommerce_checkout_fields` 等舊 filter 在區塊結帳頁無效，擴充要改走註冊欄位／金流整合／Store API。另文拆解 product-collection、cart、checkout 子區塊與 lock 行為。

→ [Block 時代](https://oberonlai.blog/woocommerce-block/) · [區塊初探](https://oberonlai.blog/woocommerce-product-cart-checkout-block/)

### 綠界官方模組：支援版本與最新 Release
目前支援矩陣為 **WP 6.8.3／Woo 10.4.3／PHP 8.2**。GitHub 最新 Release **1.1.2606090**：中華郵政運送描述、發票載具更名、開立金額反映退款／其他費用等；舊分拆模組已下架勿並存。金流／物流／電子發票各有一組 Merchant ID、Hash Key、Hash IV。

→ [綠界開發者文件](https://developers.ecpay.com.tw/62167/) · [Release 1.1.2606090](https://github.com/ECPay/Woocommerce_ECPAY/releases/tag/1.1.2606090)

### 藍新官方外掛 v1.0.12
Newebpay Payment 目前 **1.0.12**：曾加 Apple Pay、智慧 ATM 2.0、TWQR；近期修超商取貨不付款參數等。目錄標示未在最新 3 個 WP 主要版本測試——若站上 WP／Woo 已較新，升級前請完整測付款回傳。

→ [外掛目錄](https://tw.wordpress.org/plugins/newebpay-payment/)

---

## 外掛與 AI

### 官方 AI 外掛 1.3.0
新增內容翻譯、Slug 建議、Custom Abilities 開關；金鑰加密實驗、Request Logging、Connector Approvals 等持續推進。需另裝 AI Connector 並在 Settings → Connectors 設定。僅支援區塊編輯器。

→ [外掛目錄](https://wordpress.org/plugins/ai/)

### MCP Adapter
把 Abilities API 接到 Model Context Protocol，讓 MCP 客戶端能發現並呼叫 WordPress abilities。

→ [GitHub](https://github.com/WordPress/mcp-adapter)

### WordPress 簽署 Open Weights 公開信
專案簽署公開信，籲請勿過早限制可下載、檢查、修改並自架的開源權重模型；論點對齊開源精神。

→ [原文](https://wordpress.org/news/2026/08/open-weight/)

---

## 資安

### Core Security Initiative
因 AI 輔助研究讓回報量大增，安全團隊啟動 ABC：更好的安全釋出流程、清 backlog、用 AI 主動掃漏洞。漏洞請走官方 HackerOne。

→ [原文](https://make.wordpress.org/security/2026/08/28/the-core-security-initiative/)

### VDP 通報準則更新
Core／Gutenberg 以外資產：僅管理員可授與的角色問題，除非能證明顯著升級攻擊，否則多半不再受理。鼓勵聚焦未登入或低權限高影響問題。

→ [原文](https://make.wordpress.org/security/2026/09/01/updates-to-the-wordpress-vulnerability-disclosure-program/)

---

## 台灣站務與活動

### 【9/15】WordPress 彩虹小聚｜有 AI 當隊友，英文為什麼還是不敢開口
台北 CollaPlay，18:30–21:20，免費。主題偏 AI 輔助英文學習系統與 Prompt，含閃電講與交流。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/315900542/)

### 【9/20】WordPress 新手工作坊 @ 雷神昇彩創客基地
13:00–17:00，台北萬華漢中街；李小胖 WordPress 101；場地費 **300 元**。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316278822/)

### 【9/29】WordPress 台北小聚 @ CollaPlay
講題「WebMCP：讓 AI 知道你的網站可以怎麼操作」。19:00–22:00。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316309009/)

### WordCamp Asia 2027 · 檳城
2027/4/9–11，Penang Waterfront Convention Centre。講者徵集約至 2026-11-08。Roadmap 顯示 9/8 志工徵求、9/15 第一波售票即將開放。

→ [官網](https://asia.wordcamp.org/2027/) · [Roadmap](https://asia.wordcamp.org/2027/roadmap/)

---

## 在地工作室部落格

### Weebly 退出台灣：9/27 網站下架前怎麼搬到 WordPress
鵠崙設計整理 Weebly 停止台灣服務的時間軸：6/29 起無法新增頁面、**9/27 網站下架**、**12/26 帳號關閉**。文中提醒下架與帳號關閉是兩回事，並建議趁期限前備份、做 301 轉址保住 SEO，以及把網域移出 Weebly。

→ [原文](https://www.design-hu.com/web-news/weebly-taiwan-shutdown.html)

### 2026 台灣主流電商金流怎麼選
同站另一篇比較綠界、藍新、行動支付與 PayPal 等選項，並談費率、結款週期、超商取貨付款，以及 WooCommerce 用官方外掛串接的實作提醒。

→ [原文](https://www.design-hu.com/web-news/taiwan-payment-gateway-comparison-2026.html)

### AI 搜尋優化懶人包：AIO、GEO、AI 摘要
金城事務所把站上 AI 搜尋相關文整理成導覽：釐清 SEO／AEO／AIO／GEO，並說明 Google AI 摘要時代「曝光在、點擊掉」的因應，例如把前段寫成可獨立引用的答案、搭配 FAQPage schema。

→ [原文](https://iseeu.tw/ai-search-optimization-guide/)

### 阿腸 AI 寫手上線
阿腸網頁設計介紹自有「阿腸 AI 寫手」外掛：在文章編輯區輸入主題或關鍵字，可生成草稿、大綱與段落，並可串 OpenAI 或 Gemini API；文中說明全代管／寫手專案客戶目前可免費使用。

→ [原文](https://achang.tw/achang-ai-writer/)

### AI 架站 vs 專業設計怎麼選
Yao Design Studio 比較 AI 架站與專業設計適合的情境，供企業評估網站服務方向。

→ [原文](https://yao-studio.com/howtochoosewobuildyourwebsite/)


---

## 主機商觀點

### WordPress 7.1 功能總覽（Kinsta）
主機商整理 7.1 重點：客戶端媒體處理（瀏覽器端縮圖／轉檔、減輕伺服器 GD／Imagick 負擔）、Notes 協作、後台與 Site Editor UI、Playlist／Tabs 區塊、SVG Icon API，以及 Abilities API 強化。並提醒客戶端媒體處理目前主要在 Chromium 系支援，可用 `wp_client_side_media_processing_enabled` 關閉。功能細節請以官方釋出為準。

→ [原文](https://kinsta.com/blog/wordpress-7-1/)

### AI 功能愈多，基礎設施壓力愈大（Kinsta）
說明聊天機器人、編輯器 AI、外部 agent 等動態請求會佔 PHP thread、繞過快取並增加資料庫與外部 API 延遲。文中給出上線前檢查清單：請求路徑、可快取範圍、API 逾時與失敗行為、staging／APM、PHP 8.x。適合已裝 AI 外掛的台灣站對照自家主機負載。

→ [原文](https://kinsta.com/blog/wordpress-ai-infrastructure/)

### HTML 過重，AI 搜尋更難引用（Rocket.net）
指出頁面 HTML 膨脹會讓 AI 爬蟲難抽出正文，提出 Signal-to-Markup Ratio 概念，並對比 page builder 易產生多層 wrapper。實作建議含合併重複導覽、把追蹤腳本移出 inline HTML。與本週 GEO／AI 搜尋話題可對讀。

→ [原文](https://rocket.net/blog/why-bloated-html-is-hurting-your-ai-search/)

### 加密環境變數：密鑰別再塞 wp-config（Pressable）
Pressable 八月更新新增 Security → Environment Variables：可在面板儲存加密 API key／憑證，儀表板遮罩顯示，不必改 `wp-config.php`。即使不用該平台，也可提醒改用主機環境變數或伺服器 Secrets，降低外掛漏洞外洩金鑰風險。

→ [原文](https://pressable.com/blog/august-product-update-a-new-activity-hub-smarter-portfolio-management-and-safer-site-setup/)

### AI Hosting：基礎設施 vs 智慧（WP Engine）
釐清「AI 主機」兩層：站點級（向量庫、語意搜尋、MCP 等）與平台級（異常偵測、邊緣 bot／AI crawler 過濾）。強調只裝 AI 外掛不等於 AI-ready，並提供評估清單。文後有自家產品對照，請標廠商立場閱讀。

→ [原文](https://wpengine.com/blog/ai-hosting/)

---

## 影音精選

### 官方｜WordPress 7.1 Highlights
約一分鐘帶過 7.1 主打體驗：常駐管理列、響應式樣式、媒體編輯器、Notes，以及 Playlist／Tabs 等。適合當版本亮點入口；功能與時程請以官方文字為準。

→ [YouTube](https://www.youtube.com/watch?v=dO751c8jkn0)

### 犬哥｜WordPress 電商：上架到金物流設定
台灣實作長片，拆解自架電商流程：主機網域、主題、商品上架，到金流／物流設定與下單驗證。可當 Woo 欄延伸教學；金物流參數仍請對照綠界／藍新／外掛目錄。

→ [YouTube](https://www.youtube.com/watch?v=AInRAtvM5o4)

### 犬哥｜Claude＋Relume MCP＋WordPress 從零建站
示範用 Claude 與 Relume MCP 產架構／內容，再落到可上線的 WordPress。屬第三方 AI 工作流教學，非 Core 官方釋出。

→ [YouTube](https://www.youtube.com/watch?v=Ht0cmidv4a4)

### WPBeginner｜用 Claude 連上並管理 WordPress
英文教學示範透過 MCP／自訂 connector（如 WP Vibe）把站接到 Claude。第三方工具鏈，權限與資安請自行核對外掛文件。

→ [YouTube](https://www.youtube.com/watch?v=0nXGidFA1ck)

### 犬哥｜做 GEO 不能跳過 SEO
近週策略向長片：主張 GEO 仍需 SEO 基礎（收錄、爬取、結構），並談商業頁與知識庫的雙軌布局。可與本週 AI 搜尋／GEO 文對讀。

→ [YouTube](https://www.youtube.com/watch?v=Dqm2WejqIU0)

---

## 社群熱議

### Reddit · r/WordPress｜客戶站上的瘋狂 SQL injection payload
約 64 讚／18 則留言。作者回報與 WP2Shell 相關的每日注入嘗試、超長 payload；改表前綴等防禦似乎躲過感染，卻因畸形查詢陷入逾時。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1w7ieu4/the_crazy_sql_injection_payloads_i_found_on_one/)

### Reddit · r/WordPress｜你怎麼把 WordPress 接到 Claude？
約 12 讚／108 則留言。VPS 站長問如何用 Claude（尤其 CLI）協助寫稿與改稿；留言量很高，是本週 AI＋WordPress 工作流熱點。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1w7qnen/how_have_you_connected_your_wordpess_to_claude/)

### Reddit · r/WordPress｜專業開發者到底怎麼高效做客戶站？
約 16 讚／58 則留言。作者評估 Kadence Blocks，問代理商真實流程：客製是不是真的在 Gutenberg 一節一節調？討論焦點是區塊建構器、程式產出與客戶可編輯之間的取捨。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1w71qah/how_do_professional_wordpress_developers_build/)

### Reddit · r/WordPress｜沒技術經驗的人，哪個 builder 最直覺？
約 4 讚／29 則留言。專案要客戶自己做多個 landing page；作者習慣寫 code、只用過 Elementor，問哪個區塊建構器對非技術使用者最友善。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1w7wg6f/best_design_builder_for_people_without_experience/)

### Reddit · r/woocommerce｜中大型店想換 premium 託管
約 8 讚／25 則留言。三個中大型 Woo 站想離開現有主機商，預算約 USD 200／月，找支援與資安更穩的 managed 方案。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1w786mp/premium_hosting_providers/)

### Reddit · r/woocommerce｜新版 Woo 更新後觸控捲動壞掉
約 6 讚／5 則留言。最新 Woo 更新後所有 Woo 相關頁觸控捲動失效（Betheme），關外掛／自訂碼仍如此。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1w7ftg8/new_woo_update_breaks_touch_scrolling_on_my/)

---

## Jobs

### 後端工程師｜千陽號旅行社｜台北市中山區
- **更新：** 2026-09-05
- **待遇：** 月薪 40,000–55,000
- **重點：** WordPress 架站／維運、WooCommerce 與金物流、Divi／Elementor、AI／AIGC

→ [職缺頁](https://www.yes123.com.tw/wk_index/job.asp?job_id=20250829112827_20553648&p_id=c20250828165840_7341729)

### 工讀／兼職 · 網站後台維護｜怡盟國際｜台北市
- **更新：** 2026-09-06
- **待遇：** 月薪 32,000–35,000
- **重點：** 需會操作 WordPress；內容維護、後台 SEO、產品資料

→ [職缺頁](https://www.yes123.com.tw/wk_index/job.asp?p_id=20250708142045_75521071&job_id=20230607141456_4686654)

### 網頁企劃人員｜帝光開發｜台北市
- **更新：** 2026-09-04
- **待遇：** 月薪 30,000 元以上
- **重點：** 略懂 WordPress 前／後端、外掛、資料庫；企業官網規劃與行銷

→ [職缺頁](https://www.yes123.com.tw/wk_index/job.asp?p_id=20150730134347_22154823&job_id=20200723114217_3618176)

---

## 快速筆記

- WordPress 7.1「Mary Lou」已於 2026-08-19 正式釋出；站長可開始排 7.1.1（9/17）維護窗。
- Woo 11.1 若有自訂區塊註冊在 REST／Store API 路徑，請查 `woocommerce_should_register_blocks` 過濾器。
- 綠界 callback、金流／物流雙組 Key、撥款對帳實務可參考：[MO Studio](https://mo-studio.org/blog/wordpress-woocommerce-ecpay)。
- 綠界／藍新上線檢查清單：[PageLab](https://makewebsites.tw/posts/ecommerce-payment-gateway/)。
- WordCamp US 2026 官方回顧：[原文](https://wordpress.org/news/2026/08/wcus-2026-recap/)。
- Composer／Bedrock 部署到管理型主機（wp-content 結構）可參考：[WP Engine 指南](https://wpengine.com/blog/deploying-composer-based-site/)。

— WP 台灣週報編輯
