# logout.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\logout.php`

## 概述
`logout.php` 檔案是一個**處理用戶登出操作**的腳本。它負責記錄用戶的登出時間，清理會話數據，並最終將用戶重定向到網站的首頁。該檔案確保了用戶在登出後，其會話信息被安全地銷毀。

## 主要功能與職責

### 1. 登出觸發條件
- 腳本通過檢查 `$_GET['act'] == 'logout'` 來判斷是否執行登出操作。這表明它通常由一個帶有 `act=logout` 參數的 GET 請求觸發。

### 2. 登出時間記錄
- **`$logouttime=date("Y-m-d, H:i:s");`**: 記錄當前的登出時間。
- **`$lasttimeout=time();`**: 記錄當前的 Unix 時間戳。
- **`$user_id=$auth->getUsername();`**: 獲取當前登入用戶的 ID。這表明 `Auth` 物件在腳本執行前已經被實例化並可用。
- **資料庫更新**: 如果 `user_id` 存在，則更新 `user_status` 表中的 `lasttimeout` 和 `stoptimestamp` 字段，記錄用戶的最後登出時間。

### 3. 會話清理
- **`$auth->logout();`**: 調用 `Auth` 庫的登出方法，執行其內部的登出邏輯，例如清除 Auth 相關的 Session 數據。
- **`unset($dbh);`**: 解除資料庫連接 `$dbh`，釋放資源。
- **`session_destroy();`**: 銷毀當前 Session，清除所有 Session 數據。

### 4. 訊息顯示與重定向
- **`$msg='您已經登出！';`**: 設置一個登出訊息。
- **`Header("Location: index_AIAL2.php");`**: 將用戶重定向到 `index_AIAL2.php`，這是網站的首頁或登入頁面。
- **`die();`**: 在重定向後立即終止腳本執行，確保不會有額外的內容輸出。

## 關鍵引用與依賴
- `Auth` 類別 (推測在腳本執行前已實例化)
- `dbh` (全局資料庫連接)
- `index_AIAL2.php` (重定向目標)
- `user_status` 資料庫表

## 安全考量
- **會話清理**: 徹底銷毀 Session 和清除相關數據是良好的安全實踐，防止會話劫持。
- **資料庫操作**: 直接使用 `$dbh->query($sql)` 執行 SQL 語句，如果 `$user_id` 來自不可信的輸入，可能存在 SQL 注入風險。然而，`$user_id` 來自 `$auth->getUsername()`，通常認為是安全的。

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `Auth` 類別是如何在 `logout.php` 執行前被實例化的。
- 了解 `user_status` 資料庫表的結構和 `lasttimeout`、`stoptimestamp` 字段的用途。
- 了解 `index_AIAL2.php` 的具體功能。
