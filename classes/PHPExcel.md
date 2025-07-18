# PHPExcel.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PHPExcel.php`

## 概述
`PHPExcel.php` 檔案定義了 `PHPExcel` 類別，這是 **PHPExcel 函式庫的核心類別**。PHPExcel 是一個流行的 PHP 函式庫，用於讀取、寫入和操作各種試算表文件格式（例如 Excel）。這個檔案是整個 Excel 處理功能的入口點，它管理著工作表集合、文件屬性、安全性、命名範圍和單元格樣式等。

## 主要功能與職責

### 1. 函式庫初始化
- 定義 `PHPEXCEL_ROOT` 常量，並引入 `PHPExcel/Autoloader.php`，用於自動載入函式庫的其他組件。

### 2. Excel 工作簿管理
- **屬性**: 包含 `uniqueID` (唯一 ID), `properties` (文件屬性), `security` (文件安全性), `workSheetCollection` (工作表集合), `calculationEngine` (計算引擎), `activeSheetIndex` (當前活動工作表索引), `namedRanges` (命名範圍), `cellXfSupervisor` (單元格樣式監管器), `cellXfCollection` (單元格樣式集合), `cellStyleXfCollection` (單元格樣式集合) 等。
- **構造函數 (`__construct`)**: 初始化一個新的 `PHPExcel` 物件，並創建一個預設的工作表。
- **析構函數 (`__destruct`)**: 清理資源，斷開工作表連接。

### 3. 工作表操作
- `getActiveSheet()`: 獲取當前活動的工作表。
- `createSheet($iSheetIndex = null)`: 創建一個新的工作表並添加到工作簿中。
- `sheetNameExists($pSheetName)`: 檢查指定名稱的工作表是否存在。
- `addSheet(PHPExcel_Worksheet $pSheet, $iSheetIndex = null)`: 添加一個工作表到工作簿中。
- `removeSheetByIndex($pIndex = 0)`: 根據索引刪除工作表。
- `getSheet($pIndex = 0)`: 根據索引獲取工作表。
- `getAllSheets()`: 獲取所有工作表。
- `getSheetByName($pName = '')`: 根據名稱獲取工作表。
- `getIndex(PHPExcel_Worksheet $pSheet)`: 獲取工作表的索引。
- `setIndexByName($sheetName, $newIndex)`: 根據名稱設置工作表的索引。
- `getSheetCount()`: 獲取工作表的數量。
- `setActiveSheetIndex($pIndex = 0)`: 設置活動工作表的索引。
- `setActiveSheetIndexByName($pValue = '')`: 根據名稱設置活動工作表的索引。
- `getSheetNames()`: 獲取所有工作表的名稱。
- `addExternalSheet(PHPExcel_Worksheet $pSheet, $iSheetIndex = null)`: 添加外部工作表。

### 4. 文件屬性與安全性
- `getProperties()`, `setProperties()`: 獲取和設置文件屬性 (例如作者、標題)。
- `getSecurity()`, `setSecurity()`: 獲取和設置文件安全性 (例如密碼保護)。

### 5. 巨集 (Macros) 和 Ribbon XML
- `hasMacros()`, `setHasMacros()`, `getMacrosCode()`, `setMacrosCode()`, `hasMacrosCertificate()`, `setMacrosCertificate()`, `getMacrosCertificate()`, `discardMacros()`: 處理 Excel 文件中的巨集代碼和憑證。
- `setRibbonXMLData()`, `getRibbonXMLData()`, `setRibbonBinObjects()`, `getRibbonBinObjects()`, `hasRibbon()`, `hasRibbonBinObjects()`: 處理 Excel 2007 及更高版本中的自定義 Ribbon XML 數據和二進制對象。

### 6. 命名範圍 (Named Ranges)
- `getNamedRanges()`, `addNamedRange()`, `getNamedRange()`, `removeNamedRange()`: 管理 Excel 文件中的命名範圍。

### 7. 單元格樣式 (Cell Styles)
- `getCellXfCollection()`, `getCellXfByIndex()`, `getCellXfByHashCode()`, `cellXfExists()`, `getDefaultStyle()`, `addCellXf()`, `removeCellXfByIndex()`: 管理單元格的格式化樣式。
- `getCellStyleXfCollection()`, `getCellStyleXfByIndex()`, `getCellStyleXfByHashCode()`, `addCellStyleXf()`, `removeCellStyleXfByIndex()`: 管理單元格的樣式。

### 8. 垃圾回收 (`garbageCollect()`)
- 移除未被引用的單元格樣式，並更新所有單元格和列的樣式索引，以優化文件大小。

### 9. 複製與克隆
- `copy()`: 創建工作簿的副本。
- `__clone()`: 實現深度克隆，確保複製的物件是獨立的。

## 關鍵引用與依賴
- `PHPExcel/Autoloader.php` (用於自動載入其他 PHPExcel 組件)
- `PHPExcel_Worksheet`, `PHPExcel_DocumentProperties`, `PHPExcel_DocumentSecurity`, `PHPExcel_Calculation`, `PHPExcel_NamedRange`, `PHPExcel_Style` 等 PHPExcel 內部類別。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解因材網專案中哪些地方使用了 PHPExcel 函式庫，以及其具體用途（例如，匯入/匯出數據、生成報告等）。
- 查找 `classes/PHPExcel/` 目錄下的其他 PHPExcel 相關檔案，以了解其完整結構。
