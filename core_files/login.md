# login.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\login.php`

## 概述
`login.php` 是因材網應用程式的**主要登入頁面**。它負責處理用戶的身份驗證、會話管理、以及登入後的重定向。此檔案包含了前端的 HTML、CSS 和 JavaScript，以及後端的 PHP 邏輯，是一個典型的混合式應用程式入口點。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置，定義了常量和基本設定。
- `include/adp_core_function.php`: 核心通用函數庫，包含 `getUserIP()` 等。
- `include/security_function.php`: 安全相關函數，包含 `xss_filter()`、`str_security()`、`my_filter()` 等。
- `include/adp_core_security.php`: 核心安全函數，包含 `preventXss()` 等。
- `modules/OpenID/config_openid.php`: OpenID 相關配置。

### 2. 輸入數據過濾與安全
- 對 `$_POST`、`$_GET` 和 `$_COOKIE` 中的所有輸入數據進行 XSS 過濾 (`xss_filter()`)，這是防止跨站腳本攻擊的重要措施。
- 在處理登入表單提交時，對 `$_POST` 數據進行進一步的過濾和安全檢查，包括 `FILTER_SANITIZE_STRING` 和 `array_walk_recursive` 結合 `str_security()` 和 `xss_clean()`。

### 3. 登入邏輯與身份驗證
- **PEAR Auth 庫**: 使用 `Auth/Auth.php` 進行身份驗證。它實例化 `Auth` 類別，並使用 `DB` 驅動進行資料庫驗證。
- **`createAuthOptions('user_account')`**: 創建 Auth 類別所需的參數，可能指定了用戶數據的來源。
- **OpenID 登入**: 如果會話中存在 OpenID 相關的 `access_token`、`OpenID_user_id` 和 `OpenID_pwd`，則使用 OpenID 進行登入。
- **`$auth->start()` 和 `$auth->checkAuth()`**: 啟動身份驗證會話並檢查用戶是否已登入且會話有效。
- **登入成功處理**: 
    - 記錄登入 IP 和時間到 `user_status` 表。
    - 根據用戶的 `access_level` 和隱私權協議簽署狀態，決定登入後的重定向 URL（`modules_new.php?op=main` 或 `agreement.php`）。
    - 如果密碼長期未更新，會設置 `$_SESSION['modifyAlertPassword']` 提醒用戶修改密碼。

### 4. 用戶數據處理與 Cookie 管理
- **學校代號處理**: 針對不同用戶類型（訪客、家長、管理單位），對學校代號進行特殊處理。
- **Cookie 設置**: 將用戶的登入選項（`id_level`、`username`、`captcha_code`、`school` 相關數據）保存到 Cookie 中，以便下次登入時自動填充。
- **Session 數據**: 將 `cityarea` 和 `user_pw` 保存到 Session 中，用於後續的問題回報和強制修改密碼機制。

### 5. 前端互動與樣式
- **HTML 結構**: 包含登入表單、錯誤訊息顯示區域、以及相關的 CSS 和 JavaScript 引入。
- **CSS 樣式**: 引入 `css/teacher_root.css`、`css/font-awesome/css/all.min.css` 和 `css/adllogin.css`，定義了頁面的視覺風格。
- **JavaScript 邏輯**: 
    - `set_school()`: 根據 Cookie 中的學校數據自動填充縣市、區域和學校下拉選單。
    - `modalBox()`: 顯示彈出式訊息框。
    - `showPassword()`: 切換密碼輸入框的顯示/隱藏。
    - **教育雲/因材網選擇**: 提供了選擇前往教育雲入口網或因材網的選項，並通過 AJAX 調用 `turnpage.php` 進行處理。
    - **鍵盤導航**: 處理 Tab 鍵的焦點樣式，提升無障礙體驗。

### 6. IP 獲取與 CSRF 防護
- **`getUserIP()` 函數**: 獲取用戶的真實 IP 地址，並包含 CSRF token 驗證邏輯，防止跨站請求偽造。

### 7. 登入問題與幫助
- 提供了常見登入問題、忘記密碼和如何申請帳號的鏈接。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `include/security_function.php`
- `include/adp_core_security.php`
- `modules/OpenID/config_openid.php`
- `Auth/Auth.php` (PEAR Auth 庫)
- `turnpage.php` (用於 AJAX 請求)
- `$_SESSION` 和 `$_COOKIE` 超級全局變數

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `security_function.php` 中 `xss_filter()`、`str_security()`、`my_filter()` 和 `xss_clean()` 的具體實現。
- 了解 `createAuthOptions()` 函數的定義和作用。
- 了解 `Auth/Auth.php` 和 `Auth/Container/DB.php` 的詳細互動方式。
- 探索 `turnpage.php` 如何處理來自 `login.php` 的 AJAX 請求。
- 了解 `config.php` 中 `_IDLETIME` 和 `COOKIE_OPTIONS` 等常量的具體配置。
- 了解 `agreement.php` 的功能和觸發條件。
- 了解 `user_recovery.php` 的功能。