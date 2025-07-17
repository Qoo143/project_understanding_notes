# adp_core_security.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\include\adp_core_security.php`

## 概述
`adp_core_security.php` 檔案主要負責應用程式的**基礎安全防護**，特別是針對跨站腳本 (XSS) 攻擊的防禦。它定義了用於過濾和清理用戶輸入的函數，以確保輸入的安全性。

## 主要功能與職責

### 1. 全局設定
- `header('Content-Type: text/html; charset=UTF-8')`: 設定 HTTP 響應頭，指定內容類型和字符編碼。
- `define("REPLACE_PLUS_SYMBOL", "@@@")`: 定義一個常量，用於在處理 URL 編碼時替換 `+` 符號，以避免 `urldecode` 將其錯誤地轉換為空格。

### 2. XSS 防護函數

#### `xss_filter($content)`
- **功能**: 對輸入內容進行 XSS 過濾。
- **參數**: `$content` (字串或任何類型)。
- **邏輯**:
    - 如果 `$content` 是字串，則呼叫 `filter()` 函數進行初步過濾。
    - 然後使用 `htmlspecialchars($cleanContent, ENT_QUOTES, 'UTF-8')` 將 HTML 特殊字符轉換為 HTML 實體，防止瀏覽器解析惡意腳本。
    - 如果 `$content` 不是字串，則直接返回原始內容。
- **用途**: 這是對外暴露的主要 XSS 過濾函數，通常在顯示用戶輸入內容到頁面之前使用。

#### `filter($input)`
- **功能**: 執行實際的 XSS 模式匹配和替換。
- **參數**: `$input` (字串)。
- **邏輯**:
    - 定義了一組正則表達式模式 (`$vPattern`)，用於匹配常見的 XSS 攻擊向量，例如 `onerror`、`onload`、`prompt`、`alert`、`href`、`javascript:`、`<script>`、`<meta>` 等。
    - **特殊處理**: 如果請求的 `HTTP_REFERER` 來自 `$_SERVER['HTTP_HOST'] . '/HomePage'`，則會從 `$vPattern` 中移除 `href=(.*)("|'|\))` 模式，這表明首頁功能可能允許特定的 `href` 屬性，需要注意其安全性。
    - `html_entity_decode($input, ENT_QUOTES, 'UTF-8')`: 在進行正則匹配之前，先將 HTML 實體解碼，以確保能夠匹配到原始的攻擊字串。
    - **`+` 符號處理**: 在 `urldecode` 之前將 `+` 替換為 `REPLACE_PLUS_SYMBOL`，`urldecode` 之後再替換回來，以避免 `+` 被錯誤地解釋為空格。
    - `urldecode($input)`: 對輸入進行 URL 解碼，這是資安建議的步驟，因為攻擊者可能會對惡意載荷進行多層編碼。
    - 遍歷 `$vPattern` 中的每個模式，如果匹配到，則將匹配到的部分替換為空字串 (`''`)。
- **用途**: 這是 `xss_filter()` 的內部函數，執行更細粒度的惡意代碼模式匹配和清理。

## 與 `preventXss()` 的關係
在 `adp_core_function.php` 中定義的 `preventXss()` 函數，其內部會呼叫 `xss_filter_html()`，而 `xss_filter_html()` 又會呼叫 `getHtmlPurifier()` 和 `xss_filter()`。這表明 `preventXss()` 是更高層次的 XSS 防護函數，它結合了 `xss_filter()` 的正則匹配和 `HTMLPurifier` 的更強大 HTML 清理功能。

## 安全考量
- **正則表達式過濾的局限性**: 僅依靠正則表達式進行 XSS 防護可能不夠全面，因為攻擊者可以利用各種編碼和混淆技術繞過簡單的模式匹配。
- **`href` 屬性白名單**: 針對首頁功能移除 `href` 屬性過濾的邏輯需要仔細審查，確保不會引入新的漏洞。
- **`urldecode` 的使用**: 在過濾之前進行 `urldecode` 是正確的做法，因為攻擊者可能會對惡意載荷進行多層編碼。
- **`htmlspecialchars` 的重要性**: 最終使用 `htmlspecialchars` 將輸出內容轉換為 HTML 實體是防止 XSS 的關鍵一步，它確保了惡意腳本不會被瀏覽器執行。

## 關鍵引用與依賴
- `preventXss()` (定義在 `adp_core_function.php`，但依賴此檔案中的 `xss_filter()`)
- `xss_filter_html()` (定義在 `adp_core_function.php`，依賴此檔案中的 `filter()` 和 `getHtmlPurifier()`)
- `HTMLPurifier` (通過 `vendor/autoload.php` 引入)

## 閱讀狀態
已閱讀

## 待辦事項
- 繼續閱讀 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 探索 `include/creatHierselectList.php` 的功能。
- 查找 `createAuthOptions()` 函數的定義和作用（它可能是一個簡單的陣列構造，或者定義在其他未被直接引入的檔案中）。
- 查找 `validateRedirect` 函數的定義。
