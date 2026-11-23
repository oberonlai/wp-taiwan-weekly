# WP 台灣週報 · 第 14 期

**出刊日：** 2026-11-23

本週重點：PHP 11/19 發布安全更新，其中 CVE-2026-129289（PHP-FPM）嚴重度 9.2，Wordfence 在實驗室用原生 WordPress 7.1.5 的 REST 請求重現了免登入執行程式；只有特定伺服器設定會中招，但請盡快確認主機的 PHP 版本。Wordfence 也發現自 10/26 起，針對台灣 .tw 網站的掃描攻擊明顯增加。WooCommerce 11.3.1 安全更新發布，在地金流外掛 woomp 也連續修正電子發票與定期定額問題。活動方面，台北 11/24 與 11/30 各有一場 WordPress 小聚。

---

## 本週熱點

### PHP 安全更新：CVE-2026-129289 影響 PHP-FPM 站台，請向主機確認

PHP 於 11/18、11/19 釋出修補版本：8.2.36、8.3.37、8.4.28、8.5.13。Wordfence 指出，CVE-2026-129289 是 PHP-FPM 的嚴重漏洞（CVSS v4.0 為 9.2），在特定條件下，未登入的攻擊者可讓 PHP 執行「沒有經過網頁伺服器檢查」的請求，進而以網站 PHP 程序的權限執行程式碼。

要注意的是，並非所有網站都會中招：

- **需要特定設定**：網頁伺服器必須重複使用與 PHP-FPM 之間的連線。NGINX 預設不開啟（`fastcgi_keep_conn` 預設為 off），但部分伺服器管理工具的範本會開啟；PHP 官方公告也列出 Apache `mod_proxy_fcgi` 搭配 `enablereuse=on` 的組合。
- **WordPress 核心可作為觸發路徑**：Wordfence 在隔離實驗室，用原生 WordPress 7.1.5 的 REST 請求搭配 PHP 8.2.35 重現了未登入執行程式；升到 8.2.36 或關閉連線重複使用後就無法重現。不需要有漏洞的外掛或佈景主題。
- **舊版 PHP 沒有修補**：5.6 到 8.1 的未支援版本沒有修補版，需改用仍受支援的版本。
- **尚無已知實際攻擊**：截至 11/20，該 CVE 未列入 CISA 已知遭利用漏洞清單，但技術細節已公開。

更新 WordPress 無法修補 PHP。共享或代管主機請查主機商公告，或直接詢問「PHP-FPM 是否已升級到修補版（含廠商的向後移植版）並重啟？」；自管主機請安裝修補版並重啟 PHP-FPM，同時確認網頁伺服器是否開啟連線重複使用。

另外，WordPress 核心團隊 11/18 與 PHP 社群的交流摘要提到，約 14.5% 的 WordPress 站台仍跑在 PHP 7.4、約 9% 在 8.1，PHP 8.2 也接近安全支援終點；團隊對 WordPress 7.2 相容 PHP 8.6 的信心很高，外掛生態則是最大變數，並將發布給外掛作者的 PHP 8.6 開發者說明。

