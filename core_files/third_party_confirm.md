# third_party_confirm.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\third_party_confirm.php`

## 概述
`third_party_confirm.php` 檔案是一個**後端處理腳本**，主要用於**確認第三方帳號（例如家長/大學伴與學生）的綁定**。它接收一個帶有加密 Token 的請求，該 Token 通常來自認證郵件中的連結。腳本會驗證 Token 的有效性、時效性，並根據郵件類型執行不同的帳號綁定或更新操作。頁面最終會顯示綁定結果並自動跳轉回首頁。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `modules/UserManage/combine_account.php`: 包含帳號綁定和修改相關的函數。

### 2. CSRF 檢查
- 如果請求方法為 POST，則檢查 `$_GET['csrf_token']` 是否與 `$_SESSION['csrf_token']` 匹配。這是一個基本的 CSRF 防護機制。

### 3. Token 驗證與數據解碼
- 頁面通過 `$_GET['token']` 參數接收一個加密的 Token。
- **時效性驗證**: Token 的解密鍵 (`$sMd5Keyen1`, `$sMd5Keyen2`) 包含當前時間和前一小時的 MD5 雜湊值，這使得 Token 具有時效性（約 1 小時）。
- **數據解碼**: Token 經過 `htmlspecialchars()` 處理後，使用動態生成的 MD5 鍵作為分隔符進行 `explode()`，然後對第一部分進行 `base64_decode()` 和 `json_decode()`，解析出 `parent` (家長/大學伴用戶 ID)、`std` (學生用戶 ID) 和 `mail_type` (郵件類型，'A' 或 'B')。

### 4. 帳號綁定邏輯
- **重複綁定檢查**: 呼叫 `combineAlready($fuser_id, $suser_id)` 函數檢查該家長/學生組合是否已綁定。
- **根據郵件類型執行操作**: 
    - **`mail_type == 'A'`**: 直接呼叫 `combine_family($fuser_id, $suser_id)` 進行綁定。
    - **`mail_type == 'B'`**: 執行更複雜的帳號更新和綁定流程。這可能用於處理訪客帳號轉正或帳號變更後再綁定的情況。它會更新 `user_info` 表中的 `user_account` 和 `email`，並更新 `user_account_history` 和 `user_status` 表，最後再呼叫 `combine_family()`。

### 5. 資料庫互動
- 腳本與 `user_family` (親子綁定關係)、`user_info` (用戶基本信息)、`user_account_history` (用戶帳號變更歷史) 和 `user_status` (用戶狀態) 等資料庫表進行互動。

### 6. 前端顯示與自動跳轉
- 頁面包含 HTML 和 JavaScript，用於顯示綁定結果（成功、失敗、驗證過期、已綁定等）並自動倒計時跳轉回首頁 (`/HomePage/home/`)。
- 不同的結果會顯示不同的圖標和顏色。

### 7. 輔助函數
- `combineAlready($fuser_id, $suser_id)`: 檢查家長和學生是否已綁定。
- 檔案中包含一些註釋掉的函數和邏輯，可能代表過去的實現或待開發的功能。

## 關鍵引用與依賴
- `include/config.php`
- `modules/UserManage/combine_account.php` (包含 `combine_family`, `change_history`, `change_account_info`, `change_account_status` 等函數)
- `$_SESSION['csrf_token']`
- `dbh` (全局 PDO 資料庫連接)

## 安全考量
- **CSRF 防護**: 存在 CSRF 檢查，但其依賴 `$_GET['csrf_token']` 進行 POST 請求的驗證，這在實踐中較為少見，且 `$_GET` 參數容易被記錄。應確保 CSRF Token 的生成和驗證機制足夠健壯。
- **Token 安全性**: Token 的時效性依賴於 `md5(date('Y-m-d H'))`，`md5` 並非加密演算法，且時間戳的粒度較大，可能存在被猜測或重放的風險。Token 透過 `$_GET` 傳遞也增加了被記錄的風險。
- **數據驗證**: 雖然對 `$_GET['token']` 進行了 `htmlspecialchars`，但解碼後的數據 (`$vCombineData`) 在傳遞給 `combine_account.php` 中的函數之前，需要確保這些函數內部對數據進行了充分的驗證和清理，以防止 SQL 注入或其他惡意輸入。
- **錯誤訊息**: 錯誤訊息中包含行號 (`__LINE__`)，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/UserManage/combine_account.php` 中相關函數的具體實現，特別是它們如何處理數據驗證和資料庫操作。
- 重新評估 Token 生成和驗證的安全性，考慮使用更強大的加密和驗證機制。
- 檢查 CSRF 防護機制，確保其在所有請求類型中都足夠安全。
- 了解 `config.php` 中 `_SPLIT_SYMBOL` 的定義。
