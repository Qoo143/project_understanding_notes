# agreement.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\agreement.php`

## 概述
`agreement.php` 檔案是一個**用戶協議和隱私政策展示頁面**。它旨在向用戶展示因材網的各項條款和條件，包括帳號申請使用同意書、個人資料提供同意書、因材網同意書和帳號申請保密承諾書。用戶必須閱讀並同意這些條款才能繼續使用因材網服務。頁面採用分頁（Tab）形式展示不同協議的內容，並在最後提供一個勾選框和確認按鈕。

## 主要功能與職責

### 1. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頁首（帶有 logo 和登出連結）、四個隱藏的 `<span>` 標籤用於控制 Tab 顯示，以及一個主要的 `div` 容器 (`#tab`)，其中包含 Tab 導航和對應的內容區。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `css/normalize.css`、`css/newcss.css`、`css/teacher_root.css`、`css/function.css` 和 `css/cms-index.css`，用於定義頁面的基本樣式和佈局。
- **內聯樣式**: 頁面底部包含大量的內聯 `<style>` 標籤，用於定義 Tab 樣式、複選框樣式和滾動條樣式等。

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
- **SweetAlert2**: 引入 `scripts/sweetalert2/sweetalert2.all.min.js`，用於顯示美觀的警告、成功或錯誤訊息。
- **Tab 切換**: 通過點擊 Tab 導航 (`<ul>` 中的 `<a>` 標籤) 來切換顯示不同的協議內容。這通過 `location.hash` 的改變和 CSS 的 `:target` 偽類來實現，實現了純 CSS 的 Tab 切換效果。
- **「下一頁」/「上一頁」按鈕**: 每個 Tab 內容下方都有導航按鈕 (`nextTo1`, `nextTo2`, `nextTo3`, `nextTo4`)，點擊後會改變 `location.hash`，從而切換到下一個或上一個 Tab。
- **同意條款與提交**: 
    - 頁面底部有一個複選框 (`#agreeCheckbox`)，用戶必須勾選表示已閱讀並同意所有條款。
    - 「確定」按鈕 (`#OK_btn`) 的點擊事件會檢查所有複選框是否都被勾選。
    - 如果所有複選框都已勾選，則發送一個 AJAX POST 請求到 `PrivacyUpdate.php`，數據為 `act: 'PrivacyUpdate'`。
    - 如果 `PrivacyUpdate.php` 返回 `1`，則重定向到 `modules_new.php?op=main`，表示用戶已成功同意協議並可以繼續使用服務。
    - 如果未勾選所有複選框，則使用 SweetAlert 彈出警告訊息。

## 關鍵引用與依賴
- `../../css/normalize.css`
- `../../css/newcss.css`
- `../../css/teacher_root.css`
- `../../css/function.css`
- `../../css/cms-index.css`
- `scripts/jquery-3.5.1.min.ob.js`
- `scripts/sweetalert2/sweetalert2.all.min.js`
- `images/logo1.png`
- `images/pdf.png`
- `PrivacyUpdate.php` (後端處理腳本)
- `modules.php` (用於登出連結)
- `modules_new.php` (成功同意後的重定向目標)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `PrivacyUpdate.php` 的功能，了解其如何處理用戶的協議同意狀態，以及它在資料庫中更新了哪些信息。
- 了解 `modules.php?act=logout` 的具體實現。
- 了解 `teacher_root.css` 中定義的樣式，特別是與無權限預設樣式相關的部分。
