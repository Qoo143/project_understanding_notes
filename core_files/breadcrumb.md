# breadcrumb.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\breadcrumb.php`

## 概述
`breadcrumb.php` 檔案主要負責**動態生成頁面標題和麵包屑導航**。它根據當前頁面的模組名稱 (`name`)、檔案名稱 (`file`) 以及其他參數，從預定義的菜單結構中查找對應的層級關係，並輸出 HTML 格式的麵包屑和頁面標題。這有助於用戶了解他們在網站中的位置，並提供快速導航。

## 主要功能與職責

### 1. 參數獲取
- 從 `$_GET` 獲取 `name` (模組名稱)、`file` (檔案名稱)、`action` 和 `mode` 等參數，這些參數用於確定當前頁面的唯一標識。

### 2. 麵包屑數據結構定義 (`$aMenuBreadCrumb`)
- 檔案中定義了一個大型的 `$aMenuBreadCrumb` 陣列，它是一個巢狀結構，用於定義網站的菜單層級和麵包屑路徑。
- 每個項目包含 `name`、`file`，以及可選的 `child` 陣列，用於定義子菜單或子頁面。
- 註解中詳細說明了如何添加新的麵包屑路徑，包括在 `acl.php` 中添加參數和在 `$aMenuBreadCrumb` 中定義結構。

### 3. 麵包屑陣列翻轉 (`reverseArray`)
- `reverseArray($sThisKey, $arrThisValue)` 函數是一個遞歸函數，用於將 `$aMenuBreadCrumb` 陣列進行「翻轉」。
- 它的目的是為每個子節點添加一個 `parents` 屬性，指向其父節點。這樣可以方便地從任何一個節點向上追溯到其所有父節點，從而構建完整的麵包屑路徑。
- 翻轉後的結果儲存在 `$aReverseaMenuBreadCrumb` 全局變數中。

### 4. 麵包屑和標題生成核心函數
- **`getParents($aParentData)`**: 遞歸獲取一個節點的所有父層麵包屑數據。
- **`getSelf($aSelfData)`**: 獲取當前節點本身的麵包屑數據。
- **`getBreadCrumb($aReverseaMenuBreadCrumb, $sName, $sFile, $sOtherConditions)`**: 根據當前頁面的 `name`、`file` 和其他條件，結合翻轉後的麵包屑陣列，生成完整的麵包屑路徑陣列 (`$aBreadCrumb`)。
- **`getBreadTitle($aReverseaMenuBreadCrumb, $sName, $sFile, $sOtherConditions)`**: 根據當前頁面的 `name`、`file` 和其他條件，獲取頁面的標題 (`$sBreadcrumbsTitle`)。
- **`getBreadCrums($sNameUrl, $sFileUrl)`**: 這個函數在註解中被提及，但其定義並不在 `breadcrumb.php` 中。它應該是一個外部函數，用於根據 `name` 和 `file` 獲取麵包屑的標題和 URL。**（待辦事項：查找此函數定義）**

### 5. 特殊頁面處理
- 檔案中包含一個大型的 `switch ($sFile)` 語句，用於處理各種特殊頁面的麵包屑和標題邏輯，例如：
    - `ks_viewlist_new` (課程總覽): 根據年級、科目等參數動態生成標題和麵包屑。
    - `ks_viewunit_new` (課程內容): 根據出版社、單元等信息生成詳細麵包屑。
    - `useditem` (大考專區): 簡單的兩級麵包屑。
    - `answer` (大考專區測驗/記錄頁面): 根據 `mode` 參數顯示「測驗頁面」或「記錄頁面」。
    - `coin_student_history` (代幣紀錄): 標題會帶入學生姓名。
    - `discussDetail` (討論): 根據用戶身份（學生/教師）調整麵包屑父層。
    - `backstage/item`, `backstage/itemgroup`, `backstage/paper` (後台題目/題組/試卷管理): 根據 `action` 參數調整標題和麵包屑。
    - `backstage/preview` (後台預覽): 根據 `mode` 參數顯示不同的預覽標題。
- 這些特殊處理邏輯通常會查詢資料庫 (`dbh`, `dbh_slave`) 以獲取額外的信息來構建麵包屑和標題。

### 6. HTML 輸出
- 最終生成的麵包屑和標題會被組合成 HTML 結構，並使用 `preg_replace` 進行簡單的 HTML 標籤過濾，然後輸出到頁面。

## 關鍵引用與依賴
- `acl.php` (麵包屑參數的來源)
- `dbh`, `dbh_slave` (全局資料庫連接，用於查詢科目、單元等信息)
- `$_SESSION['user_data']` (用戶權限，用於 `discussDetail` 的特殊處理)
- `id2subject()` (可能定義在 `adp_core_function.php`)

## 安全考量
- **SQL 注入**: 在特殊頁面處理中，有多處直接使用 `$_GET` 或 `$_REQUEST` 參數構建 SQL 查詢（例如 `ks_viewlist_new` 和 `ks_viewunit_new`）。雖然使用了 `bindParam`，但仍需仔細檢查所有參數是否都經過了適當的綁定和過濾，以防止 SQL 注入漏洞。
- **XSS**: 最終輸出的 HTML 經過 `preg_replace` 進行過濾，但仍需確保其過濾規則足夠嚴格，防止惡意腳本注入。

## 閱讀狀態
已閱讀

## 待辦事項
- **查找 `getBreadCrums()` 函數的定義**：這是理解麵包屑生成核心邏輯的關鍵。
- 深入檢查所有涉及資料庫查詢的特殊頁面處理邏輯，確保所有用戶輸入都經過了安全的參數綁定或過濾。
- 了解 `acl.php` 中麵包屑參數的具體定義方式。
- 了解 `id2subject()` 函數的具體實現。
