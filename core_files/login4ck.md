# login4ck.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\login4ck.php`

## 概述
`login4ck.php` 檔案是因材網與**酷課雲 (Cooc Cloud) 或其他第三方服務整合登入**的處理頁面。它接收來自 `api_login.php` 或其他第三方認證服務的數據，判斷用戶是否已在因材網中擁有帳號。如果沒有，它會自動創建一個新帳號；如果已有帳號，則直接登入。該檔案處理數據解碼、帳號檢查、創建、登入和重定向，是第三方登入流程的關鍵環節。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_API.php`: 核心 API 引入檔案。
- `include/adp_core_function.php`: 核心通用函數。
- `include/security_function.php`: 安全相關函數，包含 `preventXss()` 等。

### 2. 數據接收與解碼
- 頁面通過 `$_GET['data']` 參數接收一個編碼過的數據字串。這個數據字串可能來自 `api_login.php`，包含了第三方服務返回的用戶信息。
- `base64_decode(str_replace("%2B", "+", $sLoginData))`: 對接收到的數據進行 `base64` 解碼，並處理 URL 編碼中 `+` 符號的特殊情況。
- `explode(_SPLIT_SYMBOL, $chc_token)`: 使用 `_SPLIT_SYMBOL` 分隔符將解碼後的字串分割成多個部分，其中第一部分是 JSON 格式的用戶信息，第二部分是時間戳。
- `json_decode($chc_token1[0])`: 將 JSON 格式的用戶信息解析為 PHP 物件。

### 3. 時間戳驗證
- 腳本會檢查當前時間與傳入時間戳的差值。如果超過 10 秒，則認為時間延宕過久，顯示錯誤訊息並終止執行。這有助於防止重放攻擊。

### 4. 帳號檢查與處理
- **帳號存在性檢查**: 查詢 `user_info` 表，判斷是否存在與第三方服務用戶 ID 相關聯的因材網帳號（使用 `190047-` 作為前綴，`190047` 可能是酷課雲的固定學校 ID）。
- **無帳號處理**: 如果因材網中沒有對應帳號 (`$data_count == '0'`)，則會自動創建一個新帳號。
    - 將接收到的用戶信息 (`chc_token1[0]`) 儲存到 `$_SESSION['ck_login_data']`。
    - 設置 `$_SESSION['creat_account'] = 'YES'`。
    - 調用 JavaScript 函數 `creat_account()`，將用戶信息傳遞給前端進行帳號創建的 AJAX 請求。
- **多個帳號處理**: 如果找到多個帳號 (`$data_count > '1'`)，則顯示錯誤訊息並終止執行。
- **單一帳號處理**: 如果找到一個帳號 (`$data_count == '1'`)，則獲取該帳號的密碼 (`viewpass`)，並使用 `Auth` 庫進行登入。

### 5. 帳號創建 (`creat_account` JavaScript 函數)
- **AJAX 請求**: 向 `modules/ck/prodb_insert_userinfo.php` 發送 AJAX POST 請求，提交用戶信息以創建新帳號。
- **參數**: 包含 `type_tab`、`user_id`、`name`、`email`、`sex`、`schoolid` (固定為 `190047`)、`city_code` (固定為 `19`)、`grade`、`classno`、`level`、`login_type`、`new_pass`、`guid` 和 `ck_sub`。
- **響應處理**: 根據後端返回的 `json_sys` 判斷是否成功。成功則顯示 SweetAlert 提示並重定向到 `login4ck.php?op=login4ck&creatacc=Y&data=NN`；失敗則顯示錯誤訊息。

### 6. 登入處理
- 使用 `Auth/Auth.php` 庫進行因材網內部登入。
- 設置 `$_POST['username']` 和 `$_POST['password']`，並啟動 `Auth` 認證。
- 成功登入後，設置 `$_SESSION['user_id']` 和 `$_SESSION['user_data']`。
- 設置 `$_SESSION['loginfrom'] = 'ck'`。
- 檢查用戶是否已填寫隱私權政策，如果沒有，則重定向到 `agreement.php`；否則重定向到 `modules.php?op=main`。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_API.php`
- `include/adp_core_function.php`
- `include/security_function.php`
- `Auth/Auth.php`
- `modules/ck/prodb_insert_userinfo.php` (後端帳號創建腳本)
- `agreement.php` (隱私權政策頁面)
- `modules.php` (主模組入口)
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **時間戳驗證**: 防止重放攻擊。
- **數據過濾**: 對 `$_GET['data']` 進行 `preventXss()` 處理，但需要確保解碼後的 JSON 數據在處理前也經過充分驗證。
- **固定學校 ID**: 使用固定學校 ID `190047` 創建帳號，這可能需要與酷課雲的整合邏輯相符。
- **密碼處理**: 在登入時，直接使用 `viewpass` 作為密碼進行認證，這可能存在安全風險，因為 `viewpass` 可能是明文或弱加密的密碼。應確保 `viewpass` 的安全性，或者使用更安全的密碼驗證機制。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/ck/prodb_insert_userinfo.php` 的具體實現，了解其如何處理帳號創建和數據儲存。
- 了解 `user_info` 和 `user_third_party` 資料庫表的結構和關聯。
- 了解 `Auth/Auth.php` 庫在登入流程中的具體作用。
- 重新評估 `viewpass` 的安全性，並考慮使用更安全的密碼驗證方式。

