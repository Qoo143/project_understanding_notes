# SemeDate.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\SemeDate.php`

## 概述
`SemeDate.php` 檔案定義了 `SemeDate` 類別，這是一個**靜態工具類別**，專門用於**處理學年度學期的開始和結束日期**。它提供了一個方法，可以根據因材網特有的學年度學期編碼，計算出對應的具體日期時間。

## 主要功能與職責

### 1. 常量定義
- `DATE_GET_START_DATE`, `DATE_GET_END_DATE`: 用於指定獲取學期開始或結束日期的模式。
- `DATE_FIRST_SEME_START`, `DATE_FIRST_SEME_END`: 定義上學期的開始和結束月份日期。
- `DATE_LAST_SEME_START`, `DATE_LAST_SEME_END`: 定義下學期的開始和結束月份日期。
- `VID_THIS_YEAR`, `VID_NEXT_YEAR`: 用於將學年度轉換為西元年份的偏移量。

### 2. 獲取學期日期 (`static function getSemeDate(string $iYearSeme, int $iSemeMode): DateTime`)
- **目的**: 根據學年度學期編碼 (`$iYearSeme`) 和模式 (`$iSemeMode`)，返回對應的 `DateTime` 物件。
- **參數**: 
    - `$iYearSeme`: 學年度學期，例如 `1121` (112學年度上學期) 或 `1122` (112學年度下學期)。
    - `$iSemeMode`: 指定是獲取學期開始日期 (`1`) 還是結束日期 (`2`)。
- **邏輯**: 
    - 從 `$iYearSeme` 中提取學年度 (`$iYear`) 和學期標誌（最後一位數字）。
    - 根據學期標誌判斷是上學期還是下學期。
    - 根據學期和模式，結合預定義的月份日期常量和年份偏移量，構建一個日期字串。
    - 使用 `new DateTime()` 將日期字串轉換為 `DateTime` 物件並返回。

## 關鍵引用與依賴
- `Formater.php` (雖然在檔案中被 `require_once`，但 `SemeDate` 類別本身並未直接使用 `Formater` 類別的任何方法。這可能是一個通用引入，或者 `Formater` 中的常量被其他地方使用)。

## 安全考量
- 該類別主要進行日期計算，不涉及資料庫操作或用戶輸入，因此直接的安全風險較低。
- 確保輸入的 `$iYearSeme` 格式正確，以避免 `substr` 和 `intval` 函數的意外行為。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `Formater.php` 被引入此檔案的具體原因，即使 `SemeDate` 類別本身沒有直接使用它。
- 了解因材網中學年度學期編碼的具體規則和歷史演變。
