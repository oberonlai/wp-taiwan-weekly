# WP 台灣週報 · 第 13 期

**出刊日：** 2026-11-19

本週重點：WordPress 7.1.5 安全版推出，一次修補 20 個問題，其中一項是不需登入、可能導致遠端執行程式的權限提升，請盡快更新。NGINX 11/18 修補兩個 HTTP/2 嚴重漏洞，和上週的 Apache 一樣得靠主機商處理。WordPress 7.2 RC1 在 11/17 如期推出，正式版仍是 12/8；官方也推出社群維護的 SMTP 外掛，並預告 jQuery 4 的升級時程。活動方面，桃園（11/21）與台北（11/24）各有一場小聚。

---

## 本週熱點

### WordPress 7.1.5 安全版：請盡快更新

WordPress 7.1.5 於 11/18 推出，官方說明這是安全釋出，建議立即更新；開啟自動背景更新的網站會自動升級。這次共修補 20 個問題，值得留意的有：

- **最嚴重**：未登入的訪客可能透過「範本部件」區塊的屬性提升權限，進而導致遠端執行程式。
- **資料庫**：核心的 `WP_Meta_Query`（巢狀 relation 值）與外觀「自訂」功能的首頁設定預覽，各有一個 SQL 注入問題。
- **多站台**：站台管理員可透過外觀自訂的選單預覽，接管網路管理員（Super Admin）帳號。
- **資訊外洩**：未登入者可經 REST API 搜尋端點看到私有分類項目；作者以上的角色可經留言端點讀到用戶 Email；訂閱者可經 XML-RPC 讀到留言。
- **XSS（跨站腳本）**：多處，包括未登入者透過留言短代碼屬性的儲存型 XSS，以及舊版媒體庫彈窗的反射型 XSS。
- **服務中斷**：批次 XML-RPC pingback 可造成未登入者發動阻斷服務。

建議動作：到「儀表板 → 更新」確認已升到 7.1.5；有 staging、客戶站台的也逐一確認。7.2 仍預定 12/8 推出。

