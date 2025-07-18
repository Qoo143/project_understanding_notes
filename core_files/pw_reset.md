# pw_reset.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\pw_reset.php`

## 概述
`pw_reset.php` 檔案是一個**密碼重設頁面**。它接收一個包含重設 Token 的 GET 請求，驗證該 Token 的有效性和時效性。如果 Token 有效，則顯示一個表單供用戶輸入新密碼並確認。新密碼會通過 AJAX 請求發送到後端 (`turnpage_out.php`) 進行處理。該頁面是因材網密碼恢復流程中的關鍵一步。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `hash2string()` 等。
- `include/adp_core_security.php`: 核心安全函數，包含 `preventXss()` 等。
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserChangePassword` 等。

### 2. Token 驗證與時效性檢查
- **`$_GET['token']` 參數**: 頁面通過 `$_GET['token']` 參數接收一個密碼重設 Token。該 Token 經過 `preventXss()` 處理。
- **資料庫查詢**: 查詢 `user_recovery_record`、`user_info` 和 `user_status` 表，根據 Token 獲取用戶 ID、帳號、權限等級和 Token 的時間戳。
- **有效性檢查**: 
    - 如果 Token 無效或查詢不到相關信息，則終止腳本並顯示「連結已失效！」或「請不要任意輸入網址！」的訊息。
    - **時效性檢查**: 檢查 Token 的時間戳，如果超過 24 小時，則終止腳本並顯示「連結時效已過，請重新取得！」的訊息。

### 3. 密碼重設表單
- 如果 Token 驗證通過，頁面會顯示一個表單，包含兩個密碼輸入框 (`#password` 和 `#passwordChk`)，供用戶輸入新密碼和確認新密碼。
- 頁面還會顯示密碼設定的提示信息，包括長度要求和字符組合要求。

### 4. 前端密碼驗證
- **`checkData()` JavaScript 函數**: 在用戶點擊「送出」按鈕前，在前端對新密碼進行驗證，包括：
    - 密碼不得為空。
    - 密碼長度必須在 8 到 20 之間。
    - 兩次輸入的密碼必須相同。
    - 密碼不得包含非法字元。
    - 如果用戶不是學生，密碼必須包含數字、英文大寫、英文小寫、特殊符號中至少三種。
- **實時反饋**: 密碼輸入框會實時顯示密碼長度，並根據長度改變顏色。
- **顯示/隱藏密碼**: 提供一個眼睛圖標 (`#eye`)，用於切換密碼的顯示/隱藏。

### 5. AJAX 提交新密碼
- 當前端驗證通過後，點擊「送出」按鈕會觸發一個 AJAX POST 請求到 `turnpage_out.php`。
- 提交的數據包括 `router` (指向 `prodb_user_recovery`)、`act` (reset)、`t` (Token)、`password` 和 `passwordChk`。
- 後端處理成功後，前端會彈出「密碼重設成功！」的提示，並重定向到網站首頁。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php` (用於 `hash2string()`)
- `include/adp_core_security.php` (用於 `preventXss()`)
- `classes/adp_core_class.php` (用於 `UserChangePassword` 類別)
- `dbh` (全局資料庫連接)
- `user_recovery_record`, `user_info`, `user_status` 資料庫表
- `turnpage_out.php` (後端處理腳本)

## 安全考量
- **Token 時效性**: 24 小時的 Token 有效期是合理的，防止 Token 被長期濫用。
- **前端驗證**: 雖然前端進行了驗證，但後端 (`prodb_user_recovery.php`) 必須再次進行嚴格的驗證，以防止惡意請求。
- **密碼雜湊**: 密碼在儲存前會進行雜湊處理，這是良好的安全實踐。
- **CSRF 防護**: 雖然此頁面沒有明確的 CSRF Token 驗證，但其後端處理腳本 (`prodb_user_recovery.php`) 應該包含 CSRF 防護。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `prodb_user_recovery.php` 中 `reset` 動作的具體實現，了解其如何更新密碼和處理 `user_past_password`。
- 了解 `hash2string()` 函數的具體實現。
- 了解 `USER_STUDENT_GROUP` 常量的定義。
