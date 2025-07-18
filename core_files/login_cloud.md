# login_cloud.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\login_cloud.php`

## 概述
`login_cloud.php` 檔案是因材網與**教育雲端帳號 (OpenID Connect)** 整合的登入處理頁面。它負責接收來自教育雲端認證服務的回調，獲取 Access Token 和用戶資訊，並根據用戶在因材網中的帳號狀態（已存在、不存在、多個帳號等）進行相應的處理，最終將用戶導向因材網的內部頁面。

## 主要功能與職責

### 1. 引入核心依賴與初始化
- `include/adp_core_function.php`: 引入核心通用函數，例如 `safeRedirect()` 和 `getUserIP()`。
- `include/config.php`: 應用程式的全局配置。
- `modules/learningcloud/learningcloud_config.php`: 教育雲端相關配置，例如 `TOKEN_ENDPOINT`、`REDIR_URI0` 等。
- `modules/learningcloud/learningcloud.class.php`: 包含 `openid` 類別，用於處理 OpenID Connect 流程。
- 啟動 Session (`session_start()`)。

### 2. 認證回調處理與錯誤檢查
- 接收來自教育雲端認證服務的 `code`、`state` 和 `session_state` 參數。
- **基本驗證**: 檢查 `code` 和 `state` 是否存在。如果不存在，則顯示錯誤訊息並提供登出教育雲端的連結。
- **獲取 Access Token**: 使用 `openid` 類別的 `getAccessToken()` 方法，通過 `code` 和配置的端點獲取 Access Token 和 ID Token。
- **錯誤處理**: 如果無法獲取 Access Token，則顯示錯誤訊息並終止執行。
- **Session 儲存**: 將獲取的 `access_token` 和 `id_token` 儲存到 `$_SESSION` 中。

### 3. 獲取用戶資訊
- 使用 `openid` 類別的 `getUserinfo()` 方法，通過 Access Token 獲取教育雲端用戶的詳細資訊 (`userinfo`)，包括 `email`、`sub` (OpenID Connect 的用戶唯一標識) 和 `name`。
- 將教育雲端的 `sub` 儲存到 `userinfo['cloud_sub']`，以便區分。

### 4. 帳號綁定與重定向
- **個人資訊綁定**: 如果 `$_SESSION['combineAccount']` 為 `educloud`，則將教育雲端的 `sub` 和 `name` 儲存到 Session，並重定向到 `$_SESSION['cloud_redirect']` 指定的頁面。
- **綁定家長頁面**: 如果 `$_SESSION['combineFamily']` 為 `educloud`，則將教育雲端的 `sub`、`name` 和 `email` 儲存到 Session，並重定向到 `$_SESSION['cloud_redirect']` 指定的頁面。

### 5. 因材網帳號處理邏輯
- **帳號檢查**: 使用 `checkThirdPartyAccount()` 函數（推測定義在 `modules/UserManage/create_account.php`）檢查因材網中是否存在與教育雲端帳號相關聯的帳號（通過 `email` 和 `sub`）。
- **已存在帳號處理**: 
    - 如果找到一個匹配的帳號 (`count == 1` 或 `count2 == 1`)，則獲取該帳號的 `user_id`。
    - 實例化 `UserData` 類別，並將用戶數據儲存到 `$_SESSION`。
    - 使用 `Auth` 類別創建認證 Session。
    - 調用 `updateCloudSub()` 更新 `user_third_party` 表中的 `cloud_sub`。
    - 調用 `updateLoginStatus()` 更新 `user_status` 表中的登入頻率、時間和 IP。
    - 重定向到 `modules_new.php?op=main` 或 `$_SESSION['cloud_redirect']`。
- **多個帳號處理**: 如果通過 `email` 找到多個帳號 (`count > 1`)，則顯示錯誤訊息「帳號異常，請洽客服」。
- **帳號不存在處理**: 如果沒有找到匹配的帳號 (`count == 0 && count2 == 0`)，則自動創建一個新的訪客帳號。
    - 定義 `SCHOOL_ID` 為 `190046` (預設訪客學校 ID)。
    - 構造一個包含用戶信息的陣列 (`inputArray`)，用於創建帳號。
    - 設置 `createData` 陣列，包含創建帳號所需的各種參數，例如 `access_level` (預設為 `USER_STUDENT_DEMO2`)、`auth_stop` (30 天有效期)、`org_id` 等。
    - 調用 `createAccount()` 函數（推測定義在 `modules/UserManage/create_account.php`）創建帳號。
    - 如果創建成功，則調用 `updateThirdAccount()` 綁定教育雲端的 `cloud_sub`，並更新登入狀態。
    - 重定向到 `modules_new.php?op=main` 或 `$_SESSION['cloud_redirect']`。
- **錯誤訊息顯示**: 如果處理過程中發生錯誤，則顯示相應的錯誤訊息。

### 6. 輔助函數
- `updateThirdAccount($sUserID, $sSub)`: 更新 `user_third_party` 表中的 `cloud_sub`。
- `updateLoginStatus($user_id)`: 更新 `user_status` 表中的登入頻率、時間和 IP。
- `updateCloudSub($user_id, $cloud_sub)`: 更新 `user_third_party` 表中的 `cloud_sub`。
- `getAccount2UserID($user_account)`: 根據 `user_account` 從 `user_info` 表中獲取 `user_id`。
- `getUserIP()`: 獲取用戶的 IP 地址，並包含 CSRF 檢查。

## 關鍵引用與依賴
- `include/adp_core_function.php`
- `include/config.php`
- `modules/learningcloud/learningcloud_config.php`
- `modules/learningcloud/learningcloud.class.php`
- `classes/adp_core_class.php`
- `Auth/Auth.php`
- `modules/UserManage/create_account.php` (推測)
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **CSRF 防護**: `getUserIP()` 函數中包含 CSRF 檢查，但需要確保其在整個流程中被正確應用。
- **數據驗證**: 雖然從教育雲端獲取的數據是受信任的，但在處理和儲存到因材網資料庫之前，仍應進行適當的驗證和清理。
- **密碼處理**: 在創建新帳號時，密碼是隨機生成的 (`password_hash(time(), PASSWORD_DEFAULT)`)，這是一個良好的安全實踐。
- **錯誤訊息**: 錯誤訊息中包含行號 (`__LINE__`)，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/learningcloud/learningcloud.class.php` 中 `openid` 類別的具體實現，了解其如何與教育雲端 OpenID Connect 服務進行互動。
- 深入研究 `modules/UserManage/create_account.php` 中 `checkThirdPartyAccount()` 和 `createAccount()` 函數的具體實現。
- 了解 `config.php` 中與教育雲端相關的配置。
- 了解 `Auth/Auth.php` 和 `classes/adp_core_class.php` 在此流程中的具體作用。
