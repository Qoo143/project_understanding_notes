# SchoolData.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\SchoolData.php`

## 概述
`SchoolData.php` 檔案定義了 `SchoolData` 類別，這是一個用於**獲取學校和班級數據**的組件。它主要負責從資料庫中查詢學校的基本信息以及特定學年度的班級成員列表。該類別旨在提供對學校和班級數據的訪問介面。

## 主要功能與職責

### 1. 構造函數 (`SchoolData($school_id)`)
- 初始化類別，接收學校 ID (`$school_id`)。
- 從 `organization` 表中查詢學校的基本數據，並儲存到 `basic_data` 屬性中。

### 2. 學年度班級數據 (`SemeYearClass($semeyear)`)
- **目的**: 根據學年度獲取班級數據。然而，該方法目前的實現與構造函數重複，並且沒有實際查詢班級數據的邏輯，註釋中提到需要完成學生升級功能。

### 3. 班級成員數據 (`ClassData($cl_item)`)
- **目的**: 根據組織 ID、年級和班級獲取班級成員列表。
- **數據來源**: 從 `user_info` 和 `user_status` 表中查詢學生數據，篩選條件包括組織 ID、年級、班級以及 `access_level < 10` (排除教師和管理員等)。
- **數據處理**: 將查詢結果格式化為包含 `uid`, `user_id`, `uname`, `access_level` 的字串，並儲存到 `member` 屬性中。同時也儲存了 `ClassUserID` 和 `ClassUserName` 陣列。

### 4. 數據獲取方法 (`get*` 系列)
- `getBasicData()`: 獲取學校的基本數據。
- `getSemeYearClass($semeyear)`: 獲取指定學年度下的所有班級名稱（目前實現有問題）。
- `getClassMember()`: 獲取班級成員的詳細信息。
- `getClassUserID()`: 獲取班級成員的用戶 ID 列表。
- `getClassUserName()`: 獲取班級成員的用戶姓名列表。

## 數據流與安全考量
- **資料庫互動**: 類別通過全局 `$dbh` (PDO 連接) 與 `organization`, `user_info`, `user_status` 等資料庫表互動。
- **SQL 注入**: 類別中的資料庫查詢使用了 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。但 `ClassData()` 方法中的 SQL 查詢直接拼接了 `$cl_item` 陣列中的值，這存在**潛在的 SQL 注入風險**，需要確保 `$cl_item` 中的數據在傳遞給此方法之前經過了嚴格的驗證和清理。

## 閱讀狀態
已閱讀

## 待辦事項
- 修正 `SemeYearClass()` 方法的實現，使其能夠正確獲取指定學年度的班級數據。
- **修復 `ClassData()` 方法中的 SQL 注入風險**，確保 `$cl_item` 中的數據經過參數綁定或嚴格過濾。
- 了解 `organization`, `user_info`, `user_status` 資料庫表的具體結構和數據含義。