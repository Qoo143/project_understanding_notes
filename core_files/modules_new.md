# modules_new.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\modules_new.php`

## 概述
`modules_new.php` 是因材網應用程式的**主要入口點和控制器**。它負責初始化應用程式環境、處理用戶請求、管理會話、執行安全檢查、載入模組，並根據用戶權限和請求參數進行頁面導向。這個檔案整合了前端資源的引入和後端邏輯的調度，是理解整個系統運作流程的關鍵。

## 主要功能與職責

### 1. 核心檔案引入
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_security.php`: 核心安全函數，包含 `preventXss`。
- `37include/adp_core_class_37.php`: 可能與特定舊系統或模組相關的類別。
- `modules/OpenID/config_openid.php` 和 `modules/OpenID/library.class.php`: OpenID 身份驗證相關配置和庫。
- `include/adp_core_function.php`: 核心通用函數。
- `auth_chk.php`: 身份驗證檢查，判斷用戶是否已登入。

### 2. 安全與會話管理
- **XSS 防護**: 使用 `preventXss($_REQUEST)` 對所有請求數據進行預處理，防止跨站腳本攻擊。
- **操作員 PDO 重新宣告**: 如果用戶權限達到 `USER_OPERATOR` 等級，會重新宣告 `$dbh` 和 `$dbh_slave`，可能用於特定的資料庫操作權限管理。
- **登出邏輯**: 處理用戶登出請求，包括教育雲 SSO、OpenID 和本地登出，並根據來源進行重定向。
- **OpenID Token 刷新**: 檢查 OpenID 的 refresh token 是否過期，並自動刷新以保持會話。
- **Session 安全**: 包含 `session_status()` 檢查，以及對 `$_SESSION['user_agent']` 和 `session_regenerate_id(true)` 的使用，以防範 Session 劫持和固定攻擊。
- **密碼檢查與帳號綁定提醒**: 引入 `check_password.php` 和 `check_accountCombine.php`，並根據結果設置 Session 變數，用於前端顯示密碼修改或帳號綁定提醒。

### 3. 路由與模組載入
- **模組載入機制**: 根據 `$_REQUEST['op']` (通常為 `modload`)、`$_REQUEST['name']` (模組名稱) 和 `$_REQUEST['file']` (模組內檔案名稱) 參數，動態構建並引入對應的模組檔案 (`modules/{module_name}/{sub_path}/{file_name}.php`)。
- **檔案路徑處理**: 包含複雜的邏輯來解析模組名稱和子路徑，處理 `basename` 和 `explode`，以確保正確的檔案路徑。
- **文件驗證**: 在非開發環境下，會檢查載入的 PHP 檔案的 SHA256 雜湊值是否與 `adl_hash_file.json` 中記錄的一致，這是一個重要的安全措施，防止檔案被篡改。
- **重定向邏輯**: 根據用戶權限 (`access_level`) 和請求 URI，將用戶重定向到不同的預設頁面或維護頁面。

### 4. 用戶資訊與狀態
- `$_SESSION['user_data']`: 儲存用戶的詳細資訊，如權限等級 (`access_level`)、組織名稱、班級、用戶名等。
- `updateActionTime()`: 函數用於更新用戶的最後活動時間，記錄在 `user_status` 表中。
- **訊息與待辦事項**: 查詢 `srl_calendar` 和 `user_feedback` 表，獲取用戶的待辦事項和未讀訊息數量，並在前端顯示。
- **環境識別**: 根據主機名判斷當前運行環境（如 `cron-server`, `test-adlweb`, `dev-adlweb`, `stg-adlweb` 等）。

### 5. 前端資源引入
- **CSS 引入**: 根據用戶權限 (`access_level`) 動態引入不同的 CSS 檔案（學生、教師、家長、管理員）。
- **JS 引入**: 根據運行環境引入不同版本的 Vue.js 和 Vue Router (生產環境使用 `prod.js`，開發環境使用 `global.js`)，以及 jQuery 和其他多種前端庫/插件（如 `colorPick`, `drawPad`, `iziModal`, `sweetalert`, `axios`, `html2canvas`, `chartjs`, `venobox`, `hammerjs` 等）。
- **Google Tag Manager**: 整合 Google Tag Manager 進行數據追蹤。
- **字體引入**: 定義了多種中文字體。

### 6. 介面與互動
- **導航欄**: 包含菜單按鈕、Logo、環境顯示、操作介紹、課程總覽、AI 學伴、指派任務、外部連結（教育雲、Google Meet、Classroom）和登出按鈕。
- **個人資訊區**: 顯示用戶頭像、組織、身份、姓名、訊息數量和待辦事項數量。
- **問題回報功能**: 整合 `html2canvas` 進行截圖，並通過 AJAX 將圖片數據發送到 `turnpage.php` 處理，然後打開問題回報視窗。
- **彈出視窗**: 使用 `iziModal` 和 `venobox` 處理彈出視窗。
- **菜單開關**: 處理側邊菜單的顯示/隱藏邏輯。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_security.php` (包含 `preventXss`)
- `37include/adp_core_class_37.php`
- `modules/OpenID/config_openid.php`
- `modules/OpenID/library.class.php`
- `include/adp_core_function.php`
- `auth_chk.php`
- `modules/menu/without_nav_content.php`
- `include/check_password.php`
- `include/check_accountCombine.php`
- `breadcrumb.php`
- `adl_hash_file.json` (用於文件完整性檢查)
- `turnpage.php` (作為 AJAX 請求的路由處理器)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `auth_chk.php` 和 `turnpage.php`，它們是 `modules_new.php` 的重要依賴。
- 了解 `preventXss` 和 `validateRedirect` 等安全函數的具體實現。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
