# index_material.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\index_material.php`

## 概述
`index_material.php` 檔案是一個**教材分類展示頁面**，主要用於呈現因材網中不同學段（國小、國中、高中）的各科目教材入口。它以視覺化的方式將教材內容進行分類，並提供連結引導用戶進入具體的教材列表頁面。該頁面主要由 HTML 結構和 CSS 樣式組成，輔以少量的 JavaScript 用於 Google Analytics 追蹤。

## 主要功能與職責

### 1. 教材分類展示
- 頁面將教材分為「國小」、「國中」和「高中」三個主要區塊，每個區塊有不同的背景顏色和標題樣式。
- 在每個學段區塊內，教材進一步按年級分類，並列出該年級下的各個科目（例如國語文、數學、英語文、自然、生物、理化等）。
- 每個科目都是一個超連結，點擊後會導向 `modules.php?op=modload&name=assignMission&file=ks_viewlist`，並帶有學期 (`seme`)、學科組 (`subjectG`)、科目 (`subject`) 和年級 (`grade`) 等參數。這表明它會加載一個知識結構列表頁面。

### 2. 前端結構與樣式
- **HTML 結構**: 頁面主要由 `div` 元素構成，用於劃分不同的教材區塊和內容列表。使用了 `dl`、`dt`、`dd` 標籤來組織年級和科目列表。
- **CSS 樣式**: 頁面頂部包含大量的內聯 `<style>` 標籤，定義了頁面的佈局、顏色、字體大小、響應式行為等。例如，`materialList` 類用於彈性佈局，`divred`、`orange`、`divblue` 類用於不同學段的顏色區分。
- **響應式設計**: 包含多個 `@media screen` 查詢，以適應不同螢幕尺寸（如桌面、平板、手機）的顯示，調整佈局和元素大小。

### 3. Google Analytics
- 頁面引入了 Google Analytics 追蹤代碼，用於收集用戶在教材分類頁面上的行為數據。

## 關鍵引用與依賴
- `modules.php` (作為教材列表頁面的入口)
- `images/logo.png` (推測，雖然代碼中沒有直接引用，但作為首頁通常會有)
- `css/adl_material.css` (推測，雖然沒有直接引入，但從樣式命名判斷可能存在)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `modules.php` 如何處理 `op=modload&name=assignMission&file=ks_viewlist` 這樣的請求，以及 `ks_viewlist` 頁面如何根據傳入的參數顯示具體的教材列表。
- 了解 `subjectG` 和 `subject` 參數在因材網中的具體含義和對應關係。
- 了解 `seme` 參數（學期）的具體含義和如何影響教材的顯示。
