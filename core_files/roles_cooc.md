# roles_cooc.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\roles_cooc.php`

## 概述
`roles_cooc.php` 檔案是因材網與**酷課雲 (Cooc Cloud) 整合後，用於用戶選擇登入身份的頁面**。當用戶通過酷課雲登入因材網，並且其酷課雲帳號關聯了多個身份（例如，在不同學校擔任教師、學生等），此頁面會列出這些身份供用戶選擇。它是一個前端展示頁面，通過 AJAX 與自身進行互動，處理用戶的身份選擇和最終的登入重定向。

## 主要功能與職責

### 1. 引入核心依賴與權限檢查
- `include/config.php`: 應用程式的全局配置。
- `include/ftp_function.php`: 可能包含與 FTP 相關的函數，但在此檔案中未直接使用。
- `classes/adp_core_class.php`: 核心類別，可能包含 `UserData` 等。
- `include/adp_core_function.php`: 核心通用函數，包含 `arraytoJS()` 等。
- `modules/UserManage/create_account.php`: 用於創建帳號的函數。
- `Auth/Auth.php`: PEAR Auth 庫，用於因材網內部認證。
- **登入狀態檢查**: 如果用戶已經登入 (`isset($_SESSION['USERDATA'])`)，則直接重定向到 `modules_new.php?op=main`，不允許再次選擇身份。

### 2. 數據準備與前端傳遞
- 頁面從 `$_SESSION['cooc_data']` 中獲取酷課雲返回的用戶身份數據 (`sso_role`)。這些數據包含了用戶在不同學校和身份下的詳細信息。
- `arraytoJS()` 函數用於將 PHP 陣列轉換為 JavaScript 可用的 JSON 字串，並傳遞給前端。

### 3. 身份選擇與登入處理 (`switch($_POST['status'])`)
- **`choice` 動作**: 當用戶在前端選擇一個身份並提交時，此動作被觸發。
    - **參數獲取**: 獲取用戶選擇的身份 (`sRole`) 和學校代碼 (`sOrgCode`)。
    - **數據驗證**: 嚴格驗證傳入的身份和學校代碼是否與 Session 中儲存的酷課雲數據匹配，防止篡改。
    - **用戶數據整理**: 根據用戶選擇的身份，從 Session 中整理出詳細的用戶數據，包括 `hash_guid`、`cooc_sub`、`user_name`、`user_email`、`priori_name`、`identity`、`role`、`access_level`、`grade`、`class`、`seat` 和 `childs`。
    - **帳號查詢與登入**: 
        - 根據 `hash_guid` 或 `cooc_sub` 查詢 `user_info` 和 `user_third_party` 表，查找匹配的因材網帳號。
        - 如果找到單一匹配帳號，則更新 `user_info` 表中的用戶信息（Email、hash_guid、priori_name、年級、班級），並更新 `user_third_party` 表中的 `cooc_sub`。
        - 根據用戶角色（教師、學生、家長），更新 `seme_teacher_subject`、`seme_student` 或 `user_family` 表中的學年班級信息或親子關係。
        - 實例化 `UserData` 類別，設置 Session，並使用 `Auth` 庫進行登入。
        - 返回成功狀態和重定向 URL (`modules_new.php?op=main`)。
    - **帳號創建**: 如果沒有找到匹配帳號，則呼叫 `creatCoocAccount()` 函數創建新帳號。如果用戶是家長，則可能還會呼叫 `creatUserChildsAccount()` 創建子帳號。
    - **錯誤處理**: 如果處理過程中發生錯誤，則返回錯誤訊息。

### 4. 前端介面
- 頁面包含一個頁首、一個用於顯示身份選擇的 `div` (`#choice_identity`) 和一個頁尾。
- **動態生成身份選項**: JavaScript 根據從 PHP 傳遞的 `vIdentity` 數據動態生成學校區塊 (`schoolBlock`) 和身份選擇按鈕 (`choiceStyle`)。
- **自動跳轉**: 如果只有一個身份選項，則會自動觸發點擊事件，直接登入。
- **返回酷課雲**: 提供一個「返回酷課雲」按鈕，用於登出並返回酷課雲主頁。

### 5. 輔助函數 (PHP)
- `checkArrayData($arrData,$arrCheckKeys)`: 檢查陣列中指定鍵的值是否為空。
- `updateLoginStatus($user_id)`: 更新 `user_status` 表中的登入頻率、時間和 IP。
- `addTeacherSeme($arrData)`: 更新教師的學年班級信息。
- `addStudentSeme($arrData)`: 更新學生的學年班級信息。
- `addChildFamily($sOrgCode, $sChildId, $vChild)`: 添加親子關係。
- `getUserMaxUid()`: 獲取 `user_info` 表的下一個 `AUTO_INCREMENT` 值。
- `updateCoocSub($sCoocSub, $sUserId)`: 更新 `user_third_party` 表中的 `cooc_sub`。
- `findUserChilds($sOrgCode, $vChild)`: 查找子帳號。
- `creatCoocAccount($sOrgCode, $arrUserData)`: 創建酷課雲帳號。
- `creatUserChildsAccount($sOrgCode, $arrUserData)`: 創建用戶子帳號。

## 關鍵引用與依賴
- `include/config.php`
- `include/ftp_function.php`
- `classes/adp_core_class.php`
- `include/adp_core_function.php`
- `modules/UserManage/create_account.php`
- `Auth/Auth.php`
- `$_SESSION` 超級全局變數
- `dbh` (全局資料庫連接)

## 安全考量
- **數據驗證**: 對接收到的 POST 數據進行了 `htmlspecialchars()` 處理，並在 PHP 端進行了數據驗證，這是良好的安全實踐。
- **Session 數據依賴**: 腳本嚴重依賴 `$_SESSION['cooc_data']`，需要確保這些 Session 數據的完整性和安全性。
- **SQL 注入**: 使用 `dbh->prepare()` 和 `bindValue()` 進行資料庫操作，有助於防止 SQL 注入。
- **錯誤訊息**: 錯誤訊息中包含行號，這在生產環境中可能洩露敏感信息，應考慮移除。

## 閱讀狀態
已閱讀

## 待辦事項
- 深入研究 `modules/UserManage/create_account.php` 中 `createAccount()` 函數的具體實現。
- 了解 `include/ftp_function.php` 在此檔案中的潛在作用。
- 了解 `adp_core_class.php` 中 `UserData` 類別的具體實現。
- 了解 `Auth/Auth.php` 庫在登入流程中的具體作用。
