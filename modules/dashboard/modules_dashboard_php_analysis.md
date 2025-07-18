# dashboard/modules_dashboard.php 分析

此檔案作為**教師儀表板**的主要入口點。它動態加載和顯示已分配的任務，並提供廣泛的篩選和查看選項。

### 主要功能：

1.  **基於角色的訪問**：
    *   它首先檢查會話中的使用者 `access_level` 以確定他們是教師還是家長。這表明雖然該檔案主要用於教師，但它可能對其他角色有不同的變體。

2.  **動態任務顯示**：
    *   頁面的核心是高度互動的任務查看介面。它本身不包含任務數據，而是提供 HTML 結構和 JavaScript 鉤子來獲取和顯示數據。
    *   **AJAX 驅動**：頁面設置了幾個 JavaScript 變數（`routerForDashboard`、`routerForMissionLayout` 等），其中包含 AJAX 端點。這些端點由隨附的 JavaScript 檔案（`mission_list.js`、`assignment.js`）用於異步加載任務數據。主要數據源似乎是 `modules_teacher_get_mission.php`。
    *   **多種視圖**：它支持三種不同的任務視圖，使用者可以在它們之間切換：
        *   **卡片視圖** (`missionLayout_card`)：將每個任務顯示為單獨的卡片。
        *   **列表視圖** (`missionLayout_list`)：以更緊湊的列表格式顯示任務。
        *   **快速表格視圖** (`quickTable`)：一個強大的網格視圖，在 Y 軸上顯示學生，在 X 軸上顯示任務，並帶有圖標指示每個學生每個任務的完成狀態。

3.  **高級篩選**：
    *   它具有一個全面的篩選側邊欄，允許教師根據以下內容縮小顯示的任務範圍：
        *   學期 (`semeSelector`)
        *   班級類型和班級號碼 (`classTypeSelector`、`classNumSelector`)
        *   指派者（例如，教師指派、學生指派） (`founder`)
        *   任務類型 (`missionType`)
        *   特定子女（針對家長） (`child`)
        *   關鍵字搜索 (`searchText`)

4.  **教師操作**：
    *   一個顯眼的「+ 指派任務」按鈕鏈接到任務分配介面 (`assign_mission`)，允許教師為學生創建新任務。

5.  **狀態持久化**：
    *   該腳本旨在通過從 URL 讀取參數（`$_GET['page']`、`$_GET['searchText']` 等）並將它們傳遞給 JavaScript 來記住使用者的篩選設定和當前頁面，以便在重新加載時保持狀態。

### 結論：

`modules_dashboard.php` 是教師管理和監控學生任務的中心樞紐。它是一個複雜的、客戶端驅動的介面，嚴重依賴 JavaScript 和 AJAX 來提供靈活和互動的使用者體驗。它在類似 MVC 的模式中充當「視圖」，數據從其他 PHP 腳本（`modules_teacher_get_mission.php`）獲取，使用者互動由專用的 JavaScript 檔案處理。
