# adl_video.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\adl_video.php`

## 概述
`adl_video.php` 檔案是一個專門用於**播放影片**的頁面。它支援播放兩種主要來源的影片：直接的影片檔案（MP4 或 HLS 串流）和 YouTube 嵌入影片。該頁面主要負責接收影片 URL 或 YouTube 嵌入 ID，並使用 Video.js 或 `<iframe>` 標籤來呈現影片播放器。

## 主要功能與職責

### 1. 引入核心依賴
- `include/adp_core_security.php`: 引入核心安全函數，其中包含 `xss_filter()` 和 `preventXssForVideoNote()`。
- `include/adp_core_function.php`: 引入核心通用函數，其中包含 `hash2string()`。

### 2. 影片來源處理
- 頁面通過 `$_GET` 參數接收影片來源：
    - `$_GET['v']`: 經過加密的影片 URL（MP4 檔案或 HLS 串流的原始 URL）。
    - `$_GET['sv']`: 經過加密的 HLS 串流 URL（如果影片是 HLS 格式）。
    - `$_GET['embed']`: YouTube 影片的嵌入 ID。
- **解密與過濾**: 使用 `hash2string()` 解密影片 URL，並使用 `xss_filter()` 對其進行 XSS 過濾，確保 URL 的安全性。
- **影片類型判斷**: 根據 `$_GET['sv']` 是否存在以及 `videoUrl` 是否包含 `manifest.mpd` 來判斷影片類型是 MP4 還是 HLS 串流 (`application/x-mpegURL`)。

### 3. 影片播放器渲染
- **Video.js 播放器**: 如果 `$_GET['embed']` 為空（即播放內部影片），則使用 Video.js 播放器 (`<video>` 標籤)。
    - 影片來源 (`src`) 和類型 (`type`) 會動態設置。
    - `oncontextmenu="return false;"`: 禁用右鍵菜單，防止用戶下載影片。
    - `preventXssForVideoNote($videoType)`: 對影片類型進行 XSS 防護。
- **YouTube 嵌入播放器**: 如果 `$_GET['embed']` 存在，則使用 YouTube 的 `<iframe>` 嵌入播放器。
    - `htmlspecialchars($_GET['embed'], ENT_QUOTES, 'UTF-8')`: 對 YouTube 嵌入 ID 進行 HTML 特殊字符轉義，防止 XSS 攻擊。

### 4. 頁面標題與影片標題顯示
- 頁面標題預設為「增能研習影片」。
- 如果 `$_GET['viedo_title']` 存在，則會在頁面中顯示該影片的標題。

### 5. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頂部導航欄（logo 和標題）和一個用於顯示影片播放器的區域 (`videoscope`)。
- **CSS 引入**: 引入了 `css/adl_material.css` 和 `css/video-js.css`，以及一些內聯樣式，用於控制頁面佈局和影片播放器的樣式。
- **響應式設計**: 包含媒體查詢 (`@media screen`)，以適應不同螢幕尺寸和方向的顯示。

### 6. Google Analytics
- 頁面引入了 Google Analytics 追蹤代碼，用於收集用戶觀看影片的行為數據。

## 關鍵引用與依賴
- `include/adp_core_security.php` (用於 `xss_filter()` 和 `preventXssForVideoNote()`)
- `include/adp_core_function.php` (用於 `hash2string()`)
- `css/video-js.css`
- `scripts/video.min.js`
- `images/logo.png`

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `hash2string()` 函數的具體實現，以確認其解密方式和安全性。
- 了解 `preventXssForVideoNote()` 函數的具體實現。
- 了解 `adl_material_workshop.php` 如何生成影片 URL 並傳遞給此頁面。
