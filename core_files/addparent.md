# addparent.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\addparent.php`

## 概述
`addparent.php` 是一個用於**家長/大學伴帳號註冊與學生綁定**的頁面。它允許家長或大學伴創建一個新的帳號，並將其與一個現有的學生帳號進行綁定。此頁面包含了前端的表單、JavaScript 驗證邏輯以及後端的 PHP 處理，用於接收和處理註冊請求。

## 主要功能與職責

### 1. 引入核心依賴
- `include/config.php`: 應用程式的全局配置。
- `include/adp_API.php`: 核心 API 引入檔案，可能包含 `UserData` 類別的定義。

### 2. 學生帳號驗證
- 頁面通過 `$_GET['t']` 參數接收一個編碼過的學生 ID (`child_userId`)。這個參數使用 `_SPLIT_SYMBOL` 分隔符和 `base64_decode` 進行解碼。
- 實例化 `UserData($child_userId)` 來檢查該學生帳號是否存在。如果學生帳號不存在，則會顯示錯誤訊息並重定向到首頁。

### 3. 前端表單與 JavaScript 驗證
- **註冊表單**: 包含家長/大學伴姓名 (`uname`)、帳號（手機號碼或 Email，`email`）和密碼 (`pass1`) 的輸入欄位。
- **隱藏欄位**: `child_uid` 隱藏欄位用於傳遞加密後的學生 ID。
- **JavaScript 驗證**: `checkAccountAndPassword()` 函數在前端執行嚴格的輸入驗證，包括：
    - 帳號和密碼不得為空。
    - 帳號和密碼的長度限制。
    - 帳號和密碼不得包含非法字元。
    - 密碼不得與帳號相同。
    - 密碼必須包含數字、英文大寫、英文小寫、特殊符號中至少三種。
- **錯誤提示**: 驗證失敗時，使用 `showModalBox()` 顯示警告訊息。

### 4. AJAX 數據提交
- 當用戶點擊「送出」按鈕 (`#regist`) 時，會觸發一個 AJAX POST 請求到 `modules/UserManage/pro_insert_parentID.php`。
- 提交的數據包括：`type` (insert)、`uname`、`user_account` (Email/手機號碼)、`pass` (密碼)、`access_level` (從 `$_GET['t']` 參數中獲取，表示家長或大學伴的權限等級) 和 `child_uid` (學生 ID)。

### 5. 後端響應處理
- AJAX 請求成功後，根據 `pro_insert_parentID.php` 返回的 `db_code` 處理不同的結果：
    - `00000_new_parent_account`: 成功創建新帳號並綁定學生。
    - `00000_exists_parent_account`: 帳號已存在，成功綁定學生。
    - `student_bound`: 此帳號已綁定該學生。
    - `identity_verification_failed`: 身份驗證失敗。
    - 其他: 帳號創建失敗。
- 成功綁定後，會顯示成功訊息並引導用戶前往首頁登入。

### 6. 安全考量
- **XSS 防護**: 雖然此檔案本身沒有直接對 `$_POST` 或 `$_GET` 進行全局 `xss_filter`，但它依賴於 `config.php` 和 `adp_API.php` 中可能包含的全局過濾機制。前端的 `preventXss()` 函數也用於處理學生 ID。
- **密碼處理**: 前端對密碼的複雜度要求較高，但實際的密碼儲存和加密應在後端 `pro_insert_parentID.php` 中處理。
- **學生 ID 加密**: 學生 ID 使用 `string2hash()` 進行加密，增加了安全性。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_API.php`
- `UserData` 類別
- `modules/UserManage/pro_insert_parentID.php` (後端處理腳本)
- `string2hash()` (可能定義在 `adp_core_function.php`)
- `preventXss()` (可能定義在 `adp_core_security.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/UserManage/pro_insert_parentID.php` 的功能，了解其如何處理家長/大學伴帳號的創建和學生綁定，以及密碼的儲存方式。
- 確認 `UserData` 類別的定義和功能。
- 確認 `string2hash()` 和 `preventXss()` 函數的具體實現。
- 了解 `_SPLIT_SYMBOL` 常量的定義。
