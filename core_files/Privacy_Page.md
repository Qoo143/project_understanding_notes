# Privacy_Page.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\Privacy_Page.php`

## 概述
`Privacy_Page.php` 檔案是一個**獨立的隱私權政策和用戶協議展示頁面**。它向用戶展示因材網的各項條款和條件，包括帳號申請使用同意書、個人資料提供同意書、因材網同意書和帳號申請保密承諾書。該頁面採用分頁（Tab）形式展示不同協議的內容，與 `agreement.php` 功能高度相似，但 `Privacy_Page.php` 似乎是一個純展示頁面，不包含用戶同意的交互邏輯。

## 主要功能與職責

### 1. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頁首（帶有 logo 和登出連結）、四個隱藏的 `<span>` 標籤用於控制 Tab 顯示，以及一個主要的 `div` 容器 (`#tab`)，其中包含 Tab 導航和對應的內容區。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `../../css/teacher_root.css`、`../../css/function.css`。頁面還包含大量的內聯 `<style>` 標籤，用於定義 Tab 樣式、複選框樣式和滾動條樣式等。

### 2. 協議內容展示
- 頁面內容分為四個 Tab，每個 Tab 顯示一個獨立的協議：
    - **帳號申請使用同意書** (`tab-content-1`)
    - **個人資料提供同意書** (`tab-content-2`)
    - **因材網同意書** (`tab-content-3`)
    - **帳號申請保密承諾書** (`tab-content-4`)
- 每個協議內容都以 `<p>` 標籤組織，並包含詳細的條款說明。
- 每個協議下方都提供一個 PDF 連結，允許用戶下載協議的完整版本。

### 3. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.5.1.min.ob.js`。
- **Tab 切換**: 通過點擊 Tab 導航 (`<ul>` 中的 `<a>` 標籤) 來切換顯示不同的協議內容。這通過 `location.hash` 的改變和 CSS 的 `:target` 偽類來實現，實現了純 CSS 的 Tab 切換效果。
- **「下一頁」/「上一頁」按鈕**: 每個 Tab 內容下方都有導航按鈕 (`nextTo1`, `nextTo2`, `nextTo3`, `nextTo4`)，點擊後會改變 `location.hash`，從而切換到下一個或上一個 Tab。

## 與 `agreement.php` 的相似性
- `Privacy_Page.php` 在內容和結構上與 `agreement.php` 高度相似。主要區別在於 `Privacy_Page.php` 似乎是一個純粹的展示頁面，不包含用戶勾選同意和提交的邏輯。它可能作為一個公開的隱私政策頁面，供用戶隨時查閱，而 `agreement.php` 則是在用戶登入流程中強制用戶同意的頁面。

## 關鍵引用與依賴
- `../../css/teacher_root.css`
- `../../css/function.css`
- `scripts/jquery-3.5.1.min.ob.js`
- `images/logo1.png`
- `images/pdf.png`

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `Privacy_Page.php` 和 `agreement.php` 在系統中的具體使用場景和區別。
- 了解 `teacher_root.css` 和 `function.css` 中定義的樣式。
