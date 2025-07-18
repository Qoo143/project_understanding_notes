# new_styleGuide.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\new_styleGuide.php`

## 概述
`new_styleGuide.php` 檔案是一個**因材網前端樣式指南或組件展示頁面**。它主要用於展示和測試因材網應用程式中使用的各種 UI 組件、樣式和互動模式。該頁面不屬於應用程式的實際功能部分，而是作為開發人員參考和測試前端元素的一種工具。

## 主要功能與職責

### 1. 訪問控制
- 頁面開頭的 PHP 代碼 (`$IPinschool1=strpos($_SERVER["REMOTE_ADDR"],'120.108.208'); ... if($IPinschool1 === false && $IPinschool2 === false){ exit; }`) 限制了只有來自特定 IP 範圍 (`120.108.208` 或 `120.108.212`) 的用戶才能訪問此頁面。這表明它是一個內部開發或測試工具，不對外公開。

### 2. 樣式與組件展示
- 頁面通過導航欄 (`<nav>`) 分類展示了多種 UI 組件和樣式，包括：
    - **小元件 (`#tools`)**: Switch 開關、按鈕（普通、帶圖標、圓形）、提示訊息（點擊提示、滑入提示）、Radio 和 Checkbox、RadioButton 組、下拉選單、進度條、邊框樣式。
    - **ModalBox (`#modalBox`)**: 模態彈出框（小視窗 `ModalBox` 和大視窗 `ModalBoxBig`）的用法和示例。
    - **下拉搜尋 (`#multiselect`)**: 帶有搜尋功能的下拉選單。
    - **備註樣式 (`#noteWord`)**: 突出顯示關鍵信息的樣式。
    - **分頁頁碼 (`#paginationArea`)**: 分頁組件的展示。
    - **頁籤切換 (`#pageTab`)**: Tab 頁切換組件。
    - **表格樣式 (`#tableStyleBorder`)**: 不同樣式的表格。
    - **顯示切換 (`#displaySwitch`)**: 內容顯示切換。
    - **步驟式 (`#stepTab`)**: 多步驟流程的展示。
    - **顏色 (`:root`)**: 定義在 CSS 變數中的顏色列表。

### 3. CSS 引入與內聯樣式
- 引入了多個 CSS 檔案，如 `css/cms-index.css`、`css/teacher_root.css`、`css/normalize.css`、`css/newcss.css`、`css/function.css`。這表明它使用了因材網的通用樣式。
- 頁面中包含大量的內聯 `<style>` 標籤，用於定義和覆蓋組件的樣式，以便在單個頁面中展示多種樣式變體。

### 4. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.5.1.min.ob.js`。
- **`customStyle_function.js`**: 引入了自定義的 JavaScript 函數，用於實現各種 UI 組件的互動邏輯，例如 `modalBox()`、`modalBoxBig()`、`pluralSelected()`、`getPagination()` 等。
- **樣式切換**: 通過 Radio 按鈕 (`name="cssType"`)，可以切換頁面使用的 CSS 文件 (`css/teacher_root.css`, `css/student_root.css`, `css/manager_root.css`)，以預覽不同角色下的樣式。
- **組件互動**: 頁面中的 JavaScript 實現了各種組件的互動，例如 ModalBox 的顯示/隱藏、下拉選單的選擇、搜尋過濾等。

## 關鍵引用與依賴
- `css/teacher_root.css`, `css/student_root.css`, `css/manager_root.css` (用於樣式切換)
- `js/customStyle_function.js` (包含大部分組件的 JavaScript 邏輯)
- `scripts/jquery-3.5.1.min.ob.js`

## 安全考量
- **訪問控制**: 頁面開頭的 IP 限制是重要的安全措施，確保只有授權人員才能訪問此內部工具。
- **XSS 防護**: 由於頁面展示了許多動態內容和用戶輸入的示例，需要確保 `customStyle_function.js` 中的函數在處理和渲染數據時進行了充分的 XSS 防護。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `js/customStyle_function.js`，了解其中定義的各種 UI 組件函數的具體實現。
- 了解 `css/teacher_root.css`、`css/student_root.css` 和 `css/manager_root.css` 之間的差異，以及它們如何定義不同角色的視覺主題。
