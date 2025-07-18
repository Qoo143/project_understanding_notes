# adp_core_class.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\adp_core_class.php`

## 概述
`adp_core_class.php` 是一個**因材網的核心類別庫**，包含了多個關鍵的 PHP 類別，用於處理用戶數據、概念結構、試題數據、補救教學、班級數據、學校狀態、代幣獎勵以及用戶密碼變更等核心業務邏輯。這個檔案在整個應用程式中被廣泛引用，提供了許多基礎性的數據模型和操作方法。

## 主要類別與職責

### 1. `UserData` 類別
- **功能**: 用於獲取和管理用戶的詳細資訊。在用戶登入後，會實例化此類別來填充 `$_SESSION['user_data']`。
- **數據來源**: 主要從 `user_info` 和 `user_status` 資料庫表獲取數據。
- **屬性**: 包含 `user_id`, `user_account`, `uname`, `email`, `access_level`, `organization_id`, `grade`, `class_name` 等。
- **關鍵點**: 處理用戶的中文班級名稱 (`cht_class`)、OpenID `sub`、以及判斷帳號是否啟用 (`id_used`) 和 Email 是否驗證 (`email_verify`)。

### 2. `ConceptDataBN` 類別
- **功能**: 用於獲取和管理單元概念的數據，特別是與知識結構相關的檔案路徑和元數據。
- **數據來源**: 從 `concept_info` 資料庫表獲取數據。
- **屬性**: 包含 `cs_id`, `publisher_id`, `subject_id`, `vol`, `unit`, `grade`, `concept_name`, `matrix_map_file` 等。
- **關鍵點**: 構建概念結構相關檔案的本地路徑 (`file_path`) 和 URL 路徑 (`url_path`)，並將其儲存到 `$_SESSION['cs_path']` 和 `$_SESSION['pic_path']`。

### 3. `ItemData` 類別
- **功能**: 用於獲取和管理單個試題的詳細數據。
- **數據來源**: 從 `concept_item` 和 `concept_info` 資料庫表獲取數據。
- **屬性**: 包含 `item_sn`, `item_content`, `item_filename`, `op_filename` (選項圖片檔名), `item_correct_answer`, `score` 等。
- **關鍵點**: 處理試題內容、選項、答案、分數，並構建相關圖片和語音檔案的路徑。

### 4. `RemedyData` 類別
- **功能**: 用於獲取和管理補救教學概念的數據，特別是與補救結構和閾值相關的資訊。
- **數據來源**: 從 `concept_info` 和 `concept_remedy` 資料庫表獲取數據，並讀取 Excel 檔案 (`read_excel()`)。
- **關鍵點**: 處理補救教學的結構 (`structure`) 和閾值 (`threshold`)，以及與試題概念的關聯。

### 5. `ClassData` 和 `ClassDataSemester` 類別
- **功能**: 用於獲取和管理班級成員的數據。
- **數據來源**: 從 `user_info`, `user_status`, `teacher_class_member`, `remedial_student`, `seme_student` 等資料庫表獲取數據。
- **關鍵點**: 根據組織 ID、年級、班級或自組班級（例如教師自組班級、學扶班）篩選學生，並返回班級成員的用戶 ID 和姓名列表。

### 6. `Concept_Structure` 類別
- **功能**: 用於處理概念結構的數據，包括結構圖、百分比圖和試題序列。
- **數據來源**: 從 `concept_info` 資料庫表獲取數據，並讀取 Excel 檔案 (`read_excel()`)。
- **關鍵點**: 計算試題的順序 (`set_item_sequence()`)，並處理隨機排序 (`set_rand_sequence()`, `set_order_sequence()`)。

### 7. `Print_Student_Data` 類別
- **功能**: 用於生成學生的診斷報告，包括基本資料、學習記錄、概念掌握情況和補救建議。
- **數據來源**: 從 `exam_record`, `map_node_student_status`, `video_review_record`, `prac_answer`, `exam_record_dynamic`, `prac_blank_filling_record` 等資料庫表獲取數據。
- **關鍵點**: 整合多個數據源，生成詳細的 HTML 報告，並處理遊戲化（金幣獎勵）的顯示邏輯。

### 8. `schoolStatus` 和 `schoolStatusSeme` 類別
- **功能**: 用於獲取和管理學校或班級的學生學習狀態數據，特別是針對知識節點的掌握情況。
- **數據來源**: 從 `user_info`, `user_status`, `seme_student`, `map_node_student_status`, `map_node`, `organization` 等資料庫表獲取數據。
- **關鍵點**: 計算學生在各個知識節點（大節點 `bNode` 和小節點 `sNode`）的通過、未通過和未測驗人數，並進行統計。

### 9. `giveCoin` 類別
- **功能**: 用於計算和發放學生的代幣獎勵。
- **數據來源**: 從 `mission_info`, `mission_google_form`, `video_review_record`, `prac_answer`, `exam_record_interactive`, `prac_blank_filling_record`, `exam_record`, `eduexam_record`, `exam_record_indicate`, `exam_record_dynamic`, `opt_record`, `exam_record_literacy`, `exam_record_literacy_interactive`, `mc_write_submit`, `mc_speech_submit`, `role_coin_total`, `role_reward_way` 等資料庫表獲取數據。
- **關鍵點**: 根據不同的學習動作（影片、練習題、測驗等）和任務類型，計算學生應獲得的代幣數量，並更新 `role_coin_total` 和 `role_coin_history` 表。

### 10. `UserChangePassword` 類別
- **功能**: 用於處理用戶密碼的變更和管理密碼歷史記錄。
- **數據來源**: 從 `user_past_password`, `user_status`, `user_info` 資料庫表獲取數據。
- **關鍵點**: 檢查新密碼是否與過去的密碼重複，發送密碼變更確認郵件，並更新資料庫中的密碼和密碼更新時間。

## 數據流與安全考量
- **數據獲取**: 大部分類別都直接或間接通過全局 `$dbh` (PDO 連接) 與資料庫互動，執行 SQL 查詢來獲取數據。
- **XSS 防護**: 許多地方使用了 `htmlspecialchars()` 和 `preventXss()` 對從資料庫讀取或用戶輸入的數據進行處理，以防止 XSS 攻擊。
- **密碼雜湊**: `UserChangePassword` 類別使用 `password_hash()` 進行密碼雜湊，這是良好的安全實踐。
- **Session 依賴**: 許多類別依賴 `$_SESSION` 中的用戶數據或配置信息。
- **複雜的 SQL 查詢**: 許多類別中的 SQL 查詢非常複雜，涉及多表 JOIN 和條件判斷，需要確保所有參數都經過了正確的綁定和過濾，以防止 SQL 注入。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究每個類別中涉及的資料庫表結構和它們之間的關聯。
- 了解 `adp_API.php` 中 `UserData` 類別的具體實現。
- 了解 `phpmailer_sendmail.php` 的具體實現。
- 了解 `read_excel()` 函數的具體實現。
- 了解 `unserialize_with_hash()` 函數的具體實現。
