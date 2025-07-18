# HashedFileCheck.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\HashedFileCheck.php`

## 概述
`HashedFileCheck.php` 是一個**命令行介面 (CLI) 腳本**，用於**檢查專案檔案的完整性**。它通過計算檔案的 SHA256 雜湊值，並將其與預先記錄的雜湊列表進行比較，以檢測檔案是否被篡改或遺失。這個腳本在專案的部署和維護中扮演著重要的安全角色。

## 主要功能與職責

### 1. 運行環境判斷
- 腳本首先判斷其是否在 CLI 環境中運行 (`substr(php_sapi_name(), 0, 3) == 'cli'`)。如果不是，它會將用戶重定向到網站首頁，表明它不應該通過 Web 訪問。

### 2. 命令行參數處理
- 腳本根據傳入的命令行參數執行不同的操作：
    - **`php HashedFileCheck.php [目錄路徑]`**: 建立雜湊驗證檔案。
    - **`php HashedFileCheck.php [目錄路徑] verify`**: 驗證雜湊驗證檔案。
    - **`php HashedFileCheck.php [目錄路徑] further`**: 補上沒加進去雜湊驗證檔的檔案。
    - **`php HashedFileCheck.php help`**: 顯示幫助信息。

### 3. 檔案遍歷與雜湊計算
- **`getDirfiles($dir, &$results)` 函數**: 遞歸遍歷指定目錄下的所有檔案，包括子目錄。它會收集 `.php`, `.html`, `.css`, `.vue`, `.js` 等類型的檔案。
- **`hash_file('sha256', $file)`**: 對每個檔案計算其 SHA256 雜湊值。同時也會記錄檔案的最後修改時間 (`filemtime`)。

### 4. 雜湊列表管理
- **生成雜湊列表**: 如果 `hash_compare_list.txt` 不存在，腳本會遍歷所有檔案，計算雜湊值和修改時間，並將其寫入 `hash_compare_list.txt`。
- **補齊雜湊列表 (`further`)**: 如果 `hash_compare_list.txt` 存在且指定 `further` 參數，腳本會檢查所有檔案，將那些不在雜湊列表中的檔案的雜湊信息追加到 `hash_compare_list.txt` 中。
- **驗證雜湊列表 (`verify`)**: 如果 `hash_compare_list.txt` 存在且指定 `verify` 參數，腳本會遍歷所有檔案，並將其當前雜湊值和修改時間與 `hash_compare_list.txt` 中記錄的進行比較：
    - 如果檔案的雜湊值或修改時間不匹配，則輸出 `[Warning] file :... has been modified`。
    - 如果檔案不在雜湊列表中，則輸出 `[Error] file :... not found in hash list`。

### 5. 錯誤處理與提示
- 腳本會檢查參數是否正確，如果目錄不存在或缺少參數，會輸出相應的錯誤或警告信息。
- 在驗證模式下，會輸出所有不一致的檔案信息。

## 關鍵引用與依賴
- `include/config.php`
- `sanitizeInputForPath()` (可能定義在 `include/security_function.php` 或 `include/adp_core_function.php`)
- `preventXss()` (可能定義在 `include/adp_core_security.php`)

## 安全考量
- **文件完整性**: 這是防止惡意代碼注入和文件篡改的重要安全機制，尤其是在生產環境中。
- **雜湊列表本身的安全**: `hash_compare_list.txt` 檔案本身必須受到保護，防止被篡改。如果攻擊者能夠修改這個列表，那麼文件完整性檢查就會失效。
- **CLI 執行權限**: 由於腳本在 CLI 環境中運行，執行它的用戶需要有足夠的權限來讀取和寫入相關文件和目錄。應確保這些權限受到最小化原則的限制。
- **`sanitizeInputForPath()` 的重要性**: 確保傳入的目錄路徑經過嚴格的清理，防止路徑遍歷漏洞。

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `sanitizeInputForPath()` 函數的具體實現和安全性。
- 了解 `adl_hash_file.json` (在 `CLAUDE.md` 中提到) 與 `hash_compare_list.txt` 之間的關係，它們可能都是用於文件完整性檢查，但用途或生成方式可能不同。