→ [原文（WordPress 7.1.5）](https://wordpress.org/news/2026/11/wordpress-7-1-5-release/)

### NGINX 兩個 HTTP/2 嚴重漏洞：同樣要靠主機修補

NGINX 11/18 釋出 1.30.7（穩定版）與 1.31.9（主線版），修補 CVE-2026-123007（HTTP/2 流量控制的記憶體損毀，官方列為嚴重）與 CVE-2026-123633（HTTP/2 的 use-after-free），兩者都可能導致遠端執行程式，而且伺服器要接受 HTTP/2 才會中招。Wordfence 指出，已有研究團隊公開概念驗證，CERT-EU 11/18 時回報尚無已知的實際攻擊。

和上週 Apache 的漏洞一樣，這是網頁伺服器層的問題，更新 WordPress、外掛或安全外掛都修不到；網站前面有 CDN 提供 HTTP/2，也不代表後端的 NGINX 沒有開。

建議動作：共享或代管主機的使用者，留意主機商公告，或直接詢問「NGINX 是否已升級到修補版並重啟、有沒有接受 HTTP/2？」自己管主機的，安裝 1.30.7／1.31.9 或系統廠商的修補套件並重啟；暫時無法更新時，可先從 `listen` 設定移除 `http2`。

→ [原文（NGINX 安全公告）](https://nginx.org/en/security_advisories.html)
→ [原文（Wordfence 說明）](https://www.wordfence.com/blog/2026/11/nginx-http2-vulnerabilities-wordpress/)

### WordPress 7.2 RC1：正式版仍是 12/8

7.2 RC1 於 11/17 推出，進入「只修錯、不加功能」的階段，官方請外掛與佈景主題作者趁現在用 RC1 測試。這一版收尾的項目包括：客戶端媒體上傳的修正、「隨機文章」跳轉功能的改善、後台工具列新增鍵盤用的跳至內容連結、外掛相依性訊息、Interactivity API 的穩定度，並更新內建的 PHPMailer、Requests 等函式庫。

開發者另外要注意 Make Core 本週的 7.2 說明：

- **REST API**：修訂版與自動儲存的回應不再包含未做版本控管的 meta；留言端點的 `status` 參數改為陣列，原本拿字串比對的程式要改。
- **媒體**：媒體庫格狀頁、「新增媒體檔案」頁與媒體彈窗的圖片上傳，也改走瀏覽器端處理流程；動態 GIF／WebP 的縮圖會保留動畫；`wp_start_cross_origin_isolation_output_buffer()` 與 `wp_add_crossorigin_attributes()` 已棄用。
- **jQuery**：7.2 內建 jQuery Migrate 3.6.0，讓外掛先看到棄用警告；預計 7.3 才升級到 jQuery 4，2027 年底（7.5 或 7.6）才會預設停用 Migrate，時程仍取決於測試結果。

→ [原文（7.2 RC1）](https://wordpress.org/news/2026/11/wordpress-7-2-release-candidate-1/)
→ [原文（REST API 變更）](https://make.wordpress.org/core/2026/11/17/rest-api-changes-in-wordpress-7-2/)
→ [原文（媒體變更）](https://make.wordpress.org/core/2026/11/17/media-changes-in-wordpress-7-2/)
→ [原文（jQuery 4 準備）](https://make.wordpress.org/core/2026/11/17/preparing-for-jquery-4-with-wordpress-7-2/)

### 官方推出社群維護的 SMTP 外掛

外掛團隊 11/16 發表新外掛 SMTP，上架於外掛目錄，只負責設定 WordPress 內建的寄信機制，不做其他事。可設定任何 SMTP 主機、連接埠、加密與寄件地址；建議在 `wp-config.php` 用常數設定；在 7.2 以上，密碼會存進新的 Secrets API 加密保存。另有寄信紀錄（只記收件人、主旨、狀態與錯誤，不存內文與附件，預設 30 天清除），並可在寄信失敗時用寄信以外的管道通知。官方說明，不少主機會停用 `mail()` 或封鎖 25 埠，導致密碼重設信默默寄不出去，這個外掛是為此準備的中立選項。

→ [原文](https://make.wordpress.org/plugins/2026/11/16/introducing-smtp-a-community-maintained-way-to-send-wordpress-email-reliably/)

---

## 台灣站務與活動

### 桃園小聚 #63：有了 AI，大家駭起來（11/21 週六 14:00）

Taoyuan WordPress Meetup 舉辦，Meetup 頁面顯示目前已報名 7 人、剩 1 個名額。

→ [活動頁（Meetup 搜尋結果）](https://www.meetup.com/find/?keywords=wordpress&location=tw--Taiwan&source=EVENTS)

### 台北彩虹小聚：用 AI 做小遊戲，讓人人都能參與社會議題（11/24 週二 18:30）

Taipei WordPress Meetup 舉辦，Meetup 頁面顯示已有 24 人報名。

→ [活動頁（Meetup 搜尋結果）](https://www.meetup.com/find/?keywords=wordpress&location=tw--Taiwan&source=EVENTS)

---

## 在地工作室與教學部落格

### 犬哥網站：AI-Ready Website 是什麼？網站要怎麼讓 AI 讀得懂

犬哥 11/13 的文章把「AI 就緒網站」定義成「讀得到、讀得懂、信得過」：AI 爬蟲不能被 robots.txt、防火牆或 CDN 擋住；服務、營業時間等重點要寫成 HTML 文字而不是藏在圖片裡；公司名稱、地址、電話要在官網與 Google 商家一致，並有作者與更新日期。文章也提醒，Google 文件說出現在 AI 摘要沒有額外要求，SEO 基本功仍是前提，AI-Ready 不等於保證被推薦；結尾附有 8 項自我檢測與 5 個優化步驟。

→ [原文](https://frankknow.com/what-is-ai-ready-website/)

---

## 主機商觀點

### Kinsta：Lighthouse 98 分，不代表結帳頁沒問題（廠商立場）

Kinsta 11/17 的文章以一個 WooCommerce 結帳卡住的案例說明：首頁快取後 Lighthouse 分數很高，但顧客填送貨地址時結帳頁卻停住，因為問題出在登入後才會跑的流程與後台排程，單看首頁分數查不到。文章主張代理商需要看到站台實際執行了什麼、請求把時間花在哪裡，並提醒不要把 Lighthouse 實驗室分數與 PageSpeed 的真實使用者數據混為一談。這是主機商的觀點文章，立場是推廣其監控與除錯能力。

→ [原文](https://kinsta.com/blog/operational-visibility-vs-performance-scores/)

---

## 社群熱議

### r/woocommerce：客戶想把自己的 AI 代理連到你做的商店，你怎麼管？

一位 Woo 接案者發問：有了 MCP、WordPress Abilities API 與能讓 ChatGPT、Claude 連上商店的外掛，客戶可以自己讓 AI 代理建立商品、改訂單、改設定，一個錯誤指令幾秒內就能改掉 500 個商品。他想知道大家有沒有把「AI 存取」和後台權限分開看待、是自己幫客戶設定專用使用者與受限角色（或應用程式密碼），還是交給客戶；維護合約有沒有寫明代理弄壞東西由誰修；有沒有客戶沒說就自己連上去。他的結論是多數人還沒有相關政策。貼文發出約 20 小時，已有 20 多則留言。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xzaejd/do_you_let_your_woocommerce_clients_connect_their/)

---

## 職缺（近 7 日）

### 104｜WordPress 專員（赫爾墨思行銷）

台中北屯；月薪 40,000～43,500 元。負責外語專案的 WordPress 網站內容維護、外掛與佈景主題設定測試、SEO 優化與備份；需 1 年以上 WordPress 建置或維運經驗，熟悉 Elementor、Bricks、Divi 或 Gutenberg 其中一種。（更新日期 11/19）

→ [職缺](https://www.104.com.tw/job/93ati)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
