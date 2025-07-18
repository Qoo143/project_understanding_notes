# PrivacyUpdate.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\PrivacyUpdate.php`

## 概述
`PrivacyUpdate.php` 檔案是一個**後端處理腳本**，專門用於**更新用戶的隱私權政策同意時間和 IP 地址**。它接收來自前端的 AJAX 請求，並在資料庫中記錄用戶同意隱私權政策的行為。該檔案是 `agreement.php` 頁面中用戶點擊「確定」按鈕後觸發的後端邏輯。

## 主要功能與職責

### 1. 引入核心依賴與初始化
- `session_start()`: 啟動 Session。
- `include_once "include/config.php"`: 應用程式的全局配置。
- `require_once "include/adp_API.php"`: 核心 API 引入檔案。
- `require_once "include/security_function.php"`: 安全相關函數。
- `$user_data = $funUnserialize($_SESSION['USERDATA'])`: 從 Session 中反序列化用戶數據。
- `$ip = $_SERVER['REMOTE_ADDR']`: 獲取用戶的 IP 地址。

### 2. 隱私權政策更新邏輯
- **`switch ($_POST['act'])`**: 腳本根據 `$_POST['act']` 參數來執行不同的操作。目前只處理 `PrivacyUpdate` 這個動作。
- **`PrivacyUpdate` 動作**: 
    - **獲取用戶 ID**: 首先，它通過查詢 `user_info` 表，根據 Session 中儲存的用戶帳號 (`$_SESSION['_authsession']['username']`) 來獲取對應的 `user_id`。
    - **更新資料庫**: 然後，它更新 `user_status` 表中的 `PrivacyTimestamp` 字段為當前時間 (`$datetime`)，並將 `PrivacyIP` 字段更新為用戶的 IP 地址 (`$ip`)。這表示用戶已同意隱私權政策。
    - **返回狀態**: 成功更新後，返回 JSON 格式的狀態碼 `1`。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_API.php`
- `include/security_function.php`
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)
- `user_info` 資料庫表
- `user_status` 資料庫表

## 安全考量
- **Session 數據依賴**: 腳本直接從 Session 中獲取用戶帳號 (`$_SESSION['_authsession']['username']`)。需要確保 Session 的安全性，防止 Session 劫持或篡改。
- **SQL 注入**: 使用 `dbh->prepare()` 和 `execute()` 進行資料庫操作，這有助於防止 SQL 注入。但需要確保所有綁定參數都正確使用。
- **IP 地址記錄**: 記錄用戶同意時的 IP 地址，有助於追溯和審計。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `agreement.php` 如何觸發此腳本，以及它如何處理返回的狀態。
- 了解 `$_SESSION['_authsession']['username']` 是如何被設置的。
- 了解 `user_info` 和 `user_status` 資料庫表的結構和關聯。
