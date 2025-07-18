# Rank.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Rank.php`

## 概述
`Rank.php` 檔案定義了 `Rank` 類別，這是一個**靜態工具類別**，主要用於**生成用戶在不同學習活動中的排行榜數據**。它提供了 `listing()` 方法，可以根據時間範圍和活動類型，查詢並返回用戶的學習時長或得分排名。

## 主要功能與職責

### 1. 排行榜數據生成 (`static function listing($sStartTime, $sEndTime, $sType, $iLength=3)`)
- **目的**: 根據指定的條件獲取用戶的學習排名數據。
- **參數**:
    - `$sStartTime`, `$sEndTime`: 時間範圍，用於篩選學習記錄。
    - `$sType`: 活動類型，包括 `video` (影片觀看時長)、`practice` (練習題作答次數/得分)、`dynamic` (動態評量得分)、`indicate` (縱貫測驗得分) 和 `unit` (單元測驗得分)。
    - `$iLength`: 返回的排名數量，預設為前 3 名。
- **數據來源**: 根據 `$sType` 參數，從不同的資料庫表（`video_review_record`, `prac_answer`, `exam_record_dynamic`, `exam_record_indicate`, `exam_record`, `exam_record_indicator`）中查詢數據。
- **SQL 查詢構建**: 根據活動類型動態構建 SQL 查詢，包括選擇的欄位 (`$sField`)、表名 (`$sTable`) 和時間欄位 (`$sTime`)。查詢會排除例外組織的用戶，並只包含 `access_level` 為 1 的用戶（學生）。
- **結果排序與限制**: 查詢結果按 `value` (學習時長或得分) 降序排序，並限制返回的記錄數量。

## 關鍵引用與依賴
- 全局 `$dbh_slave` (資料庫讀取連接)
- `organization_exception` 資料庫表
- `user_status` 資料庫表

## 安全考量
- **SQL 注入**: 類別中的資料庫查詢使用了 `prepare()` 和 `execute()`，這有助於防止 SQL 注入。但需要確保所有動態拼接的 SQL 片段（例如 `$sField`, `$sTable`, `$sTime`, `$sNotZero`）都是安全的，並且 `$sStartTime`, `$sEndTime` 等參數在傳遞給 SQL 之前經過了適當的驗證和清理。
- **數據敏感性**: 排行榜數據可能包含用戶的學習行為和表現，需要注意數據的隱私保護。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `organization_exception` 資料庫表的用途，以及它如何定義需要排除的組織。
- 了解 `user_status` 表中 `access_level` 為 1 的具體含義。
- 了解 `video_review_record`, `prac_answer`, `exam_record_dynamic`, `exam_record_indicate`, `exam_record`, `exam_record_indicator` 等資料庫表的結構和數據含義。
