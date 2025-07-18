# adl_material_new.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\adl_material_new.php`

## 概述
`adl_material_new.php` 檔案是一個用於**教材創建與編輯**的頁面。它提供了一個介面，讓用戶（可能是教師或管理員）可以上傳、編輯和管理各種教材，包括影片、練習題、概念結構等。該檔案結合了前端的表單、JavaScript 互動邏輯以及後端的 PHP 處理，是一個複雜的內容管理工具。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_API.php`: 核心 API 引入檔案，可能包含通用函數和類別。

### 2. 前端結構與樣式
- **HTML 結構**: 頁面包含一個用於創建或編輯教材的表單。從其引入的 CSS 和 JavaScript 庫判斷，它可能是一個多步驟或多標籤的表單，用於處理不同類型的教材。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `css/manager_root.css`、`css/normalize.css`、`css/newcss.css`、`css/function.css`、`css/cms-index.css`、`css/cms-header.css`、`css/cms-footer.css`，以及 `scripts/venobox/venobox.css` 和 `scripts/sweetalert2/sweetalert2.min.css`。這表明頁面使用了標準的因材網樣式和一些第三方組件（如 Venobox 和 SweetAlert2）。

### 3. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.6.1.min.ob.js`，表明大量互動邏輯將依賴 jQuery。
- **LoadingOverlay**: 引入 `scripts/loadingoverlay.min.js`，用於在數據加載時顯示加載動畫。
- **adp_core_validation.js**: 引入 `scripts/adp_core_validation.js`，可能包含前端表單驗證或數據處理的通用函數。
- **Venobox**: 引入 `scripts/venobox/venobox.min.js`，用於處理彈出式視窗或燈箱效果。
- **SweetAlert2**: 引入 `scripts/sweetalert2/sweetalert2.min.js`，用於顯示美觀的警告、成功或錯誤訊息。
- **`customStyle_function.js`**: 引入 `js/customStyle_function.js`，可能包含自定義的樣式或互動函數。
- **`$(function() { ... });`**: 頁面加載完成後執行的主要 JavaScript 區塊。
    - **`sHomePageUrl`**: 根據當前 URL 是否包含 `aialtest` 來判斷首頁 URL，這表明對測試環境的適應性。
    - **`modalBox()`**: 定義了一個用於顯示自定義模態框的函數，可以顯示不同類型的圖標（警告、信息、成功、錯誤）。
    - **`showModalBox()`**: 封裝了 `modalBox()`，用於顯示不同類型的提示訊息。
    - **`material_list_table` 初始化**: 頁面中定義了一個空的 `material_list_table`，這暗示其內容將通過 AJAX 動態加載。

### 4. 數據提交與處理 (推測)
- 頁面可能通過 AJAX 向後端（例如 `prodb_adl_material_new.php` 或類似的檔案）提交教材數據。
- 後端負責處理教材的上傳、數據庫儲存、以及與不同教材類型相關的邏輯。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_API.php`
- `scripts/jquery-3.6.1.min.ob.js`
- `scripts/loadingoverlay.min.js`
- `scripts/adp_core_validation.js`
- `scripts/venobox/venobox.min.js`
- `scripts/sweetalert2/sweetalert2.min.js`
- `js/customStyle_function.js`

## 閱讀狀態
已閱讀

## 待辦事項
- 查找與此前端頁面相關的後端處理檔案（例如 `prodb_adl_material_new.php`），以了解數據是如何提交和處理的。
- 了解不同教材類型（影片、練習題、概念結構等）的具體處理邏輯。
- 了解 `adp_API.php` 中可能包含的通用函數和類別。
- 了解 `customStyle_function.js` 中是否有與此頁面相關的特定邏輯。

