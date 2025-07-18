# report_learningeffect_class.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\report_learningeffect_class.php`

## 概述
`report_learningeffect_class.php` 檔案定義了 `ReportLearningeffectClass` 類別，這是一個**核心的數據報表處理組件**。它負責從多個資料庫表中獲取、處理和匯總各種學習數據，以生成不同層級（學校、班級、個人）和不同維度（影片、練習題、動態評量、縱貫測驗、單元測驗、AI 學習夥伴、登入日誌、遊戲時間、語音辨識等）的學習效果報告。該類別的設計旨在提供靈活的數據查詢和報告生成功能。

## 主要功能與職責

### 1. 屬性與參數
- 類別內部定義了大量的私有屬性，用於暫存查詢結果和配置查詢參數，例如 `vSubjectOption` (科目選項), `vCityArea` (城市區域), `vSchoolReportData` (學校報告數據), `vStudentReportData` (學生報告數據), `vPersonalReportParams` (個人報告參數) 等。
- 公開屬性如 `sArea`, `sCity`, `sSchoolCode`, `sStartTime`, `sEndTime`, `sCourse` 等，用於接收外部傳入的查詢條件。

### 2. 構造函數 (`__construct`)
- 初始化類別，獲取當前用戶的基本資訊 (`vUserData`)。
- 根據用戶的 `access_level` 判斷是否為區域計畫帳號或教育局處承辦人，並獲取相應的區域或城市代碼。
- 獲取用戶有權限查看的科目列表 (`vSubjectList`)。

### 3. 數據獲取與創建 (`create*` 系列函數)
- `createSubjectOption()`: 獲取所有可顯示的科目選項。
- `createCityArea()`: 獲取所有城市區域數據。
- `createCityArea_area()`: 根據區域帳號獲取該區域的所有縣市數據。
- `createOrganizationGroup()`: 獲取中心/核心/種子學校的數據。
- `createOrganizationGroup_Area()`: 獲取區域帳號下各區的合作學校數據。
- `createOrganizationGroup_Base_Area()`: 獲取基地學校數據。
- `createAreaSelection()`: 獲取區域選單數據。
- `createOtherAraeSelection()`: 獲取區域外的區域選單數據。
- `createSchoolFounded()`: 獲取在指定時間區間內建立的學校清單。
- `createSemeOption()`: 獲取所有學期選項。
- `createClassroom()`: 獲取班級選項，會根據教師權限篩選任教班級。
- `createExamRangeOption()`: 獲取考試範圍選項。
- `createSchoolData()`: 生成學校級數據報表資料，包括學校的班級數、學生數、教師數、活躍用戶數、學習時長等。它會根據地區、學等、合作學校、實施學校、基地學校等條件進行篩選和匯總。
- `createStudentData()`: 生成學生級數據報表資料，包括學生在不同學習活動（任務、影片、練習題、動態評量、單元測驗、縱貫測驗、AI 學習夥伴、登入日誌、遊戲時間、語音辨識等）中的表現數據。
- `createSubjectStatisticalData()`: 統計各科目的使用數據，包括學習時長、人數和次數。
- `findClassEachStudentNodeStatus()`: 撈取班級中每個學生的大、小節點狀態資料。
- `searchGradeNode()`: 找出該年級、科目的節點資訊。
- `searchRobotRecord()`, `searchVideoRecord()`, `searchPracticeRecord()`, `searchDynamicRecord()`: 找出學生在年級區間內，各種學習活動的作答或觀看紀錄。
- `findSubjectPublisher()`: 找出該學年度、年級、每個科目的版本資訊。
- `getPresetSubjectPublisher()`: 撈出各科目預設版本。

### 4. 數據處理與匯總 (`handle*` 系列函數)
- `handleRemedialNodeList()`: 處理需補救的節點清單，加上需補救的影片、練習題等。
- `handleSchoolData()`: 處理學校級數據報表，將人名轉換為人數統計。
- `handleSubjectStatisticalData()`: 處理科目統計數據，進行排序和加總。
- `handleSchoolAdminData()`: 處理校管報表資料，特別是班級節點狀態。
- `handleTeacherReportData()`: 處理教師報表資料。
- `handlePersonalData()`: 處理個人報表資料，包括學生學習記錄。

### 5. 輔助函數
- `getError()`: 獲取錯誤日誌。
- `checkParam($sParam, $sMessage)`: 確認變數是否為空，如果為空則拋出異常。
- `gradeRange()`: 根據學校類型獲取年級範圍。
- `getOtherSchoolType($organization_id)`: 獲取其他學校類型。
- `checkCitySchool($organization_id, $city_code)`: 檢查學校是否屬於指定縣市。
- `getStuDataJoinSqls($vParams)`: 根據參數獲取學生數據的 JOIN SQL 片段。

## 數據流與安全考量
- **資料庫互動**: 類別大量使用 PDO 進行資料庫操作，與 `organization`, `city_area`, `cooperation_city_area`, `organization_subject_group`, `seme_student`, `user_info`, `user_status`, `report_dailyusage`, `user_login_log`, `user_games_play_time`, `conversation`, `conversation_detail`, `course_list`, `student_engagement`, `course_rollcall`, `mc_speech_submit`, `mc_write_submit`, `math_laboratory_exam_record`, `video_review_record`, `video_concept_item`, `prac_answer`, `prac_questions`, `exam_record_dynamic`, `subject`, `map_info`, `map_node_grade`, `seme_publisher`, `publisher`, `map_node`, `concept_priori`, `teacher_class_member`, `teacher_class`, `remedial_student`, `remedial_class`, `export_school` 等眾多資料庫表互動。
- **權限控制**: 許多方法會根據 `vUserData['access_level']` 進行權限判斷，以限制數據的訪問範圍。
- **SQL 注入**: 類別中包含大量的 SQL 查詢，雖然使用了 `prepare()` 和 `bindValue()`，但仍需仔細檢查所有動態拼接的 SQL 片段和參數綁定，確保其安全性。
- **數據處理複雜性**: 類別中包含許多複雜的數據處理邏輯，例如 `GROUP_CONCAT` 的使用、字串解析和陣列操作，需要確保這些操作的正確性和效率。
- **錯誤訊息**: 錯誤訊息中包含行號 (`__LINE__`)，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究每個方法中涉及的資料庫表結構和它們之間的關聯。
- 仔細檢查所有動態 SQL 拼接的部分，確保沒有 SQL 注入漏洞。
- 了解 `fn_summary_time()` 函數的具體實現。
- 了解 `PUBLISHER_MAPPING_SNODE_SUBJECT` 常量的具體定義。
