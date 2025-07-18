# DynamicAssessment.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\DynamicAssessment.php`

## 概述
`DynamicAssessment.php` 檔案定義了兩個類別：`DynamicAssessment` 和 `DAitem`。它們共同用於處理因材網中的**動態評量 (Dynamic Assessment)** 功能，包括從資料庫中獲取評量試題、組織試題序列以及處理試題的詳細信息。

## 主要類別與職責

### 1. `DynamicAssessment` 類別
- **功能**: 作為動態評量的主要控制器，負責根據指標 ID (`ind_id`) 和任務序號 (`mission_sn`) 獲取相關的評量試題，並組織試題序列。
- **構造函數**: `__construct($ind_id, $mission_sn, $new="OldDA")` 接收指標 ID、任務序號和一個標誌 (`new`)，用於區分新舊版本的動態評量。
- **數據來源**: 主要從 `concept_item` 和 `concept_info` 資料庫表查詢試題數據。它會篩選 `template_id` 為 2 的試題，並按 `indicator_item_num` 排序。
- **屬性**: 包含 `ind_id`, `item` (試題數據陣列), `mission_sn`, `max_item_num` (最大題號) 等。
- **核心方法**: 
    - `set_item_sequence()`: 設置試題的序列，目前是按題號順序出題。
    - `set_item_data()`: 實例化 `DAitem` 類別來處理每個試題的詳細數據。
    - `get_item_num()`, `get_item_sequence()`, `get_ind_id()`, `get_selected_item_data()`, `get_mission_sn()`: 提供獲取評量相關數據的介面。
- **錯誤處理**: 如果沒有找到試題，會設置 `status` 為「沒有試題」。

### 2. `DAitem` 類別
- **功能**: 代表動態評量中的單個試題，負責解析和提供試題的詳細信息，包括圖片、選項、答案和相關路徑。
- **構造函數**: `__construct($myitem, $mission_sn, $new)` 接收一個包含試題原始數據的陣列，以及任務序號和新舊版本標誌。
- **數據解析**: 從傳入的 `$myitem` 陣列中提取試題的各項屬性，例如 `item_sn`, `exam_paper_id`, `item_filename`, `op_filename` (選項檔名), `op_content` (作答回饋), `template_id`, `item_correct_answer`, `points` (分數) 等。
- **路徑構建**: 根據 `publisher_id`, `subject_id`, `vol`, `unit` 等信息構建試題圖片和相關檔案的路徑。
- **核心方法**: 
    - `splitPaperId($paper_id, $subject_id)`: 輔助函數，用於解析試卷 ID，從中提取出版商 ID、科目 ID、冊別、單元和卷別。
    - 提供一系列 `get_` 方法來獲取試題的各項屬性，例如 `get_item_pic()`, `get_item_select_pic()`, `get_item_correct_answer()` 等。

## 數據流與安全考量
- **資料庫互動**: 兩個類別都直接或間接通過全局 `$dbh` (PDO 連接) 與資料庫互動，查詢 `concept_item` 和 `concept_info` 表來獲取試題數據。
- **數據解析**: 試題數據的解析涉及字串分割 (`explode(_SPLIT_SYMBOL, ...)`)，需要確保分隔符的正確使用和數據格式的一致性。
- **路徑構建**: 試題相關檔案的路徑是動態構建的，需要確保路徑的安全性，防止路徑遍歷漏洞。
- **XSS 防護**: 雖然檔案中沒有直接的 `preventXss()` 調用，但其輸入參數和輸出到 HTML 的數據應在調用方或前端進行適當的 XSS 防護。
- **SQL 注入**: 類別中的資料庫查詢使用了 `prepare()`，這有助於防止 SQL 注入。但需要確保所有綁定參數都正確使用。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `concept_item` 和 `concept_info` 資料庫表的具體結構和數據含義。
- 了解 `_ADP_EXAM_DB_PATH` 和 `_ADP_CS_UPLOAD_PATH` 等常量的定義。
- 了解 `SingleSelectItemDA.php` 和 `SingleSelectItemDA_new.php` 模板的具體內容和渲染方式。
