# index_AIAL2.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\index_AIAL2.php`

## 概述
`index_AIAL2.php` 檔案是一個**簡單的重定向腳本**，主要用於處理網站的入口點。它的核心功能是檢查系統是否處於維護模式，並根據結果將用戶重定向到維護頁面或網站的首頁。該檔案的註解顯示它曾經是一個更複雜的登入首頁，但目前已被簡化為純粹的重定向邏輯。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置，其中包含 `SiteConfig['setMaintainTime']` 和 `SiteConfig['setMaintainShowPage']` 等維護模式相關的設定。

### 2. 維護模式檢查與重定向
- **`if ($SiteConfig['setMaintainTime'] == 1)`**: 檢查 `config.php` 中定義的 `$SiteConfig['setMaintainTime']` 是否為 `1`。如果為 `1`，表示系統處於維護模式。
- **重定向到維護頁面**: 如果系統處於維護模式，腳本會將用戶重定向到 `$SiteConfig['setMaintainShowPage']` 中指定的維護頁面。這通常是一個靜態 HTML 頁面，用於告知用戶系統正在維護。
- **重定向到首頁**: 如果系統不處於維護模式，腳本會將用戶重定向到 `./HomePage/home/`，這是網站的實際首頁。
- **`die()`**: 在重定向後立即終止腳本執行，確保不會有額外的內容輸出。

### 3. 註釋掉的代碼
- 檔案中註釋掉了對 `modules/OpenID/config_openid.php`、`modules/OpenID/library.class.php` 和 `include/network_function.php` 的引入。這表明該檔案曾經可能處理 OpenID 登入或網路相關的功能，但這些功能已被移除或轉移到其他地方。

## 關鍵引用與依賴
- `include/config.php`
- `HomePage/home/` (網站首頁)
- `$SiteConfig['setMaintainShowPage']` (維護頁面路徑)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `config.php` 中 `$SiteConfig['setMaintainTime']` 和 `$SiteConfig['setMaintainShowPage']` 的具體配置。
- 查找 `HomePage/home/` 目錄下的實際首頁文件。
- 了解為什麼 OpenID 和網路相關的引入被註釋掉，以及這些功能目前在何處實現。
