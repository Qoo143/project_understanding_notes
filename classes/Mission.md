# Mission.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Mission.php`

## 概述
`Mission.php` 檔案定義了 `Mission` 類別，這是一個用於**管理因材網中學習任務**的核心組件。它提供了任務的創建、查詢、學生任務記錄的生成和更新等功能。該類別旨在簡化任務相關的資料庫操作，並確保數據的一致性。

## 主要功能與職責

### 1. 任務創建 (`static function create($mission_data)`)
- **目的**: 創建一個新的學習任務並將其儲存到資料庫中。
- **自動填充**: 自動填充 `semester` (學期) 和 `share` (共享設定) 欄位。
- **必填欄位檢查**: 檢查 `mission_nm` (任務名稱)、`target_id` (目標學生/班級 ID)、`subject_id` (科目 ID)、`node` (任務內容節點)、`teacher_id` (教師 ID)、`target_type` (目標類型) 和 `mission_type` (任務類型) 等必填欄位。
- **資料庫插入**: 將任務數據插入 `mission_info` 表。
- **學生記錄生成**: 成功創建任務後，會呼叫 `createStudentRecord()` 為任務目標中的每個學生創建 `mission_stud_record`。

### 2. 任務查詢 (`static function search($mission_sn=null, $mission_type=null)`)
- **目的**: 根據任務序號 (`mission_sn`) 或任務類型 (`mission_type`) 查詢任務信息。
- **靈活性**: 支援查詢單個任務或多個任務。
- **數據返回**: 返回 `Mission` 類別的實例或實例陣列。

### 3. 學生任務記錄管理
- `createStudentRecord()`:
    - **目的**: 為任務目標中的每個學生在 `mission_stud_record` 表中創建一條記錄。
    - **初始化**: `finish_node` 欄位被序列化為空陣列，表示學生尚未完成任何節點。
- `getRecord($user_id=null)`:
    - **目的**: 獲取指定學生或所有學生的任務記錄。
- `initialize()`:
    - **目的**: 初始化任務實例的內部屬性，例如將 `target_id`、`group_id`、`node` 等字串轉換為陣列。
    - **數據處理**: 根據 `target_type` 處理目標學生列表，並篩選出已啟用的學生。
    - **完成率計算**: 計算任務的完成率 (`mission_finish_rate`)。
- `getStudentRecord()`:
    - **目的**: 從 `mission_stud_record` 表中獲取學生的任務記錄。
    - **數據處理**: 反序列化 `finish_node` 和 `action_compelete_count` 欄位，並計算 `remaining_node`。

### 4. 任務更新 (`public function update($column, $value)`)
- **目的**: 更新 `mission_info` 表中的指定欄位。

### 5. 輔助函數
- `static function toArray($string)`: 將字串轉換為陣列，使用 `_SPLIT_SYMBOL` 分隔。
- `static function toUser($seme, $array)`: 根據學期、組織 ID、年級和班級從 `seme_student` 表中獲取學生 ID 列表。
- `static function toClassUser($class_type, $seme, $class_sn)`: 根據班級類型（自組班級或學扶班級）獲取學生 ID 列表。
- `static function usedTarget($userList)`: 篩選出已啟用且未被導出的學生 ID 列表。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `mission_info`, `mission_stud_record`, `user_info`, `seme_student`, `teacher_class_member`, `remedial_student`, `export_school` 等表互動。
- **數據驗證**: `create` 方法中包含必填欄位檢查。在執行資料庫操作前，應確保所有輸入數據都經過了充分的驗證和清理。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **錯誤處理**: 包含 `try-catch` 區塊處理 PDO 異常。
- **序列化/反序列化**: `finish_node` 和 `action_compelete_count` 欄位使用 `serialize()` 和 `unserialize()` 儲存複雜數據結構，需要確保反序列化過程的安全性，防止反序列化漏洞。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `_SPLIT_SYMBOL` 常量的具體定義。
- 深入研究 `mission_info` 和 `mission_stud_record` 資料庫表的結構和數據含義。
- 了解 `unserialize_with_hash()` 函數的具體實現。
