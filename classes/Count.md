# Count.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Count.php`

## 概述
`Count.php` 檔案定義了 `Count` 類別，這是一個**簡單的工具類別**，提供了靜態方法用於統計用戶在因材網中進行特定學習活動的次數。它主要用於獲取用戶觀看影片、完成練習題和動態評量的總次數。

## 主要功能與職責

### 1. 統計學習活動次數
- `static function video()`: 
    - **功能**: 回傳用戶觀看影片的次數。
    - **數據來源**: 查詢 `video_review_record` 表，統計 `finish_rate` 為 100% 的記錄。
- `static function practice()`: 
    - **功能**: 回傳用戶完成練習題的次數。
    - **數據來源**: 查詢 `prac_answer` 表，統計所有記錄。
- `static function dynamic()`: 
    - **功能**: 回傳用戶完成動態評量的次數。
    - **數據來源**: 查詢 `exam_record_dynamic` 表，統計所有記錄。

### 2. 數據來源與參數
- 所有方法都通過全局 `$dbh` (PDO 連接) 與資料庫互動。
- 查詢條件都基於 `$_SESSION['user_id']`，這表明這些統計是針對當前登入用戶的。

## 關鍵引用與依賴
- `dbh` (全局 PDO 資料庫連接)
- `$_SESSION['user_id']`
- `video_review_record` 資料庫表
- `prac_answer` 資料庫表
- `exam_record_dynamic` 資料庫表

## 安全考量
- 類別中的資料庫查詢使用了 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解這些統計數據在因材網前端的具體應用場景（例如，是否用於用戶儀表板、成就系統或學習進度展示）。
- 了解註釋掉的 SQL 語句 (`UPDATE user_status ...`) 的用途，它似乎與用戶的 `guide_step` 相關，可能是一個用於引導用戶完成學習步驟的機制。