→ [原文（Wordfence：CVE-2026-129289）](https://www.wordfence.com/blog/2026/11/cve-2026-129289-wordpress-php-vulnerabilities/)
→ [原文（PHP 8.5.13 發布公告）](https://www.php.net/releases/8_5_13.php)
→ [原文（Make Core：PHP 與 WordPress 交流摘要）](https://make.wordpress.org/core/2026/11/20/php-wordpress-chat-nov-2026/)

### PHP 8.6 正式發布

PHP 官網顯示 PHP 8.6 於 11/19 釋出，新功能包括部分函式應用（Partial Function Application）、`\Time\Duration` 類別、Polling API 與 `clamp()` 函式。是否要升級，仍建議等外掛與主機環境確認相容後再進行。

→ [原文（PHP 8.6 發布頁）](https://www.php.net/releases/8.6/en.php)

### Wordfence：針對台灣 .tw 網站的掃描攻擊自 10/26 起增加

Wordfence 檢視亞洲各國家頂級網域的防火牆資料，發現 10/26 到 11/13 之間，每天遭到「惡意檔案上傳」規則攔截的 .tw 網站平均 426 個，前八週平均為 131 個；SQL 注入攔截的 .tw 網站由每日 216 個增至 599 個。日本、韓國、香港、新加坡與中國網域沒有類似變化。Wordfence 將增幅追溯到單一 IP 位址 45.38.60.254，攔截的請求中，攻擊的都是 2017～2024 年已公開的外掛與軟體漏洞，上傳的檔案只是一小段會輸出雜湊值的 PHP（用來確認上傳是否成功），沒有後門；研判是在為台灣網站「探測」有漏洞的目標。該 IP 自 11/14 起的活動大幅減少。

這意味著：長期沒更新的外掛會是被盯上的第一個點。建議盤點站上超過一兩年沒更新的外掛，移除不用的，並確認防火牆與檔案上傳限制有開。

→ [原文（Wordfence 說明）](https://www.wordfence.com/blog/2026/11/45-38-60-254-the-scanner-behind-the-november-2026-rise-in-attacks-on-tw-websites/)

### WordPress 7.2 開發者導覽（Field Guide）出爐

7.2 的 Field Guide 於 11/20 發布，整理給外掛與佈景主題開發者的重點：本版涵蓋 440 多張 Core Trac 工單、21 個新 hook；Gutenberg 版本 23.7～24.3 的更新也一併納入。主要變更包括：客戶端媒體處理擴大到媒體庫格狀頁與「新增媒體檔案」頁（需 Chrome／Edge 137 以上，其他瀏覽器自動退回伺服器端處理）、Abilities API 可宣告每次呼叫都需要的權限（`required_capabilities`），以及上週提過的 REST、jQuery 準備事項。外掛作者請對照清單用 RC 版測試。

→ [原文（7.2 Field Guide）](https://make.wordpress.org/core/2026/11/20/wordpress-7-2-field-guide/)
→ [原文（Abilities API 更新）](https://make.wordpress.org/core/2026/11/19/abilities-api-updates-in-wordpress-7-2/)

---

## WooCommerce／電商

### WooCommerce 11.3.1：安全更新，headless 店面要檢查 CORS

WooCommerce 11.3.1 於 11/19 發布，官方標示為安全更新，且含資料庫更新。內容涵蓋結帳與訂單驗證（地址、可銷售地區、運費與稅金計算、優惠券使用與每位顧客限制）、顧客帳號與訂單資訊的隱私保護、API 權限收緊，以及商品與 CSV 匯入的資料處理。

開發者要特別留意：

- **Headless 店面**：Store API 的跨網域請求若帶有登入 cookie，現在只允許 WordPress 已允許的來源；不在名單內會出現瀏覽器 CORS 錯誤。需要的話，用 `allowed_http_origins` 過濾器加入店面網域；只用購物車 token 的請求，可改成 `credentials: 'omit'`。
- **後台 REST**：舊版 wc-admin options 端點現在要求 `manage_woocommerce` 權限，自訂「只能管訂單」角色的整合可能受影響。
- **佈景主題**：我的帳號新增 `myaccount/form-confirm-email.php` 範本。

更新後請實際走一遍購物車與結帳流程。

→ [原文（WooCommerce 11.3.1 發布說明）](https://developer.woocommerce.com/2026/11/19/woocommerce-11-3-1/)

### woomp（台灣金流／發票外掛）一週內連發 4 個版本

開源的在地 WooCommerce 外掛 woomp 在 11/19～11/21 釋出 v3.5.31～v3.5.34，重點如下：

- **v3.5.31～v3.5.32**：PayUni V3 定期定額客戶可在「我的帳號」自行換卡；v3.5.32 修正 v3.5.31 的 5 元綁卡授權沒有退回、3D 驗證導回等問題。作者提醒：用過 v3.5.31 換卡的站台，請先更新並暫緩通知客戶換卡，並手動檢查 PayUni 後台的 5 元交易。
- **v3.5.33**：修正 ezPay 電子發票「自動作廢」從未觸發的問題。更新前就已退款但發票仍有效的訂單，不會被自動補作廢，需要手動處理。
- **v3.5.34**：修正 PayNow 電子發票會把買家完整地址送給 PayNow 的隱私問題；需要恢復時可用 `paynow_ei_send_buyer_address` 過濾器。

使用上述金流或發票功能的站台，建議先在測試環境確認後再更新。

→ [原文（woomp Releases）](https://github.com/zenbuapps/woomp/releases)

---

## 台灣站務與活動

### 台北彩虹小聚：用 AI 做小遊戲，讓人人都能參與社會議題（11/24 週二 18:30）

Taipei WordPress Meetup 舉辦，Meetup 頁面顯示已有 24 人報名。

→ [活動頁（Meetup 搜尋結果）](https://www.meetup.com/find/?keywords=wordpress&location=tw--Taiwan&source=EVENTS)

### WordPress 台北小聚 @ 可能存在的遊樂園 CollaPlay（11/30 週一 19:00）

Taipei WordPress Meetup 的 11 月例行小聚，地點在 CollaPlay。

→ [活動頁（Meetup 搜尋結果）](https://www.meetup.com/find/?keywords=wordpress&location=tw--Taiwan&source=EVENTS)

---

## 在地工作室與教學部落格

### 犬哥網站：網站改版會影響 SEO／GEO 嗎？

犬哥 11/19 的文章指出，改版本身不一定讓排名下滑，真正的風險是網址、內容、標題與連結的變動沒有被妥善銜接。文章依「改版前 → 新站測試 → 上線當天 → 上線後」整理保護項目：改版前先用 Search Console 與 GA 盤點高價值網址並建立新舊網址對照表；上線時對舊網址做 301 轉址；內容別被 JavaScript 或動畫包住、Schema 與作者資訊別遺失。文章也提醒，換網域、換 CMS、改版型最好不要同時進行，且這些保護只是基礎，不保證被 AI 引用。

→ [原文](https://frankknow.com/how-redesign-affect-seo-geo/)

---

## 主機商觀點

### Kinsta：PHP 8.6 有哪些新東西（廠商立場）

Kinsta 11/20 的文章介紹 PHP 8.6 的新功能（Duration 類別、`clamp()`、部分函式應用與管線運算子等），並回顧發布前出現過的 PHP2Shell 安全漏洞：文章指出已在 10/21 的 RC4 修正。這是主機商的內容行銷文章，立場為其部落格的技術解說，僅供了解版本特性。

→ [原文](https://kinsta.com/blog/php-8-6/)

---

## 社群熱議

### r/Wordpress：每月有 7 萬個網站離開 WordPress，只有 13% 轉往其他 CMS

一位使用者引用網站監測服務 theweb.report 的資料（每月重新造訪 CrUX 資料集內的網站，追蹤同一批網域的變化）：本月有 70,603 個網站不再是 WordPress，其中只有 9,481 個（13%）換到其他平台；其餘包含轉址、伺服器預設頁、停放網域、空白頁等，另有約 3.4 萬個無法辨識，作者推測其中不少是用 AI 重建的網站，有些甚至保留了 WordPress 的 uploads/年/月 檔案路徑。留言區的討論分成兩派：有人用本地模型把小型網站改成靜態站，大幅降低成本；也有人認為需要編輯、電商或會員功能的站台不適合這麼做，並擔心把電商搬到自訂的 Next.js、Laravel 後難以長期維護。貼文發出約 4 天，已有 139 則留言。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1y07t01/70603_sites_left_wordpressorg_a_month_only_9481/)

---

## 職缺（近 7 日）

### 104｜網站、行銷、FB文字編輯（愛露營）

台中北屯；月薪 29,500 元以上。需熟悉 WordPress 網站後台操作，負責露營與戶外主題的文字編輯、品牌文案與社群經營，以及網站與社群平台的維護；相關經歷或觀光、旅遊背景可加分。（更新日期 11/23）

→ [職缺](https://www.104.com.tw/job/7t6wy)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
