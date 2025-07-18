# CLAUDE.md 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\CLAUDE.md`

## 概述
`CLAUDE.md` 檔案是一個**專案概覽和開發指南**，主要用於為 Claude Code (claude.ai/code) 提供關於 `ntcu-nbnat-aial` 專案的背景信息、架構、開發規範和常用命令。它詳細介紹了因材網平台的核心組件、開發流程、安全要求和代碼約定，是理解整個專案的入門文件。

## 主要內容與職責

### 1. 專案概覽 (Project Overview)
- **專案名稱**: 因材網 (Adaptive Learning Educational Platform)。
- **開發單位**: 國立臺中教育大學。
- **核心組件**: 
    - **後端**: PHP 7.4+，使用 PDO 進行資料庫操作。
    - **前端**: Vue.js 3，使用 Vue Router 實現動態組件。
    - **資料庫**: MySQL/MariaDB，支持主從配置。
    - **身份驗證**: 支持多種提供者（OpenID、教育雲 SSO）。
    - **內容管理**: 教材、評量和適性學習路徑。

### 2. 開發命令 (Development Commands)
- **部署**: 提供了生產環境和測試環境的部署命令（通過 Google Cloud Build 的 webhook 觸發）。
- **包管理**: 說明了如何安裝和更新 PHP 依賴 (`composer install/update`) 和 JavaScript 依賴 (`npm install/update`)。
- **構建過程**: 提到了 JavaScript 壓縮、文檔生成（PHPMailer）和 QR Code 庫合併等構建任務。

### 3. 架構概覽 (Architecture Overview)
- **核心框架結構**: 
    - **入口點**: `modules_new.php` (主應用程式路由器，包含身份驗證和安全層)。
    - **配置**: `include/config.php` (中央配置，包含環境特定設定)。
    - **身份驗證**: `auth_chk.php` 和 OpenID 整合。
    - **安全**: XSS 防護、檔案雜湊驗證、CSP 頭和輸入清理。
- **模組系統**: 說明了 `/modules/` 目錄下的模組化架構，列舉了主要模組及其功能（例如 `assignMission`、`dashboard`、`ExamResult` 等）。
- **資料庫架構**: 提到了主從配置 (`$dbh` 寫入，`$dbh_slave` 讀取)、基於角色的存取控制、內容儲存和審計日誌。
- **前端架構**: 說明了 Vue.js 3 的使用、Vue Router、自定義 CSS 框架和資產管道。

### 4. 重要檔案路徑 (Important File Paths)
- 列舉了核心檔案、配置檔案和安全組件的關鍵檔案路徑。

### 5. 開發指南 (Development Guidelines)
- **安全要求**: 強調所有用戶輸入必須使用 `preventXss()` 驗證、資料庫查詢必須使用 PDO 預處理語句、檔案上傳需要雜湊驗證等。
- **代碼約定**: 說明了 PHP (PSR 標準)、資料庫命名 (snake_case)、CSS 命名 (kebab-case/camelCase) 和 JavaScript (ES6+) 的代碼約定。
- **檔案命名約定**: 詳細說明了前端顯示檔案、後端處理檔案 (`prodb_` 前綴)、模組組織和測試檔案的命名模式。
- **AJAX 路由器系統 (`turnpage.php`)**: 詳細解釋了中央 AJAX 路由器系統的架構、Token 系統、編碼方式 (`encodeAjaxUrlBase64()`)、安全特性和所需設置。
- **用戶角色與存取級別**: 說明了系統支持的層級用戶角色（學生、教師、校管、系統管理員）及其對應的 `access_level`。
- **環境檢測**: 說明了如何根據主機名模式自動檢測環境（生產、測試、開發、維護）。

### 6. 測試與質量保證 (Testing & Quality Assurance)
- **檔案完整性系統**: 提到了生產環境使用 SHA256 雜湊驗證 (`adl_hash_file.json`)。
- **自動化代碼審查**: 提到了 GitLab CI/CD 集成 PR Agent。
- **瀏覽器兼容性**: 說明了對現代瀏覽器的支持和響應式設計。

### 7. 常見開發任務 (Common Development Tasks)
- 提供了添加新模組、資料庫更改和前端更新的指南。

## 閱讀狀態
已閱讀

## 待辦事項
- 根據 `CLAUDE.md` 中提到的關鍵組件和檔案，進一步深入分析尚未詳細閱讀的檔案和模組，例如 `HashedFileCheck.php`、`include/security_function.php`、`modules/OpenID/` 下的檔案等。
- 了解 `make minify`、`./docs/generatedocs.sh` 和 `./tools/merge.sh` 等構建命令的具體作用。
