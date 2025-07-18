# choiceIdentity.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\choiceIdentity.php`

## 概述
`choiceIdentity.php` 檔案是一個**教育雲端帳號 (OpenID) 登入後的身份選擇頁面**。當用戶通過教育雲端帳號登入因材網後，如果其教育雲端帳號關聯了多個身份（例如，在不同學校擔任教師、學生等），此頁面會列出這些身份供用戶選擇。它還提供了綁定現有因材網帳號、創建新因材網帳號、更新年班資料以及處理重複帳號等功能，是教育雲端帳號與因材網帳號整合的關鍵環節。

## 主要功能與職責

### 1. 引入核心依賴與全局變數
- `include/config.php`: 應用程式的全局配置。
- `include/adp_core_function.php`: 核心通用函數，可能包含 `preventXss()` 等。
- `$_SERVER['DOCUMENT_ROOT']`: 用於構建絕對路徑。
- `$sLogoutUrl`: 登出後重定向的 URL。

### 2. 前端結構與樣式
- **HTML 結構**: 頁面包含一個頁首（帶有 logo 和登出按鈕）、一個主要內容區 (`#mainbox`) 和一個頁尾（顯示客服資訊）。
- **CSS 引入**: 引入了多個 CSS 檔案，包括 `css/normalize.css`、`css/teacher_root.css`、`css/adl.css`、`css/adlcomponent.css`、`css/adlplugin.css`、`css/adllogin.css`，以及 `scripts/sweetalert.css` 和 `scripts/sweetalert2/sweetalert2.min.css`。這表明頁面使用了標準的因材網樣式和 SweetAlert 彈出框。
- **動態顯示區塊**: 頁面包含多個 `div` 區塊，它們會根據不同的登入情境動態顯示或隱藏：
    - `displayOpenIDerrorInfo`: 顯示教育雲端帳號資料錯誤訊息。
    - `displayOpenIDList`: 顯示從教育雲端獲取的職稱資料列表，供用戶選擇。
    - `diplaySelectedLoginInfo`: 顯示用戶選擇的即將登入的資料。
    - `bindUserAccountBox`: 用於綁定現有因材網帳號。
    - `displayUserCreateAccountClassSelector`: 用於創建新因材網帳號時選擇年班。
    - `diplayUserModifiedClassSelector`: 用於更新因材網帳號年班資料。
    - `displayEnabledLoginInfo`: 顯示重複帳號列表，供用戶選擇啟用。

### 3. JavaScript 互動邏輯
- **jQuery 引入**: 頁面引入了 `scripts/jquery-3.5.1.min.ob.js`。
- **SweetAlert2**: 引入 `scripts/sweetalert2/sweetalert2.min.js`，用於顯示美觀的警告、成功或錯誤訊息。
- **LoadingOverlay**: 引入 `scripts/loadingoverlay.min.js`，用於在數據加載時顯示加載動畫。
- **常量定義**: 定義了多個 JavaScript 常量，用於 AJAX 請求的 `router` 和 `action` 參數，以及響應狀態碼。
- **`SemesterDataSet` 類別**: 用於處理和整理學期數據的 JavaScript 類別。
- **初始化流程**: 
    - 頁面加載時，首先顯示加載動畫 (`$.LoadingOverlay("show")`)。
    - 調用 `getUserData()` 函數（通過 AJAX 請求 `turnpage_out.php`，`action: GET_OPENID_USER_DATA`）獲取教育雲端帳號資料。
    - 根據 `getUserData()` 的返回結果，判斷顯示哪個區塊：
        - `RESPONSE_GET_USERINFO_SUCCESS`: 顯示 `displayOpenIDList`，列出職稱供用戶選擇。
        - `RESPONSE_GET_USERINFO_FAIL`: 顯示 `displayOpenIDerrorInfo`，顯示錯誤訊息。
