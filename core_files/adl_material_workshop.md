# adl_material_workshop.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\adl_material_workshop.php`

## 概述
`adl_material_workshop.php` 檔案是一個用於**展示增能研習影片列表**的頁面。它從資料庫中獲取影片資訊，並以卡片形式展示，同時提供按影片類型篩選的功能。該頁面主要用於提供教師或用戶觀看研習影片的入口。

## 主要功能與職責

### 1. 引入核心依賴與資料庫連接
- `include/adp_core_function.php`: 引入核心通用函數。
- `include/adp_core_security.php`: 引入核心安全函數，用於 `preventXss()`。
- `include/config.php`: 引入全局配置。
- `config_aial_db.php`: 引入資料庫配置，並創建 PDO 連接 (`$dbh_slave`) 到 `adl_materials` 資料庫。

### 2. 影片數據獲取
- 頁面通過 SQL 查詢從 `materials_workshop` 表中獲取影片數據，包括 `kind` (類型)、`video_src` (影片來源 URL)、`workshop_date` (研習日期)、`video_name` (影片名稱)、`lector` (講師) 和 `icon` (圖標)。

### 3. 動態生成影片列表 HTML
- 遍歷從資料庫獲取的影片數據 (`$arrVideo`)，為每個影片生成一個 HTML 卡片。
- 每個卡片包含：
    - 影片名稱，並作為一個鏈接到 `adl_video.php` 的超連結，傳遞 `embed` (YouTube 影片 ID) 和 `viedo_title` 參數。
    - 研習日期和講師。
    - 影片類型按鈕 (`$vSearchBar`)，用於前端篩選。
- 使用 `preventXss()` 對生成的 HTML 內容進行 XSS 防護。

### 4. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頂部導航欄（logo 和標題）、一個用於篩選影片類型的按鈕區，以及一個用於展示影片卡片的網格 (`card-group`)。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `css/font-awesome/css/all.css`、`css/adl_material.css`、`css/adl_material_page.css`、`HomePage/Styles/main.min.css` 和 `HomePage/Styles/home-edu.min.css`。
- **無障礙設計**: 包含「跳到主要內容」的跳轉鏈接 (`skip-link`)，提升無障礙體驗。

### 5. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.6.1.min.ob.js`。
- **Google Analytics**: 頁面引入了 Google Analytics 追蹤代碼。
- **影片類型篩選**: 實現了按影片類型篩選的功能。當用戶點擊篩選按鈕時，會根據 `data-kind` 屬性顯示或隱藏對應的影片卡片。

## 關鍵引用與依賴
- `config_aial_db.php` (資料庫配置)
- `include/adp_core_function.php`
- `include/adp_core_security.php`
- `include/config.php`
- `adl_video.php` (影片播放頁面)
- `scripts/jquery-3.6.1.min.ob.js`
- `images/logo.png`

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `adl_video.php` 如何接收和處理影片播放請求。
- 了解 `config_aial_db.php` 中 `adl_materials` 資料庫的具體結構和 `materials_workshop` 表的定義。
- 了解 `preventXss()` 函數的具體實現。
