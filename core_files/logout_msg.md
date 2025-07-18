# logout_msg.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\logout_msg.php`

## 概述
`logout_msg.php` 檔案是一個**登出訊息顯示頁面**。它主要用於處理用戶登出後的會話清理，並向用戶顯示登出原因（例如會話超時）。頁面會自動倒計時並重定向到登入頁面，同時提供手動跳轉的連結。該檔案確保了用戶在登出或會話失效後能夠得到明確的提示和引導。

## 主要功能與職責

### 1. 引入核心依賴與會話清理
- `include/config.php`: 應用程式的全局配置，可能包含 `_IDLETIME` 等常量。
- **緩存控制**: 設置 HTTP 頭，禁用緩存 (`Cache-Control`, `Expires`, `Pragma`)，確保頁面內容始終是最新的。
- **會話清理**: 
    - `session_status()`: 檢查當前 Session 狀態。
    - `$_SESSION['out']['msg']`: 從 Session 中獲取要顯示的登出訊息。
    - `setcookie('PHPSESSID', "", time()-_IDLETIME, '/')`: 清除 PHPSESSID Cookie，使 Session 失效。
    - `unset($dbh)`: 解除資料庫連接。
    - `session_destroy()`: 銷毀當前 Session。
- **Auth 登出**: 如果 `Auth` 物件存在且未通過認證，則調用 `$auth->logout()` 進行額外的登出處理。

### 2. 重定向 URL 處理
- 預設的登出重定向 URL 為 `./HomePage/home/`。
- **教育雲/OpenID 登出**: 如果 Session 中存在 `id_token`，則根據 `$_SESSION['loginfrom']`（`sso` 或 `oidc`）構建教育雲或 OpenID 的登出 URL，並將 `id_token_hint` 和 `post_logout_redirect_uri` 傳遞給外部認證服務，實現單點登出。

### 3. 前端結構與樣式
- **HTML 結構**: 頁面包含一個居中的 `div` 元素 (`.content`)，用於顯示警告圖標、登出訊息、倒計時和跳轉按鈕。
- **CSS 引入**: 引入 `css/font-awesome/css/all.css` (圖標庫)，並包含大量的內聯 `<style>` 標籤，用於定義頁面的佈局、顏色、字體大小和響應式行為。

### 4. JavaScript 倒計時與自動跳轉
- **倒計時**: 頁面顯示一個倒計時器（預設 5 秒），提示用戶自動跳轉。
- **`countdown()` 函數**: 每秒更新倒計時顯示。當倒計時結束時，自動將頁面重定向到 `$sLogoutUrl`。
- **手動跳轉**: 提供一個「回登入頁」按鈕，允許用戶手動跳轉。

## 關鍵引用與依賴
- `include/config.php`
- `$_SESSION` 超級全局變數
- `Auth` 類別 (如果存在)
- `HomePage/home/` (預設重定向目標)
- 教育雲/OpenID 登出端點

## 安全考量
- **會話清理**: 徹底銷毀 Session 和清除 Cookie 是良好的安全實踐，防止會話劫持。
- **單點登出**: 與教育雲/OpenID 的單點登出整合，確保用戶在所有相關服務中同時登出。
- **錯誤訊息顯示**: 顯示的訊息來自 Session，應確保其內容是安全的，防止 XSS 攻擊。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `Auth` 類別在登出流程中的具體作用。
- 了解 `config.php` 中 `_IDLETIME` 的定義。
- 了解教育雲和 OpenID 的單點登出協議和端點。
