# acl.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\modules\menu\acl.php`

## 概述
`acl.php` 檔案負責因材網應用程式的**菜單權限控制**。它根據用戶的 `access_level`（權限等級）動態生成不同的菜單結構 (`$arrMenuACL`)，並定義了每個菜單項目的可見性和可訪問的頁面 (`view` 陣列)。此外，它還包含一個 `initPermission()` 函數，用於在頁面載入時驗證用戶是否有權限訪問當前 URL。

## 主要功能與職責

### 1. 全局常量定義
- `MENU_LEVEL_ONE_INDEXEMPTY`: 首頁菜單項的 HTML 模板。
- `MENU_LEVEL_ONE_INDEX_SCHOOLADMIN`: 學校管理員首頁菜單項的 HTML 模板。
- `MENU_LEVEL_ONE_REPORT`: 報表菜單項的 HTML 模板。

### 2. 菜單功能群組設定 (`$arrMenuGroup`)
- 定義了多個 `$arrMenuGroup` 陣列，每個陣列代表一個菜單功能群組，例如 `pd_discuss` (主任校長討論區)、`t_class` (教師班級管理)、`s_report` (學生報表) 等。
- 每個群組包含一個標題（通常帶有圖標）和一個包含多個 `<li><a>` 標籤的列表，每個 `<a>` 標籤代表一個菜單項目，包含 `href` (連結)、`title` (標題) 和 `crumbs` (麵包屑)。
- 菜單項目的 `href` 通常是 `modules_new.php?op=modload&name={module_name}&file={file_name}&crumbs={breadcrumb_text}` 的格式。
- 某些菜單項目的顯示會根據條件判斷，例如 `$bHasHighSchool` (是否為高中或大學) 和 `getAccessPermissions()` 函數的結果。

### 3. 身份選單設定 (`$arrMenuACL`)
- 這是核心的 ACL (Access Control List) 陣列，根據不同的 `USER_` 權限等級（例如 `USER_SCHOOL_DIRECTOR`, `USER_TEACHER`, `USER_STUDENT`, `USER_ADMIN` 等）定義了兩個子陣列：
    - `menu`: 包含該權限等級用戶可見的菜單結構（由 `$arrMenuGroup` 中的元素組成）。
    - `view`: 包含該權限等級用戶可以訪問的 URL 列表。這些 URL 通常是 `modules_new.php?op=modload&name={module_name}&file={file_name}` 的形式。
- 透過 `implode('', $arrMenuGroup['...'])` 將功能群組的 HTML 內容嵌入到特定權限等級的菜單中。
- 存在一些特殊邏輯，例如根據 `$_SESSION['user_data']->organization_id` 或 `$_SESSION['user_id']` 來 `unset` 某些菜單項或添加特殊菜單。

### 4. 共用功能設定 (`$arrMenuGeneral`)
- 包含所有用戶都可以訪問的通用功能 URL 列表，這些功能通常不顯示在導航菜單中，但仍然需要權限驗證。

### 5. 權限驗證初始化 (`initPermission()`)
- **功能**: 在頁面載入時執行，用於驗證當前用戶是否有權限訪問當前請求的 URL。
- **邏輯**:
    - 獲取當前請求的 URL 參數 (`op`, `name`, `file`)。
    - 收集所有該用戶權限等級下可見的菜單 URL (`$arrMenuACL[...]['menu']` 中的 `href` 屬性)。
    - 將這些菜單 URL 與 `$arrMenuGeneral` (共用功能 URL) 和 `$arrMenuACL[...]['view']` (內嵌功能 URL) 合併到 `$aAllUrl` 陣列中。
    - 遍歷 `$aAllUrl`，將每個 URL 的 `name` 和 `file` 參數與當前請求的 `name` 和 `file` 參數進行比較。
    - 如果找到匹配項，則設定 `$bHavePermission = true` 並跳出循環。
    - 如果用戶是 `USER_ADMIN`，則會記錄執行日誌。
    - 如果 `$bHavePermission` 為 `false`，則表示用戶無權訪問當前頁面。在測試環境 (`_TEST_SITE_LIST`) 會顯示「ACL無權限」的提示，否則會發送錯誤郵件 (`sendErrorMessage()`) 並終止程式。

### 6. 記憶體限制設定 (`initMemoryLimit()`)
- 根據用戶的 `access_level` 設定 PHP 的 `memory_limit`，為不同權限等級的用戶分配不同的記憶體上限。

### 7. 麵包屑生成 (`getBreadCrums($sNameUrl, $sFileUrl)`)
- 根據模組名稱和檔案名稱，從 `$aAllUrl` 中查找對應的麵包屑文本 (`crumbs`)，並構建麵包屑 URL。

### 8. 特殊選單權限 (`getAccessPermissions($sFolderName, $sSubFolderName, $sFilename)`)
- 查詢 `organization_ace_setting` 和 `website_module` 資料庫表，判斷特定模組在特定組織和權限等級下是否啟用。
- 如果在資料庫中沒有找到設定，則預設返回 `true` (允許訪問)。

## 關鍵引用與依賴
- `$_SESSION['user_data']` (用戶權限和組織資訊)
- `modules_new.php` (作為菜單項目的基礎 URL)
- `getAccessPermissions()` (用於動態判斷菜單項目的可見性)
- `sendErrorMessage()` (用於處理無權限訪問的錯誤)
- `_INCLASS_PLATFORM` (定義在 `config.php`)
- `USER_TEACHER_GROUP`, `USER_SCHOOL_ADMIN`, `USER_CITY_ADMIN`, `USER_EDU_ADMIN`, `USER_ADL_OPERATOR_GROUP` 等常量 (定義在 `config.php`)
- `_TEST_SITE_LIST` (定義在 `config.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `adl_hash_file.json` 的生成和維護機制。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能。
- 查找 `createAuthOptions()` 函數的定義和作用（它可能是一個簡單的陣列構造，或者定義在其他未被直接引入的檔案中）。
