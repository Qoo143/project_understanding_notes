# Formater.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\Formater.php`

## 概述
`Formater.php` 檔案定義了 `Formater` 類別，這是一個**靜態工具類別**，主要用於**格式化字串和構建 SQL 查詢的條件語句**。它提供了一系列輔助方法，旨在簡化 SQL 查詢中 `IN` 語句、條件組合以及 `JOIN` 條件的生成，提高程式碼的可讀性和維護性。

## 主要功能與職責

### 1. SQL 運算子常量
- 定義了多個常量，代表常用的 SQL 運算子，例如：
    - `OPERATOR_IN` (`"IN"`)
    - `OPERATOR_EQUAL` (`"="`)
    - `OPERATOR_EQUAL_NOT` (`"!="`)
    - `OPERATOR_LIKE` (`"LIKE"`)
    - `OPERATOR_IS_NULL` (`"IS NULL"`)
    - `OPERATOR_IS_NOT_NULL` (`"IS NOT NULL"`)

### 2. 字串格式化
- `static function createCommaStr(array $arrDataList)`:
    - **功能**: 將一個陣列轉換為逗號分隔的單引號字串，例如 `['value1', 'value2']` 轉換為 `'value1','value2'`。
    - **用途**: 主要用於 SQL 查詢中的 `IN` 語句。
- `static function createSeperatorStr(string $sSeperator, array $arrDataList)`:
    - **功能**: 將一個陣列轉換為由指定分隔符分隔的單引號字串。
    - **用途**: 提供了更通用的字串連接方式。

### 3. SQL 條件構建
- `static function createOperatorStr($sOperator, $sTarget)`:
    - **功能**: 根據運算子和目標值，生成 SQL 條件的一部分，例如 `IN('value1','value2')` 或 `= 'value'`。
- `static function createConditionArr($sColName, $sCondition)`:
    - **功能**: 創建一個包含列名和條件的陣列，用於後續組合多個條件。
- `static function createConditionStr(array $arrCondition)`:
    - **功能**: 將多個條件陣列組合成一個完整的 `WHERE` 子句字串，使用 `AND` 連接。
- `static function createJoinConditionArr($sJoinTable, $sCondition)`:
    - **功能**: 創建一個包含連接表名和連接條件的陣列，用於後續組合多個 JOIN 條件。
- `static function createJoinConditionStr(array $arrJoinCondition)`:
    - **功能**: 將多個 JOIN 條件陣列組合成一個完整的 `JOIN` 語句字串。

## 關鍵引用與依賴
- 無外部依賴，是一個自包含的工具類別。

## 安全考量
- 該類別主要用於構建 SQL 語句的片段。**它本身不執行資料庫查詢，也不處理參數綁定。** 因此，在使用這些格式化後的字串構建最終 SQL 查詢時，**必須確保使用 PDO 的 `prepare()` 和 `bindValue()` 等方法進行參數綁定，以防止 SQL 注入漏洞。** 簡單地將這些字串直接拼接到 SQL 查詢中將會導致嚴重的安全問題。

## 閱讀狀態
已閱讀

## 待辦事項
- 查找因材網專案中哪些地方使用了 `Formater` 類別來構建 SQL 查詢，並檢查它們是否正確地使用了參數綁定來防止 SQL 注入。
