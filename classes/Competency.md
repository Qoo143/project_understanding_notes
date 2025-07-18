# Competency.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Competency.php`

## 概述
`Competency.php` 檔案定義了 `Competency` 類別，這是一個專門用於處理**核心素養評量**的組件。它主要負責從資料庫中獲取與素養單元相關的數據，計算學生的素養分數，並將其分類和匯總。該類別支援不同類型的素養評量，例如 CPS (合作問題解決) 和 GC (全球素養)。

## 主要功能與職責

### 1. 初始化與資料庫連接
- 構造函數 `__construct()` 初始化類別，並通過 `global $dbh;` 獲取全局資料庫連接。

### 2. 模擬登入 (`login()`)
- 包含一個 `login()` 方法，用於設置 Session 變數以模擬教師登入。這可能是一個測試或開發用途的功能，不應在生產環境中被實際調用。

### 3. 素養單元數據獲取 (`getElementById()`, `getUnit()`)
- `getElementById($id)`: 根據 `unit_id` 從 `competency_unit` 表中獲取單元數據。它還會根據素養類型（CPS, GC, CT）構建單元內容的 URL，並可能包含任務相關的參數 (`aialcode`)。
- `getUnit($unit_id="all")`: 靜態方法，用於獲取所有或特定素養單元的數據。它會將數據按素養類型 (`genres_en`) 分組，並返回單元名稱、描述和科目 ID。

### 4. 分數計算與匯總
- `getFullScore($num)`: 獲取指定單元的總分。它從 `speak_data` 表中查詢數據，並計算每個素養指標的總分。
- `createUserScore($mission)`: 從資料庫中撈取用戶的原始素養分數。它根據任務信息 (`mission`) 和用戶列表 (`userList`) 查詢 `opt_record` 表。如果用戶是家長，則會額外篩選其綁定的學生數據。
- `getUserScore()`: 處理原始分數，計算每個素養指標的平均分，並將其與總分進行比較。
- `summary($userScore)`: 匯總用戶分數，計算總分、滿分和平均分。
- `sumScore($array, $column)`: 輔助函數，用於對陣列中指定列的分數進行加總。
- `average($numerator, $denominator)`: 輔助函數，用於計算分數的平均值。

### 5. 分組與標準化
- `groupByUnit($userScore)`: 將用戶分數按單元分組。
- `groupByUser($userScore)`: 將用戶分數按學生分組。
- `groupByStandard($userScore)`: 將用戶分數按標準分組，並根據分數範圍（<0.5, >=0.8, 其他）將學生分類為「bottom」、「top」和「mean」。
- `expandDSC($score, $type)`: 根據類型（'x' 或 'y'）擴展素養指標分數，可能用於二維素養模型的計算。
- `createDSC()`: 從 `map_dsc` 表中獲取素養指標的名稱。
- `getDSC()`: 返回素養指標的名稱列表。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫查詢，從 `competency_unit`, `speak_data`, `opt_record`, `map_dsc`, `user_family`, `user_info` 等表獲取數據。
- **Session 數據**: 類別會讀取 `$_SESSION['user_data']` 中的用戶權限 (`access_level`)，並在 `login()` 方法中設置 Session 數據。
- **XSS 防護**: 雖然檔案中沒有直接的 `preventXss()` 調用，但其輸入參數和輸出到 HTML 的數據應在調用方或前端進行適當的 XSS 防護。
- **SQL 注入**: 類別使用 `prepare()` 和 `bindValue()` 進行資料庫查詢，這有助於防止 SQL 注入。但需要確保所有綁定參數都正確使用。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `competency_unit`, `speak_data`, `opt_record`, `map_dsc` 等資料庫表的具體結構和數據含義。
- 了解 `string2hash()` 函數的具體實現。
- 了解 `unserialize_with_hash()` 函數的具體實現。
- 了解 `id2uname()` 函數的具體實現。
