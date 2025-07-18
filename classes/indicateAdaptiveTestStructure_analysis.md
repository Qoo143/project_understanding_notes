# indicateAdaptiveTestStructure.php 分析

此檔案定義了 `indicateAdaptiveTestStructure` 類別，它是平台內處理適性測驗的核心引擎。它管理適性測驗的整個生命週期，從初始化到結果提交。

## 主要職責：

1.  **測驗初始化 (`__construct`)**：
    *   接收 `mission_sn`（任務序號）作為輸入。
    *   獲取任務數據，包括科目、測驗類型和知識圖（節點）的結構。
    *   引入緩存機制（`getPreData`、`makePreData`），將預處理的測驗結構數據（`node_link_data`、`bNode_sNodes_count`、`total_items`）存儲在 `indicate_test_predata` 表中。這避免了每次測驗會話的昂貴重新計算，並檢查地圖是否已更新以使緩存失效。
    *   初始化所有節點（`bNode` - 大節點，`sNode` - 小節點）的狀態，並設置初始測驗參數。

2.  **項目選擇 (`getNextItem`)**：
    *   適性邏輯的核心。它決定下一個要呈現的問題（`item_sn`）。
    *   導航通過預定義的「小節點」（`sNodes_order`）順序。
    *   當一個小節點完成時，它會調用 `modifyNodeStatus` 更新學生的掌握狀態，並調用 `modifyLowNodeStatus` 將結果傳播到知識圖中連接的節點（例如，預測更高層次節點的掌握情況）。
    *   處理不同的測驗階段（`step=1` 用於適性，`step=2` 用於可選的完整測驗）。

3.  **回答和響應處理 (`checkAns`)**：
    *   接收使用者的答案，將其與正確答案進行比較（處理多選題），並記錄響應、時間和其他指標。
    *   更新整體項目成功率以用於統計目的。

4.  **狀態管理和持久化**：
    *   **暫停/恢復**：實現穩健的功能（`examPause`、`getExamPauseData`），將完整的測驗狀態保存到 `exam_record_indicate_tmp` 表中，允許使用者稍後恢復。
    *   **最終提交**：
        *   `makeExamSql`：編譯最終結果並將其永久保存到 `exam_record_indicate` 表中。
        *   `updateExamineeSql`：更新 `map_node_student_status` 表，該表存儲學生知識圖的長期掌握情況。
        *   與 `giveCoin` 類別整合以進行遊戲化獎勵。

## 關鍵概念和術語：

*   **任務 (`mission_sn`)**：一個特定的測驗或學習任務。
*   **節點 (`bNode`、`sNode`)**：測驗圍繞著「大節點」（概念）和「小節點」（技能）的知識圖構建。
*   **節點狀態**：表示學生對節點掌握狀態的數值代碼（-1：未嘗試，0：不正確，1：正確，2：預測正確等）。
*   **緩存 (`indicate_test_predata`)**：預先計算和存儲節點結構以提高性能。

## 依賴項：

*   `prodb_mission_record_update.php`：用於更新任務記錄。
*   `adp_core_class.php`：包含共享的核心函數/類別。
*   `giveCoin` 類別：用於獎勵系統。
*   全局資料庫句柄（`$dbh`、`$dbh_slave`）。
*   全局函數，例如 `sNode2bNode()`。

## 整體印象：

這是一個複雜而精密的適性測驗系統類別。它處理測驗流程、緩存、狀態持久化、結果存儲以及與學生學習檔案和遊戲化系統的整合。遍歷知識圖並更新節點狀態的邏輯是其最關鍵的功能。