- **事件監聽與處理**: 
    - **重新整理**: 點擊 `#btn_Reload` 重新加載頁面。
    - **登出**: 點擊 `.logout` 登出教育雲端帳號。
    - **選擇職稱**: 點擊 `.js-select-title input[type='radio']` 選擇職稱。
    - **選擇年級/班級**: 選擇年級後動態加載班級列表 (`#selectCreateAccountGrade`, `#selectModifiedAccountGrade`)。
    - **設置班級信息**: 點擊 `#btnSetClassInfo` 更新用戶班級信息。
    - **選擇登入信息**: 點擊 `.js-select-login-info` 選擇要登入的身份，並顯示確認信息。
    - **登入**: 點擊 `.js-login` 執行登入操作，根據後端返回的狀態碼處理不同的情境（錯誤、重定向、帳號不存在、重複帳號、年班資料不同）。
    - **綁定舊帳號**: 點擊 `#bindUserAccount` 綁定現有因材網帳號。
    - **啟用帳號**: 點擊 `#sendEnableAccount` 啟用重複帳號中的一個。
    - **創建新帳號**: 點擊 `#btnCreateAccount` 創建新的因材網帳號。
    - **顯示密碼**: 點擊 `.showBtn` 切換密碼顯示。
- **輔助函數**: 包含 `jumpIntoAnchor()` (滾動到指定元素)、`showPassword()` (切換密碼顯示)、`copyDeep()` (深拷貝物件)、`isObjectEmpty()` (判斷物件是否為空)、`arrayUnique()` (陣列去重)、`initailOption()` (初始化下拉選單)、`createOption()` (創建下拉選單選項)、`transformDisplaySemesterYear()` (轉換學年度學期顯示格式)、`cretateSeantnoList()` (生成座號列表)、`handleClassInfo()` (整理班級信息) 等。

### 4. 後端 API 互動 (通過 `turnpage_out.php`)
- 頁面通過 AJAX 向 `turnpage_out.php` 發送請求，`turnpage_out.php` 作為一個路由，將請求轉發到實際的後端處理邏輯（例如 `modules/OpenID/prodb_choiceIdentity.php` 或 `modules/OpenID/prodb_get_adl_classInfo.php`）。
- 涉及的後端 `action` 包括：`GET_OPENID_USER_DATA`、`LOGIN_SELECTED_IDENTITY`、`QUICK_LOGIN_CHECK`、`QUICK_LOGIN`、`BIND_USER_ACCOUNT`、`SET_ENABLED_ACCOUNT`、`SET_USER_CLASS_INFO`、`REGISTER_ACCOUNT`、`GET_SCHOOL_ALL_GRADE`、`GET_GRADE_INCLUDE_CLASS` 等。

## 關鍵引用與依賴
- `include/config.php`
- `include/adp_core_function.php`
- `css/normalize.css`、`css/teacher_root.css`、`css/adl.css`、`css/adlcomponent.css`、`css/adlplugin.css`、`css/adllogin.css`
- `scripts/sweetalert.css`、`scripts/sweetalert2/sweetalert2.min.js`
- `scripts/jquery-3.5.1.min.ob.js`
- `scripts/loadingoverlay.min.js`
- `turnpage_out.php` (作為 AJAX 請求的路由)
- `modules/OpenID/prodb_choiceIdentity.php` (推測的後端處理檔案)
- `modules/OpenID/prodb_get_adl_classInfo.php` (推測的後端處理檔案)

## 安全考量
- **XSS 防護**: 使用 `preventXss()` 對 PHP 輸出的變數進行過濾。
- **CSRF 防護**: 雖然沒有明確看到 CSRF token 的生成和驗證，但作為一個處理敏感操作的頁面，應確保後端有足夠的 CSRF 防護。
- **數據驗證**: 前端有大量的數據驗證，但後端也必須對所有接收到的數據進行嚴格的驗證和清理，以防止惡意輸入。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `turnpage_out.php` 如何將請求路由到不同的後端處理檔案。
- 深入研究 `modules/OpenID/prodb_choiceIdentity.php` 和 `modules/OpenID/prodb_get_adl_classInfo.php` 的具體實現，了解它們如何處理教育雲端帳號數據、因材網帳號綁定、創建和更新邏輯。
- 了解 `string2hash()` 和 `encodeAjaxUrlBase64()` 函數的具體實現。
- 了解 `USER_STUDENT` 和 `USER_SCHOOL_ADMIN` 等常量的定義。
