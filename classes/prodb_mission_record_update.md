# prodb_mission_record_update.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\prodb_mission_record_update.php`

## 概述
`prodb_mission_record_update.php` 檔案定義了 `update_mission_record` 類別，這是一個專門用於**更新學生任務記錄**的組件。它主要處理學生在任務中完成各種學習動作（例如觀看影片、練習題、測驗等）後的進度更新，包括已完成節點的記錄、任務完成狀態的判斷以及學習動作次數的統計。

## 主要功能與職責

### 1. 構造函數 (`__construct`)
- 初始化類別，接收 `mission_sn` (任務序號)、`action` (學習動作類型)、`stud_id` (學生 ID) 和 `mission_ind` (任務指標，例如節點 ID)。
- 檢查用戶權限，確保只有學生或特定情況下的教師才能更新任務進度。
- 檢查任務記錄是否存在，如果不存在則先插入一條新的記錄。
- 獲取任務的基本信息，包括任務類型、節點、已完成節點、總節點數等。

### 2. 任務記錄管理
- `get_mission_record()`: 從 `mission_stud_record` 和 `mission_info` 表中獲取當前任務的狀態和基本信息。
- `insert_mission_record()`: 如果學生沒有該任務記錄，則插入一條新的記錄到 `mission_stud_record` 表中。
- `check_record_exist($user_id, $mission_sn)`: 檢查指定學生和任務的記錄是否存在。
- `update_mission_record($all_finish)`: 更新 `mission_stud_record` 表中的任務狀態，包括 `finish_node` (已完成節點)、`finish_node_count` (已完成節點數)、`action_compelete_count` (學習動作完成次數)、`is_all_fin` (是否全部完成) 和 `finish_time` (完成時間)。

### 3. 任務進度計算
- `set_mission_record()`: 根據學習動作類型和任務類型，判斷要更新的任務節點，並計算 `finish_node_count` 和 `all_node_count`。
- `get_mission_node_count($mission_node_count2, $mission_type)`: 根據任務類型計算任務節點的總數。
- `caculate_action_count($update_mission_node)`: 統計每個學習動作的完成次數，並將其序列化儲存。
- `caculate_literacy_action_count($update_mission_node)`: 專門處理學科素養任務的學習動作次數統計。

### 4. 任務動作類型判斷
- `set_action_type($mission_type)`: 根據任務類型判斷學習動作的類型（例如 `knowleadge`, `questionaire`, `test`）。
- `count_assign_type($assign_type)`: 計算指派節點內有多少個任務。

### 5. 任務指派檢查
- `check_action_assigned($indicator, $action)`: 檢查指定的學習動作是否被指派給該任務。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `mission_stud_record`, `mission_info`, `user_info`, `user_status` 等表互動。
- **Session 數據**: 類別會讀取 `$_SESSION['user_data']` 中的用戶權限，並在內部使用 `UserData` 類別獲取用戶信息。
- **序列化/反序列化**: `finish_node` 和 `action_compelete_count` 欄位使用 `serialize()` 和 `unserialize()` 儲存複雜數據結構，需要確保反序列化過程的安全性，防止反序列化漏洞。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **錯誤處理**: 包含 `try-catch` 區塊處理 PDO 異常。

## 關鍵引用與依賴
- 全局 `$dbh` (資料庫連接)
- `UserData` 類別 (可能定義在 `classes/adp_core_class.php`)
- `_SPLIT_SYMBOL` 常量 (可能定義在 `include/config.php`)
- `USER_STUDENT_GROUP`, `USER_TEACHER_GROUP` 常量 (可能定義在 `include/config.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `mission_stud_record` 和 `mission_info` 資料庫表的具體結構和數據含義。
- 了解 `UserData` 類別的具體實現。
- 了解 `unserialize_with_hash()` 函數的具體實現。
