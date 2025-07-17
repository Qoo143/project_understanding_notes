# Auth.php (PEAR Auth 核心) 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PEAR\Auth.php`

## 概述
`Auth.php` 是 PEAR Auth 庫的核心檔案，提供了建立身份驗證系統所需的方法和邏輯。它處理用戶的登入、登出、會話管理、以及一些基本的安全檢查。這個類別是 `auth_chk.php` 檔案中 `Auth` 類別的實際實現。

## 主要功能與職責

### 1. 常量定義
定義了多個常量來表示不同的身份驗證狀態和錯誤碼，例如：
- `AUTH_IDLED`: 會話閒置超時
- `AUTH_EXPIRED`: 會話過期
- `AUTH_WRONG_LOGIN`: 用戶名/密碼錯誤
- `AUTH_METHOD_NOT_SUPPORTED`: 容器方法不支持
- `AUTH_SECURITY_BREACH`: 安全漏洞（如 IP 或 User Agent 變化）
- `AUTH_CALLBACK_ABORT`: 回調函數中止會話
- `AUTH_WRONG_SECURIMAGE`: 驗證碼錯誤
- `AUTH_WRONG_TOKEN`: Token 錯誤
- `AUTH_REJECT_LOGIN`: 登入被拒絕（例如連續錯誤次數過多）
- `AUTH_LOG_INFO`, `AUTH_LOG_DEBUG`: 日誌級別
- `AUTH_ADV_IPCHECK`, `AUTH_ADV_USERAGENT`, `AUTH_ADV_CHALLENGE`: 高級安全檢查的類型

### 2. 屬性 (Properties)
- `expire`: 認證會話的總生命週期（秒），0 表示永不過期。
- `idle`: 最大閒置時間（秒），每次 `checkAuth()` 被呼叫時會刷新。
- `storage`: 儲存驅動物件（例如 `Auth_Container_DB`），用於實際的用戶認證和數據存取。
- `loginFunction`, `loginCallback`, `loginFailedCallback`, `logoutCallback`: 用於自定義登入/登出流程的回調函數。
- `_sessionName`: 儲存 Auth 相關數據的 Session 變數名稱（預設為 `_authsession`）。
- `advancedsecurity`: 控制是否啟用高級安全檢查（IP 檢查、User Agent 檢查、Challenge Response）。
- `_postUsername`, `_postPassword`: POST 請求中用戶名和密碼的欄位名稱。
- `session`, `server`, `post`, `cookie`: 對 PHP 超級全局變數的引用，方便內部存取。
- `regenerateSessionId`: 是否在每次 `start()` 呼叫時重新生成 Session ID。
- `arrPassAccouunt`: 特殊邏輯帳號列表，這些帳號可以跳過某些驗證。
- `COOKIE_OPTIONS`: 定義了 Session Cookie 的選項，包括 `secure` 和 `httponly`。

### 3. 核心方法

#### `__construct($storageDriver, $options, $loginFunction, $showLogin)`
- 構造函數，初始化 Auth 類別。
- 設定 Session Cookie 選項，特別是根據 `$_SERVER['HTTP_HOST']` 判斷是否為 `edu.tw` 域名來設定 `secure`、`httponly` 和 `samesite` 屬性。
- 處理 Session 的啟動，如果 Session 未啟動會嘗試啟動，並包含錯誤處理。
- 將 PHP 超級全局變數（`$_SESSION`, `$_SERVER`, `$_POST`, `$_COOKIE`）的引用賦值給內部屬性。
- 根據 `$storageDriver` 和 `$options` 實例化儲存驅動。

#### `applyAuthOptions(&$options)`
- 應用 Auth 類別的配置選項，例如 `sessionName`, `allowLogin`, `postUsername`, `postPassword`, `advancedsecurity`, `enableLogging`, `regenerateSessionId`。

#### `_loadStorage()`
- 延遲載入儲存驅動，只有在需要時才實例化。

#### `_factory($driver, $options)`
- 根據驅動名稱和選項，創建並返回對應的儲存驅動物件（例如 `Auth_Container_DB`）。它會引入 `Auth/Container/{driver}.php` 檔案。

#### `assignData()`
- 從 POST 請求中獲取用戶名和密碼，並賦值給內部屬性。

