# assignMission/assign_mission_prodb.php 分析

此檔案是**任務分配精靈的後端處理器和 API 端點**。它接收 `assign_mission.php` 收集的所有數據，對其進行驗證、處理，並執行必要的資料庫操作以創建和分配新任務。

### 主要功能：

1.  **API 端點路由器**：
    *   類似於 `modules_teacher_get_mission.php`，此檔案充當路由器。它根據 `$_POST['act']` 參數使用 `switch` 語句來確定要執行的操作。
    *   **關鍵操作**：
        *   `getSchoolYear`、`getSubject`、`getPublisher`、`getGrade`、`getUnit`、`getIndicator`：這些操作對應於精靈步驟 2 中的級聯下拉菜單。每個操作都根據先前的選擇獲取適當的選項（例如，`getGrade` 根據選定的科目和出版商獲取年級）。
        *   `getMapAndNodes`：在知識結構任務中，為選定的大節點 (`bNode`) 獲取小節點 (`sNodes`)。
        *   `getPaperMission`、`getLiteracyMission` 等：根據篩選條件獲取實際的任務內容（試卷、素養項目）。
        *   `assignMissions`：這是最終也是最重要的操作。它從 POST 請求中獲取所有任務數據，並在資料庫中創建任務。
        *   `assignShareMission`：處理教師分配由其他教師共享的任務的邏輯。

2.  **任務創建邏輯 (`assignGeneralMission`)**：
    *   當 `act` 為 `assignMissions` 時，會調用此核心函數。
    *   **數據整合**：它從 `$_POST` 數據中收集所有任務詳細信息：名稱、開始/結束時間、目標類型（班級、組或個人）、選定的內容節點以及所有「更多設定」選項。
    *   **目標處理**：它正確處理任務目標。如果分配給班級，它會檢索該班級中的所有學生。如果分配給個人，它會使用提供的列表。如果分配給組，它會解析該組中的所有學生。
    *   **資料庫插入**：它執行兩個主要的資料庫插入操作：
        1.  **`mission_info`**：將單條記錄插入此表，其中包含任務的所有一般信息（名稱、類型、teacher_id、設定等）。任務內容（例如，節點 ID 列表或試卷 ID）存儲在 `node` 列中，通常使用特殊分隔符 (`@XX@`)。
        2.  **`mission_stud_record`**：然後它遍歷任務目標的每個學生，並將記錄插入此表，將學生鏈接到新創建的 `mission_sn`。這就是系統如何知道哪些學生被分配了哪些任務。

3.  **複雜數據處理**：
    *   **序列化**：對於「知識結構」等複雜任務類型，它會序列化一個包含詳細分配設定（例如，每個節點分配了哪些子部分，如影片或練習）的陣列，並將其存儲在 `mission_info` 的 `assign_type` 列中。
    *   **安全性**：它對所有 POST 數據使用 `xss_filter` 並檢查 CSRF token，以防止常見的網路漏洞。

### 結論：

`assign_mission_prodb.php` 是驅動任務創建過程的關鍵後端引擎。它是一個純粹的數據輸入、數據輸出腳本，處理獲取任務內容選項以及最重要的是將完全形成的任務持久化到資料庫中的所有複雜邏輯。它完美地補充了 `assign_mission.php` 前端，充當其控制器和模型。
