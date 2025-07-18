# GroupEvaluationClass.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\GroupEvaluationClass.php`

## 概述
`GroupEvaluationClass` 檔案定義了 `GroupEvaluationClass` 類別，這是一個專門用於處理**組內互評表單**的組件。它提供了教師建立、管理和評分這些表單的功能，主要用於處理組內互學檢核單。該類別負責從資料庫中讀取表單數據、處理分頁，並生成用於學生填寫表單的連結。

## 主要功能與職責

### 1. 建構函數 (`__construct`)
- 初始化類別，接收資料庫連接 (`$database_connection`)、用戶 ID (`$user_id`) 和存取等級 (`$access_level`)。
- 確保 `_SPLIT_SYMBOL` 常量已定義。

### 2. 讀取表單列表 (`loadData`)
- **目的**: 根據學期 (`$seme`) 和表單類型 (`$type`) 讀取互評表單列表。
- **權限檢查**: 要求用戶的 `access_level` 必須大於或等於 `USER_TEACHER`，否則拋出異常。
- **篩選與分頁**: 支援按學期和類型篩選，並實現了每頁 10 筆的分頁功能。
- **資料庫查詢**: 從 `check_list_table` 表中查詢數據，篩選條件包括 `teacher_id`、`is_delete = 1` 和 `type = 4` (組內互評表單)。
- **數據處理**: 將 `qusetion` 和 `score` 欄位從字串分割成陣列，並對表單的 `sn` 進行 `string2hash()` 編碼後返回。

### 3. 取得總頁數 (`getPages`)
- **目的**: 計算符合篩選條件的表單總頁數。
- **邏輯**: 類似 `loadData`，但只返回總記錄數除以每頁數量（10）後的無條件進位值。

### 4. 生成填寫連結 (`generateFilloutLink`)
- **目的**: 為指定的檢核表生成一個完整的填寫頁面 URL。
- **參數**: 接收 `check_list_sn`、可選的 `mission_id`、頁面類型 (`type`，預設為 'view') 和可選的 `base_url`。
- **URL 構建**: 將 `check_list_sn` 進行 `string2hash()` 和 `base64_encode()` 編碼後，構建指向 `modules_new.php` 的 URL，並帶有 `op=modload`, `name=srl`, `file=learning_regulated_fillout` 等參數。

### 5. 批量添加填寫連結 (`addFilloutLinksToForms`)
- **目的**: 為多個表單數據添加填寫連結。
- **邏輯**: 遍歷表單列表，對每個表單的 `hash_id` 進行 `hash2string()` 解碼，然後呼叫 `generateFilloutLink` 生成連結。

### 6. 取得單一表單詳細資料 (`getFormWithFilloutLink`)
- **目的**: 根據 hashed ID 獲取單一表單的詳細資料，並包含其填寫連結。
- **邏輯**: 解碼 `hash_id`，查詢 `check_list_table`，處理數據格式，並添加填寫連結。

## 關鍵引用與依賴
- 全局 `$dbh` (資料庫連接)
- `USER_TEACHER` 常量 (可能定義在 `include/config.php`)
- `_SPLIT_SYMBOL` 常量 (可能定義在 `include/config.php`)
- `string2hash()` 和 `hash2string()` 函數 (可能定義在 `include/adp_core_function.php`)
- `check_list_table` 資料庫表
- `modules_new.php` (作為填寫連結的基礎 URL)
- `learning_regulated_fillout.php` (填寫連結的目標檔案)

## 安全考量
- **權限控制**: `loadData` 方法實施了權限檢查，確保只有教師及以上權限的用戶才能訪問。
- **SQL 注入**: 所有資料庫查詢都使用了 PDO 的 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。
- **數據編碼/解碼**: `string2hash()` 和 `base64_encode()` 用於 URL 中的 ID 編碼，增加了 URL 的可讀性，但其安全性取決於 `string2hash()` 的強度。
- **錯誤處理**: 類別中包含 `try-catch` 區塊處理 PDO 異常，但其他方法可能需要更全面的錯誤處理。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `USER_TEACHER` 和 `_SPLIT_SYMBOL` 常量的具體定義。
- 驗證 `string2hash()` 和 `hash2string()` 函數的實現細節和安全性。
- 深入了解 `check_list_table` 資料庫表的結構和數據含義。
- 了解 `learning_regulated_fillout.php` 如何接收和處理來自此類別的編碼參數。