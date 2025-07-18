# prodb_user_recovery.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\prodb_user_recovery.php`

## 概述
`prodb_user_recovery.php` 檔案是一個**後端處理腳本**，專門用於處理**用戶帳號恢復和密碼重設**的請求。它接收來自前端的 AJAX 請求，驗證用戶提供的資訊（如身份、學校、姓名、Email、身份證字號等），並在驗證成功後發送密碼重設郵件。該檔案還處理密碼重設連結的驗證和新密碼的更新。

## 主要功能與職責

### 1. 引入核心依賴與常量定義
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserData` 等。
- `classes/phpmailer_sendmail.php`: 用於發送郵件的函數。
- `classes/securimage/securimage.php`: 用於驗證碼的類別。
- 定義了多個常量，用於表示不同的用戶身份 (`IDENTITY_STUDENT`, `IDENTITY_TEACHER` 等) 和身份驗證方法 (`ID_VERIFICATION_METHOD_IDNUM`, `ID_VERIFICATION_METHOD_EMAIL`)。
- `$_POST = preventXss($_POST)`: 對所有 POST 數據進行 XSS 防護。

### 2. 請求處理 (`switch ($_POST['act'])`)
- 腳本根據 `$_POST['act']` 參數來執行不同的操作：
    - **`getOrg`**: 獲取學校列表。根據城市名稱查詢 `organization` 和 `city_area` 表，返回學校的郵遞區號、區域、ID 和名稱。學校 ID 會被 `string2hash()` 加密。
    - **`getGradeClassList`**: 獲取班級列表。根據學校 ID 和學期查詢 `user_info` 表，返回年級和班級信息。使用 `fn_get_class_full_name_by_semester` 函數獲取班級全名。
    - **`recover`**: 處理帳號恢復請求。這是最複雜的部分。
    - **`reset`**: 處理密碼重設請求。

### 3. 帳號恢復邏輯 (`recover` 動作)
- **參數檢查**: 呼叫 `checkParameter($_POST)` 驗證所有傳入參數的格式和有效性。
- **驗證碼和 Token 檢查**: 
    - 驗證 `token_aial` 是否有效（與當前時間相關的 MD5 雜湊值）。
    - 使用 `Securimage` 驗證碼進行檢查。
- **帳號查詢**: 根據用戶選擇的身份 (`identity`) 和提供的資訊（學校、年級、班級、姓名、Email、身份證字號），查詢 `user_info` 和 `user_status` 表，查找匹配的帳號。
- **結果處理**: 
    - **找到單一帳號 (`$iCount === 1`)**: 
        - 檢查 `user_recovery_record` 表，判斷是否在 1 分鐘內重複請求密碼重設。
        - 如果沒有重複請求，則呼叫 `sendEmail()` 發送密碼重設郵件，並更新 `user_recovery_record` 表。
    - **找到多個帳號 (`$iCount >= 2`)**: 
        - 根據用戶身份提示用戶聯繫教師、校管或客服協助處理重複帳號。
    - **查無帳號**: 顯示「查無帳號！」訊息。
- **錯誤訊息返回**: 所有錯誤訊息都以 JSON 格式返回。

### 4. 密碼重設邏輯 (`reset` 動作)
- **參數檢查**: 呼叫 `checkParameter($_POST)` 驗證新密碼的格式和有效性。
- **Token 驗證**: 根據傳入的 `token` 查詢 `user_recovery_record` 表，獲取用戶信息。
- **密碼更新**: 
    - 實例化 `UserChangePassword` 類別，並調用其 `changePassword('reset')` 方法更新密碼。
    - 如果密碼更新成功，則更新 `user_recovery_record` 表中的 `confirmation` 字段為 `1`。
- **錯誤訊息返回**: 所有錯誤訊息都以 JSON 格式返回。

### 5. 輔助函數
- **`checkParameter($vParameter, $sUserAccount = null, $iAccessLevel = null)`**: 嚴格驗證輸入參數的格式和內容，包括身份證字號、Email、密碼複雜度等。這是防止惡意輸入的關鍵防線。
- **`sendEmail($vUserInfo, $sEmail)`**: 
    - 生成一個唯一的 Token，並儲存到 `user_recovery_record` 表中。
    - 構造密碼重設郵件內容，包含重設連結 (`pw_reset.php?token=...`)。
    - 呼叫 `phpmailer_sendmail()` 發送郵件。

## 關鍵引用與依賴
- `classes/adp_core_class.php`
- `classes/phpmailer_sendmail.php`
- `classes/securimage/securimage.php`
- `dbh`, `dbh_slave` (全局資料庫連接)
- `user_info`, `user_status`, `user_recovery_record`, `lecturer_list`, `city`, `city_area` 資料庫表
- `pw_reset.php` (密碼重設頁面)

## 安全考量
- **輸入驗證**: `checkParameter()` 函數提供了非常詳細和嚴格的輸入驗證，這是防止各種攻擊（如 SQL 注入、XSS、弱密碼）的關鍵。
- **Token 有效期**: 密碼重設 Token 的有效期（1 分鐘內不得重複請求）有助於防止暴力破解。
- **密碼雜湊**: 密碼重設時使用 `UserChangePassword` 類別，應確保其內部使用安全的密碼雜湊算法。
- **錯誤訊息**: 錯誤訊息中包含行號，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `classes/phpmailer_sendmail.php` 的具體實現，了解郵件發送的配置和安全性。
- 深入研究 `classes/securimage/securimage.php` 的具體實現，了解驗證碼的生成和驗證機制。
- 深入研究 `classes/adp_core_class.php` 中 `UserData` 和 `UserChangePassword` 類別的具體實現。
- 了解 `pw_reset.php` 如何接收和處理密碼重設 Token。
