# auth_chk.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\auth_chk.php`

## 概述
`auth_chk.php` 是因材網應用程式的**核心身份驗證和授權檢查檔案**。它在每個需要用戶登入的頁面（例如 `modules_new.php` 和 `turnpage.php`）被引入，負責驗證用戶的登入狀態、處理會話超時、檢查帳號和學校狀態，並根據權限進行導向。這個檔案確保了只有合法且有權限的用戶才能訪問系統資源。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `Auth/Auth.php`: 引入 PEAR Auth 庫，用於處理底層的身份驗證邏輯。
- `classes/adp_core_class.php`: 可能包含 `UserData` 類別的定義，用於處理用戶數據。

### 2. 身份驗證初始化與檢查
- **Auth 類別初始化**: 
    - `$vAuthParm = createAuthOptions('user_account')`: 創建 Auth 類別所需的參數，`user_account` 可能指明了用戶數據的來源（例如資料庫表名）。
    - `$auth = new Auth('DB', $vAuthParm, null)`: 實例化 Auth 類別，使用 `DB` 驅動進行身份驗證。
    - `$auth->start()`: 啟動身份驗證會話。
    - `$_SESSION['auth_status'] = $auth->getStatus()`: 記錄當前身份驗證狀態到 Session。
- **`$auth->checkAuth()`**: 這是最關鍵的檢查點，判斷用戶是否已登入且會話有效。如果驗證失敗，會設置 `$oCustomizeAuth` 的狀態為 `timeout` 或 `authfailed`。

### 3. 用戶數據處理
- **`$user_id = $auth->getUsername()`**: 獲取當前登入用戶的 ID。
- **`UserData` 物件管理**: 
    - 首次登入時，會根據 `$user_id` 實例化 `UserData` 物件，並將其序列化後存入 `$_SESSION['USERDATA']`。
    - 後續請求則從 Session 中反序列化 `$_SESSION['USERDATA']` 來獲取用戶數據，並將其轉換為物件和 `$_SESSION['user_data']`。這是一種將用戶數據緩存在 Session 中的常見做法。

### 4. 帳號與學校狀態檢查
- **帳號停用檢查**: 判斷 `$user_data->id_used == '0'`，如果帳號停用則阻止登入。
- **帳號授權終止檢查**: 判斷 `$user_data->access_level < 0`，如果權限等級小於 0 則阻止登入。
- **學校啟用檢查**: 判斷 `$user_data->organization_used == '0'`。如果學校未啟用，會嘗試更新 `organization` 表將其啟用，並呼叫 `creatHierselectList()` 函數（可能用於生成選單或權限相關的數據）。

### 5. 會話超時與維護模式檢查
- **會話超時檢查**: 判斷 `(time() - $_SESSION['_authsession']['timestamp'] > _IDLETIME)`，如果用戶閒置時間超過預設值 (`_IDLETIME`)，則自動登出。
- **系統維護時間檢查**: 判斷 `$SiteConfig['setMaintainTime'] == 1`，如果系統處於維護模式，則阻止登入。

### 6. 錯誤處理與重定向
- **`do...while(false)` 結構**: 用於在任何檢查失敗時跳出循環，並統一處理登入失敗的邏輯。
- **登入失敗處理**: 
    - 如果身份驗證失敗，會將錯誤訊息存入 `$_SESSION['out']`，並根據請求是否為 API 請求 (`turnpage.php`) 返回 JSON 錯誤訊息或重定向到 `logout_msg.php` 頁面。

## 關鍵引用與依賴
- `include/config.php` (定義 `_IDLETIME` 等常量)
- `Auth/Auth.php` (PEAR Auth 庫)
- `classes/adp_core_class.php` (可能包含 `UserData` 類別)
- `include/creatHierselectList.php` (在學校啟用時被呼叫)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `Auth/Auth.php` 庫的具體實現和配置方式。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能，特別是它如何影響系統的選單和權限。
- 查找 `createAuthOptions()` 函數的定義。
- 繼續閱讀 `include/adp_core_security.php` 以了解 `preventXss` 和 `validateRedirect` 的具體實現。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
