# login_openID.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\login_openID.php`

## 概述
`login_openID.php` 檔案是因材網與**教育雲端 OpenID Connect 服務**進行互動的登入頁面。它負責接收來自教育雲端認證服務的回調，獲取 Access Token 和 ID Token，並將用戶導向 `choiceIdentity.php` 頁面進行身份選擇。該檔案主要處理 OpenID Connect 流程的第一階段（授權碼交換 Access Token 和 ID Token），並在過程中處理錯誤和顯示訊息。

## 主要功能與職責

### 1. 引入核心依賴與初始化
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `safeRedirect()` 等。
- `modules/OpenID/config_openid.php`: OpenID 相關配置，例如 `TOKEN_ENDPOINT`、`REDIR_URI0`、`DYNAMICAL_ENDPOINT` 等。
- `modules/OpenID/library.class.php`: 包含 `openid` 類別，用於處理 OpenID Connect 流程。
- 頁面直接啟動 Session。

### 2. 輔助函數
- `checkState()`: 簡單檢查 `$_GET['code']` 和 `$_GET['state']` 是否存在。
- `setGlobalIP($sIP)`: 將傳入的 IP 設置為全局變數 `$ip`。
- `displayErrorMessage($sMsg)`: 在頁面中顯示錯誤訊息。
- `initReorganize()`: 顯示一個「重新整理」按鈕。
- `encapsulateJS($sfunctionText)`: 將 JavaScript 代碼包裹在 `<script>` 標籤中。
- `hideLoading()`: 隱藏加載動畫。

### 3. OpenID 登入流程 (`initOpenIDLogin` 和 `getOpenIDLoginStatus`)
- **`initOpenIDLogin($ep, $code)`**:
    - 這是主要的入口函數，用於啟動 OpenID 登入流程。
    - 它呼叫 `getOpenIDLoginStatus()` 來獲取 OpenID 登入狀態。
    - 如果成功，則重定向到 `choiceIdentity.php`。
    - 如果失敗，則隱藏加載動畫，顯示錯誤訊息，並顯示「重新整理」按鈕。
- **`getOpenIDLoginStatus($sEp, $sCode)`**:
    - 執行實際的 OpenID Connect 流程。
    - **狀態檢查**: 再次呼叫 `checkState()` 驗證 `code` 和 `state`。
    - **獲取 Access Token**: 使用 `openid` 類別的 `getAccessToken()` 方法，向 `TOKEN_ENDPOINT` 發送請求，交換 `code` 以獲取 `access_token` 和 `id_token`。
    - **錯誤處理**: 如果無法獲取 Access Token，則返回錯誤訊息。
    - **Session 儲存**: 將 `access_token`、`refresh_token` 和 `id_token` 儲存到 `$_SESSION` 中，並設置 `$_SESSION['loginfrom'] = 'oidc'`。
    - 設置全局 IP (`setGlobalIP($_SERVER['REMOTE_ADDR'])`）。

### 4. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頁首（帶有 logo 和登出按鈕）、一個主要內容區 (`#mainbox`) 和一個頁尾（顯示客服資訊）。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `css/normalize.css`、`css/teacher_root.css`、`css/adl.css`、`css/adlcomponent.css`、`css/adlplugin.css`、`css/adllogin.css`。頁面還包含大量的內聯 `<style>` 標籤，用於自定義樣式。
- **JavaScript 引入**: 引入了 `scripts/jquery-3.6.1.min.ob.js` 和 `scripts/loadingoverlay.min.js`。

### 5. 錯誤訊息顯示
- 如果在處理過程中發生錯誤，頁面會顯示一個帶有錯誤訊息的 `div` (`#displayOpenIDerrorInfo`)，並顯示「取得教育雲端使用者資料中...」的提示。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `modules/OpenID/config_openid.php`
- `modules/OpenID/library.class.php`
- `choiceIdentity.php` (成功登入後的重定向目標)
- `$_SESSION` 超級全局變數

## 安全考量
- **`state` 參數驗證**: 檔案中註釋掉的 `if($state != $_SESSION['azp_state'])` 判斷非常重要，用於防止 CSRF 攻擊。雖然目前被註釋掉，但在實際應用中應啟用並確保 `state` 的隨機性和一次性。
- **Access Token 和 ID Token 的處理**: 確保這些敏感信息在傳輸和儲存過程中受到保護。
- **錯誤訊息**: 錯誤訊息直接顯示在前端，但沒有包含敏感的後端信息。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `modules/OpenID/config_openid.php` 中定義的 OpenID Connect 端點和配置。
- 深入研究 `modules/OpenID/library.class.php` 中 `openid` 類別的具體實現，了解其如何處理 OpenID Connect 協議的各個階段。
- 了解 `choiceIdentity.php` 如何接收和處理從此頁面傳遞的 Session 數據，並引導用戶選擇身份。
- 重新評估 `state` 參數驗證的必要性，並考慮在生產環境中啟用它。