#### `start()`
- 啟動身份驗證流程。
- 如果 `regenerateSessionId` 為 true，會重新生成 Session ID。
- 呼叫 `assignData()` 獲取用戶名和密碼。
- 如果用戶未認證且允許登入，則呼叫 `login()` 方法。

#### `login()`
- 處理用戶登入邏輯。
- **登入失敗次數鎖定**: 查詢 `user_login_log_status` 表，如果連續錯誤次數達到 5 次，則鎖定帳號 15 分鐘。
- **驗證碼檢查**: 如果用戶名不在 `arrPassAccouunt` 中且未通過 OpenID 認證，會進行 `Securimage` 驗證碼檢查。
- **Token 檢查**: 檢查 `token_aial` 是否有效，這是一個自定義的 Token 驗證機制，用於防止惡意登入。
- 呼叫儲存驅動的 `fetchData()` 方法進行實際的用戶名/密碼驗證。
- 成功登入後，呼叫 `setAuth()` 設置 Session 數據，並觸發 `loginCallback`。
- 登入失敗時，設置 `status` 為對應的錯誤碼（`AUTH_WRONG_LOGIN`, `AUTH_WRONG_TOKEN`, `AUTH_WRONG_SECURIMAGE`, `AUTH_REJECT_LOGIN`），並觸發 `loginFailedCallback`。
- 如果登入失敗且 `showLogin` 為 true，會顯示登入表單（可能通過 `loginFunction` 或 `Auth_Frontend_Html::render`）。

#### `setAuth($username)`
- 在用戶成功登入後，設置 Session 變數以標記用戶已認證。
- 記錄用戶的 IP 地址、User Agent 和 `HTTP_X_FORWARDED_FOR` 到 Session，用於高級安全檢查。
- 生成 `challengekey` 和 `challengecookie`，用於 Challenge Response 安全機制。

#### `checkAuth()`
- 檢查當前會話是否有效。
- **會話過期檢查**: 根據 `expire` 屬性判斷會話是否過期。
- **閒置時間檢查**: 根據 `idle` 屬性判斷是否閒置超時。
- **高級安全檢查**: 如果 `advancedsecurity` 啟用，會檢查 IP 地址、User Agent 和 Challenge Cookie 是否發生變化，如果變化則視為安全漏洞並強制登出。
- 呼叫 `checkAuthCallback` 進行額外的自定義檢查。
- 如果所有檢查通過，則返回 `true`。

#### `logout()`
- 登出用戶，清除 Session 中的認證信息，並觸發 `logoutCallback`。
- 呼叫 `cleanSessionID()` 清除 `user_status` 表中的 `unique_id`。

#### `getUsername()`
- 獲取當前登入用戶的用戶名。
- **注意**: 這裡有一個自定義的邏輯，它會從 `user_info` 表中根據 Session 中的用戶名查詢 `user_id` 並返回。這表明系統的用戶名和用戶 ID 可能不是直接對應的。

#### `cleanSessionID()`, `checkUniqueID()`, `updateUserStatusUniqueID()`
- 這些方法用於處理多開視窗重複登入的問題，通過在 `user_status` 表中維護 `unique_id` 來確保單一會話。

### 4. 安全相關
- **Session 管理**: 嚴格的 Session 設置（`httponly`, `secure`, `samesite`, `gc_maxlifetime`），以及 Session 劫持和固定攻擊的防護。
- **登入保護**: 登入失敗次數限制、驗證碼、自定義 Token 檢查。
- **高級安全檢查**: IP 和 User Agent 變化檢測，以及 Challenge Response 機制。
- **日誌記錄**: 使用 `log()` 方法記錄 Auth 系統的行為，有助於安全審計和問題排查。

## 關鍵引用與依賴
- `Auth/Container/{driver}.php` (例如 `Auth/Container/DB.php`)
- `PEAR.php` (用於錯誤處理)
- `securimage/securimage.php` (用於驗證碼)
- `_IDLETIME` (常量，可能定義在 `include/config.php`)
- `dbh` (全局資料庫連接)
- `user_login_log_status` 表
- `user_info` 表
- `user_status` 表

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `Auth/Container/DB.php`，了解它是如何與資料庫互動進行用戶認證的。
- 了解 `createAuthOptions()` 函數的定義和作用。
- 查找 `_IDLETIME` 和 `USER_OPERATOR` 等常量的定義。
- 繼續閱讀 `include/adp_core_security.php` 以了解 `preventXss` 和 `validateRedirect` 的具體實現。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能。
