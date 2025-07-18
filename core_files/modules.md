# modules.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\modules.php`

## 概述
`modules.php` 檔案是一個**簡單的重定向腳本**。它的唯一功能是將所有對 `modules.php` 的請求重定向到 `modules_new.php`。這表明 `modules.php` 是一個舊的或兼容性入口點，而 `modules_new.php` 才是實際處理模組邏輯的檔案。

## 主要功能與職責

### 1. 引入核心依賴
- `include/adp_core_function.php`: 引入核心通用函數，其中包含 `validateRedirect()`。

### 2. 重定向邏輯
- **獲取請求 URI**: `$requestUri = $_SERVER['REQUEST_URI'];` 獲取當前請求的完整 URI。
- **替換文件名**: `$newUri = str_replace('modules.php', 'modules_new.php', $requestUri);` 將 URI 中的 `modules.php` 替換為 `modules_new.php`，從而構建新的目標 URL。
- **URL 驗證**: `$url_new = validateRedirect($newUri);` 呼叫 `validateRedirect()` 函數，對新的 URL 進行安全性驗證，防止開放重定向漏洞。
- **執行重定向**: `header("Location:$url_new");` 將用戶重定向到新的 URL。
- **終止腳本**: `die();` 在重定向後立即終止腳本執行，確保不會有額外的內容輸出。

## 關鍵引用與依賴
- `include/adp_core_function.php` (用於 `validateRedirect()`)
- `modules_new.php` (重定向目標)

## 安全考量
- **`validateRedirect()` 的使用**: 這是防止開放重定向漏洞的關鍵。確保此函數的實現是健壯和安全的，特別是在處理 `$_SERVER['REQUEST_URI']` 這種用戶可控的輸入時。

## 閱讀狀態
已閱讀

## 待辦事項
- 確認 `validateRedirect()` 函數的具體定義位置和實現細節。
- 了解 `modules_new.php` 的具體功能和它如何處理模組邏輯。

