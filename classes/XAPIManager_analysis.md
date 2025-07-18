# XAPIManager.php 分析

此檔案定義了 `XAPIManager` 類別，這是一個全面且精心設計的解決方案，用於管理 xAPI (Experience API) 學習記錄。其核心職責是將內部應用程式事件（例如，觀看影片、回答問題）轉換為標準化的 xAPI 語句格式，並將其發送到學習記錄儲存庫 (LRS)。

## 主要功能：

1.  **配置驅動**：
    *   該類別通過 PHP 常量（例如，`XAPI_ENDPOINT`、`XAPI_USERNAME`、`XAPI_ENABLED`）高度可配置。這使得在不同環境（開發、測試、生產）中輕鬆設置和修改，而無需更改代碼。

2.  **穩健的語句構建 (`buildXAPIStatement`)**：
    *   這是類別的核心。它提供了一個結構化的過程來創建有效的 xAPI 語句。
    *   `buildActor`：構建學習者物件，包括對匿名化的支持。
    *   `buildVerb`：使用預定義的映射陣列將內部應用程式動作（例如，「練習題_對」）映射到標準 xAPI 動詞（例如，「answered correctly」）。這是一種處理翻譯的簡潔方式。
    *   `buildObject`、`buildResult`、`buildContext`：這些方法創建語句的其他基本部分，通過活動、成功、分數、持續時間和上下文（如課程或班級 ID）的詳細信息豐富學習記錄。

3.  **離線儲存和彈性（容錯）**：
    *   **關鍵功能**：這是該類別最重要的優勢。如果將語句發送到 LRS 失敗（例如，由於網路問題），`sendStatement` 方法將返回 `false`，然後 `logAction` 方法將調用 `storeOfflineStatement` 將記錄保存到本地資料庫表（`xapi_offline_statements`）。這可以防止數據丟失並確保可靠性。
    *   `initializeStorage`：該類別可以在初始化時自動創建所需的資料庫表，這簡化了部署和設置。

4.  **批次處理和重試機制 (`processPendingStatements`)**：
    *   該類別包含一個處理本地資料庫中待處理語句的方法。這可以定期運行（例如，通過 cron job）以批次發送離線記錄到 LRS。
    *   它包含一個重試機制（`retry_count`），嘗試重新發送失敗的語句指定次數，防止對永久失敗的記錄進行無限重試。

## 結論：

`XAPIManager` 是一個專業、生產就緒的 xAPI 整合類別。它旨在可配置、穩健且對網路故障具有彈性。其離線儲存和批次處理功能使其成為捕獲詳細學習分析的可靠工具，這對於任何現代電子學習平台都至關重要。
