# WP 台灣週報 · 第 9 期

**出刊日：** 2026-10-26

本週重點：PHP 官方 10/21 發布緊急安全更新，修補兩個不需登入就能遠端執行程式碼的重大漏洞，所有自管主機都該確認 PHP 已升級並重啟；UpdraftPlus 的遠端管理服務 UpdraftCentral 連線金鑰外洩，已有網站被裝上後門外掛；WordPress 7.2 Beta 1 於 10/20 推出，正式版預定 12/8。WooCommerce 11.3 大改 MCP 連線方式，Woomp 修正綠界超取殘留門市代號導致宅配地址被改寫的問題。這週台南、台北、桃園連三天都有小聚。

---

## 本週熱點

### PHP 緊急安全更新：兩個免登入 RCE，請升級並重啟 PHP

PHP 官方 10/21 發布 8.5.12、8.4.27、8.3.36、8.2.35，修補 CVE-2026-109618（記憶體不足處理導致的遠端執行）與 CVE-2026-109619（請求之間殘留的全域狀態導致的遠端執行），攻擊者不需登入、甚至在 WordPress 程式碼執行前就能觸發。PHP-FPM、Apache mod_php、php-cgi、FrankenPHP、LiteSpeed 都在影響範圍，官方認為 mod_php 風險最高。PHP 8.1 以前已不再由官方提供更新。暫時無法升級時，官方列出的緩解措施包括關閉 `session.upload_progress.enabled`（改完要重啟 PHP worker）、調低 `post_max_size` 等。

WordPress 本身不需要更新，但要確認主機上的 PHP 版本：到「工具 → 網站狀態 → 資訊 → 伺服器」查看，或直接問主機商。自管 VPS 要記得升級後重啟 PHP 服務、重建容器映像檔。10/23 PHP 另發一則資訊公告（CVE-2026-115560）：只有在程式執行中把 session 序列化方式改成 `php` 的少見情況才會觸發，建議先維持 `session.upload_progress.enabled=0` 到下一個例行版本。另外提醒，PHP 8.2 的安全支援只到 2026/12/31，還在 8.2 或更舊版本的網站，建議趁這次直接規劃升到 8.3 以上。

Wordfence 統計到 74,000 多次攻擊嘗試，目前尚未確認有網站因此被入侵；攻擊者常見手法是讀取 `.env`、`wp-config.php`，或嘗試寫入 `wp2s.php` 之類的檔案，可以順手檢查網站根目錄有沒有陌生的 PHP 檔。

