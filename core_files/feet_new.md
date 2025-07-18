# feet_new.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\feet_new.php`

## 概述
`feet_new.php` 檔案是因材網應用程式的**頁尾組件**。它包含了版權信息、聯絡方式、相關連結以及一些系統監控功能。該檔案旨在為用戶提供必要的支持信息，並在後台記錄系統負載數據。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `onLineCount()` 等。

### 2. 系統負載記錄 (`SystemLoading` 函數)
- **功能**: 記錄系統的運行狀態，包括時間、用戶 ID、訪問級別、查詢字串、代碼執行時間、記憶體使用量、在線用戶數、系統負載和 IP 地址。
- **數據來源**: 從 `/mnt/ramdisk/uptime_now.txt` 讀取系統負載信息，並從 `$_SERVER` 超級全局變數獲取請求信息。
- **數據儲存**: 將收集到的數據以 CSV 格式寫入到 `/mnt/ramdisk/` 目錄下的 CSV 文件中，文件名格式為 `SERVER_ADDR_YYYY-MM-DD.csv`。
- **用途**: 這些數據可用於監控系統性能、分析用戶行為和排查問題。

### 3. 在線人數統計 (`onLineCount` 函數 - 已註釋)
- 檔案中包含一個被註釋掉的 `onLineCount()` 函數，該函數旨在通過檢查 Session 文件來統計在線用戶數。這表明系統曾經或計劃使用這種方式來統計在線人數。

### 4. 頁尾內容與連結
- **關於我們**: 包含指向 `Privacy_Page.php` (因材網同意書) 的連結。
- **操作資訊**: 包含指向教師資源分享區 (Google Drive)、系統操作說明、訪客帳號申請和網路速度測試的連結。
- **版權信息**: 顯示「© since 2016 國立臺中教育大學 ♦ 測驗統計與適性學習研究中心」。
- **最佳瀏覽建議**: 建議使用 Chrome 瀏覽器，最佳解析度為 1280x768。
- **在線人數顯示**: 如果用戶的 `access_level` 大於 69（可能代表管理員），則會顯示「目前在線人數」。

### 5. 前端結構與樣式
- **HTML 結構**: 頁尾包含左右兩個區塊 (`footer-left` 和 `footer-right`)，用於組織不同的信息。
- **CSS 樣式**: 包含內聯 `<style>` 標籤，用於調整頁尾的佈局和樣式，例如字體大小、間距和圖片位置。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `Privacy_Page.php`
- `images/teacher-logo.png`
- `images/logo.png`
- `$_SESSION['user_data']` (用於判斷是否顯示在線人數)

## 安全考量
- **敏感路徑**: `SystemLoading` 函數將數據寫入 `/mnt/ramdisk/`，這是一個敏感的系統路徑。需要確保對該目錄的寫入權限受到嚴格控制，以防止惡意寫入或信息洩露。
- **日誌數據安全**: 記錄的系統負載數據可能包含用戶 ID 和 IP 地址等信息，需要確保這些日誌文件的安全儲存和訪問控制。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `onLineCount()` 函數被註釋掉的原因，以及目前在線人數統計的實現方式。
- 確認 `/mnt/ramdisk/` 目錄的權限設置，確保其安全性。
- 了解 `Privacy_Page.php` 的具體內容。

