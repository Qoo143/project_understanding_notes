# HighSchool.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\HighSchool.php`

## 概述
`HighSchool.php` 檔案定義了 `HighSchool` 類別，它繼承自 `School` 類別，專門用於處理**高中職及大學的班級管理**。該類別提供了新增、編輯和刪除班級的功能，並能獲取高中職的群科對應關係以及班級篩選選項。它處理了高中職特有的學制和班級編號邏輯。

## 主要功能與職責

### 1. 繼承與常量
- 繼承自 `School` 類別，表明它重用了 `School` 類別的基礎功能。
- 定義了 `GRADE_RANGE` 常量，指定高中職的年級範圍為 10 到 12 年級。

### 2. 高中職群科管理
- `getSchoolDeptMappings()`:
    - **功能**: 獲取高中職的學校類型、群別和科別的對應關係。
    - **數據來源**: 查詢 `high_school_dept_mapping`、`high_school_type`、`high_school_group` 和 `high_school_dept` 等資料庫表。
    - **用途**: 用於前端介面中選擇高中職的群科組合。

### 3. 班級篩選選項
- `getFilterOptions()`:
    - **功能**: 獲取用於篩選班級的選項，包括學期、年級和群科組合。
    - **數據來源**: 查詢 `school_class_info` 表，並結合 `high_school_dept_mapping` 等表獲取詳細信息。
    - **用途**: 用於前端介面中提供班級篩選功能。

### 4. 班級增刪改查 (CRUD)
- `addClass(array $aParameter)`:
    - **功能**: 新增高中職班級。
    - **權限檢查**: 檢查用戶是否為學校教職員 (`bIsSchoolStaff`)。
    - **數據驗證**: 呼叫 `checkClassInfo()` 驗證年級、學校類型、群別和科別的合法性。
    - **邏輯**: 查詢 `high_school_dept_mapping` 獲取科別對應的 `dept_mapping_sn`。檢查班級是否已存在。自動生成 `class_number` (班級編號)，並將班級信息插入 `school_class_info` 表。
    - **事務處理**: 使用資料庫事務 (`beginTransaction`, `commit`, `rollBack`) 確保數據一致性。
- `editClass(array $aParameter)`:
    - **功能**: 編輯高中職班級信息。
    - **邏輯**: 類似 `addClass`，但會處理年級或學期變更時 `class_number` 的調整邏輯，確保班級編號的唯一性。
- `deleteClass($iClassSn)`:
    - **功能**: 刪除高中職班級。
    - **邏輯**: 檢查班級內是否還有學生或教職員，如果沒有則從 `school_class_info` 表中刪除班級。

### 5. 大學班級管理
- `addUniversityClass(array $aParameter)`:
    - **功能**: 新增大學班級。邏輯類似 `addClass`，但沒有高中職特有的群科概念。
- `editUniversityClass(array $aParameter)`:
    - **功能**: 編輯大學班級信息。邏輯類似 `editClass`。
- `checkUniversityClassInfo(array $aParameter)`:
    - **功能**: 驗證大學班級信息的合法性，檢查年級範圍（13到16年級）和班級名稱。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `high_school_dept_mapping`, `high_school_type`, `high_school_group`, `high_school_dept`, `school_class_info`, `user_info`, `seme_student` 等表互動。
- **權限控制**: 在新增、編輯和刪除班級時都進行了權限檢查 (`bIsSchoolStaff`)。
- **數據驗證**: 在新增和編輯班級前都進行了嚴格的參數驗證，防止不合法數據的寫入。
- **事務處理**: 使用事務確保資料庫操作的原子性，防止部分更新導致的數據不一致。
- **錯誤處理**: 包含 `try-catch` 區塊處理資料庫異常，並返回錯誤訊息。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **鎖定超時與死鎖**: 類別中包含了對 `Lock wait timeout` 和 `Deadlock` 異常的處理，表明在併發操作下考慮了資料庫鎖定問題。

## 關鍵引用與依賴
- `School` 類別 (父類別)
- 全局 `$dbh`, `$dbh_slave` (資料庫連接)
- `Semester_id2FullName()` 函數 (可能定義在 `include/adp_core_function.php`)
- `oUserData` 屬性 (可能來自 `UserData` 類別，包含當前用戶信息)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `School` 類別的具體實現，以及 `HighSchool` 類別如何擴展其功能。
- 了解 `oUserData` 屬性的來源和其包含的用戶信息。
- 深入研究 `high_school_dept_mapping` 等高中職相關資料庫表的結構和數據含義。