→ [原文（PHP 官方公告）](https://www.php.net/archive/2026.php#2026-10-21-5)
→ [原文（PHP 10/23 資訊公告）](https://www.php.net/archive/2026.php#2026-10-23-1)
→ [原文（WordPress.org）](https://wordpress.org/news/2026/10/critical-php-security-release/)
→ [原文（Wordfence 檢測指引）](https://www.wordfence.com/blog/2026/10/critical-php-update-wordpress-vulnerabilities-testing/)
→ [原文（Wordfence 攻擊觀察）](https://www.wordfence.com/blog/2026/10/attacks-on-php2shell-continue-but-no-known-compromises-as-a-result-yet/)

### WordPress 7.2 Beta 1 推出：響應式區塊設定、Secrets API、新預設佈景 Ipsum

7.2 Beta 1 已於 10/20 釋出，正式版預定 12/8。這版亮點很多：區塊可以依不同裝置寬度設定不同樣式（例如圖庫欄數、裁切比例）；全域樣式多了「已自訂」篩選，區塊檢查器會顯示繼承來的值；表單元素可在佈景中統一設定樣式；新增文字陰影（可用 theme.json 預設）；導覽選單可以直接在網站編輯器側欄管理；Notes 加入表情回應與縮小模式。

後台方面，媒體庫支援在瀏覽器端處理圖片；儀表板新增「歷史上的今天」小工具；管理列與選單圖示從 Dashicons 換成新的 WordPress 圖示庫；後台資源改成個別載入並預先抓取下一個畫面。開發者要注意新的 Secrets API（金鑰加密儲存、輪替、可自訂提供者）、Abilities API 擴充與 Interactivity API 的可信任 HTML。新預設佈景主題名為 Ipsum。

接下來時程：Beta 2 10/27、Beta 3 11/3、Beta 4 11/10、RC1 11/17。測試方式可用 WP-CLI `wp core update --version=7.2-beta1`、Beta Tester 外掛或 WordPress Playground，請勿在正式站測試。外掛與佈景開發者建議這幾週就開始測自己的產品，特別是有用到 Dashicons 或自訂後台資源載入的。

→ [原文（WordPress.org）](https://wordpress.org/news/2026/10/wordpress-7-2-beta-1-is-ready-for-download-and-testing/)
→ [原文（7.2 測試指南）](https://make.wordpress.org/test/2026/10/20/help-test-wordpress-7-2/)
→ [原文（7.2 開發時程）](https://make.wordpress.org/core/7-2/)

### Contributor Toolkit 2.0：不用裝 Git、Node、Docker 也能貢獻核心

WordPress 官方的貢獻者工具推出 2.0 重新設計版，安裝後不需自行準備 Git、Node 或 Docker，就能跑起核心與 Gutenberg 的開發環境，體積也比前版小約 350MB。介面目前支援 10 種語言，其他語言在翻譯完成度達 80% 後就會加入，繁體中文目前還沒上線，有興趣的人可以先到翻譯平台幫忙。

→ [原文](https://make.wordpress.org/core/2026/10/23/wordpress-contributor-toolkit-2-0-a-redesigned-app-in-your-language/)

---

## WooCommerce／電商

### WooCommerce 11.3 改變 MCP 連線方式：舊端點停用，改用 MCP Adapter

WooCommerce 11.3 會移除原本的 `/wp-json/woocommerce/mcp` 端點（之後會回應 HTTP 410），也不再支援用 REST API 金鑰連線。要讓 AI 工具透過 MCP 操作商店，必須另外安裝 WordPress 官方的 MCP Adapter 外掛（目前 0.7.1，已上架 WordPress.org），用「應用程式密碼」驗證，端點改為 `/wp-json/mcp/mcp-adapter-default-server`。商品與訂單相關的能力（abilities）預設啟用，原本的 MCP 實驗功能開關也拿掉了。已經串接 Claude、ChatGPT 等工具管理商店的人，升級前請先準備好重新設定。

→ [原文](https://developer.woocommerce.com/2026/10/21/woocommerce-11-3-mcp/)
→ [MCP Adapter 外掛](https://wordpress.org/plugins/mcp-adapter/)

### WooCommerce 徵求測試「可擴充的 abilities」

同樣在 11.3，WooCommerce 加入一項實驗功能（預設關閉，在「設定 → 進階 → 功能」開啟）：擴充外掛可以把自己的欄位掛進商品、訂單、訂單項目的 abilities，資料會放在 `extensions.<命名空間>.<欄位>` 底下，並可加上驗證與查詢篩選。做物流、金流、會員等 Woo 擴充的開發者，可以趁現在測試，讓 AI 透過 MCP 讀寫訂單時也能看到你的欄位。

→ [原文](https://developer.woocommerce.com/2026/10/21/woocommerce-11-3-mcp/)
→ [原文（徵求測試）](https://developer.woocommerce.com/2026/10/22/extensible-abilities/)

### PHP 漏洞：WooCommerce 建議 PHP 8.3 以上

WooCommerce 針對這次 PHP 漏洞發文表示，商店本身不需更新外掛，重點是 PHP 版本；WooCommerce 目前建議使用 PHP 8.3 以上，並預告最低支援版本 7.4 之後會提高。還在舊版 PHP 的商店請盡早排升級。

→ [原文](https://developer.woocommerce.com/2026/10/21/php-security-release/)

### Woomp 3.5.26：修正綠界超取殘留門市代號，宅配地址被改成門市地址

Woomp 10/20 推出 3.5.26。問題是：宅配訂單如果殘留了綠界超商門市代號，運送地址會被換成門市地址，縣市、郵遞區號、地址第二行與公司欄位也被清空。3.5.21 以前只要顧客「先選超商、再改宅配」就會發生；3.5.22 起則只在前端腳本出錯、使用自訂結帳頁或直接送出表單時才會發生，藍新超取也受影響。修正後只有選擇綠界超取時才會寫入門市資訊。升級不需要改設定，但已經出錯的舊訂單不會自動復原，請檢查近期宅配訂單，必要時聯絡顧客確認地址。

→ [原文](https://github.com/zenbuapps/woomp/releases/tag/v3.5.26)

### RY Tools for WooCommerce 2026.10.24：配合藍新 API 更新

台灣常用的金物流整合外掛 RY Tools 10/24 推出 2026.10.24 版，更新內容是配合藍新金流的 API 調整。有使用藍新的商店建議更新，並在更新後實際跑一筆測試交易。

→ [原文](https://tw.wordpress.org/plugins/ry-woocommerce-tools/)

### GTM Server Side 外掛：可能把別人的訂單個資放進 dataLayer

使用 stape 的 GTM Server Side 外掛（約 1 萬個安裝）要注意 CVE-2026-55826（CVSS 8.7）：2.0.0 到 2.1.47 版在 WooCommerce 購買事件中，未登入的訪客可以透過竄改 Cookie，讓另一位顧客的完整訂單個資出現在頁面的 dataLayer。請更新到最新版（目前 2.3.10）。

→ [原文](https://www.cve.org/CVERecord?id=CVE-2026-55826)

---

## 資安

### UpdraftCentral 連線金鑰外洩：攻擊者用合法金鑰遠端安裝後門外掛

UpdraftPlus 開發商 TeamUpdraft 證實，官網 teamupdraft.com 在 10/17–18 週末遭入侵，外洩資料包含客戶姓名、Email、帳單資料、訂單紀錄、雜湊後的密碼、雙重驗證設定，以及 UpdraftCentral／Vault／Clone 的連線金鑰；信用卡資料與外掛下載檔不受影響。攻擊者拿外洩的 UpdraftCentral 金鑰，對網站下指令安裝惡意外掛，常見描述是「Small maintenance toolkit」，名稱像隨機組合（例如 style-bridge-xxx），會建立看似「編輯」實際有管理權限的帳號（Email 多為 @example.org、顯示名稱「Site Tools」），另一種變體則會隱藏帳號或直接以第一位管理員身分登入。

UpdraftPlus 10/19 推出 1.26.12／1.26.13（Premium 為 2.26.12／2.26.13），會撤銷在事件前建立的 UpdraftCentral 金鑰，WordPress.org 已主動推送更新；Wordfence 免費版也收到緊急防火牆規則。

建議動作：
- 更改 teamupdraft.com 帳號密碼並重設雙重驗證。
- 每個網站直接登入後台，更新 UpdraftPlus，到「設定 → UpdraftPlus → 進階工具 → UpdraftCentral」刪除舊金鑰。
- 檢查使用者清單與外掛（包含 mu-plugins），變更管理員密碼；若發現可疑跡象，還原 10/17 以前的備份。
- 沒有註冊帳號、只用免費版的網站不需額外處理；Premium 用戶即使沒用過 UpdraftCentral 也建議檢查。

→ [原文（TeamUpdraft）](https://teamupdraft.com/blog/security-incident-october-2026/)
→ [原文（Wordfence）](https://www.wordfence.com/blog/2026/10/updraftcentral-connection-key-compromise/)

### Rank Math 後續：CVE-2026-111862 正式發布

上期報導的 Rank Math SEO PRO 3.0.123 供應鏈攻擊，已正式登錄為 CVE-2026-111862（CVSS 9.8）。乾淨版本仍為 3.0.124；官方知識庫補充，部分免費帳號資料可能遭刪除，使用者可能需要重設密碼。

→ [原文](https://www.cve.org/CVERecord?id=CVE-2026-111862)

### Duplicator、Prime Mover：搬家外掛的「留言注入」漏洞

Wordfence 發現 Duplicator 1.5.17 以前（Pro 4.6.10 以前）的 CVE-2026-82814（CVSS 8.8）：未登入者可以在網站留言裡藏入惡意序列化資料，等管理員建立打包檔並執行搬家時被觸發，免費版可執行系統指令，Pro 版可刪除檔案（例如刪掉 wp-config.php 進而接管網站）。已在 5.0.0 修正，目前為 5.0.6，約 100 萬個安裝；舊版做出的打包檔建議用新版重做。同樣手法也出現在 Prime Mover 2.2.1 以前（CVE-2026-115806，CVSS 9.8），請更新到 2.2.2 以上。

→ [原文（Wordfence：Duplicator）](https://www.wordfence.com/blog/2026/10/wordfence-argus-discovers-high-severity-unauthenticated-php-object-injection-in-duplicator/)
→ [原文（Prime Mover CVE）](https://www.cve.org/CVERecord?id=CVE-2026-115806)

### ACF: Extended：前台表單可被拿來遠端執行程式

Advanced Custom Fields: Extended（含 PRO，約 10 萬個安裝）0.9.2.8 以前有 CVE-2026-94267（CVSS 9.0）：網站若有公開使用它的前台文章更新表單，未登入者可繞過權限檢查並進一步執行程式碼；同版另修正兩個 8.1 分的漏洞。請更新到 0.9.2.8。

→ [原文](https://www.cve.org/CVERecord?id=CVE-2026-94267)

### LiteSpeed Cache 7.9.2：修正兩個免登入儲存型 XSS

LiteSpeed Cache（700 萬以上安裝）7.9.2 以前，在啟用 CDN 內聯 CSS 改寫或字型顯示最佳化時，未登入者可植入儲存型 XSS（CVE-2026-113499、CVE-2026-113500）。7.9.2 已於 10/20 推出，同時強化快取隔離。請盡快更新。

→ [原文（CVE-2026-113499）](https://www.cve.org/CVERecord?id=CVE-2026-113499)
→ [原文（CVE-2026-113500）](https://www.cve.org/CVERecord?id=CVE-2026-113500)

### 其他本週修補

- WebP Express 0.25.16 以前：免登入儲存型 XSS（CVE-2026-97328），約 30 萬個安裝。
- Otter Blocks 3.2.8 以前：免登入儲存型 XSS（CVE-2026-109936），約 30 萬個安裝。

→ [原文（WebP Express）](https://www.cve.org/CVERecord?id=CVE-2026-97328)
→ [原文（Otter Blocks）](https://www.cve.org/CVERecord?id=CVE-2026-109936)

---

## 台灣站務與活動

### 台南小聚 #10：Google 商家經營（10/29 週四）

10/29（四）19:00 開始，18:00 起可先到場用餐，地點在三皇三家歸仁店（臺南市歸仁區中山路三段 28 號）。講題「Google 商家經營：把陌生的客人變成熟客」，講者 Allen 魏國安。低消為每人一份點心或飲料；搭高鐵到台南站的人，報名時告知主辦，18:30 可接駁。

→ [活動頁](https://www.meetup.com/kaohsiung-wordpress-meetup/events/317044562/)

### 台北小聚：Vue SPA 官網為什麼搬回 WordPress（10/30 週五）

10/30（五）19:00–21:30，地點為可能存在的遊樂園 CollaPlay（台北市萬華區武昌街二段 83 之 6 號，樓層以活動頁為準）。講題「當 AI 爬蟲只讀到 77 個字：Vue SPA 官網為什麼搬回 WordPress」，講者是火曜領航企業社 CTO Kevin 林詩凱；19:30–20:30 主題分享，20:30 起閃電講。

→ [活動頁](https://www.meetup.com/taipei-wordpress/events/316945507/)

### 桃園小聚 #62：接案策略、Elementor 對上 Mosaic（10/31 週六）

10/31（六）14:00–17:00，地點在喜憨兒桃園南門公園庇護商店（桃園市桃園區三民路三段 264 號），低消一杯飲品，場地只開放提早 10 分鐘入場、禁止外食。兩場分享：敲敲設計 Calvin Ho「WordPress 接案、該思考的下一步策略」，黑莉企業社 Moksa「Elementor 該緊張了嗎？Mosaic 來踢館」，之後是閃電講（可現場報名）與自由交流。

→ [活動頁](https://www.meetup.com/taoyuan-wordpress-meetup/events/316950942/)

---

## 在地工作室部落格

### 設計湖：PHP 遠端執行漏洞，企業網站現在該做的 4 件事

設計湖整理了這次 PHP 漏洞的白話說明：先確認主機的 PHP 版本是否在已修補清單內，8.1 以前的網站優先規劃升到 8.3；暫時無法升級時可套用官方緩解設定；接著檢查是否有入侵跡象，並確認有異地備份。適合轉給不熟主機設定的客戶或老闆看。

→ [原文](https://www.design-hu.com/web-news/php-rce-vulnerability-cve-2026-109618.html)

---

## 主機商觀點

### 代管主機商如何處理這次 PHP 漏洞（廠商立場）

幾家代管主機商都發文說明處理狀況。Kinsta 表示已修補所有環境（含測試站），10/22 結案，並搭配 Cloudflare WAF 規則；WP Engine 說明 10/21 美中時間凌晨 3 點前已部署 WAF 規則與緩解設定，並透過 LTS 廠商取得修補，也為溝通不夠即時致歉；Pressable 表示網站已受保護。

值得注意的是 Rocket.net 的提醒：他們採用「回移修補」（backport），所以 `php -v` 或網站狀態頁顯示的版本號可能沒變，但漏洞其實已修好。也就是說，看到版本號沒升級不一定代表沒修補，用代管主機的人最可靠的做法是直接看主機商公告或詢問客服。以上皆為各主機商對自家服務的說明。

→ [原文（Kinsta）](https://kinsta.com/blog/php2shell-kinsta-wordpress/)
→ [原文（WP Engine）](https://wpengine.com/blog/php-vulnerability-update/)
→ [原文（Rocket.net）](https://rocket.net/blog/critical-php-vulnerabilities-rocket-nets-response/)
→ [原文（Pressable）](https://pressable.com/blog/your-pressable-sites-are-already-protected-from-the-recent-critical-php-security-update/)

### Kinsta：AI 功能會長時間佔用 PHP worker（廠商立場）

Kinsta 指出，網站上的 AI 功能（例如呼叫外部模型 API 產生內容）常讓一個 PHP worker 被佔住好幾秒，流量一大就可能排隊變慢，建議用 APM 工具找出是哪個外掛或請求拖住。文中以自家 APM 工具示範（Kinsta 客戶免費），但「AI 請求要算進 PHP worker 容量」這個觀念適用於任何主機。

→ [原文](https://kinsta.com/blog/monitor-wordpress-ai-workloads/)

---

## 影音

### 犬哥網站：新網站如何快速取得 SEO 排名、被 AI 推薦？

約 20 分鐘的實作影片，示範新網站上線後的 6 個 SEO／GEO 步驟：確認 Google 與 Bing 收錄、規劃關鍵字、整理內容結構、建立品牌可信度（客戶案例、Google 商家檔案、第三方提及）、AI 較容易理解的網站架構，最後用 Search Console、GA4 和 AI 搜尋工具追蹤排名與 AI 提及。

→ [影片](https://www.youtube.com/watch?v=Uu2IBgaP6wY)

---

## 社群熱議

### r/WordPress：UpdraftCentral 被植入後門的第一手紀錄

一位站長在 10/19 發文，記錄網站突然整站錯誤，原因是一個他沒裝過的外掛「Image Core」（描述為「small maintenance toolkit」），會用特定網址參數建立帶管理權限的帳號並自動登入；因為攻擊者的程式寫錯才當掉被發現。之後 TeamUpdraft 證實外洩，留言區也有多人回報中招、有人抱怨沒收到通知，另有人提醒：找不到陌生帳號不代表沒事，因為有變體會直接以既有管理員身分登入。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1x9mp8q/backdoor_plugin_dropped_on_my_wordpress_site/)

### r/WordPress：PHP 兩天內又一個漏洞，要緊張嗎？

PHP 發出 CVE-2026-115560 資訊公告後的討論。高讚留言整理得很清楚：這個漏洞只在程式執行中修改 session 序列化方式、且 upload progress 仍開啟時才會觸發，一般 WordPress 網站不太會遇到，先前已關掉 `session.upload_progress` 的人不需再做什麼。也有人提醒，代管主機多半已修補，真正危險的是沒人顧的自管 VPS 和舊的映像檔。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xdcabj/another_php_issue_php_warns_of_another_unpatched/)

### r/WordPress：備份外掛用的 AWS 金鑰外洩，被盜用 8,000 美元

一位工程師分享案例：客戶收到 AWS 異常警示，追查發現有人用外洩的 root 存取金鑰大量呼叫 Bedrock AI 模型，帳單多了約 8,000 美元。同一把金鑰被多個 WordPress 站的 UpdraftPlus 拿來備份到 S3，其中一個舊的多站台還跑著過時版本；金鑰 8 月就被掃描工具測試過，10 月才被濫用。雖然最後無法確定外洩途徑，留言建議：每個備份用權限最小的專屬 IAM 使用者、不要用 root 金鑰、設定帳單異常警示，懷疑外洩就立刻換金鑰。

→ [討論串](https://www.reddit.com/r/Wordpress/comments/1xf6z2a/careless_aws_key_handling_on_wordpress_backup/)

### r/woocommerce：Cloudflare AI 爬蟲設定，「Block」訓練會連 Google 一起擋

發文者提醒，Cloudflare 9/15 起把 AI 爬蟲控制分成搜尋、訓練、代理三類，在「訓練」選擇 Block 或「有廣告的頁面才封鎖」，會連 Googlebot、Bingbot、Applebot 這類同時做搜尋與訓練的爬蟲一起擋掉。如果只是不想被拿去訓練 AI，應選「Disallow AI Training」，它會寫入 robots.txt 的拒絕訓練偏好，同時保留搜尋收錄。Cloudflare 官方部落格也證實了這個改變。改完後可到 Search Console 的「檢索統計資料」看 403 有沒有暴增。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xchi1p/setting_up_cloudflares_ai_bot_controls_on_your/)
→ [Cloudflare 官方說明](https://blog.cloudflare.com/accountable-mixed-use-ai-crawlers/)

### r/woocommerce：傳統結帳短代碼 2027 年要被移除？

有人問 AI 聊天機器人說 WooCommerce 2027 年 3 月會移除傳統結帳短代碼，是真的嗎？回覆一致認為是 AI 幻覺：WooCommerce 團隊 2025 年 3 月在官方 FAQ 文章下回覆，目前沒有淘汰傳統購物車與結帳的計畫，會持續支援以維持相容性；區塊結帳只是新商店的預設。實務建議是列出目前依賴傳統結帳的外掛（自訂欄位、金流、地址調整），之後要評估轉換時才有依據，並以 WooCommerce 開發者部落格的正式公告為準。

→ [討論串](https://www.reddit.com/r/woocommerce/comments/1xcl5gv/is_the_classic_woocommerce_checkout_shortcode/)
→ [WooCommerce 官方 FAQ 與回覆](https://developer.woocommerce.com/2023/11/06/faq-extending-cart-and-checkout-blocks/)

---

## 職缺（近 7 日）

本期多數職缺以行銷、社群或內容工作為主，WordPress 多列為加分或輔助技能。

### 1111｜遠端工作 社群小編－影片＆商品上架｜文章撰寫（好酷商行／76DollShop）
新北三重，可遠端；時薪制。在 WordPress／WooCommerce 上架商品（名稱、價格、規格、分類、標籤、圖片），把 SEO 文章發布到 WordPress，並用 Rank Math 設定 SEO 標題、關鍵字與 Meta Description，另負責短影音製作與社群同步。（更新日期 10/21）

→ [職缺](https://www.1111.com.tw/job/132830553)

### 104｜行銷企劃專員（默聲創意，品牌事業部）
台北內湖，部分遠端；月薪 40,000～45,000 元。公司本身做網站開發，職缺要求具備電商平台經驗，WooCommerce 與 SHOPLINE、蝦皮並列為可接受的平台。（頁面更新日期 10/23）

→ [職缺](https://www.104.com.tw/job/8hvaj)

### 104｜內容行銷企劃（麗寶生醫）
台北中山；月薪 36,000～45,000 元。以社群與內容行銷為主，也要更新網站文章與商品圖，協助 WordPress／WooCommerce 後台基本操作與基礎 SEO。（頁面更新日期 10/20）

→ [職缺](https://www.104.com.tw/job/94tic)

### 104｜數位行銷專員（SEO/GEO 內容佈局＆網站專案）（玩構網路科技）
高雄楠梓；月薪 35,000～45,000 元。協助客戶在 WordPress 調整版型、發布內容與建立 Landing Page，需熟悉 WordPress／CMS 與基礎 HTML。（頁面更新日期 10/26）

→ [職缺](https://www.104.com.tw/job/8kom7)

### 1111｜前端網頁設計師（奧斯曼數位行銷）
台北松山；面議（經常性薪資 4 萬元以上）。負責網站規劃、視覺設計、切版、建置、上架與前端效能優化，條件第一項即為熟悉 WordPress，需附作品集。（更新日期 10/26）

→ [職缺](https://www.1111.com.tw/job/130212021)

### 1111｜網站管理／行銷（駿業國際精密）
新北汐止；月薪 34,000～45,000 元。負責官網整體規劃與管理，用 WordPress 等 CMS 規劃版面，上架並更新汽車與零配件產品內容。（更新日期 10/26）

→ [職缺](https://www.1111.com.tw/job/132803015)

### 104｜網頁維運／行銷企劃／社群小編（勃根地藏家）
台北大同，部分遠端；兩則職缺，一則月薪 35,000～45,000 元，另一則為週休三日、月薪 34,000～38,000 元。以社群經營搭配基本官網維護為主，曾操作 WordPress 後台者佳，未來將協助建置新網頁。（頁面更新日期 10/25）

→ [職缺（月薪 35,000～45,000）](https://www.104.com.tw/job/8cdno)
→ [職缺（週休三日）](https://www.104.com.tw/job/953tw)

---

*《WP 台灣週報》獨立研究一手來源出刊。線上目錄：https://oberonlai.github.io/wp-taiwan-weekly/*
