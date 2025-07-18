# indicateAdaptiveTestTotalItems.php 分析

此檔案定義了 `indicateAdaptiveTestTotalItems` 類別。它似乎是 `indicateAdaptiveTestStructure` 類別的專門、輕量級版本，旨在實現單一目的：計算測驗中的總項目數，而無需執行完整的適性測驗邏輯。

## 主要目的：

此類別的主要功能是確定給定測驗配置的總問題數（`total_items`）。它是一個實用類別，可能用於管理或報告環境中，在需要快速計算項目數而無需運行實際測驗的情況下使用。

## 運作方式：

1.  **初始化 (`__construct`)**：
    *   與其更複雜的對應物不同，建構函數直接獲取原始結構參數：`upmost_nodes`、`subject_id` 和 `endYear`。
    *   它**不**獲取 `mission_sn`，這意味著它與特定的、已保存的任務解耦。
    *   它重用相同的內部方法（`getNodeLinkData`、`getBNodesInfo`、`getSNodesInfo`、`nodesSort`）來構建節點層次結構並確定小節點（`sNodes_order`）的最終順序。

2.  **計算 (`getTotalItems`)**：
    *   這是核心方法。
    *   它針對 `concept_item` 表構建並執行 SQL 查詢。
    *   查詢計算在初始化階段識別的小節點（`sNodes`）所屬的項目數。
    *   它尊重 `$sNode_items` 屬性，確保計數反映了從每個小節點中提取的項目數的設定。

## 與 `indicateAdaptiveTestStructure` 的主要區別：

*   **無狀態追蹤**：它缺少與使用者進度相關的所有屬性和方法，例如 `record_answer`、`record_response`、`step`、`sNode_now` 等。
*   **無測驗執行**：它沒有用於呈現項目（`getNextItem`）、檢查答案（`checkAns`）或保存結果（`makeExamSql`）的方法。其範圍嚴格限於計算。
*   **無緩存**：它不使用 `indicate_test_predata` 表進行緩存。它每次實例化時都會重新計算節點結構。這適用於輸入參數可能經常更改的即時計算。

## 結論：

`indicateAdaptiveTestTotalItems` 是一個實用類別，用於快速計算潛在適性測驗結構的總問題數。它的存在表明應用程式內部（例如，測驗創建使用者介面）需要根據選定的參數立即提供管理員或教師問題計數，而無需進行完整的測驗模擬。
