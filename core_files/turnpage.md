# turnpage.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\turnpage.php`

## 概述
`turnpage.php` 在因材網應用程式中扮演著**核心的 AJAX 請求路由器**角色。它負責接收前端發送的 AJAX 請求，解析請求中的參數以確定要執行的後端邏輯檔案，並在執行前應用必要的安全檢查。這使得應用程式能夠集中處理動態內容載入和數據提交。

## 主要功能與職責

### 1. 請求預處理與安全檢查
- **引入核心安全檔案**:
    - `auth_chk.php`: 用於身份驗證檢查，確保請求來自已認證的用戶。
    - `include/adp_core_security.php`: 包含 `preventXss` 等安全函數。
- **請求數據處理**:
    - `parse_str(file_get_contents("php://input"), $aHttpMessageBody)`: 處理 `Content-Type` 為 `application/x-www-form-urlencoded` 的 POST 請求體，將其解析到 `$aHttpMessageBody` 陣列。
    - `$_REQUEST = array_merge($_REQUEST, $aHttpMessageBody)`: 將解析後的數據合併到 `$_REQUEST` 中，確保所有請求數據都能被統一處理。
    - `$_REQUEST = preventXss($_REQUEST)` 和 `$_POST = preventXss($_POST)`: 對 `$_REQUEST` 和 `$_POST` 中的所有數據進行 XSS 防護處理，這是防止跨站腳本攻擊的關鍵步驟。

### 2. 路由解析與檔案引入
- **`decodeParam($sEncodeUrl)` 函數**: 這是 `turnpage.php` 的核心路由邏輯。
    - 接收一個編碼過的 URL 字串 (`$sEncodeUrl`)，這個字串很可能是一個 hash 或 token。
    - 透過查詢 `$_SESSION['ADL_ROUTER_PASS']` 來獲取與 `$sEncodeUrl` 相關聯的實際模組資料夾 (`folder`) 和檔案名稱 (`filename`)。這表明應用程式在某個階段（可能在 `modules_new.php` 中）預先註冊了這些路由資訊並存儲在 Session 中。
    - 將 `$sEncodeUrl` 設定為 `$_POST['csrf_token']` 和 `$_SESSION['csrf_token']`。這是一個自定義的 CSRF 防護機制，其安全性取決於 `$sEncodeUrl` 的生成方式和不可預測性。
    - 根據 `is_custom_path` 的值，構建最終要引入的 PHP 檔案路徑，格式為 `modules/{folder}/{file}.php` 或 `{folder}/{file}.php`。
- **檔案引入**:
    - `if (file_exists($filePath)) { require $filePath; }`: 在確認檔案存在後，引入該檔案。這將執行對應的後端模組邏輯。

## 與 `modules_new.php` 的關係
- `modules_new.php` 作為應用程式的主要入口，會生成用於 AJAX 請求的路由 token（可能透過 `encodeAjaxUrlBase64()` 函數），並將這些請求發送到 `turnpage.php`。
- `turnpage.php` 則負責接收這些請求，解析 token，並載入 `modules_new.php` 所需的具體模組檔案。這兩者共同構成了應用程式的前後端互動機制。

## 安全考量
- **XSS 防護**: `preventXss` 的廣泛應用是良好的安全實踐。
- **CSRF 防護**: 使用編碼 URL 作為 CSRF token 的方式值得進一步研究其隨機性和不可預測性，以確保其有效性。
- **任意檔案引入風險**: `decodeParam` 函數和 `file_exists`、`require` 的組合，需要確保 `$filePath` 無法被惡意篡改以引入非預期的檔案，這在處理用戶輸入時尤為重要。

## 關鍵引用與依賴
- `auth_chk.php`
- `include/adp_core_security.php`
- `$_SESSION['ADL_ROUTER_PASS']` (由其他部分設定)
- `preventXss()` (定義在 `include/adp_core_security.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `auth_chk.php` 的具體實現。
- 了解 `preventXss` 和 `validateRedirect` 等安全函數的具體實現。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 繼續閱讀 `auth_chk.php`。
