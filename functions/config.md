# config.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\include\config.php`

## 概述
`config.php` 是因材網應用程式的**核心配置檔案**，定義了大量的全局常量、系統路徑、安全相關設定、用戶權限等級、檔案上傳路徑、以及一些環境相關的判斷邏輯。它是整個應用程式運行的基礎，許多其他檔案都會引入它來獲取這些全局設定。

## 主要功能與職責

### 1. 引入其他配置檔案
- `include/config_db.php`: 資料庫連接配置，可能包含敏感資訊，因此被單獨管理。
- `include/xapi_settings.php`: xAPI 相關配置。
- `adp_for_php7.php`: 可能是一個兼容 PHP 7 的核心功能檔案，根據路徑判斷，可能用於排程或特定環境。

### 2. 環境與路徑定義
- `date_default_timezone_set('Asia/Taipei')`: 設定時區為台北。
- `_ADP_PATH`: 定義應用程式的絕對路徑。
- `_ADP_URL`: 定義應用程式的 URL，根據 `SERVER_NAME` 和 `SERVER_ADDR` 判斷是 HTTPS 還是 HTTP，以及是否為正式環境 (`edu_idc`)。
- `_INCLASS_PLATFORM`: 定義課程包平台名稱。
- `_WEB_TITLE`: 網站標題。
- `_SPLIT_SYMBOL`, `_SPLIT_SYMBOL2`, `_SPLIT_SYMBOL3`, `_SPLIT_SYMBOL_API`: 定義多種分隔符號，用於數據處理和 API 傳輸。
- `_TEST_ACCOUNT`: 測試帳號。
- `_FRONT_PAGE`: 前端首頁檔案。
- `_MASTER_SITE`: 主站點的域名或 IP。
- `_HTTPS_SITE_LIST`, `_HTTPS_REQUEST_SCHEME`, `_TEST_SITE_LIST`: 用於安全相關的站點列表和協議判斷。

### 3. 郵件服務配置
- `_MAIL_ACC`, `_MAIL_PW`: 郵件發送帳號和密碼，**這部分包含敏感資訊，在實際部署中應特別注意保護。**

### 4. 班級與學校相關設定
- `_MAX_CLASS_T_NUM`: 每班最大教師數。
- `_MAX_CLASS_S_NUM`: 每班最大學生數，根據學校類型（國小、國中、高中、大學、海外）定義了不同的上限。
- `SCHOOL_TYPE_E`, `SCHOOL_TYPE_J`, `SCHOOL_TYPE_S`, `SCHOOL_TYPE_U`, `SCHOOL_TYPE_A`, `SCHOOL_TYPE_EJ`, `SCHOOL_TYPE_EJS`, `SCHOOL_TYPE_JS`, `SCHOOL_TYPE_SU`, `SCHOOL_TYPE_EJSU`: 定義了多種學校類型常量。
- `STU_IN_SCHOOL_YEAR`: 定義了不同學校類型下學生的在學年級範圍。

### 5. 密碼與任務設定
- `_MAX_PASSWORD_LENGTH`, `_MIN_PASSWORD_LENGTH`: 密碼長度限制。
- `_MAX_NODE_IN_MISSION`, `_MAX_NODE_IN_INDICATE_MISSION`: 任務中節點的最大數量。

### 6. 系統維護與提醒
- `_MAINTAIN_TIME_START`, `_MAINTAIN_TIME_STOP`: 系統維護的開始和結束時間。
- `$SiteConfig['setMaintainTime']`, `$SiteConfig['setMaintainShowPage']`: 控制是否啟用維護模式和維護頁面。
- `CORRECT_PERCENT_VIDEO`, `CORRECT_PERCENT_PRACTICE`, `CORRECT_PERCENT_DA`: SRL (自律學習) 提醒的正確率百分比。

### 7. 檔案路徑與上傳設定
- `_WORK_PATH`, `_WORK_URL`: 學生作品的儲存路徑和 URL。
- `_UPLOAD_PATH`, `_ADP_UPLOAD_PATH`: 通用上傳檔案目錄。
- `_ADP_VIDEO_PATH`: 影片上傳目錄。
- `_ADP_fb_PATH`: 截圖上傳目錄（用於問題回報）。
- `_CS_UPLOAD_PATH`, `_ADP_CS_UPLOAD_PATH`: 概念結構矩陣檔和試題的預設上傳目錄。
- `_ADP_TMP_UPLOAD_PATH`: 暫存檔案目錄。
- `_ADP_EXAM_DB_PATH`: 題庫網址。
- `_SITECONFIG_PATH`: 網站配置檔案的暫存目錄。
- `FILESIZE_KB`, `FILESIZE_MB`, `FILESIZE_GB`, `FILESIZE_TB`: 檔案大小單位常量。

### 8. 介面與主題設定
- `_TEMPLATE_DIR`: 模組預設模板目錄。
- `_THEME`, `_THEME_CSS`, `_THEME_IMG`: 布景主題相關設定。
- `_SYS_VER`: 系統版本。

### 9. 用戶權限常量 (重要)
- 定義了大量的 `USER_` 開頭的常量，代表不同用戶的權限等級，例如：
    - `USER_STUDENT`, `USER_STUDENT_DEMO1`, `USER_STUDENT_DEMO2`
    - `USER_PARENTS`, `USER_PARTNER`
    - `USER_TEACHER`, `USER_LECTURER`
    - `USER_SCHOOL_DIRECTOR`, `USER_SCHOOL_PRINCIPAL`, `USER_SCHOOL_ADMIN`
    - `USER_CITY_ADMIN`, `USER_EDU_ADMIN`, `USER_EDU_AREA`
    - `USER_OPERATOR`, `USER_ADMIN` 等。
- 定義了多個用戶群組常量，例如 `USER_STUDENT_GROUP`, `USER_TEACHER_GROUP`, `USER_PARENTS_GROUP`, `USER_ADL_OPERATOR_GROUP`。

### 10. 其他配置
- `PUBLISHER_MAPPING_SNODE_SUBJECT`: 用於組成完整小節點的科目列表。
- `GENDER_LIST_CHT`: 性別清單（中文）。
- `BASICAL_ABILITY_CITY`: 參加基本學力檢測的縣市列表。
- `_VIDEO_TYPE_COMFIRM`, `_AUDIO_TYPE_COMFIRM`: 允許的影片和音訊檔案類型。
- `_IDLETIME`: 登入後允許的閒置時間（2 小時）。
- `createDefaultPdoForOperator()`: 函數，用於教材維護人員重新宣告 PDO 連接。
- `$ntcuUnSe`: 定義 `unserialize` 函數的別名。

## 關鍵引用與依賴
- `include/config_db.php`
- `include/xapi_settings.php`
- `adp_for_php7.php`

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `include/config_db.php`，了解資料庫連接的具體配置。
- 了解 `createAuthOptions()` 函數的定義和作用（它可能定義在其他地方，或者是一個簡單的陣列構造）。
- 繼續閱讀 `include/adp_core_security.php` 以了解 `preventXss` 和 `validateRedirect` 的具體實現。
- 探索 `modules/menu/acl.php` 如何控制菜單權限。
- 了解 `adl_hash_file.json` 的生成和維護機制。
- 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
- 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
- 探索 `include/creatHierselectList.php` 的功能。
