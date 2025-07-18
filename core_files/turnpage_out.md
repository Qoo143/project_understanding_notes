# turnpage_out.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\turnpage_out.php`

## 概述
`turnpage_out.php` 檔案在因材網應用程式中扮演著**核心的 AJAX 請求路由器**角色，與 `turnpage.php` 功能高度相似。它負責接收前端發送的 AJAX 請求，解析請求中的 `router` 參數以確定要執行的後端邏輯檔案，並在執行前應用必要的安全檢查。這個檔案主要用於處理那些不需要完整身份驗證流程的 AJAX 請求，例如教育雲登入流程中的數據獲取。

## 主要功能與職責

### 1. 請求預處理與安全檢查
- **引入核心檔案**:
    - `include/config.php`: 應用程式的全局配置。
    - `include/adp_core_function.php`: 核心通用函數，包含 `decodeParam` 等。
    - `include/adp_core_security.php`: 包含 `preventXss` 等安全函數。
    - `classes/adp_core_class.php`: 核心類別。
- **請求數據處理**:
    - `parse_str(file_get_contents("php://input"), $aHttpMessageBody)`: 處理 `Content-Type` 為 `application/x-www-form-urlencoded` 的 POST 請求體，將其解析到 `$aHttpMessageBody` 陣列。
    - `$_REQUEST = array_merge($_REQUEST, $aHttpMessageBody)`: 將解析後的數據合併到 `$_REQUEST` 中，確保所有請求數據都能被統一處理。
    - `$_REQUEST = preventXss($_REQUEST)` 和 `$_POST = preventXss($_POST)`: 對 `$_REQUEST` 和 `$_POST` 中的所有數據進行 XSS 防護處理，這是防止跨站腳本攻擊的關鍵步驟。

### 2. 路由解析與檔案引入
- **`decodeParam($sEncodeUrl)` 函數**: 這是 `turnpage_out.php` 的核心路由邏輯。
    - 接收一個編碼過的 URL 字串 (`$sEncodeUrl`)，這個字串很可能是一個 hash 或 token。
    - 透過查詢 `$_SESSION['ADL_ROUTER_PASS']` 來獲取與 `$sEncodeUrl` 相關聯的實際模組資料夾 (`folder`) 和檔案名稱 (`filename`)。這表明應用程式在某個階段（可能在 `modules_new.php` 或 `choiceIdentity.php` 中）預先註冊了這些路由資訊並存儲在 Session 中。
    - 根據 `is_custom_path` 的值，構建最終要引入的 PHP 檔案路徑，格式為 `modules/{folder}/{file}.php` 或 `{folder}/{file}.php`。
- **檔案引入**:
    - `if (file_exists($filePath)) { require $filePath; }`: 在確認檔案存在後，引入該檔案。這將執行對應的後端模組邏輯。

## 與 `turnpage.php` 的關係
- `turnpage_out.php` 與 `turnpage.php` 在功能上非常相似，都作為 AJAX 請求的路由器。主要的區別可能在於它們所處理的請求類型和安全上下文。
- `turnpage.php` 通常用於處理需要完整身份驗證的 AJAX 請求，而 `turnpage_out.php` 可能用於處理那些在用戶尚未完全登入或需要特殊處理的 AJAX 請求（例如教育雲登入流程中獲取用戶數據）。

## 安全考量
- **XSS 防護**: `preventXss` 的廣泛應用是良好的安全實踐。
- **任意檔案引入風險**: `decodeParam` 函數和 `file_exists`、`require` 的組合，需要確保 `$filePath` 無法被惡意篡改以引入非預期的檔案，這在處理用戶輸入時尤為重要。
- **Session 數據依賴**: 路由邏輯依賴 `$_SESSION['ADL_ROUTER_PASS']`，需要確保這些 Session 數據的完整性和安全性。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `include/adp_core_security.php`
- `classes/adp_core_class.php`
- `$_SESSION['ADL_ROUTER_PASS']` (由其他部分設定)
- `preventXss()` (定義在 `include/adp_core_security.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的，以及 `encodeAjaxUrlBase64()` 函數的生成邏輯。
- 了解 `turnpage.php` 和 `turnpage_out.php` 在實際應用中的具體分工和使用場景。
