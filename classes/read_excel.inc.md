# read_excel.inc.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\read_excel.inc.php`

## 概述
`read_excel.inc.php` 檔案包含兩個核心函數：`read_excel()` 和 `read_excel_2j()`，它們都用於**讀取 Excel 試算表文件**。這些函數依賴於外部的 `Excel/reader.php` 和 `Excel/reader_2j.php` 函式庫來解析 Excel 內容，並將數據提取為 PHP 陣列。這個檔案在因材網中可能用於處理批次數據匯入，例如用戶資料、試題或概念結構等。

## 主要功能與職責

### 1. `read_excel($path, $prog_line)` 函數
- **目的**: 讀取指定路徑的 Excel 檔案內容。
- **依賴**: `require_once 'Excel/reader.php';`，這表明它使用了舊版或標準的 Excel 讀取器。
- **檔案存在檢查**: 呼叫 `chk_file_exist($path, $prog_line)` 函數，確保要讀取的檔案存在。
- **讀取邏輯**: 
    - 實例化 `Spreadsheet_Excel_Reader` 物件。
    - 設定輸出編碼為 `utf-8`。
    - 呼叫 `$excel_data->read($path)` 讀取 Excel 檔案。
    - 遍歷 Excel 文件的第一個工作表 (`sheets[0]`)，從第二行第二列開始讀取數據，並將其儲存到一個二維陣列 `$data` 中。
- **返回值**: 返回一個包含 Excel 數據的二維陣列。

### 2. `read_excel_2j($path, $prog_line)` 函數
- **目的**: 讀取指定路徑的 Excel 檔案內容，據註解說明，這是較新的版本。
- **依賴**: `require_once 'Excel/reader_2j.php';`，這表明它使用了另一個版本的 Excel 讀取器，可能支援更新的 Excel 格式或提供更好的性能。
- **讀取邏輯**: 與 `read_excel()` 函數的邏輯基本相同，只是引入了不同的讀取器。

## 關鍵引用與依賴
- `Excel/reader.php` (外部函式庫)
- `Excel/reader_2j.php` (外部函式庫)
- `chk_file_exist()` 函數 (可能定義在 `include/adp_core_function.php`)

## 安全考量
- **檔案路徑驗證**: `chk_file_exist()` 函數用於檢查檔案是否存在，但需要確保 `$path` 參數在傳遞給此函數之前經過了嚴格的驗證和清理，以防止路徑遍歷漏洞。
- **外部函式庫安全性**: `Excel/reader.php` 和 `Excel/reader_2j.php` 是外部函式庫，需要確保它們是最新且安全的版本，沒有已知的漏洞。
- **數據處理**: 讀取 Excel 數據後，在將其用於資料庫操作或顯示之前，應對數據進行適當的驗證和清理，以防止惡意數據注入。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `Excel/reader.php` 和 `Excel/reader_2j.php` 這兩個函式庫的具體差異和功能。
- 查找因材網專案中哪些地方使用了 `read_excel()` 或 `read_excel_2j()` 函數，以及它們處理的 Excel 檔案類型和內容。
