# 因材網專案核心理解 (ntcu-nbnat-aial)

## 專案概述與架構
因材網是一個基於 PHP 的 Web 應用程式，採用類似 MVC 的架構模式。前端主要使用 Vue.js、jQuery 和 AJAX 進行動態互動，後端則以 PHP 處理業務邏輯和資料庫操作。專案結構清晰，包含 `classes/` (類別定義)、`include/` (通用函數與配置)、`modules/` (功能模組)、`security/` (安全相關) 等核心目錄。

## 核心組件與職責
- **`modules_new.php`**: 應用程式的主要入口點和控制器，負責初始化環境、安全檢查、模組載入和路由。
- **`auth_chk.php`**: 核心身份驗證與授權檢查，使用 PEAR Auth 庫管理用戶登入狀態、會話超時和權限導向。
- **`turnpage.php`**: 核心 AJAX 請求路由器，接收前端請求，解析路由並引入對應的後端邏輯檔案，同時進行安全檢查。
- **`config.php`**: 全局配置檔案，定義了大量常量，包括系統路徑、用戶權限等級、檔案上傳路徑、郵件服務配置等。
- **PEAR Auth 庫 (`Auth.php`, `Auth_Container_DB.php`)**: 負責用戶身份驗證，`Auth_Container_DB.php` 處理與資料庫的互動，已客製化為使用 PDO。
- **`adp_core_function.php`**: 大量通用函數的集合，涵蓋日期時間、學期轉換、用戶資訊查詢、字串加密解密、XSS 防護、檔案處理等。
- **`adp_core_security.php`**: 基礎安全防護，特別是 XSS 防禦，定義了 `xss_filter` 和 `filter` 函數。
- **`XAPIManager` 類別 (由 `xapi/prodb_xapi_proxy.php` 調用)**: 管理 xAPI 學習記錄，將應用程式事件轉換為 xAPI 語句並發送到 LRS，具備離線儲存和批次處理能力。
- **適性測驗模組 (`indicateAdaptiveTestStructure`, `indicateTestFun`)**: 處理適性測驗的核心邏輯，包括測驗初始化、題目選擇、答案處理、狀態持久化和結果報告。新版採用物件導向設計，舊版則依賴 `$_SESSION`。
- **任務管理模組 (`assignMission`, `ExamResult`)**: 提供教師創建、分配和報告學習任務的介面和後端邏輯。
- **SRL 檢核表模組 (`checklists_new`, `learning_regulated_fillout`)**: 允許教師創建自律學習檢核表，並供學生填寫和評分。

## 數據流與權限管理
- **用戶認證**: 用戶通過 `login.php` 登入，經 `auth_chk.php` 驗證身份。支持本地帳號、Google SSO (`googleLogin.php`, `loginAuth.php`)、教育雲 OpenID (`login_openID.php`, `choiceIdentity.php`) 和酷課雲 SSO (`login_cooc.php`, `roles_cooc.php`)。
- **會話管理**: 廣泛使用 `$_SESSION` 儲存用戶數據和會話狀態，並有 Session ID 重生、閒置超時等機制。
- **數據傳輸**: 前後端主要通過 AJAX 請求 (`turnpage.php` 作為路由) 傳輸 JSON 數據。
- **權限控制**: `acl.php` 根據 `access_level` 動態生成菜單和控制頁面訪問權限。`config.php` 定義了詳細的 `USER_` 權限常量。
- **資料庫互動**: 大量使用 PDO 進行資料庫操作，通常通過 `$dbh` 和 `$dbh_slave` 進行讀寫分離。

## 安全機制
- **XSS 防護**: 廣泛應用 `preventXss()`、`xss_filter()` 和 `HTMLPurifier` 進行輸入過濾和輸出轉義。
- **CSRF 防護**: 在多個關鍵頁面（如 `confirm.php`, `prodb_user_recovery.php`, `turnpage.php`, `loginAuth.php`）實施 CSRF Token 驗證。
- **檔案完整性檢查**: 生產環境下，`modules_new.php` 會檢查引入 PHP 檔案的 SHA256 雜湊值 (`adl_hash_file.json`)，防止檔案篡改。
- **密碼處理**: 密碼重設流程包含 Token 驗證和時效性檢查，新密碼會進行雜湊處理並檢查與歷史密碼的重複性。
- **URL 驗證**: `validateRedirect()` 函數用於驗證和清理 URL，防止開放重定向漏洞。
- **IP 限制**: `quickLogin4LAB.php` 和 `new_styleGuide.php` 等內部工具通過 IP 限制訪問。
- **錯誤訊息處理**: 錯誤訊息中包含行號，生產環境應考慮移除以避免信息洩露。
- **敏感資訊保護**: `config.php` 中郵件帳密等敏感資訊應特別注意保護。

## 關鍵發現與待辦事項
- **技術棧**: PHP (後端), Vue.js (前端), jQuery, Composer, npm, PDO, PEAR Auth, SweetAlert2, Video.js, html2canvas, D3.js, MathJax, Showdown, Hammer.js 等。
- **歷史演進**: 存在新舊模組並存的情況（例如適性測驗的 `indicateTestFun.php` 和 `indicateAdaptiveTestStructure`），表明專案在不斷演進和重構。
- **待辦事項**: 需持續深入分析各模組間的詳細互動邏輯，特別是 AJAX 請求的後端處理檔案，以及資料庫表的具體結構和關聯。