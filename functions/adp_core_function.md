# adp_core_function.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\include\adp_core_function.php`

## 概述
`adp_core_function.php` 是一個包含大量因材網應用程式通用函數的檔案。這些函數涵蓋了日期時間處理、學年度學期轉換、用戶資訊查詢、資料庫相關操作、字串加密解密、XSS 防護、檔案處理、以及一些輔助性的工具函數。這個檔案在整個應用程式中被廣泛引用，提供了許多基礎性的功能。

## 主要功能與職責

### 1. 引入依賴
- `include/adp_core_security.php`: 引入核心安全函數，例如 `preventXss` 和 `xss_filter_html`。
- `vendor/autoload.php`: 引入 Composer 自動載入器，用於載入第三方庫（例如 HTMLPurifier）。

### 2. 日期與學期處理
- `getYearSeme()`: 取得因材網目前學年度學期（阿拉伯數字）。
- `Semester_id2FullName($semester)`: 將學年度學期（阿拉伯數字）轉換為中文。
- `getNowSemeYear()`: 取得因材網目前學年度。
- `date2YearSeme($year, $month)`: 透過西元年、月，取得因材網學年度學期。

### 3. 資料庫相關查詢與轉換
- `subject2mapSN($subject)`: 透過 `subject_id` 取得 `map_sn`。
- `mapSN2subject($map_sn)`: 透過 `map_sn` 取得 `subject_id`。
- `user_id2user_account($user_id)`: 透過 `user_id` 取得 `user_account`。
- `user_account2user_id($user_account)`: 透過 `user_account` 取得 `user_id`。
- `GetoverseasSchool_id()`: 抓取海外學校的 `organization_id` 白名單。
- `array2sqlIN($array)`: 將陣列轉換為 MySQL `IN()` 語句的參數值，並進行 XSS 防護。
- `id2uname($id)`: 透過 `user_id` 取得使用者姓名。
- `id2UserLevel($id)`: 透過 `user_id` 取得 `access_level`。
- `id2level($id)`: 透過 `access_level` 取得存取等級名稱。
- `id2publisher($id)`: 透過 `publisher_id` 取得出版商名稱。
- `id2subject($id)`: 透過 `subject_id` 取得科目名稱。
- `id2city($id)`: 透過 `city_code` 取得城市名稱。
- `id2org($org_id)`: 透過 `organization_id` 取得學校名稱。
- `getSchoolType($org)`: 透過 `organization_id` 取得學校類型。
- `getOrgGradeRange($sOrgId)`: 透過 `organization_id` 取得學校年級範圍。
- `learningStage2Grade($sStage)`: 透過學習階段取得年級範圍。
- `PaperSn2PaperInfo($paper_sn)`: 透過單元卷 `paper_sn` 取得卷名稱與科目別。
- `getMissionDetail($mission_sn)`: 透過 `mission_sn` 取得任務資料。
- `getStudentGroupAndGroupLeader($user_id)`: 透過 `user_id` 取得學生的 `group_id` 清單。
- `sNode2bNode($sNode, $subject)`: 透過小節點和 `subject_id` 取得對應的節點年級、大節點。
- `getSnodeGradeByMapSn($sSnode, $iMapSn)`: 透過小節點和 `map_sn` 取得節點年級。
- `getSnodeGradeBySubjectId($sSnode, $iSubjectId)`: 透過小節點和 `subject_id` 取得節點年級。
- `getSnodePublisherMappingSn($sSnode, $iSubjectId)`: 取得預設的 `publisher mapping sn`。
- `getManagingMapSN()`: 取得目前使用者（教材維護人員、admin）擁有權限的星空圖領域清單。
- `getSqlIndicatorSort($sNodeType, $sNodeColumnName)`: 取得能力指標大/小節點排序 SQL 語法。
- `getCSlicense($user_id)`: 取得用戶的 CS 許可證。
- `getEPlicense($u_data)`: 取得用戶的 EP 許可證。
- `get_mad_res($ep_id, $item_num, $user_id)`: 獲取 MAD 相關結果。

### 4. 字串處理與加密解密
- `string2hash($str, $bUseRand)`: 將字串進行因材網自定義加密，可選擇是否使用隨機鹽值。
- `hash2string($DecStr, $bUseRand)`: 將因材網自定義加密的字串解密。
- `str2compiler($str)`: 字串轉換函數。
- `compiler2str($str)`: 字串反轉換函數。
- `EncDecStr2Compiler($pass)`: 字串編譯函數。
- `microtime_float()`: 獲取微秒時間。

### 5. XSS 防護與 HTML 清理
- `preventXss(&$aValue)`: 遞歸地對輸入數據（字串或陣列）進行 XSS 防護過濾。
- `HTMLPurifier_HTMLModule_Custom` 類別: 擴展 HTMLPurifier，定義了允許的 HTML 標籤及其屬性，用於更精細的 HTML 清理。
- `getHtmlPurifier()`: 獲取 HTMLPurifier 實例，並設定允許的 HTML 標籤和屬性。
- `xss_filter_html($content)`: 使用 HTMLPurifier 對含有 HTML 標籤的字串進行 XSS 防護過濾。
- `isJsonArray($sValue)`: 判斷字串是否為有效的 JSON 陣列。
- `decodeHtmlEntities($sValue)`: 將字串進行 HTML 實體解碼。

### 6. 檔案處理與下載
- `force_download($data, $name, $mimetype, $filesize)`: 強制檔案下載。
- `ob_clean_all()`: 清除所有輸出緩衝。
- `exam_clean_all()`: 清除考試相關的 Session 變數。
- `chk_file_exist($exec_file, $exec_line)`: 檢查檔案是否存在，如果不存在則輸出錯誤訊息並停止。

