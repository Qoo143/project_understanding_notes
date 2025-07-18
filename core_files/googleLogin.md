# googleLogin.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\googleLogin.php`

## 概述
`googleLogin.php` 檔案是一個**用於整合 Google 登入功能**的頁面。它主要包含前端的 HTML 和 JavaScript，用於顯示 Google 登入按鈕，處理用戶通過 Google 帳號登入後的響應，並將 Google 返回的 ID Token 發送到後端 (`loginAuth.php`) 進行驗證和處理。此頁面是因材網實現 Google SSO (單點登入) 的前端入口。

## 主要功能與職責

### 1. Google Sign-In 整合
- **Meta 標籤**: 頁面頭部包含兩個 `meta` 標籤，用於配置 Google Sign-In：
    - `google-signin-scope`: 定義了請求的用戶資料範圍，這裡請求的是 `profile` 和 `email`。
    - `google-signin-client_id`: 指定了 Google API 客戶端 ID (`743108754340-tqgt317sospqdfje6tage054l422g12t.apps.googleusercontent.com`)，這是與 Google 應用程式關聯的唯一標識符。
- **Google 平台 JavaScript 庫**: 引入了 `https://apis.google.com/js/platform.js`，這是 Google Sign-In 功能所需的核心 JavaScript 庫。
- **Google 登入按鈕**: 頁面中包含一個 `div` 元素 (`<div class="g-signin2" data-onsuccess="onSignIn" data-theme="dark"></div>`)，它會被 Google 平台庫渲染成一個可點擊的 Google 登入按鈕。`data-onsuccess="onSignIn"` 指定了成功登入後要執行的 JavaScript 函數。

### 2. JavaScript 登入處理
- **`onSignIn(googleUser)` 函數**: 
    - 這是 Google 登入成功後的回調函數。它接收一個 `googleUser` 物件，其中包含了已登入用戶的資訊。
    - `var id_token = googleUser.getAuthResponse().id_token;`: 從 `googleUser` 物件中提取 Google 返回的 ID Token。這個 ID Token 是一個 JWT (JSON Web Token)，包含了用戶的身份信息，並且可以被後端安全地驗證。
    - `postData("./loginAuth.php", { id_token })`: 調用 `postData` 函數，將獲取的 `id_token` 發送到後端腳本 `loginAuth.php`。
    - `then(data => { location.href = data.message; })`: 處理 `loginAuth.php` 返回的響應。如果成功，則將頁面重定向到 `data.message` 中指定的 URL。
    - `catch(error => console.error(error))`: 捕獲並記錄錯誤。
- **`postData(url, data)` 函數**: 
    - 這是用於發送 POST 請求的異步函數，它使用 `fetch` API。
    - `body: JSON.stringify(data)`: 將要發送的數據轉換為 JSON 字串。
    - `headers: { 'content-type': 'application/json' }`: 指定請求的內容類型為 JSON。
    - `method: 'POST'`: 指定請求方法為 POST。
    - `mode: 'cors'`: 允許跨域請求。
    - `return fetch(...).then(response => response.json())`: 發送請求並將響應解析為 JSON。

## 關鍵引用與依賴
- Google Sign-In JavaScript 庫: `https://apis.google.com/js/platform.js`
- 後端驗證腳本: `loginAuth.php`

## 安全考量
- **ID Token 驗證**: 雖然前端將 ID Token 發送給後端，但真正的安全性在於後端對這個 ID Token 的嚴格驗證。後端必須驗證 Token 的簽名、發行者、受眾、有效期等，以確保其合法性。
- **客戶端 ID 洩露**: `google-signin-client_id` 直接暴露在前端代碼中是正常的，因為它是公開的。
- **HTTPS**: 確保整個登入流程都在 HTTPS 環境下進行，以防止中間人攻擊。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `loginAuth.php` 的功能，了解它如何驗證 Google ID Token，並將 Google 帳號與因材網內部帳號進行關聯或創建新帳號。
- 了解因材網後端如何處理 Google 登入後的用戶會話管理和權限分配。
