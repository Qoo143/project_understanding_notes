# loginAuth.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\loginAuth.php`

## 概述
`loginAuth.php` 檔案是因材網與 **Google 登入整合的後端驗證和帳號處理腳本**。它接收來自前端 `googleLogin.php` 的 Google ID Token，向 Google API 驗證該 Token 的有效性，然後根據用戶在因材網中的帳號狀態（已存在、不存在、多個帳號等）進行相應的處理，包括自動創建訪客帳號、綁定第三方帳號，並最終將用戶導向因材網的內部頁面。該檔案以 JSON 格式返回響應。

## 主要功能與職責

### 1. 引入核心依賴與初始化
- `include/config.php`: 應用程式的全局配置，可能包含 `USER_STUDENT_GROUP` 等常量。
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserData` 等。
- `Auth/Auth.php`: PEAR Auth 庫，用於因材網內部認證。
- `modules/UserManage/create_account.php`: 用於創建新帳號的函數。
- 啟動 Session (`session_start()`)。
- 定義 `SCHOOL_ID` 為 `190046` (預設訪客帳號的學校 ID)。

### 2. Google ID Token 驗證
- 接收來自前端的 JSON 格式的 `id_token`。
- 使用 cURL 向 `https://oauth2.googleapis.com/tokeninfo` 發送請求，驗證 `id_token` 的有效性。
- 如果 Google API 返回錯誤（例如 `invalid_token`），則終止腳本並返回錯誤訊息。
- 解析 Google API 返回的響應，獲取用戶的 `sub` (Google 用戶唯一 ID) 和 `email`。

### 3. 帳號檢查與處理
- **檢查 `user_third_party` 表**: 查詢 `user_third_party` 表，根據 Google 返回的 `sub` 檢查是否已存在綁定的因材網帳號。
- **檢查 `user_info` 表**: 查詢 `user_info` 表，根據 Google 返回的 `email` 檢查是否已存在因材網帳號。
- **處理邏輯**: 根據上述檢查結果，執行不同的處理流程：
    - **已綁定帳號 (`count == 1`)**: 
        - 重新生成 Session ID (`session_regenerate_id(true)`)。
        - 實例化 `UserData` 類別，並將用戶數據儲存到 `$_SESSION`。
        - 使用 `Auth` 類別創建認證 Session。
        - 調用 `updateLoginStatus()` 更新 `user_status` 表中的登入頻率、時間和 IP。
        - 返回成功狀態和重定向 URL (`modules_new.php?op=main`)。
    - **未綁定但存在帳號 (`count == 0 && count2 >= 1`)**: 
        - 返回錯誤訊息「您已經有帳號: [email]，請從一般登入進入因材網」。
    - **帳號異常 (`count > 1`)**: 
        - 返回錯誤訊息「帳號異常，請洽客服」。
    - **無帳號 (`count == 0 && count2 == 0`)**: 
        - **自動創建訪客帳號**: 
            - 設置訪客帳號的有效期為 30 天。
            - 構造一個包含用戶信息的陣列 (`inputArray`)，用於創建帳號。
            - 設置 `createData` 陣列，包含創建帳號所需的各種參數，例如 `access_level` (預設為 `USER_STUDENT_DEMO2`)、`org_id` (固定為 `SCHOOL_ID`) 等。
            - 調用 `createAccount()` 函數創建帳號。
            - 重新生成 Session ID (`session_regenerate_id(true)`)。
            - 調用 `updateThirdAccount()` 綁定 Google `sub`。
            - 調用 `updateLoginStatus()` 更新登入狀態。
            - 返回成功狀態和重定向 URL (`modules_new.php?op=main`)。
- **錯誤訊息返回**: 所有錯誤訊息都以 JSON 格式返回，包含 `status` 和 `message`。

### 4. 輔助函數
- `updateThirdAccount($sUserID, $sSub)`: 將 Google `sub` 綁定到 `user_third_party` 表中的因材網 `user_id`。
- `updateLoginStatus($user_id)`: 更新 `user_status` 表中的登入頻率、時間和 IP。
- `getRedirectURL($user_id)`: 根據用戶的 `access_level` 和隱私權協議簽署狀態，決定重定向 URL（如果不是學生且未簽署協議，則重定向到 `agreement.php`）。

## 關鍵引用與依賴
- `include/config.php`
- `classes/adp_core_class.php`
- `Auth/Auth.php`
- `modules/UserManage/create_account.php`
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **ID Token 驗證**: 腳本向 Google API 驗證 ID Token，這是確保登入安全性的關鍵步驟。
- **Session ID 重新生成**: 在成功登入和創建帳號後重新生成 Session ID，有助於防止 Session 固定攻擊。
- **密碼處理**: 在自動創建帳號時，密碼是隨機生成的 (`password_hash(time(), PASSWORD_DEFAULT)`)，這是一個良好的安全實踐。
- **錯誤訊息**: 錯誤訊息中包含行號 (`__LINE__`)，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/UserManage/create_account.php` 中 `createAccount()` 函數的具體實現，了解其帳號創建邏輯。
- 了解 `user_third_party`、`user_info` 和 `user_status` 資料庫表的結構和關聯。
- 了解 `Auth/Auth.php` 庫在登入流程中的具體作用。
- 了解 `agreement.php` 的功能和觸發條件。