### 7. 輔助與調試函數
- `debug_msg($title, $showarry)`: 用於調試，輸出變數內容。
- `debugBai($line, $file, $data, $type)`: 用於調試，根據類型輸出調試信息（例如 `console.log`）。
- `validateRedirect($url)`: 驗證並清理 URL，防止開放重定向漏洞。
    - **功能**: 確保重定向 URL 的安全，防止惡意用戶將用戶重定向到惡意網站。
    - **參數**: `$url` (要驗證的 URL 字串)。
    - **邏輯**: 
        - 使用 `parse_url()` 解析 URL 的各個部分（scheme, host, path, query）。
        - 根據當前伺服器環境（`$_SERVER['HTTP_HOST']` 或 `$_SERVER['SERVER_ADDR']`）和 `config.php` 中定義的 `_HTTPS_SITE_LIST`，判斷使用 `http://` 或 `https://`。
        - 重新構建 URL，確保 `host` 部分是安全的，如果原始 URL 沒有 `host`，則使用當前伺服器的 `SERVER_NAME`。
        - 確保 `path` 以 `/` 開頭。
        - 確保 `query` 以 `?` 開頭。
        - 最後使用 `filter_var($new_url, FILTER_VALIDATE_URL)` 再次驗證最終構建的 URL 是否有效。
        - 如果驗證失敗，則輸出「錯誤的URL。」並終止程式。
    - **安全考量**: 這是防止開放重定向漏洞的重要防護措施。
- `validateRedirect($url)`: 驗證並清理 URL，防止開放重定向漏洞。
    - **功能**: 確保重定向 URL 的安全，防止惡意用戶將用戶重定向到惡意網站。
    - **參數**: `$url` (要驗證的 URL 字串)。
    - **邏輯**: 
        - 使用 `parse_url()` 解析 URL 的各個部分（scheme, host, path, query）。
        - 根據當前伺服器環境（`$_SERVER['HTTP_HOST']` 或 `$_SERVER['SERVER_ADDR']`）和 `config.php` 中定義的 `_HTTPS_SITE_LIST`，判斷使用 `http://` 或 `https://`。
        - 重新構建 URL，確保 `host` 部分是安全的，如果原始 URL 沒有 `host`，則使用當前伺服器的 `SERVER_NAME`。
        - 確保 `path` 以 `/` 開頭。
        - 確保 `query` 以 `?` 開頭。
        - 最後使用 `filter_var($new_url, FILTER_VALIDATE_URL)` 再次驗證最終構建的 URL 是否有效。
        - 如果驗證失敗，則輸出「錯誤的URL。」並終止程式。
    - **安全考量**: 這是防止開放重定向漏洞的重要防護措施。

### 8. 數據格式轉換
- `num2chinese($id)`: 阿拉伯數字轉中文數字。
- `num2English($id)`: 阿拉伯數字轉英文字母。
- `get_csid($pid, $sid, $vid, $uid)`: 組合 `cs_id`。
- `get_epid($pid, $sid, $vid, $uid, $paper_vol)`: 組合 `ep_id`。
- `explode_cs_id($cs_id)`: 分解 `cs_id`。
- `explode_ep_id($ep_id)`: 分解 `ep_id`。
- `CSid2FullName($cs_id)`: `cs_id` 轉全名。
- `EPid2FullName($ep_id)`: `ep_id` 轉全名。
- `getChtExamTitle($cs_id, $paper_vol)`: 獲取中文考試標題。
- `EPid2CSid($ep_id)`: `ep_id` 轉 `cs_id`。
- `org2citycode($id)`: 學校機關轉城市編號。
- `vol2grade($subject, $id)`: 冊別轉年級學期。
- `sn2name($nodes)`: 概念 sn 轉名稱。
- `sn2nameForVideo($nodes, $map_sn)`: 概念 sn 轉名稱（針對影片）。
- `chkNodeFileExist($node, $bNode, $subject)`: 檢查節點檔案是否存在。

### 9. AJAX URL 編碼
- `encodeAjaxUrlBase64($folder, $filename, $bCustom)`: 取得因材網前端呼叫後端 API 所需的 `router` 參數值，並將允許的路徑存放到 `$_SESSION['ADL_ROUTER_PASS']`。這個函數在 `turnpage.php` 的路由機制中扮演關鍵角色。

## 關鍵引用與依賴
- `include/adp_core_security.php`
- `vendor/autoload.php` (用於 HTMLPurifier)
- `$_SESSION['ADL_ROUTER_PASS']` (由 `encodeAjaxUrlBase64` 填充，由 `turnpage.php` 使用)
- `dbh`, `dbh_slave` (全局資料庫連接)
- `_HTTPS_SITE_LIST` (定義在 `include/config.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 繼續深入研究 `auth_chk.php` 中提到的依賴檔案和函數：
  - 了解 `createAuthOptions()` 函數的定義和作用（它可能是一個簡單的陣列構造，或者定義在其他未被直接引入的檔案中）。
  - 探索 `modules/menu/acl.php` 如何控制菜單權限。
  - 了解 `adl_hash_file.json` 的生成和維護機制。
  - 探索 `encodeAjaxUrlBase64()` 函數的生成邏輯，以及 `$_SESSION['ADL_ROUTER_PASS']` 是如何被填充的。
  - 了解 `classes/adp_core_class.php` 中 `UserData` 類別的詳細結構和數據來源。
  - 探索 `include/creatHierselectList.php` 的功能。