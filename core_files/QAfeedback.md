# QAfeedback.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\QAfeedback.php`

## 概述
`QAfeedback.php` 檔案是一個**問題回報表單頁面**，允許用戶向因材網提交他們遇到的問題、建議或錯誤。該頁面集成了前端表單、JavaScript 驗證和互動邏輯，以及後端數據處理。它還支持上傳截圖，以幫助開發人員更好地理解問題。

## 主要功能與職責

### 1. 引入核心依賴與權限檢查
- `session_start()`: 啟動 Session。
- `auth_chk.php`: 引入身份驗證檢查，確保用戶已登入。
- `include/config.php`: 應用程式的全局配置。
- `include/adp_API.php`: 核心 API 引入檔案，可能包含 `UserData` 類別。
- `include/security_function.php`: 安全相關函數，包含 `xss_clean()` 等。
- **用戶數據獲取**: 如果用戶已登入，則獲取其 `user_id` 和 `user_data`。如果未登入，則重定向到 404 頁面。

### 2. 輸入數據過濾
- 對 `$_GET` 中的所有輸入數據進行 XSS 過濾 (`xss_clean()`)。

### 3. 科目列表獲取
- 從資料庫中查詢 `subject` 表，獲取科目列表 (`$aSubjectList`)，用於問題類型中的「各科領域問題」下拉選單。

### 4. 前端表單與 JavaScript 驗證
- **回報表單**: 包含電子信箱 (`fb_email`)、電話 (`fb_phone`)、問題描述 (`fb_text`)、問題類型 (`chk_fb_sub`) 和截圖 (`fb_img_path`) 等輸入欄位。
- **`chk()` JavaScript 函數**: 在前端執行嚴格的輸入驗證，包括：
    - 檢查 Email 和電話是否填寫。
    - 檢查 Email 格式是否正確。
    - 檢查問題描述是否填寫。
    - 檢查問題領域是否選擇。
    - 檢查截圖是否已上傳且格式為 PNG。
- **SweetAlert2**: 使用 SweetAlert2 顯示美觀的警告、成功或錯誤訊息。
- **動態下拉選單**: 根據選擇的問題類型，動態顯示或隱藏「各科領域問題」和「教材建議」的下拉選單。
- **重新截圖**: 提供「重新截圖」按鈕 (`#btn05`)，允許用戶重新截取當前頁面。
- **提交表單**: 點擊「完成」按鈕 (`#complete-answer`) 時，如果前端驗證通過，則顯示 SweetAlert2 提示，然後提交表單到 `modules/assignMission/prodb_feedback_save.php`。
- **清空表單**: 點擊「清空」按鈕 (`#clear-answer`) 清空表單內容。

### 5. 截圖處理
- 頁面通過 `$_GET["imgpath"]` 接收截圖路徑，並將其顯示在表單中。
- 隱藏的 `fb_img_path` 欄位用於將截圖路徑傳遞給後端。

### 6. 外部連結
- 提供常見問題集 (`HomePage/faq/`) 和操作手冊 (`HomePage/webinfo/?id=4`) 的連結。
- 「AI學伴(通用型、學科領域)內容問題」選項會連結到一個 Google 表單。

## 關鍵引用與依賴
- `auth_chk.php`
- `include/config.php`
- `include/adp_API.php`
- `include/security_function.php`
- `modules/assignMission/prodb_feedback_save.php` (後端處理腳本)
- `scripts/jquery-3.5.1.min.ob.js`
- `scripts/loadingoverlay.min.js`
- `scripts/sweetalert2/sweetalert2_7284.js`
- `scripts/adp_core_validation.js`
- `images/pdf.png` (推測，用於 PDF 連結)

## 安全考量
- **XSS 防護**: 對 `$_GET` 和用戶輸入進行了 XSS 過濾，但需要確保所有輸出到 HTML 的數據都經過了適當的轉義。
- **文件上傳**: 截圖上傳到 `prodb_feedback_data.php`，需要確保該腳本對文件類型、大小和內容進行了嚴格的驗證，防止惡意文件上傳。
- **重定向**: 未登入時的重定向到 404 頁面，並使用 `validateRedirect()` 確保安全性。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/assignMission/prodb_feedback_save.php` 的具體實現，了解其如何儲存回報數據和截圖。
- 了解 `adp_API.php` 和 `security_function.php` 中 `UserData` 和 `xss_clean()` 的具體實現。
- 了解 `head_iframe.php` 如何生成截圖並傳遞給此頁面。
