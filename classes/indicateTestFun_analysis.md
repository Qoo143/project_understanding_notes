# indicateTestFun.php 分析

此檔案包含一系列函數，而不是一個類別，它們共同實現了一個適性測驗系統。這個系統似乎是 `indicateAdaptiveTestStructure` 類別的一個替代方案，而且可能更早實現。它的整個狀態管理都依賴於 `$_SESSION` 超級全局變數。

## 核心功能：

1.  **基於會話的狀態管理**：
    *   測驗的整個狀態——包括節點信息、項目詳細信息和使用者進度——都存儲在 `$_SESSION['indicateTest']` 陣列中。
    *   `buildNodeSESSION`：這是主要的初始化函數。它接收一個起始 `bNode`（大節點），從資料庫中獲取其所有子 `sNodes`（小節點）及其對應的項目，並用初始測驗結構填充 `$_SESSION` 變數。

2.  **節點和項目處理**：
    *   `sNodeNum` 和 `sNodeSort`：這些函數檢索然後排序 `bNode` 下的 `sNodes`，以根據它們的依賴關係確定測驗順序。
    *   `sNodeItemNum`：獲取屬於特定 `sNode` 的所有項目。
    *   `nextBNodeSESSION`：確定當前 `bNode` 完成後要測試的下一個 `bNode`。

3.  **響應檢查和狀態更新**：
    *   `chkStuAns`：檢查學生的答案並更新項目和父 `sNode` 在 `$_SESSION` 中的狀態（例如，增加 `doItemNum`、`trueItemNum`）。
    *   `chkSNodeStatus` 和 `chkBNodeStatus`：當節點的所有子項目完成時觸發這些函數。它們評估節點是否「通過」並更新其在會話中的狀態。
    *   `lowNode2True`：實現適性「預測」邏輯。如果一個節點通過，此函數會遞歸地將其所有下游節點標記為通過（狀態 `2`），允許測驗跳過它們。

4.  **渲染和持久化**：
    *   `showItem`：生成單個問題的完整 HTML，包括問題幹、選項和提交表單。
    *   `saveExamRecord`：在測驗結束時，此函數從 `$_SESSION` 中獲取最終節點狀態，並將它們寫回 `map_node_student_status` 資料庫表，更新學生的長期學習檔案。

## 與 `indicateAdaptiveTestStructure` 類別的比較：

*   **狀態存儲**：這是最顯著的區別。`indicateTestFun.php` **完全依賴 `$_SESSION`** 進行實時狀態管理。相比之下，`indicateAdaptiveTestStructure` 將狀態封裝在類別實例中，並使用更穩健的基於資料庫的臨時存儲（`exam_record_indicate_tmp`）進行暫停和恢復。
*   **架構**：這是一種使用一組全局函數的程序化方法，而另一種是物件導向方法。基於類別的方法提供了更好的封裝、可重用性和可維護性。
*   **底層邏輯**：核心邏輯非常相似。兩個系統都圍繞 `bNode`/`sNode` 概念構建，並旨在更新 `map_node_student_status` 表。這表明它們是針對相同問題的兩種不同實現，其中 `indicateTestFun.php` 可能是早期版本。

## 結論：

`indicateTestFun.php` 是一個自包含的、基於會話的適性測驗引擎。雖然功能齊全，但它對 `$_SESSION` 的嚴重依賴使其可能不如更現代的、基於類別的 `indicateAdaptiveTestStructure` 實現那麼可擴展和穩健。它代表了適性測驗功能早期開發階段的快照。
