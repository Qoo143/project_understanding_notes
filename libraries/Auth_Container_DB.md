# Auth_Container_DB.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PEAR\Auth\Container\DB.php`

## 概述
`Auth_Container_DB.php` 檔案定義了 `Auth_Container_DB` 類別，它是 PEAR Auth 認證套件的一個儲存驅動，專門用於透過資料庫進行用戶認證。它負責處理與資料庫的連接、用戶憑證的獲取，以及用戶的增刪改查等操作。這個類別是 `auth_chk.php` 中 `Auth` 類別實際進行資料庫認證的底層實現。

## 關鍵依賴
- `Auth/Container.php`: Auth 容器的基礎類別。
- `DB.php`: PEAR DB 抽象層，用於資料庫互動。

## 屬性
- `options`: 包含容器的配置選項，例如 `table` (預設 'auth')、`usernamecol` (預設 'username')、`passwordcol` (預設 'password')、`dsn`、`db_fields`、`cryptType` (預設 'hash')、`db_options`、`db_where`、`auto_quote`。
- `db`: PEAR DB 連接物件。
- `dsn`: 資料庫連接的資料來源名稱。
- `activeUser`: 儲存當前活躍用戶的用戶名。

## 核心方法

### `__construct($dsn)`
- 構造函數，初始化容器。
- 設定預設選項並解析提供的 DSN。
- **注意**: 它不會立即建立資料庫連接，而是延遲到需要時才連接。

### `_connect($dsn)`
- 使用 PEAR DB 的 `DB::Connect()` 方法建立資料庫連接。
- 處理各種 DSN 格式（字串或陣列），並在失敗時返回 PEAR 錯誤物件。

### `_prepare()`
- 準備資料庫連接。如果尚未建立連接，則呼叫 `_connect()`。
- 如果啟用 `auto_quote`，則會引用表名和列名，以防止 SQL 注入。

### `query($query)`
- 確保資料庫連接已準備好後，執行 SQL 查詢。

### `_setDefaults()`
- 設定容器的預設配置選項。

### `_parseOptions($array)`
- 解析選項陣列，覆蓋預設值。

### `_quoteDBFields()`
- 根據 `auto_quote` 設定引用資料庫欄位名稱，用於構建 SQL 查詢。

### `fetchData($username, $password, $isChallengeResponse=false)`
- **核心認證方法**: 實際的用戶認證發生在這裡。
- **客製化**: 註解表明，原始的 PEAR DB 邏輯已被**修改**，直接使用 PDO 進行資料庫互動 (`GLOBAL $dbh;`)。這是一個重要的客製化，意味著它與專案現有的 PDO 設定整合。
- 它查詢 `user_info` 表（根據註解）以根據提供的 `$username` 獲取 `user_id` 和 `pass`。
- 然後呼叫 `verifyPassword()`（可能是父 `Auth_Container` 或 `Auth` 類別的方法）來比較提供的 `$password` 和獲取的密碼，並考慮 `cryptType`（例如 'md5'、'none'、'hash'）。
- 如果認證成功，它會使用 `_auth_obj->setAuthData()` 將額外的用戶數據（不包括 'pass' 和 'user_id'）儲存到 Auth Session 中。

### `listUsers()`
- 從配置的資料庫表中檢索用戶列表。

### `addUser($username, $password, $additional="")`
- 向資料庫添加新用戶。在儲存之前，它會對密碼應用配置的 `cryptType`。

### `removeUser($username)`
- 從資料庫中刪除用戶。

### `changePassword($username, $password)`
- 更改資料庫中用戶的密碼，並應用配置的 `cryptType`。

### `supportsChallengeResponse()`
- 根據 `cryptType` 指示此容器是否支援挑戰-響應認證。

### `getCryptType()`
- 返回配置的密碼加密類型。

### 安全考量
- `fetchData` 中使用 PDO（根據註解）是防止 SQL 注入的積極步驟，前提是 PDO 語句正確準備和執行。
- `_prepare()` 方法的 `auto_quote` 功能也有助於通過引用標識符來防止 SQL 注入。
- `cryptType` 選項允許不同的密碼雜湊機制，這提供了靈活性，但實際強度取決於所選方法（例如，'md5' 在今天通常被認為是弱密碼雜湊）。

## 與 `Auth.php` 和 `auth_chk.php` 的關係
- `auth_chk.php` 實例化 `Auth`（來自 `Auth.php`），而 `Auth` 又使用 `Auth_Container_DB`（此檔案）作為其儲存驅動。
- `Auth_Container_DB` 的 `fetchData` 方法由 `Auth` 的 `login` 方法呼叫，用於驗證資料庫中的用戶憑證。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `createAuthOptions()` 函數的定義和作用。
- 查找 `_IDLETIME` 和 `USER_OPERATOR` 等常量的定義。
- 繼續閱讀 `include/adp_core_security.php` 以了解 `preventXss` 和 `validateRedirect` 的具體實現。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能。
