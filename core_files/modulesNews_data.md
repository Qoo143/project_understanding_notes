# modulesNews_data.js 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\modulesNews_data.js`

## 概述
`modulesNews_data.js` 檔案是一個 JavaScript 文件，它定義了一個名為 `newsData` 的全局物件。這個物件用於儲存因材網的**最新消息和公告數據**，並根據不同的用戶身份（角色）進行分類。這些數據可能被前端頁面用於動態顯示新聞列表或彈出公告。

## 主要功能與職責

### 1. 新聞數據結構定義 (`newsData` 物件)
- `newsData` 物件的鍵是不同的用戶身份（角色），包括：
    - `"teacher"`: 教師
    - `"student"`: 學生
    - `"principal"`: 校長
    - `"parent"`: 家長
    - `"schoolAdmin"`: 校管理者
    - `"all"`: 所有身份
- 每個身份對應一個新聞陣列，陣列中的每個元素代表一條新聞，包含以下屬性：
    - `startdate` (string): 新聞的起始日期 (格式: `YYYY/MM/DD`)
    - `enddate` (string): 新聞的結束日期和時間 (格式: `YYYY/MM/DD HH:MM:SS`)
    - `icon` (string): 新聞圖標，可以是 `'new'` (最新消息) 或 `'warning'` (警告圖標)
    - `newsTitle` (string): 新聞標題
    - `newsDetail` (string): 新聞的詳細內容，可以直接包含 HTML 標籤，例如 `<p>`、`<b>`、`<a>` 和 `<img>`。這允許新聞內容具有豐富的格式和圖片。

### 2. 數據用途
- 這些數據通常會被因材網的前端 JavaScript 腳本讀取，然後根據當前用戶的身份和日期，篩選並顯示相關的新聞和公告。
- `newsDetail` 中的 HTML 內容允許直接嵌入圖片（例如 `images/modulesnews/3858/3858-01.jpg`），這表明新聞系統支持圖文並茂的公告。

### 3. 身份分類參考
- 檔案開頭的註釋說明了身份分類的對應關係，例如 `"teacher"` 對應 `USER_TEACHER_GROUP`，這表明前端會根據用戶的 `access_level` 來判斷顯示哪些新聞。

## 關鍵引用與依賴
- `images/modulesnews/` 目錄下的圖片資源。
- `modules_new.php` (新聞連結中可能引用的頁面)。
- `HomePage/faq/` (新聞連結中可能引用的頁面)。
- `contest/adlresource/frame` (新聞連結中可能引用的競賽頁面)。

## 閱讀狀態
已閱讀

## 待辦事項
- 查找因材網前端中哪些 JavaScript 腳本會讀取和處理 `newsData` 物件，以及它們如何根據用戶身份和日期來顯示新聞。
- 了解 `images/modulesnews/` 目錄下的圖片命名規則和管理方式。

