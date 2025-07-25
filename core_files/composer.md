# composer.json 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\composer.json`

## 概述
`composer.json` 檔案是 PHP 專案的**依賴管理配置文件**。它定義了專案所需的 PHP 函式庫及其版本約束，以及其他 Composer 相關的配置。通過這個檔案，Composer 可以自動下載、安裝和管理專案的依賴項，確保開發環境的一致性。

## 主要功能與職責

### 1. `require` 區塊
- 此區塊列出了專案運行所需的 PHP 函式庫及其版本約束。這些函式庫是因材網後端功能的重要組成部分：
    - **`ezyang/htmlpurifier`: `^4.17`**
        - **用途**: 用於過濾和清理 HTML，防止 XSS (Cross-Site Scripting) 攻擊。它確保只有安全的 HTML 標籤和屬性能夠被渲染，是應用程式安全的重要組成部分。
    - **`guzzlehttp/guzzle`: `^7.0`**
        - **用途**: 一個流行的 PHP HTTP 客戶端，用於發送 HTTP 請求。在因材網中，它可能用於與外部 API 服務（例如教育雲端 OpenID、其他第三方服務）進行通訊。
    - **`guzzlehttp/promises`: `^1.5`**
        - **用途**: Guzzle 的一個組件，提供了 Promise 實現，用於處理異步操作。與 `guzzlehttp/guzzle` 配合使用，可以實現非阻塞的 HTTP 請求。
    - **`guzzlehttp/psr7`: `^2.0`**
        - **用途**: 實現 PSR-7 (HTTP 訊息介面) 的組件，提供了 HTTP 請求、響應和 URI 的標準化表示。與 Guzzle 配合使用，確保 HTTP 訊息處理的兼容性和標準性。
    - **`inhere/php-validate`: `^2.0`**
        - **用途**: 一個 PHP 數據驗證函式庫。它可能用於後端對用戶輸入數據進行更複雜的驗證規則檢查，例如表單數據的格式、長度、類型等。
    - **`phpoffice/phpspreadsheet`: `^1.29.0`**
        - **用途**: 用於讀取、寫入和操作各種電子表格文件（例如 Excel）。在因材網中，它可能用於處理批次用戶數據匯入、成績匯出或報表生成等功能。
    - **`phpoffice/phpword`: `^1.2.0`**
        - **用途**: 用於讀取、寫入和操作 Word 文檔。它可能用於生成 Word 格式的報告、證書或教材。

### 2. `config` 區塊
- **`platform`**: 此區塊用於指定 Composer 在解析依賴時應使用的平台信息。這對於確保在特定 PHP 版本環境下正確安裝依賴非常重要。
    - **`php`: `7.4.0`**: 指定專案所需的 PHP 版本為 `7.4.0`。這意味著專案的代碼是基於 PHP 7.4.0 或更高版本編寫的，並且依賴項也會根據這個版本進行解析。

## 關鍵作用
- **依賴管理**: 確保所有必要的 PHP 函式庫都能被正確安裝和管理。
- **環境一致性**: 幫助開發者在不同的開發環境中保持相同的依賴版本，減少「在我機器上可以運行」的問題。
- **版本控制**: 通過版本約束，確保函式庫的更新不會引入不兼容的變更。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解這些函式庫在因材網專案中的具體應用場景，特別是 `guzzlehttp` 系列和 `inhere/php-validate`。
- 檢查 `php_cli` 目錄下是否有與 `phpspreadsheet` 或 `phpword` 相關的批次處理腳本。
