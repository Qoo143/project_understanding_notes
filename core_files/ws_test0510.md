# ws_test0510.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\ws_test0510.php`

## 概述
`ws_test0510.php` 檔案是一個**簡單的 WebSocket 連接測試腳本**。它主要包含前端 JavaScript 代碼，用於建立與 WebSocket 伺服器的連接，發送測試訊息，並監聽接收到的訊息和錯誤。這個檔案很可能是一個用於開發、測試或調試 WebSocket 功能的工具，而不是應用程式的核心業務邏輯。

## 主要功能與職責

### 1. WebSocket 連接測試
- **連接建立**: JavaScript 代碼嘗試建立一個 WebSocket 連接，目標 URL 為 `wss://<?= $_SERVER['HTTP_HOST']; ?>/wss`。這表明 WebSocket 伺服器運行在與當前網頁相同的域名下，使用安全 WebSocket 協議 (WSS)。
- **事件處理**: 
    - `ws.onopen`: 當 WebSocket 連接成功建立時觸發。它會彈出「連接成功」的提示，並向伺服器發送一個字串「tom」。
    - `ws.onmessage`: 當從伺服器接收到訊息時觸發。它會彈出接收到的訊息內容。
    - `ws.addEventListener('error')`: 監聽 WebSocket 連接錯誤，並將錯誤信息記錄到瀏覽器控制台。

## 關鍵引用與依賴
- WebSocket API
- `$_SERVER['HTTP_HOST']` (用於動態獲取當前主機名)

## 安全考量
- 由於這是一個測試腳本，其安全性影響範圍有限。但在生產環境中，應確保 WebSocket 連接的安全性，例如使用 WSS (WebSocket Secure) 協議，並對傳輸的數據進行適當的驗證和授權。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解因材網專案中 WebSocket 的具體用途和實現方式（例如，是否用於即時通知、聊天或協同功能）。
- 查找 WebSocket 伺服器的相關配置和程式碼（例如，在 `webscoket_server/` 目錄下）。