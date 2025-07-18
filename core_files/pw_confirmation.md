# pw_confirmation.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\pw_confirmation.php`

## 概述
`pw_confirmation.php` 檔案是一個**密碼變更確認頁面**。它接收一個帶有 Token 的請求，該 Token 包含了密碼重設或變更的相關信息。腳本會驗證這個 Token 的有效性，並根據驗證結果更新用戶的密碼。該頁面主要用於處理用戶點擊密碼重設郵件中的連結後，進行最終的密碼確認和更新操作。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `hash2string()` 等。
- `include/adp_core_security.php`: 核心安全函數，包含 `preventXss()` 和 `xss_filter()` 等。
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserChangePassword` 等。

### 2. CSRF 檢查
- 頁面開頭包含一個 CSRF 檢查，如果請求方法是 POST，則驗證 `$_POST['csrf_token']` 是否與 `$_SESSION['csrf_token']` 匹配。這表明該頁面可能也用於處理某些 POST 請求，並且需要 CSRF 保護。

### 3. Token 處理與驗證
- **`$_GET['token']` 參數**: 頁面通過 `$_GET['token']` 參數接收一個加密的 Token。這個 Token 包含了密碼變更的相關數據。
- **解密與解析**: 
    - `sParam = substr($get['token'], -1 * strlen($get['token']), -1)`: 提取 Token 的主要部分。
    - `vParam = json_decode(hash2string(explode(strtoupper(md5('adl')), $sParam)[0], false), true) ?: []`: 複雜的解密和解析過程。它使用 `md5('adl')` 作為分隔符，然後對第一部分進行 `hash2string()` 解密，並將結果解析為 JSON 陣列。這個陣列包含了 `id` (user_past_password 的 sn)、`user_id` 和 `new_pass` 等信息。
- **數據驗證**: 
    - 檢查 `vParam` 是否有效。
    - 查詢 `user_past_password` 表，根據 `sn` 和 `user_id` 檢查記錄是否存在，並驗證 `new_pass` 是否與儲存的雜湊密碼匹配 (`password_verify()`)。
    - 檢查 Token 的時效性 (`(time() - (int) $vParam['time']) < 60 * 60`)，確保在 60 分鐘內進行確認。

### 4. 密碼更新邏輯
- **`UserChangePassword` 類別**: 實例化 `UserChangePassword` 類別，並傳入用戶 ID 和新密碼。
- **密碼重複檢查**: `!$oUserChangePassword->bPasswordExists` 檢查新密碼是否與用戶過去的密碼重複。
- **更新 `user_info` 表**: 如果驗證通過且密碼不重複，則更新 `user_info` 表中的 `pass` (雜湊密碼) 和 `viewpass` (清空)。
- **更新 `user_past_password` 表**: 調用 `oUserChangePassword->changePassword('self', (int) $vParam['id'])`，將 `user_past_password` 表中的 `confirmation` 字段設置為 `1`，表示該密碼變更已確認。

### 5. 訊息顯示與重定向
- 根據驗證和更新的結果，設置 `$sMsg` 變數，包含不同的提示訊息（例如「您的連結已失效！」、「密碼不可與前3次相同！」、「您的密碼變更成功！」）。
- 頁面最終會彈出一個 JavaScript `alert()` 顯示 `$sMsg`，然後重定向到網站首頁 (`/HomePage/home/`)。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php` (用於 `hash2string()`)
- `include/adp_core_security.php` (用於 `preventXss()` 和 `xss_filter()`)
- `classes/adp_core_class.php` (用於 `UserChangePassword` 類別)
- `dbh` (全局資料庫連接)
- `user_past_password` 資料庫表
- `user_info` 資料庫表

## 安全考量
- **Token 的安全性**: Token 的生成和解密過程非常複雜，需要確保其隨機性、不可預測性和防篡改性。`md5('adl')` 作為分隔符可能不是最安全的做法。
- **密碼雜湊**: 使用 `password_hash()` 進行密碼雜湊是良好的安全實踐。
- **密碼重複檢查**: 檢查新密碼是否與過去的密碼重複，增加了帳號安全性。
- **CSRF 防護**: 頁面開頭的 CSRF 檢查是必要的，但需要確保 `$_SESSION['csrf_token']` 的生成和管理是安全的。
- **錯誤訊息**: 錯誤訊息直接通過 JavaScript `alert()` 顯示，應確保其內容是安全的，防止 XSS 攻擊。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `hash2string()` 函數的具體實現，了解其解密邏輯和安全性。
- 深入研究 `UserChangePassword` 類別的具體實現，了解其密碼更新和歷史記錄管理方式。
- 了解 `user_past_password` 資料庫表的結構和用途。
- 重新評估 Token 生成和驗證的安全性，考慮使用更強大的加密和驗證機制。
