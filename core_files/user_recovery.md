# user_recovery.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\user_recovery.php`

## 概述
`user_recovery.php` 檔案是一個**前端頁面**，用於因材網的**帳號恢復和密碼重設流程**。它提供了一個表單，允許用戶根據其身份（學生、教師、校管理者、家長、其他）輸入相關資訊（學校、班級、姓名、身份證字號後5碼或電子信箱）以及驗證碼，以請求密碼重設通知信。該頁面主要負責前端的數據收集、驗證和通過 AJAX 向後端 (`prodb_user_recovery.php` 經由 `turnpage_out.php`) 發送請求。

## 主要功能與職責

### 1. 引入核心依賴與安全 Token
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `encodeAjaxUrlBase64()`。
- `$sAialToken = md5('AIAL_' . md5(date('Y-m-d H:i')));`: 生成一個時間敏感的 Token，用於前端請求的驗證。

### 2. 前端結構與樣式
- **HTML 結構**: 頁面包含一個標題、一個提示訊息（關於教育雲帳號的處理），以及一個用於收集用戶信息的表單。
- **CSS 引入**: 引入了 `css/teacher_root.css`、`css/function.css` 和 `css/font-awesome/css/all.min.css`，並包含大量的內聯 `<style>` 標籤，用於定義頁面的佈局、顏色、字體和響應式行為。
- **輸入欄位**: 包含身份選擇 (`#identity`)、學校信息（縣市、區域、學校，`#city`, `#area`, `#organization`)、班級信息（年級、班級，`#grade`, `#class`)、姓名 (`#name`)、身份核對方式 (`#idVerificationMethod`)、身份證字號後5碼 (`#idNum`)、電子信箱 (`#email`) 和驗證碼 (`#captcha_code`)。

### 3. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.6.1.min.ob.js`。
- **LoadingOverlay**: 引入 `scripts/loadingoverlay.min.js`，用於在 AJAX 請求期間顯示加載動畫。
- **adp_core_validation.js**: 引入 `scripts/adp_core_validation.js`，可能包含前端表單驗證的通用函數。
- **常量定義**: 定義了 AJAX 路由 (`router`) 和各種身份常量 (`IDENTITY_STUDENT`, `IDENTITY_TEACHER` 等) 以及身份核對方式常量。
- **動態表單顯示**: 根據用戶選擇的身份 (`#identity`)，動態顯示或隱藏相關的輸入欄位（例如，學生身份需要班級和姓名，教師則不需要）。
- **級聯下拉選單**: 
    - 當選擇縣市 (`#city`) 後，通過 AJAX 調用 `turnpage_out.php` (act: `getOrg`) 獲取區域和學校列表。
    - 當選擇學校 (`#organization`) 後，通過 AJAX 調用 `turnpage_out.php` (act: `getGradeClassList`) 獲取年級和班級列表。
- **身份核對方式切換**: 根據選擇的核對方式 (`#idVerificationMethod`)，動態顯示身份證字號後5碼或電子信箱輸入框。
- **驗證碼功能**: 
    - 顯示驗證碼圖片 (`classes/securimage/securimage_show.php`)。
    - 提供「更換圖片」 (`#captcha_chg`) 和「語音播放」 (`#captcha_audio_source`) 功能。
- **前端數據驗證 (`checkData()`)**: 在提交前對所有輸入欄位進行嚴格的格式和內容驗證，包括 Token、身份、Email 格式、身份證字號格式和驗證碼格式。
- **AJAX 提交 (`recover()`)**: 當前端驗證通過後，點擊「送出」按鈕 (`#submit`) 會觸發一個 AJAX POST 請求到 `turnpage_out.php` (act: `recover`)，提交所有收集到的數據。
- **響應處理**: 根據後端返回的 `status` 顯示成功或失敗訊息，並在成功後彈出提示並重新加載頁面。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `turnpage_out.php` (作為 AJAX 請求的路由)
- `prodb_user_recovery.php` (推測的後端處理腳本，由 `turnpage_out.php` 引入)
- `classes/securimage/securimage_show.php` (驗證碼圖片)
- `classes/securimage/securimage_play.php` (驗證碼語音)
- `scripts/jquery-3.6.1.min.ob.js`
- `scripts/loadingoverlay.min.js`
- `scripts/adp_core_validation.js`

## 安全考量
- **前端驗證**: 雖然前端進行了大量的數據驗證，但後端 (`prodb_user_recovery.php`) 必須再次進行嚴格的驗證，以防止惡意請求。
- **Token 時效性**: `$sAialToken` 的生成依賴於分鐘級的時間戳，這提供了一定的時效性，但 `md5` 並非加密演算法，且 Token 透過前端傳遞，仍需後端嚴格驗證。
- **驗證碼**: 使用 `Securimage` 驗證碼增加了防禦自動化攻擊的能力。
- **敏感信息傳輸**: 身份證字號後5碼和 Email 等敏感信息通過 POST 請求傳輸，應確保整個過程在 HTTPS 環境下進行。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `prodb_user_recovery.php` 的具體實現，了解其如何處理帳號恢復邏輯、發送郵件和更新資料庫。
- 了解 `adp_core_validation.js` 中 `checkData()` 函數的具體實現。
- 了解 `encodeAjaxUrlBase64()` 函數的具體實現。
