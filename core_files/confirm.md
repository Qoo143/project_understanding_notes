# confirm.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\confirm.php`

## 概述
`confirm.php` 檔案主要用於**處理帳號啟用和確認訊息顯示**。它接收來自用戶點擊郵件驗證連結的請求，驗證傳入的啟用碼和用戶 ID，並根據驗證結果更新用戶帳號狀態。此外，它還包含一個基本的 CSRF 檢查和一個通用的確認訊息顯示函數。

## 主要功能與職責

### 1. CSRF 檢查
- **`if ($_SERVER['REQUEST_METHOD'] === 'POST')`**: 檢查請求方法是否為 POST。
- **`if (!isset($_GET['csrf_token']) || $_GET['csrf_token'] !== $_SESSION['csrf_token'])`**: 驗證 CSRF token。如果 `$_GET['csrf_token']` 不存在或與 `$_SESSION['csrf_token']` 不匹配，則終止腳本並顯示「CSRF vaild failed」。這表明該頁面可能也用於處理某些 POST 請求，並且需要 CSRF 保護。

### 2. 帳號啟用邏輯 (`account_active` 函數)
- **觸發條件**: 當 `$_GET['ac']` (帳號) 和 `$_GET['aakey']` (啟用碼) 都存在時，會呼叫 `account_active()` 函數。
- **引入依賴**: 引入 `include/config.php` 和 `include/adp_API.php`。
- **參數處理**: 
    - `$user_id = hash2string($account)`: 用戶 ID 經過 `hash2string()` 解密。
    - `$account_active_key = $key`: 啟用碼直接使用。
- **資料庫查詢**: 查詢 `user_status` 表，獲取用戶的 `email_verify_code` 和 `email_verify_time`。
- **驗證流程**: 
    - **期限檢查**: 判斷當前時間 (`NowTime`) 是否超過 `email_verify_time` 加上 259200 秒（3 天）。如果超過期限，則清空資料庫中的驗證碼和時間，並顯示「超過期限，請重新申請。」的訊息。
    - **驗證碼匹配**: 如果啟用碼 (`$account_active_key`) 與資料庫中的 `email_verify_code` 相同，則將 `email_verify` 設置為 `1`（表示已驗證），並清空驗證碼和時間，顯示「帳號啟用成功」的訊息。
    - **請求失敗**: 如果驗證碼不匹配，則顯示「請求失敗，請與管理人員聯絡。」的訊息。
- **重定向**: 無論成功或失敗，最終都會重定向到 `https://adl.edu.tw/`。

### 3. 確認訊息顯示 (`confirm_msg` 函數)
- **功能**: 顯示一個 JavaScript 彈出警告框 (`window.alert()`)，然後將用戶重定向到指定的 URL (`location.href`)。
- **參數**: `$msg` (要顯示的訊息) 和 `$redirect` (重定向的 URL)。
- **預設行為**: 如果沒有 `ac` 和 `aakey` 參數，則會直接呼叫 `confirm_msg('請勿亂連！','https://adl.edu.tw/')`，防止未經授權的訪問。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_API.php`
- `hash2string()` (可能定義在 `adp_core_function.php`)
- `dbh` (全局資料庫連接)
- `$_SESSION['csrf_token']` (CSRF token)

## 安全考量
- **CSRF 防護**: 雖然存在 CSRF 檢查，但需要確保 `$_SESSION['csrf_token']` 的生成是安全的，並且在每次請求後都會刷新，以防止 CSRF 攻擊。
- **URL 重定向**: `confirm_msg()` 函數直接使用 `location.href` 進行重定向，如果 `$redirect` 參數來自用戶輸入且未經充分驗證，可能存在開放重定向漏洞。然而，在此檔案中，重定向目標是硬編碼的 `https://adl.edu.tw/`，因此風險較低。
- **`hash2string()` 的安全性**: 用戶 ID 經過解密，需要確保 `hash2string()` 的實現是安全的，並且不會導致信息洩露或繞過。
- **驗證碼有效期**: 驗證碼的 3 天有效期是合理的安全措施。

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `hash2string()` 函數的具體實現和安全性。
- 了解 `$_SESSION['csrf_token']` 是如何生成和維護的。
- 了解 `adp_API.php` 中可能包含的其他通用函數。
