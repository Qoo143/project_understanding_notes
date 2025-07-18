# out_of_service.html 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\out_of_service.html`

## 概述
`out_of_service.html` 檔案是一個**靜態的服務中斷頁面**。它用於在因材網系統因故無法提供服務時，向用戶顯示一個友好的提示訊息。該頁面設計簡潔，主要用於傳達服務中斷的信息，並引導用戶返回主頁。

## 主要功能與職責

### 1. 顯示服務中斷通知
- 頁面核心內容是「Out of Service 因材網維護中，會盡快完成請見諒！」的訊息。這與 `maintain.html` 的訊息略有不同，可能表示不同的服務中斷原因或階段。

### 2. 視覺設計
- **背景**: 頁面背景顏色為深棕色 (`#4B2E1D`)，與 `maintain.html` 相同。
- **Logo**: 頁面頂部顯示因材網的 Logo (`images/logo.png`)，並作為一個連結，其 `href="modules.php?op=main"` 表明它會引導用戶返回主模組入口頁面。
- **圖形元素**: 頁面包含一個 SVG 圖像 (`images/404notfound/404-star.svg`)，作為裝飾性元素。
- **響應式調整**: 頁面底部的 JavaScript 會根據瀏覽器窗口的大小動態調整 `wrap`、`content` 和 `notFoundStar` 元素的高度和寬度，以確保在不同設備上都能良好顯示。

### 3. CSS 引入與內聯樣式
- 引入了 `css/cms-index.css`，並包含大量的內聯 `<style>` 標籤，用於定義頁面的佈局、顏色、字體大小和響應式行為。

## 關鍵引用與依賴
- `css/cms-index.css`
- `images/logo.png`
- `images/404notfound/404-star.svg`
- `modules.php` (重定向目標)

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `modules.php` 的具體功能，以及它如何處理 `op=main` 參數。
- 了解 `maintain.html` 和 `out_of_service.html` 之間的區別，以及它們在系統中被觸發的條件。
