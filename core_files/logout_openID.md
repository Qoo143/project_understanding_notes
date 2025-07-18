# logout_openID.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\logout_openID.php`

## 概述
`logout_openID.php` 檔案是一個**處理 OpenID 登出或取消登入流程**的頁面。它負責清理 Session 數據，並根據不同的情境向用戶顯示訊息，同時提供返回因材網或登出 OpenID 服務的連結。該檔案主要用於引導用戶完成 OpenID 相關的登出操作。

## 主要功能與職責

### 1. 引入核心依賴與會話清理
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數。
- `include/security_function.php`: 安全相關函數。
- `modules/OpenID/config_openid.php`: OpenID 相關配置，包含 `OPENID_WEB_STIE_REDIRECT` 等常量。
- **會話清理**: 無論何種情況，都會銷毀資料庫連接 (`unset($dbh)`)，啟動 Session (`session_start()`)，並銷毀當前 Session (`session_destroy()`)。

### 2. 登出情境處理
- **`if ($_GET['act'] == 'logout')`**:
    - 如果 `act` 參數為 `logout`，則顯示訊息「您已申請成功，待 校管理者 審核您的資料後，會再以Email通知，謝謝！」。
    - 重定向連結為 `index_AIAL2.php`。
    - 這種情況可能發生在用戶申請帳號後，等待審核的提示頁面。
- **其他情況 (取消登入)**:
    - 預設情況下，如果 `act` 參數不是 `logout`，則顯示訊息「您已取消登入因材網，若要登出請按「登出OpenID」至教育體系單一簽入服務登出！」。
    - **登出 OpenID 連結**: `sHref1` 指向 `https://oidc.tanet.edu.tw/auth-logout`，用於登出教育體系單一簽入服務。
    - **返回因材網連結**: `sHref2` 指向 `OPENID_WEB_STIE_REDIRECT`，並根據 `$_SERVER['DOCUMENT_ROOT']` 是否包含 `aialtest` 來判斷是否添加 `/aialtest` 路徑，用於返回因材網首頁。

### 3. 前端結構與樣式
- **HTML 結構**: 頁面包含一個居中的 `div` 元素 (`class-list2-box`)，用於顯示提示訊息、圖標和兩個按鈕（登出 OpenID 和返回因材網）。
- **CSS 引入**: 引入 `css/cms-index.css`，並包含一些內聯 `<style>` 標籤，用於定義頁面的佈局和樣式。
- **Meta 標籤**: 設置 `noindex` 和 `googlebot`，防止頁面被搜索引擎索引。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `include/security_function.php`
- `modules/OpenID/config_openid.php`
- `index_AIAL2.php` (重定向目標)
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **會話清理**: 徹底銷毀 Session 是良好的安全實踐。
- **單點登出**: 提供登出 OpenID 服務的連結，有助於實現單點登出。
- **訊息顯示**: 顯示的訊息來自 PHP 變數，應確保其內容是安全的，防止 XSS 攻擊。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `modules/OpenID/config_openid.php` 中 `OPENID_WEB_STIE_REDIRECT` 的具體配置。
- 了解 `index_AIAL2.php` 的功能。
- 了解 `adp_core_function.php` 和 `security_function.php` 中可能包含的通用函數。
