# WP 台灣週報 · 第 7 期

**出刊日：** 2026-10-12

本週重點：**WordPress 7.1.3** 一口氣修補 7 個資安問題，請立即更新；7.2 公布發布派對時程，**Beta 1 在 10/20（台北時間 23:00）**，同時有兩則開發者需要注意的底層變更。WooCommerce **11.2** 正式版與 **11.2.1** 修正版相繼推出，11.2 本身含資安修正。**Contact Form 7 6.2** 把 PHP 需求直接拉到 8.3，舊主機上的站要小心。本月台北彩虹小聚（10/19）與高雄小聚（10/20）仍可報名。

---

## 本週熱點

### WordPress 7.1.3：7 個資安修正，建議立即更新
10/6 釋出，含 7 個資安修正與 4 個錯誤修正。資安項目包括：留言管理頁可透過待審留言觸發的 stored XSS、WXR 匯出的二階 SQL Injection、未登入者可讀到私人或未發佈文章的留言、Imgur 嵌入 XSS、作者角色可以把文章設為置頂等。修補會回溯到所有仍收資安更新的舊分支（目前到 4.7）。有開自動背景更新的站會自動套用，其他站請手動確認。

→ [原文](https://wordpress.org/news/2026/10/wordpress-7-1-3-maintenance-and-security-release/)

### WordPress 7.2 發布派對時程：Beta 1 為 10/20
核心團隊公布 7.2 各階段的發布時間，都在 UTC 15:00（台北時間 23:00）：**Beta 1 10/20**，之後每週二出 Beta 2～4；**RC1 11/17**、RC2 11/24、RC3 12/1；**正式版 12/8**。派對在 Making WordPress Slack 的 #core 頻道進行，任何人都能參加測試。

→ [原文](https://make.wordpress.org/core/2026/10/06/wordpress-7-2-release-party-schedule/)

### 7.2 開發者注意：wp_kses() 改寫，NO_BACKSLASH_ESCAPES 預設關閉
兩項底層變更已進入 7.2。第一，負責過濾不安全 HTML 的 `wp_kses()` 改用 HTML API 重寫，介面不變、更快更省記憶體，但對奇特輸入的處理結果可能不同；若外掛依賴舊行為，可暫時用 `wp_kses_force_legacy_parser` 過濾器切回舊版，官方也希望大家在正式版前回報問題。第二，WordPress 連線資料庫時會自動關閉 MySQL 的 `NO_BACKSLASH_ESCAPES` 模式，避免部分主機開啟這個模式後造成查詢錯誤；真的需要的站可在 `db.php` drop-in 用 `incompatible_sql_modes` 過濾器保留。

→ [wp_kses() 進度報告](https://make.wordpress.org/core/2026/10/07/progress-report-wp_kses/) · [SQL 模式說明](https://make.wordpress.org/core/2026/10/09/no_backslash_escapes-sql-mode-is-now-turned-off-by-default-in-wordpress-7-2/)

### 統一通知系統重啟，目標隨 7.2 推出 0.3.0
沉寂多年的 Feature Notifications 專案（為 WordPress 打造統一的後台通知中心）公布新計畫：0.3.0 將是第一個真正能建立、儲存、顯示與關閉通知的可用版本，目標與 7.2 同步在 12 月推出。外掛開發者未來可能有官方管道發送後台通知，不必各自塞橫幅。

→ [原文](https://make.wordpress.org/core/2026/10/10/feature-notifications-status-update-and-plan-for-0-3-0/)

---

## WooCommerce／電商

### WooCommerce 11.2：含資安修正，所有商店都該升級
10/7 正式釋出（含資料庫更新）。新功能：商品 CSV 匯入在沒有 ID 或 SKU 時，可用 GTIN／UPC／EAN／ISBN 比對既有商品；結帳區塊的額外欄位 API 支援日期欄（例如生日、指定到貨日）；新增訂單撤回申請的通知信。資安方面，重設密碼、新帳號等含驗證連結的信件不再能設定副本／密件副本，下載商品的授權檢查更嚴格，商店管理員也不能再編輯同時具有其他高權限角色的使用者。少數自訂或匯入的下載連結可能因此失效，需要重新發放。

→ [原文](https://developer.woocommerce.com/2026/10/07/woocommerce-11-2-0-release-notes/)

### WooCommerce 11.2.1：修傳統結帳移除地址欄位後無法下單
10/9 釋出。11.2.0 在傳統結帳頁上，若商店用過濾器移除了地址欄位，可能出現「請輸入地址」卡住結帳、沒收稅或運送選項消失。台灣不少商店會精簡結帳欄位，已升 11.2.0 的請盡快補上 11.2.1。

→ [原文](https://developer.woocommerce.com/2026/10/09/woocommerce-11-2-1-release-notes/)

### RY Tools 2026.10.9：調整物流外包裝尺寸計算
台灣常用的金物流整合外掛再出新版，調整物流外包裝尺寸的計算方式。有依材積計算運費或使用綠界等物流的店家，升級後建議抽幾筆訂單確認運費。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### 3D Product configurator for WooCommerce ≤2.16.2：未授權遠端執行程式碼（CVE-2026-103889）
未登入者即可透過 `xpv_image` 參數在伺服器執行程式碼（CVSS **9.8**）。外掛目錄現行版為 **2.16.3**，有用的商店請立即升級。

→ [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-103889)

---

## 資安

### Avada Builder ≤7.16.1：未登入者可觸發任意 action hook（CVE-2026-97670）
在預設設定的 Avada 表單（AJAX 送出、通知信含欄位）上，未登入者可呼叫任意 WordPress action hook，已驗證可永久刪除垃圾桶中的文章、頁面與留言（CVSS **9.1**）。官方已推出 **Avada 7.16.2**，同版一併修了 6 個資安問題。

→ [Avada 公告](https://avada.com/blog/version-7-16-2-security-update/) · [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-97670)

### Bricksforge ≤3.1.8.9：未授權上傳 PHP 檔（CVE-2026-85097）
Bricks 主題常用的擴充外掛，攻擊者可繞過檢查上傳並執行 PHP（CVSS **9.8**）。有用的站請對照官方更新日誌升級到修補版，修補前先停用含檔案上傳的前台表單。

→ [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-85097) · [官方更新日誌](https://bricksforge.io/version-changelog/)

### Advanced IP Blocker ≤8.13.13：兩步驟登入可被繞過（CVE-2026-104732）
這款資安外掛的 2FA 登入流程沒有確認使用者完成第一步，未登入者可直接繞過驗證（CVSS **9.8**）。外掛目錄現行版為 **8.13.16**。

→ [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-104732)

### Frontend Dashboard 3.0.0–3.0.4：未登入即可接管任何帳號（CVE-2026-103692）
未做權限與 nonce 檢查，未登入者可呼叫任意 PHP 函式，進而接管包含管理員在內的帳號。修補版為 3.0.5，目前最新為 3.0.12。

→ [CVE 記錄](https://www.cve.org/CVERecord?id=CVE-2026-103692)

---

## 外掛

### Contact Form 7 6.2：需要 PHP 8.3 與 WordPress 7.1
10/6 釋出的 6.2 是大改版，最低需求提高到 **WordPress 7.1、PHP 8.3**，之後 6.2.1、6.2.2 各修一個小問題。作者另發文說明，外掛只支援 WordPress「建議」的 PHP 版本，並呼籲其他外掛開發者一起提高 PHP 需求，推動仍有約 18% 站點停在 PHP 7.4 的生態往前走。台灣不少共享主機預設 PHP 8.1／8.2，裝了 CF7 的站請先確認主機版本。

→ [6.2 釋出](https://contactform7.com/2026/10/06/contact-form-7-620/) · [作者觀點](https://contactform7.com/2026/10/10/follow-the-recommendation-not-the-requirement/)

---

## 台灣站務與活動

### 【10/19】WordPress 彩虹小聚：當老闆本人就是 ERP
林馬具負責人林昀與工程師 Moksaweb 分享 WP 電商長大後怎麼整理營運流程、把 LINE 預約串進系統，另有閃電講。**2026-10-19（一）18:30–21:30**，台北言文字（重慶南路一段 11 號），入場低消一杯飲料。

→ [Meetup](https://www.meetup.com/taipei-wordpress/events/316808850/)

### 【10/20】高雄 WordPress 小聚
講題「Google 商家經營：把陌生的客人變成熟客」（Allen 國安）。**2026-10-20（二）19:00–21:00**，高雄 Second Space RED（七賢一路 294 號），場地費 100 元，需先完成報名。

→ [Meetup](https://www.meetup.com/kaohsiung-wordpress-meetup/events/316748093/)

---

## 在地工作室部落格

### 分類排序小幫手：分類排序與側邊欄樹狀分類二合一
金城事務所的免費自製外掛。後台拖曳排好分類順序，前台與側邊欄都照著顯示；側邊欄樹狀分類可收合，主分類會加總子分類文章數。文中提到老牌樹狀分類外掛 WP-dTree 下架後一直缺替代品，也比較了 Category Order and Taxonomy Terms Order 的適用情境。

→ [原文](https://iseeu.tw/mr-category/)

### 圖片整理小幫手：舊文章圖片也能自動歸進資料夾
同站自製的媒體庫資料夾外掛，依文章自動建資料夾（用標題命名），連舊文章用過的圖片都會歸檔；實測 16,500 張圖片約 1 分鐘整理完。文中比較 FileBird、HappyFiles，並提醒「未連結」資料夾裡的圖片可能仍用在側邊欄或頁尾，別直接刪。

→ [原文](https://iseeu.tw/mr-folder/)

---

## 主機商觀點

### Bot 打上門時怎麼辦：一份處理流程（Kinsta）
主張不要慌、也不要全部封鎖：先判斷是一般 bot 問題還是攻擊，必要時先穩住網站，再決定哪些放行、挑戰或封鎖，最後持續監控並記錄。文末附檢查清單，換成其他主機也適用。廠商立場：Kinsta。

→ [原文](https://kinsta.com/blog/when-bots-hit-your-site/)

### AI agent 需要護欄，而不是無限權限（Kinsta）
以 AI agent 刪除正式資料庫與備份的事件為例，說明 agent 會「回報成功卻默默出錯」，自動更新外掛也分辨不出被植入後門的版本；建議把破壞性步驟留給人工核准、先在 staging 驗證，並給 agent 最小權限。廠商立場：Kinsta（文中示範自家 API 與後台功能）。

→ [原文](https://kinsta.com/blog/ai-agent-guardrails/)

---

## 社群熱議

### Reddit｜Contact Form 7 6.2 讓 PHP 8.2 的站當掉？
發文者抱怨 CF7 新版在不符 PHP 需求的站上被自動更新、整站掛掉。有人查了核心程式，WordPress 本來就會在 PHP 版本不足時拒絕更新；最後原 PO 自己找到原因：更新是由 Softaculous 觸發，它回報的是伺服器的 PHP 版本，而不是網站實際使用的版本。用主機面板自動更新的人值得檢查一下設定。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wzohes/contact_form_7_v62_crashing_sites_running_php82/)

### Reddit｜QUIC.cloud 取消隨用隨付，改成月費方案
QUIC.cloud 官方文件寫明 **11/10** 起改為方案制，原本的免費 CDN 與 Standard 方案停止，付費方案每個網域 5 美元起；免費方案保留，但只走較小的美歐節點。手上 20 個小站的發文者大呼吃不消。高讚留言提醒：LiteSpeed Cache 的頁面快取在自家伺服器上，不依賴 QUIC.cloud，會受影響的是圖片最佳化、Critical CSS 與 CDN；多數人建議先把 DNS 搬到 Cloudflare 免費方案。台灣使用 LiteSpeed 主機的站不少，值得提早評估。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wzrlr9/quiccloud_dropping_pay_as_you_go/) · [QUIC.cloud 方案說明](https://docs.quic.cloud/billing/plan-based/)

### Reddit｜Bot 狂刷 Google Maps API，45 分鐘燒掉 220 歐元
一個用到 Google Maps API 的 WordPress 站被 bot 狂打，因為 Google Cloud 的帳單警示有約 45 分鐘延遲，發現時已經被收了 220 歐元。留言建議幫公開的 API 呼叫加上速率限制、快取與用量上限，並分享首次誤用通常可以向 Google 申請部分或全額退款。幫客戶串地圖的接案者，交站前記得設好配額。

→ [Reddit](https://www.reddit.com/r/Wordpress/comments/1wzk4p4/our_wordpress_site_got_hit_by_a_bot_burning_220/)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
