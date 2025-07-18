# indicateTest/indicateAdaptiveTest.php 分析

此檔案是現代、基於班級的適性測驗的**執行器**。它與 `indicateAdaptiveTestStructure` 類別密切配合，以提供更穩健和可維護的測驗體驗。

## 功能分解：

1.  **與 `indicateAdaptiveTestStructure` 的耦合**：
    *   檔案開頭包含 `indicateAdaptiveTestStructure.php`，表示其直接依賴性。
    *   整個測驗狀態通過 `indicateAdaptiveTestStructure` 類別的**實例**進行管理。此物件被序列化並存儲在 `$_SESSION['indicateTest']` 中。這比舊的實現（在會話中存儲一個大型、非結構化的陣列）是一個顯著的架構改進。

2.  **測驗生命週期管理**：
    *   **初始化**：首次加載時（`!isset($_SESSION['indicateTest'])`），它會創建 `indicateAdaptiveTestStructure` 的新實例，並立即調用 `checkExamPause()` 來處理測驗恢復。
    *   **答案處理**：當使用者提交答案（`isset($_POST['user_answer'])`）時，它會從會話中反序列化測驗物件，調用其 `checkAns()` 方法來處理答案，然後將更新後的物件序列化回會話中。
    *   **獲取下一個項目**：處理答案後，它會調用物件的 `getNextItem()` 方法來確定下一個問題的 `item_sn`。
    *   **測驗中斷**：它包含處理測驗中斷（`$_GET['stopExam']==1`）的清晰邏輯，將使用者重定向到基於其角色的適當儀表板。

3.  **前端渲染 (`showItem`)**：
    *   此檔案中的 `showItem` 函數負責根據 `item_sn` 從資料庫中獲取項目數據並生成問題 HTML。
    *   它支持各種媒體類型（圖像、音頻、影片）和問題格式（包括多選題）。
    *   它渲染一個現代使用者介面，包括用於中斷測驗的模態確認和防止重複表單提交的機制。

## 與舊版 `indicateTest.php` 的比較：

*   **架構**：這是關鍵區別。新版本採用**物件導向方法**，將複雜的測驗邏輯封裝在 `indicateAdaptiveTestStructure` 類別中。此檔案充當該類別的乾淨驅動程式。舊版本是程序性的，狀態和函數分散。
*   **狀態管理**：新版本使用**序列化物件**進行狀態持久化，這更清晰、更安全。舊版本直接操作一個大型、複雜的會話陣列，這容易出錯且難以維護。
*   **代碼清晰度**：邏輯更清晰。此檔案專注於「初始化、處理答案、獲取下一個項目、顯示」的簡單循環，而所有複雜的適性邏輯都由 `indicateAdaptiveTestStructure` 類別內部處理。

## 結論：

`modules/indicateTest/indicateAdaptiveTest.php` 是**現代適性測驗系統的執行頁面**。它代表了對舊系統（`indicateTest.php` 和 `indicateTestFun.php`）的重大重構和升級，採用了更穩健、物件導向的設計模式，提高了可維護性和可靠性。
