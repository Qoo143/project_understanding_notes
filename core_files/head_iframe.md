# head_iframe.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\head_iframe.php`

## 概述
`head_iframe.php` 檔案是一個**用於 iframe 內頁的頭部組件**。它主要包含了各種 CSS 樣式表和 JavaScript 庫的引入，以及一些用於問題回報和拖曳功能的 JavaScript 邏輯。這個檔案的目的是為因材網中以 iframe 形式嵌入的頁面提供統一的樣式和基礎功能。

## 主要功能與職責

### 1. 頁面基本設定
- **HTML 結構**: 包含 `<!doctype html>`、`<html>` 和 `<head>` 標籤，定義了頁面的基本結構。
- **Meta 標籤**: 設定字符集、標題、Google 翻譯禁用、內容語言和響應式視口。
- **Favicon**: 引入 `favicon.ico` 作為網站圖標。

### 2. CSS 引入
- 引入了多個 CSS 檔案，用於頁面的樣式和佈局：
    - `css/cms-index.css`, `css/cms-header.css`, `css/cms-footer.css`: 可能用於內容管理系統的通用樣式、頁首和頁尾樣式。
    - `css/jquery.tipsy.css`: 用於 jQuery Tipsy 提示框的樣式。
    - `css/sticky.css`: 用於 Sticky.js 元素的樣式。
    - `css/sortable-theme-minimal.css`: 用於 Sortable.js 拖曳排序的最小化主題樣式。
    - `scripts/sweetalert.css`: 用於 SweetAlert 彈出框的樣式。
    - `scripts/venobox/venobox.css`: 用於 Venobox 燈箱效果的樣式。
    - `scripts/tab/easy-responsive-tabs.css`: 用於響應式 Tab 頁的樣式。
    - `scripts/menu/menuzord.css`: 用於菜單組件的樣式。

### 3. JavaScript 引入
- 引入了多個 JavaScript 庫和腳本，用於頁面的互動功能：
    - `scripts/jquery-3.5.1.min.ob.js`: jQuery 庫。
    - `scripts/jquery.tipsy.js`: jQuery Tipsy 提示框插件。
    - `scripts/sticky.js`: Sticky.js 插件，用於使元素在滾動時保持固定。
    - `scripts/sortable.min.js`: Sortable.js 庫，用於拖曳排序功能。
    - `scripts/sweetalert.min.js`: SweetAlert 彈出框庫。
    - `scripts/loadingoverlay.min.js`: 用於顯示加載動畫。
    - `scripts/venobox/venobox.min.js`: Venobox 燈箱效果插件。
    - `scripts/draggabilly.pkgd.min.js`: Draggabilly 庫，用於元素拖曳功能。
    - `scripts/html2canvas.js`: 用於將 HTML 內容渲染為 Canvas 圖像，通常用於截圖功能。
    - `scripts/tab/easyResponsiveTabs.js`: 用於響應式 Tab 頁的腳本。

### 4. 問題回報功能
- 頁面包含一個可拖曳的「問題回報」按鈕 (`#ireport`)。
- 當點擊 `#frame2canvas` 按鈕時，會觸發 `html2canvas` 截取當前頁面的螢幕截圖。
- 截圖數據會通過 AJAX POST 請求發送到 `modules/assignMission/prodb_feedback_data.php` 進行處理。
- 成功後，頁面會重定向到 `QAfeedback.php`，並將圖片路徑作為參數傳遞。
- `draggabilly` 用於實現 `#ireport` 元素的拖曳功能。
- `iclosedialog` 按鈕用於隱藏問題回報框。

### 5. 右鍵禁用
- 根據 `$user_data->access_level` 的值（如果小於等於 2），會設置 `oncontextmenu="window.event.returnValue=false"`，禁用頁面右鍵菜單。這通常用於防止用戶複製內容或查看源代碼。

## 關鍵引用與依賴
- `favicon.ico`
- 各種 CSS 和 JavaScript 庫 (如 jQuery, SweetAlert, Venobox, html2canvas, Draggabilly)
- `modules/assignMission/prodb_feedback_data.php` (後端處理截圖數據)
- `QAfeedback.php` (問題回報頁面)
- `$_SESSION['user_data']` (用於判斷是否禁用右鍵)

## 安全考量
- **XSS 防護**: 雖然此檔案本身沒有直接處理用戶輸入，但它引入了許多第三方庫和自定義腳本。需要確保這些庫和腳本本身是安全的，並且在處理任何動態內容時都進行了適當的 XSS 防護。
- **截圖數據處理**: `prodb_feedback_data.php` 接收截圖數據，需要確保其對數據的處理是安全的，防止惡意文件上傳或濫用。
- **右鍵禁用**: 禁用右鍵雖然可以防止一些簡單的複製行為，但並不能提供真正的安全保護，因為用戶仍然可以通過其他方式獲取頁面內容。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/assignMission/prodb_feedback_data.php` 如何處理截圖數據。
- 了解 `QAfeedback.php` 的功能和如何顯示問題回報介面。
- 了解 `MY_SHORT_SERVER_IP` 變數的來源和用途。
