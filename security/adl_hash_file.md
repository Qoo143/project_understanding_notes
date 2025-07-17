# adl_hash_file.json 理解筆記

## 檔案路徑
`/opt/lampp/htdocs/ntcu-nbnat-AIAL/adl_hash_file.json` (在生產環境中)

## 概述
`adl_hash_file.json` 是一個 JSON 格式的檔案，用於儲存因材網應用程式中**可執行 PHP 檔案的 SHA256 雜湊值**。其主要目的是在生產環境中提供**文件完整性驗證**，防止未經授權的檔案篡改。

## 生成與維護機制
- **生成腳本**: `php_cli\rs_hash_file_check.php` 似乎是負責生成或更新此雜湊文件的腳本。這個腳本可能遍歷專案中的所有可執行 PHP 檔案，計算其 SHA256 雜湊值，並將其記錄到 `adl_hash_file.json` 中。
- **部署流程**: 在部署到生產環境時，應該會執行此腳本，確保 `adl_hash_file.json` 包含所有正確的檔案雜湊值。

## 使用方式
- 在 `modules_new.php`、`modules\DynamicAdaptiveTest\DA_new.php`、`modules\contest\adlresource\shared.php`、`modules\contest\adlresource\frame.php`、`modules\contest\2021summer\frame.php`、`modules\contest\2021mathscience\frame.php` 等多個核心檔案中，在引入其他 PHP 檔案之前，會讀取 `adl_hash_file.json` 的內容。
- 程式碼會檢查即將引入的 PHP 檔案的 SHA256 雜湊值是否與 `adl_hash_file.json` 中記錄的值一致。
- **安全檢查**: 
    - 如果雜湊值不匹配，則表示檔案可能已被篡改，程式會輸出「文件驗證失敗。」並終止執行，從而阻止潛在的惡意代碼執行。
    - 在開發環境中（根據 `modules_new.php` 中的 `preg_match('/cron-server|test-adlweb|dev-adlweb/i', gethostname())` 判斷），此檢查會被繞過，以方便開發和調試。

## 安全考量
- **文件完整性**: 這是防止惡意代碼注入和文件篡改的重要安全機制。
- **部署安全**: 確保 `adl_hash_file.json` 本身在部署過程中不被篡改至關重要。
- **開發與生產環境差異**: 開發環境繞過檢查是合理的，但需要確保生產環境嚴格執行此驗證。
- **雜湊演算法**: 使用 SHA256 是一種強大的雜湊演算法，但應定期評估其安全性。

## 關鍵引用與依賴
- `modules_new.php` (主要使用點)
- `php_cli\rs_hash_file_check.php` (生成腳本)
- `file_get_contents()`
- `hash_file('sha256', ...)`

## 閱讀狀態
已閱讀

## 待辦事項
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能。
- 查找 `createAuthOptions()` 函數的定義和作用（它可能是一個簡單的陣列構造，或者定義在其他未被直接引入的檔案中）。