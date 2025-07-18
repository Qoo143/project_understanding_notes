# TeacherClasses.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\TeacherClasses.php`

## 概述
`TeacherClasses.php` 檔案定義了 `TeacherClasses` 類別，這是一個專門用於**管理教師所屬班級和學生列表**的組件。它提供了獲取教師任教的普通班級、自組班級、學習扶助班級以及學生小組的功能。該類別旨在為教師提供其所負責的學生群體的全面視圖，以便進行教學管理和數據分析。

## 主要功能與職責

### 1. 常量定義
- 定義了多個常量來表示不同的班級類型：
    - `CLASS_N` (`normal_class`): 普通年班
    - `CLASS_API` (`normal_class_api`): 普通年班 (API 用)
    - `CLASS_T` (`teacher_class`): 自組班級
    - `CLASS_R` (`remedial_class`): 學習扶助班級
    - `CLASS_G` (`student_group`): 學生小組
- `CLASS_TYPES`: 包含所有班級類型的陣列。
- `ORDER_STRING`: 定義了班級列表的排序規則。

### 2. 構造函數 (`__construct`)
- 初始化類別，接收教師 ID (`$sTeacherId`)、是否需要取得小組資料 (`$bGetGroup`) 和指定學期 (`$semester`)。
- 實例化 `UserData` 類別，獲取教師的用戶數據。

### 3. 獲取班級列表 (`getAllClasses()`, `getAllClassesForSchoolAdmin()`, `getAllClassesApi()`)
- `getAllClasses()`:
    - **目的**: 獲取教師所屬的所有班級列表。
    - **權限判斷**: 根據教師的 `access_level`，判斷是獲取學校所有班級（校管）還是教師自己的班級。
    - **數據來源**: 內部呼叫 `getSchoolAllClasses()` 或 `getTeacherAllClasses()`。
- `getAllClassesForSchoolAdmin()`: 專門為校管獲取所有班級列表。
- `getAllClassesApi()`: 專門為 API 請求獲取教師所屬的普通班級列表。

### 4. 內部班級獲取方法 (`protected`)
- `getSchoolAllClasses()`: 獲取學校所有班級（普通班級、自組班級、學習扶助班級、學生小組）。
- `getSchooAllNormalClasses()`: 獲取學校所有普通班級。
- `getSchoolAllTeacherClasses()`: 獲取學校所有自組班級。
- `getSchoolAllRemedialClasses()`: 獲取學校所有學習扶助班級。
- `getStudentGroup()`: 獲取學生小組。
- `getTeacherAllClasses()`: 獲取教師所屬的所有班級（包括普通班級、自組班級、學習扶助班級和學生小組）。
- `getTeacherAllApiClasses()`: 獲取教師所屬的普通班級列表 (API 用)。

### 5. 獲取學生清單 (`getStudentList($sClassType, $sClassSn, $sSeme)`)
- **目的**: 根據班級類型、班級編號和學期獲取該班級的學生清單。
- **數據來源**: 根據班級類型，從 `seme_student`, `teacher_class_member`, `teacher_class`, `remedial_student`, `remedial_class`, `user_group` 等資料庫表獲取學生數據。
- **數據處理**: 對 `class_sn` 進行 `hash2string()` 解碼，並對 `user_id` 進行 `string2hash()` 編碼。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `user_info`, `user_status`, `seme_student`, `teacher_class`, `teacher_class_member`, `remedial_class`, `remedial_student`, `seme_group`, `user_group`, `export_school` 等表互動。
- **權限控制**: 許多方法會根據 `vUserData->access_level` 進行權限判斷，以限制數據的訪問範圍。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **數據編碼/解碼**: `class_sn` 和 `user_id` 進行了 `string2hash()` 和 `hash2string()` 編碼/解碼，增加了 URL 的可讀性，但其安全性取決於 `string2hash()` 的強度。
- **錯誤處理**: 包含 `try-catch` 區塊處理 PDO 異常。

## 關鍵引用與依賴
- `UserData` 類別 (可能定義在 `classes/adp_core_class.php`)
- 全局 `$dbh_slave` (資料庫讀取連接)
- `Semester_id2FullName()` 函數 (可能定義在 `include/adp_core_function.php`)
- `string2hash()` 和 `hash2string()` 函數 (可能定義在 `include/adp_core_function.php`)
- `USER_TEACHER_GROUP`, `USER_SCHOOL_ADMIN`, `USER_STUDENT_GROUP` 等常量 (可能定義在 `include/config.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `teacher_class`, `remedial_class`, `seme_group` 等資料庫表的具體結構和數據含義。
- 了解 `fn_get_class_full_name_by_semester()` 函數的具體實現。