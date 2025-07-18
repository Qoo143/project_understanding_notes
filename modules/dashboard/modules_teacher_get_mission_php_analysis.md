# dashboard/modules_teacher_get_mission.php 分析

此檔案是教師任務儀表板的**後端數據提供者（API 端點）**。它是一個純邏輯檔案，接收來自前端（`modules_dashboard.php`）的 AJAX 請求，根據提供的篩選條件查詢資料庫，並以 JSON 格式返回任務數據。

### 主要功能：

1.  **基於動作的控制器**：
    *   整個檔案作為一個控制器，根據 `action_code` 參數路由請求。
    *   它使用 `switch` 語句處理不同的動作，例如：
        *   `GET_MISSION_TOTAL`：根據篩選條件獲取任務總數。
        *   `GET_MISSION_INFO`：獲取特定頁面上任務的詳細信息。
        *   `GET_QUICK_TABLE_INFO`：獲取構建「快速表格」視圖（學生 vs. 任務網格）所需的數據。
        *   `SET_MISSION_UNABLE`：將任務標記為已刪除。
        *   `GET_SELECT_OPT`：獲取篩選下拉菜單的選項（例如，班級列表、任務類型）。
        *   `SET_SHARE_CODE`：為任務生成或更新共享代碼。

2.  **複雜的資料庫查詢**：
    *   此檔案的核心是構建和執行複雜的 SQL 查詢，以從 `mission_info` 表和相關表（`mission_stud_record`、`user_info` 等）中獲取任務數據。
    *   **動態查詢構建**：`getMissionInfoByMissionCondition` 和 `getMissionTotalCount` 函數根據從前端發送的眾多篩選條件（搜索文本、學期、班級類型、指派者類型等）動態構建 SQL 查詢的 `WHERE` 和 `JOIN` 子句。這是一種強大但複雜的方法。
    *   **基於角色的範圍**：它根據使用者的角色（`access_level`）正確調整查詢範圍。例如，校長（`ALL_CLASS`）可以看到學校中的所有任務，而教師（`TEACHER_CLASS`）只能看到自己的任務。

3.  **數據處理和豐富**：
    *   從資料庫獲取原始數據後，腳本在將其作為 JSON 返回之前對其進行大量處理和豐富。這包括：
        *   **反序列化**：它反序列化資料庫中以序列化格式存儲的任務數據（使用 `unserialize_with_hash`）。
        *   **計算完成率**：它調用 `getMissionCompleteAndTotalCount` 來計算完成每個任務的學生數量和總通過率。
        *   **生成 URL**：它構建正確的 URL，供學生開始任務的每個部分（例如，影片、練習題、動態評估的鏈接）。
        *   **格式化數據**：它格式化數據以供顯示（例如，創建「剩餘 5 天」的持續時間字符串）。
        *   **加密**：它使用 `string2hash` 在將敏感 ID（如 `mission_sn`）發送到客戶端之前對其進行加密。

4.  **任務修補**：
    *   它包含對 `createMissionPatch` 的調用，這似乎是一種機制，用於自動將任務分配給在任務最初分配後加入班級的新學生，確保沒有人被遺漏。

### 結論：

`modules_teacher_get_mission.php` 是**教師儀表板的後端主力**。它是一個純數據處理腳本，封裝了篩選、檢索和準備任務數據的複雜業務邏輯。它在 MVC 模式中充當「模型」和「控制器」，通過 AJAX 向 `modules_dashboard.php`「視圖」提供數據。廣泛使用常量來表示動作代碼和任務類型使代碼更具可讀性和可維護性。
