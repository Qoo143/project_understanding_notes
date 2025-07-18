# login_cooc.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\login_cooc.php`

## 概述
`login_cooc.php` 檔案是因材網與**酷課雲 (Cooc Cloud) SSO 整合的登入處理頁面**。它負責接收來自酷課雲認證服務的回調，獲取 Access Token 和用戶資訊，並根據用戶在因材網中的帳號狀態進行相應的處理，最終將用戶導向因材網的內部頁面或顯示錯誤訊息。該檔案是因材網實現酷課雲 SSO 的核心後端處理邏輯。

## 主要功能與職責

### 1. 引入核心依賴與初始化
- `session_start()`: 啟動 Session。
- `include/adp_core_function.php`: 引入核心通用函數。
- `include/config.php`: 應用程式的全局配置。
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserData` 等。
- `Auth/Auth.php`: PEAR Auth 庫，用於因材網內部認證。
- `modules/cooc/config_cooc.php`: 酷課雲相關配置，例如 `TOKEN_ENDPOINT`、`USERINFO_ENDPOINT` 等。

### 2. 認證回調處理與錯誤檢查
- 接收來自酷課雲認證服務的 `code` 和 `series` 參數。
- **基本驗證**: 檢查 `code` 和 `series` 是否存在。如果不存在，則顯示 JavaScript 警告並終止執行。
- **`code` 參數清理**: 對 `code` 參數進行嚴格的過濾和清理，包括 `strip_tags()`、`filter_var()` 和 `preg_match()`，以確保其安全性。

### 3. 與酷課雲 API 互動
- **獲取 Access Token (`getCOOCToken($sCode)`)**:向酷課雲的 `TOKEN_ENDPOINT` 發送請求，交換 `code` 以獲取 Access Token。
- 使用 `file_get_contents()` 發送 POST 請求。
- 處理 API 調用失敗和無效 JSON 響應的情況。
- **獲取用戶資訊 (`getUserInfo($sAccessToken)`)**:使用獲取的 Access Token 向酷課雲的 `USERINFO_ENDPOINT` 發送請求，獲取用戶的詳細資訊。
- 使用 cURL 發送 GET 請求，並進行 URL 驗證。
- 處理 cURL 請求失敗和無效 JSON 響應的情況。

### 4. 酷課雲用戶數據處理
- 從 `getUserInfo()` 返回的 `$arrUserData` 中提取用戶的 `uuid` (作為 `cooc_sub`)、姓名、Email、身份證字號等信息，並儲存到 `$_SESSION['cooc_data']`。
- **角色處理**: 遍歷 `$arrUserData['sso_role']`，特別是 `ldap` 來源的角色，提取用戶的姓名、身份證字號等。
- **學校角色處理**: 將酷課雲返回的學校角色信息 (`arrHandleData['role']`) 進行處理，提取學校代碼、類型、名稱、城市代碼、身份和角色詳細信息，並儲存到 `$_SESSION['cooc_data']['sso_role']`。
- **班級信息處理**: 對 `school_class_no`、`school_grade_year` 和 `school_seat_no` 進行數值轉換和驗證。

### 5. 因材網帳號處理與重定向
- **資料庫查詢**: 根據酷課雲返回的學校代碼，查詢因材網的 `organization` 表，獲取學校名稱和城市代碼。
- **重定向**: 成功處理後，將用戶重定向到 `roles_cooc.php`，這是一個用於選擇身份的頁面。

### 6. 錯誤處理與訊息顯示
- 如果在處理過程中發生錯誤（例如無法獲取 Access Token、無法獲取用戶資訊、無效的認證狀態），則會設置 `$msg` 變數，並在前端顯示相應的錯誤訊息。
- 錯誤訊息會根據 `$msg` 的值顯示不同的提示，例如「您的認證已失效」、「尚未開放您使用的登入方式」、「尚無因材網帳號」等。
- 錯誤訊息頁面包含一個自動跳轉計時器，或提供手動跳轉回酷課雲首頁的連結。

## 關鍵引用與依賴
- `include/adp_core_function.php`
- `include/config.php`
- `classes/adp_core_class.php`
- `Auth/Auth.php`
- `modules/cooc/config_cooc.php`
- `modules/cooc/learningcloud.class.php` (推測，實際為 `modules/learningcloud/learningcloud.class.php`)
- `roles_cooc.php` (重定向目標)
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **輸入驗證**: 對 `code` 參數進行了嚴格的清理和驗證，這是一個良好的安全實踐。
- **錯誤訊息**: 錯誤訊息直接顯示在前端，但沒有包含敏感的後端信息，這點做得不錯。
- **SSL/TLS**: 與酷課雲 API 的通訊應始終通過 HTTPS 進行，以確保數據的機密性和完整性。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/cooc/config_cooc.php` 中定義的酷課雲 API 端點和配置。
- 深入研究 `modules/cooc/learningcloud.class.php` 中 `openid` 類別的具體實現，了解其如何與酷課雲 API 進行互動。
- 了解 `roles_cooc.php` 如何處理從此頁面傳遞的 Session 數據，並引導用戶選擇身份。
- 了解 `Auth/Auth.php` 和 `classes/adp_core_class.php` 在此流程中的具體作用。
