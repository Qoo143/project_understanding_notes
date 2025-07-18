# lecturer.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\lecturer.php`

## 概述
`lecturer.php` 檔案是一個用於**展示合格講師名單**的頁面。它從資料庫中獲取講師資訊，並提供按縣市和講師類別篩選的功能。頁面以表格形式展示講師的詳細信息，並根據所選的講師類別動態顯示不同的聯絡方式。該頁面主要用於提供用戶查詢和聯絡因材網合格講師的資訊。

## 主要功能與職責

### 1. 引入核心依賴與資料庫連接
- `include/config.php`: 應用程式的全局配置。
- 頁面直接使用全局資料庫連接 `$dbh` 來查詢數據。

### 2. 講師數據獲取
- 頁面通過一個複雜的 SQL 查詢從 `lecturer_list` 和 `city` 表中獲取講師數據。
- 查詢使用了多個 `UNION` 操作，根據 `category` 字段（講師類別）分組查詢，並在每個類別中選擇 `create_time` 最新的記錄（在一個 60 秒的時間窗口內）。這表明講師名單是按類別和最新更新時間進行管理的。
- 查詢結果包含 `category`、`display_order`、`city`、`school_name`、`lecturer_name`、`email`、`specialty`、`field`、`remark` 和 `create_time` 等字段。
- 查詢結果被處理成 `$vLecturer` 陣列，按 `category` 分組。
- `$sUpdateDate` 變數記錄了講師名單的最新更新日期。

### 3. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頁首（帶有 logo）、一個篩選區（縣市和分類下拉選單）、一個講師名單表格和一個頁尾（包含聯絡資訊）。
- **CSS 引入**: 引入了 Bootstrap 4 CSS (`bootstrap.min.css`) 以及多個自定義 CSS 檔案，如 `css/adluser.css`、`css/teacher_root.css`、`css/function.css` 和 `css/font-awesome/css/all.min.css`。頁面還包含大量的內聯 `<style>` 標籤，用於自定義樣式和響應式佈局。

### 4. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.5.1.min.ob.js`。
- **數據初始化**: PHP 變數 `$vLecturer` 中的數據被 `json_encode` 後直接嵌入到 JavaScript 變數中（例如 `array1`, `array2` 等），供前端使用。
- **篩選功能**: 
    - **縣市篩選**: `#citySelect` 下拉選單允許用戶按縣市篩選講師。縣市選項是根據當前顯示的講師數據動態生成的。
    - **分類篩選**: `#lecturerListType` 下拉選單允許用戶按講師類別篩選。當類別改變時，`vChoiceData` 會更新為對應類別的講師數據。
    - `changeData(vType, vCity)` 函數負責根據選定的類型和城市更新表格內容和縣市下拉選單。
- **表格列動態顯示**: `changeData` 函數根據選定的講師類別 (`vType`) 動態顯示或隱藏表格的不同列，以適應不同類別講師的資訊展示需求。
- **聯絡方式動態顯示**: 頁面底部 (`.ContactInfo`) 的聯絡方式會根據選定的講師類別動態改變，顯示該類別講師的特定聯絡資訊。
- **專長加註提示**: 表格頭部「專長加註」列包含一個提示框 (`tooltip-specialty`)，解釋其含義。

## 關鍵引用與依賴
- `include/config.php`
- `dbh` (全局資料庫連接)
- `scripts/jquery-3.5.1.min.ob.js`
- `images/logo.png`
- `images/teacher-logo.png`

## 安全考量
- **SQL 注入**: 頁面直接使用 `$dbh->prepare()` 和 `execute()` 進行資料庫查詢，這有助於防止 SQL 注入。但需要確保所有綁定參數都正確使用。
- **XSS 防護**: 頁面中沒有看到對 PHP 輸出到 HTML 的數據進行 `htmlspecialchars()` 或 `preventXss()` 處理，這可能存在 XSS 漏洞。例如，`$vValue['school_name']` 等直接輸出到 HTML 中。

## 閱讀狀態
已閱讀

## 待辦事項
- **檢查 XSS 防護**: 仔細檢查所有從資料庫讀取並輸出到 HTML 的數據，確保它們都經過了適當的 XSS 防護（例如使用 `htmlspecialchars()`）。
- 了解 `lecturer_list` 和 `city` 資料庫表的具體結構和數據含義。
- 了解 `category` 字段的具體含義和對應的講師類別。
