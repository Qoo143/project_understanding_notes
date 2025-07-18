# indicateTest/indicateTest.php 分析

此檔案作為基於會話的適性測驗的**啟動器和初始化器**。它負責設置測驗環境、構建測驗結構並顯示第一個問題。它與 `classes/indicateTestFun.php` 中的函數緊密耦合，並使用這些函數來驅動測驗過程。

## 功能分解：

1.  **參數處理和初始化**：
    *   它處理傳入的請求參數，例如 `map_sn`（知識圖 ID）、`subject` 和 `result_sn`（可能等同於 `mission_sn`）。
    *   它調用 `chk_exam_indicate` 函數，檢查 `exam_record_indicate_tmp` 表中是否有當前使用者的未完成測驗記錄。

2.  **測驗流程控制**：
    *   **恢復測驗 (`continue_Exam`)**：如果找到未完成的測驗，則調用此函數。它從資料庫中讀取保存的狀態，將其恢復到 `$_SESSION['indicateInfo']` 變數中，確定下一個問題，並調用 `showItem` 顯示它，允許使用者無縫地繼續。
    *   **開始新測驗**：如果沒有未完成的測驗，它會執行一大塊初始化邏輯：
        *   **構建節點順序**：從初始節點（`firstIndicate`）開始，它遞歸查詢 `indicateTest` 資料庫表，以構建 `bNodes` 和 `sNodes` 的完整、有序序列。
        *   **準備項目**：它遍歷排序後的 `sNodes`，並從 `concept_item` 表中獲取所有相關問題。
        *   **保存到會話**：關鍵的是，它將所有準備好的測驗數據——節點序列、項目列表、答案、狀態——保存到一個大型 `$_SESSION['indicateInfo']` 陣列中。
        *   **顯示第一個項目**：最後，它調用 `showItem` 函數（來自 `indicateTestFun.php`）來渲染第一個問題，正式開始測驗。

3.  **與 `indicateTestFun.php` 的關係**：
    *   此檔案是 `indicateTestFun.php` 庫的**消費者**。它準備 `indicateTestFun.php` 中的函數所依賴的複雜 `$_SESSION['indicateInfo']` 數據結構。一旦會話準備好，它就將控制權交給這些函數來管理測驗的互動部分。

## 結論：

`modules/indicateTest/indicateTest.php` 是基於會話的適性測驗流程的**設置和啟動腳本**。它處理測驗開始前的所有準備工作，包括參數解析、恢復中斷的測驗以及為新測驗構建整個測驗結構。這證實了 `indicateTest.php` 和 `indicateTestFun.php` 協同工作，形成了適性測驗系統的完整（儘管可能較舊）實現。
