# adl_material_tl_assessment.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\adl_material_tl_assessment.php`

## 概述
`adl_material_tl_assessment.php` 檔案是一個用於**展示師資類科學科知能教學媒體專區教材列表**的頁面。它將教材按照「國語」、「數學」、「自然」、「社會」等大類進行分類，並提供可展開/收合的介面，讓用戶可以瀏覽每個大類下的具體科目教材。該頁面主要用於內容展示和導航，引導用戶進入更詳細的教材列表頁面 (`adl_material_list_tl_assessment.php`)。

## 主要功能與職責

### 1. 引入核心依賴
- `include/adp_core_function.php`: 引入核心通用函數，其中包含 `string2hash()` 函數，用於對 URL 參數進行加密。
- `include/config.php`: 引入全局配置，雖然檔案中沒有直接使用 `config.php` 的常量，但其引入是為了確保環境的正確配置。

### 2. 教材數據定義 (`$vSubjectData`)
- 頁面頂部定義了一個 PHP 陣列 `$vSubjectData`，其中包含了各個學科（國語、數學、自然、社會）及其對應的子科目和出版商（固定為「國民小學師資類科學科知能教學媒體專區」）。
- 這個陣列是頁面動態生成科目列表的數據來源。

### 3. 動態生成科目列表函數 (`getSubjectData`)
- `getSubjectData($sSubjectName)` 函數接收一個學科名稱（例如「國語」），然後遍歷 `$vSubjectData` 中對應的子科目。
- 對於每個子科目，它會構建一個指向 `adl_material_list_tl_assessment.php` 的 URL，並將科目名稱 (`subject`) 和出版商 (`publisher`) 進行 `string2hash()` 加密後作為 GET 參數傳遞。
- 最後，它返回一個包含這些科目連結的 HTML 字串。

### 4. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頂部導航欄（logo 和標題）、一個警告訊息（建議使用 Chrome 瀏覽器觀看影片），以及一個主要內容區，其中包含可展開/收合的學科列表。
- **CSS 引入**: 引入了 `css/font-awesome/css/all.css` (圖標庫) 和 `css/adl_material.css` (頁面專用樣式)。
- **Google Analytics**: 頁面引入了 Google Analytics 追蹤代碼，用於收集用戶行為數據。

### 5. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.6.1.min.ob.js`。
- **展開/收合功能**: 頁面使用 jQuery 實現了學科列表的展開/收合功能。當用戶點擊 `dt` 標籤時，會切換其下 `dd` 標籤的顯示狀態，並更新 `dt` 的樣式 (`showMore`/`showLess`)。
- **Google Analytics 事件**: `gtag()` 函數用於發送 Google Analytics 事件。

## 關鍵引用與依賴
- `include/adp_core_function.php` (用於 `string2hash()`)
- `adl_material_list_tl_assessment.php` (作為連結目標頁面)
- `images/logo.png`
- `css/font-awesome/css/all.css`
- `css/adl_material.css`
- `scripts/jquery-3.6.1.min.ob.js`

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `string2hash()` 函數的具體實現，以確認其加密方式和安全性。
- 了解 `adl_material_list_tl_assessment.php` 如何接收和處理來自此頁面的加密參數。
- 了解 `adl_material.css` 中定義的樣式，特別是與展開/收合功能相關的樣式。
