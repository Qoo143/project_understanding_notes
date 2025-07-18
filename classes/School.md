# School.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\School.php`

## 概述
`School.php` 檔案定義了 `School` 類別，這是一個用於**學校班級管理**的基礎組件。它提供了獲取班級信息列表、班級序號與班級編號轉換等功能。這個類別是其他更具體學校類型（例如 `HighSchool.php`）的父類別，為它們提供了通用的班級管理邏輯。

## 主要功能與職責

### 1. 構造函數 (`__construct`)
- 初始化類別，接收用戶 ID (`$sUserId`)。
- 實例化 `UserData` 類別，獲取當前用戶的詳細信息 (`oUserData`)。
- 設置內部標誌，判斷當前用戶是否為 Session 管理員 (`bIsSessionUserAdmin`)、學生 (`bIsStudent`) 或學校教職員 (`bIsSchoolStaff`)。

### 2. 獲取班級信息列表 (`getClassInfoList`)
- **目的**: 根據組織 ID 和搜尋條件獲取班級信息列表。
- **權限檢查**: 如果當前用戶是學校教職員或學生，會檢查其 `organization_id` 是否與查詢的 `sOrganizationId` 匹配，以限制數據訪問。
- **篩選條件**: 支援按學期 (`semester`)、年級 (`grade`)、高中職群科對應 (`high_school_dept_mapping`) 和班級名稱 (`class_name`) 進行篩選。
- **排序**: 支援按年級、學校類型、群別、科別和班級名稱進行排序。
- **分頁**: 支援分頁功能，每頁顯示 10 筆記錄。
- **數據來源**: 查詢 `school_class_info` 表，並 `LEFT JOIN` `high_school_dept_mapping`, `high_school_type`, `high_school_group`, `high_school_dept` 等表以獲取詳細的班級信息。
- **數據處理**: 將 `semester` 轉換為中文名稱 (`semester_name`)。可選地對 `class_sn` 進行 `string2hash()` 編碼。
- **返回值**: 返回一個包含班級信息列表、總記錄數的陣列。

### 3. 班級序號與班級編號轉換
- `classSn2ClassNumber($iClassSn)`:
    - **目的**: 將班級序號 (`class_sn`) 轉換為班級編號 (`class_number`)。
    - **數據來源**: 查詢 `school_class_info` 表。
- `classFullName2ClassSn($sOrganizationId, $iSemester, $iGrade, $sClassFullName)`:
    - **目的**: 將班級全名（例如「資訊科 忠班」）轉換為班級序號 (`class_sn`)。
    - **數據來源**: 查詢 `school_class_info`, `high_school_dept_mapping`, `high_school_dept` 表。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `school_class_info`, `high_school_dept_mapping`, `high_school_type`, `high_school_group`, `high_school_dept` 等表互動。
- **權限控制**: 在 `getClassInfoList` 方法中實施了嚴格的權限檢查，確保用戶只能訪問其有權限的班級數據。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **數據編碼/解碼**: `class_sn` 可以選擇進行 `string2hash()` 編碼，增加了 URL 的可讀性，但其安全性取決於 `string2hash()` 的強度。

## 關鍵引用與依賴
- `UserData` 類別 (可能定義在 `classes/adp_core_class.php`)
- 全局 `$dbh`, `$dbh_slave` (資料庫連接)
- `Semester_id2FullName()` 函數 (可能定義在 `include/adp_core_function.php`)
- `string2hash()` 函數 (可能定義在 `include/adp_core_function.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `school_class_info`, `high_school_dept_mapping`, `high_school_type`, `high_school_group`, `high_school_dept` 等資料庫表的具體結構和數據含義。
- 了解 `UserData` 類別的具體實現。
- 了解 `Semester_id2FullName()` 和 `string2hash()` 函數的具體實現。
