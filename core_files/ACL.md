# ACL.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\ACL.php`

## 概述
`ACL.php` 檔案主要負責應用程式的**存取控制列表 (ACL)** 功能，特別是處理用戶身份不符時的重定向。它定義了一系列函數，用於檢查當前用戶的 `access_level` 是否符合特定頁面或模組所需的身份，如果不符合，則將用戶重定向回首頁。

## 主要功能與職責

### 1. 引入依賴
- `include/config.php`: 引入全局配置，其中包含 `_HTTPS_SITE_LIST` 和各種 `USER_` 權限組常量。

### 2. 核心重定向函數 `redirectHompageifIncompatible($identity, $bIsNotModules = false)`
- **功能**: 檢查當前用戶的 `access_level` 是否在 `$identity` 陣列中。如果不在，則將用戶重定向到首頁。
- **參數**:
    - `$identity` (array): 允許訪問的 `access_level` 列表（例如 `[USER_STUDENT, USER_TEACHER]`）。
    - `$bIsNotModules` (boolean): 標誌，指示當前請求是否通過 `modules.php` 處理。這會影響重定向 URL 的構建方式。
- **邏輯**:
    - **URL 構建**: 根據 `$_SERVER['SERVER_PORT']` 判斷使用 `http://` 或 `https://`。同時，會檢查 `$_SERVER['HTTP_HOST']` 是否在 `_HTTPS_SITE_LIST` 中，以確保構建安全的 URL。
    - **身份檢查**: 使用 `in_array($_SESSION['user_data']->access_level, $identity)` 判斷用戶權限。如果 `$bIsNotModules` 為 `true`，則會從 `$_SESSION['USERDATA']` 中反序列化用戶數據來獲取 `access_level`。
    - **安全重定向**: 在執行 `header("Location:$url_new")` 之前，會呼叫 `validateRedirect($url)` 函數。這是一個重要的安全措施，用於驗證重定向 URL 的安全性，防止開放重定向漏洞。
    - **測試區特例**: 如果 `MY_SHORT_SERVER_IP` 為 `0836`（可能代表測試環境），則會添加 `/aialtest` 路徑前綴。

### 3. 身份特定重定向輔助函數
- 為了方便使用，`ACL.php` 定義了一系列包裝函數，這些函數內部呼叫 `redirectHompageifIncompatible()` 並傳入預定義的用戶權限組常量：
    - `redirectHompageIsNotStudent()`: 學生、學生(展示)、展示學生 (`USER_STUDENT_GROUP`)
    - `redirectHompageIsNotParent()`: 家長、大學伴 (`USER_PARENTS_GROUP`)
    - `redirectHompageIsNotTeacher()`: 教師、講師、校長、主任 (`USER_TEACHER_GROUP`)
    - `redirectHompageIsNotShoolAdminGroup()`: 主任、校長 (`USER_SCHOOL_ADMIN_GROUP`)
    - `redirectHompageIsNotShoolAdmin()`: 校管 (`[USER_SCHOOL_ADMIN]`)
    - `redirectHompageIsNotUserOperator()`: 試題維護人員 (`[USER_OPERATOR]`)
    - `redirectHompageIsNotAdmin()`: 系統管理者 (`[USER_ADMIN]`)

## 關鍵引用與依賴
- `include/config.php` (用於 `_HTTPS_SITE_LIST` 和 `USER_` 權限組常量)
- `validateRedirect()` (可能定義在 `include/adp_core_function.php` 或 `include/adp_core_security.php`)
- `$_SESSION['user_data']` 或 `$_SESSION['USERDATA']` (用戶會話數據)

## 安全考量
- **強制重定向**: 確保只有具有適當權限的用戶才能訪問特定頁面，增強了應用程式的安全性。
- **`validateRedirect()` 的使用**: 這是防止開放重定向漏洞的關鍵。確保此函數的實現是健壯和安全的。
- **`_HTTPS_SITE_LIST` 的配置**: 正確配置此列表對於確保在 HTTPS 環境下構建正確的 URL 至關重要。

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `validateRedirect()` 函數的具體定義位置和實現細節。
- 了解 `config.php` 中 `_HTTPS_SITE_LIST` 和各種 `USER_` 權限組常量的具體定義。
- 了解 `$_SESSION['user_data']` 和 `$_SESSION['USERDATA']` 的區別和使用場景。
