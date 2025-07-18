# api_login.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\api_login.php`

## 概述
`api_login.php` 檔案是一個**後端 API 登入處理腳本**，主要用於與外部認證服務 (`tl-assessment.edu.tw`) 進行互動，實現用戶的單點登入 (SSO) 或第三方認證。它接收來自前端的請求，向外部 API 發送數據，處理返回的響應，並最終將用戶重定向到因材網內部的登入處理頁面 (`login4ck.php`)。該檔案不直接面向用戶，而是作為一個中間層處理 API 通訊。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置，可能包含 `_SPLIT_SYMBOL` 等常量。

### 2. IP 偵測與調試
- 腳本會嘗試獲取用戶的 IP 地址 (`$userip`)。
- 如果 `$userip` 為 `120.108.208.127`，則會啟用 PHP 的錯誤報告 (`ini_set('display_errors','1'); error_reporting(E_ALL);`)，這表明這是一個用於開發或調試的特定 IP 地址。

### 3. 與外部 API 互動 (用戶註冊/登入)
- **API 端點**: `https://tl-assessment.edu.tw/api/v1/users`。
- **請求方法**: POST。
- **數據**: 接收 `$_GET` 參數作為 `$vMathData`，並添加 `type='save'`。這些數據會被 `http_build_query()` 轉換為 URL 編碼的字串作為 POST 請求的內容。
- **SSL 驗證**: `ssl` 選項中的 `verify_peer` 和 `verify_peer_name` 都設置為 `true`，表示會進行嚴格的 SSL 憑證驗證。註解 `////SSL Verification Bypass` 可能暗示曾經有繞過驗證的需求或嘗試。
- **`file_get_contents()`**: 使用此函數向外部 API 發送請求並獲取響應。
- **響應處理**: 接收到的 JSON 響應會被 `json_decode()` 解析。如果 `status` 為 `OK`，則繼續下一步。

### 4. 與外部 API 互動 (獲取用戶 ID)
- **API 端點**: `https://tl-assessment.edu.tw/api/v1/gusers`。
- **請求方法**: POST。
- **數據**: 將從上一步獲取的 `accesstoken` 和原始的 `$_GET` 參數作為 `$vLoginUser` 發送。
- **響應處理**: 再次解析 JSON 響應。如果 `status` 為 `OK`，則表示成功獲取用戶信息。

### 5. 內部登入處理與重定向
- **特定 IP 的調試邏輯**: 如果是特定調試 IP (`120.108.208.127`)，會引入 `Auth/Auth.php` 和 `classes/adp_core_class.php`，並執行 `Auth` 的 `logout()` 和 `session_destroy()`，這可能用於測試環境的會話清理。
- **重定向**: 最終，腳本會構建一個 URL，將用戶重定向到 `login4ck.php`，並將從外部 API 獲取到的用戶信息和開始時間進行 `base64_encode` 後作為 `data` 參數傳遞。`str_replace("+","%2B",...)` 用於處理 URL 編碼中 `+` 符號的特殊處理。

### 6. 錯誤處理
- 如果任何一步的 API 請求失敗或返回的 `status` 不是 `OK`，則會輸出「登入失敗」訊息和外部 API 返回的錯誤訊息。

### 7. 輔助函數 `string2array($String)`
- 該函數似乎是一個自定義的字串解析函數，用於將特定格式的字串轉換為陣列。從其邏輯判斷，它可能用於處理舊版或特定格式的數據，但在此檔案中並未被直接調用。

## 關鍵引用與依賴
- `include/config.php`
- 外部 API: `https://tl-assessment.edu.tw/api/v1/users` 和 `https://tl-assessment.edu.tw/api/v1/gusers`
- `login4ck.php` (重定向目標)
- `Auth/Auth.php` (在特定調試模式下引入)
- `classes/adp_core_class.php` (在特定調試模式下引入)

## 安全考量
- **SSL 驗證**: 嚴格的 SSL 驗證是良好的安全實踐，確保與外部 API 的通訊是加密和受信任的。
- **錯誤訊息處理**: 直接輸出外部 API 返回的錯誤訊息可能存在信息洩露風險，應考慮對錯誤訊息進行適當的處理或通用化。
- **URL 重定向**: 重定向到 `login4ck.php` 時，將敏感信息 (`result2`) 進行 `base64_encode` 傳遞，雖然不是明文，但 `base64` 並非加密，仍需確保 `login4ck.php` 對這些數據的處理是安全的。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `login4ck.php` 如何接收和處理來自此頁面的數據。
- 了解 `tl-assessment.edu.tw` 外部 API 的具體功能和認證機制。
- 了解 `config.php` 中 `_SPLIT_SYMBOL` 的定義。
- 了解 `Auth/Auth.php` 和 `classes/adp_core_class.php` 在特定調試模式下被引入的目的。

