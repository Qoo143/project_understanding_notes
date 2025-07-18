# Step.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Step.php`

## 概述
`Step.php` 檔案定義了 `Step` 類別，它繼承自 `Count` 類別，專門用於**管理用戶的新手引導步驟**。該類別會根據用戶在因材網中完成的學習活動（觀看影片、練習題、動態評量）來判斷其當前的引導進度，並在必要時更新資料庫中的引導步驟。

## 主要功能與職責

### 1. 繼承與靜態屬性
- 繼承自 `Count` 類別，這意味著它可以直接使用 `Count` 類別中定義的 `video()`, `practice()`, `dynamic()` 等靜態方法來獲取學習活動的次數。
- 包含一個靜態屬性 `$step`，用於儲存用戶當前的引導步驟。

### 2. 運行流程 (`static function run()`)
- **目的**: 執行新手引導的檢查和更新流程。
- **邏輯**: 如果初始化時的步驟與檢查後的步驟不一致，則呼叫 `update()` 方法更新步驟。

### 3. 初始化步驟 (`static function init()`)
- **目的**: 從資料庫中獲取用戶當前的引導步驟。
- **數據來源**: 查詢 `user_status` 表中的 `guide_step` 欄位。

### 4. 檢查步驟 (`static function check()`)
- **目的**: 根據用戶完成的學習活動，判斷其應該處於哪個引導步驟。
- **邏輯**: 
    - 從 `step 0` 開始，如果用戶已觀看影片 (`self::video() > 0`)，則步驟加 1。
    - 如果用戶已完成練習題 (`self::practice() > 0`)，則步驟再加 1。
    - 如果用戶已完成動態評量 (`self::dynamic() > 0`)，則步驟再加 1。
- **關鍵點**: 這裡使用了 `case` 的 Fall-through 特性，即如果滿足 `case 0` 的條件，它會繼續執行 `case 1` 和 `case 2` 的邏輯，直到遇到 `break` 或函數結束。

### 5. 更新步驟 (`static function update()`)
- **目的**: 將用戶最新的引導步驟儲存到資料庫中。
- **數據更新**: 更新 `user_status` 表中的 `guide_step` 欄位。

## 數據流與安全考量
- **資料庫互動**: 類別通過全局 `$dbh` (PDO 連接) 與 `user_status` 資料庫表互動。
- **Session 數據**: 類別會讀取 `$_SESSION['user_id']` 來識別用戶。
- **SQL 注入**: 資料庫查詢使用了 `prepare()` 和 `bindValue()`，這有助於防止 SQL 注入。

## 關鍵引用與依賴
- `Count` 類別 (父類別)
- 全局 `$dbh` (資料庫連接)
- `user_status` 資料庫表

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `user_status` 資料庫表中 `guide_step` 欄位的具體含義和用途。
- 了解因材網前端如何根據 `guide_step` 的值來顯示不同的新手引導內容。
