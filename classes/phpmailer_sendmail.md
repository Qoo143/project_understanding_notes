# phpmailer_sendmail.php 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\phpmailer_sendmail.php`

## 概述
`phpmailer_sendmail.php` 檔案定義了一個名為 `phpmailer_sendmail()` 的函數，該函數封裝了 **PHPMailer 函式庫**的使用，用於在因材網應用程式中發送電子郵件。它配置了 SMTP 伺服器（Gmail），並處理郵件的主題、內容、收件人以及可選的附件。

## 主要功能與職責

### 1. 引入 PHPMailer 函式庫
- `require_once('PHPMailer/class.phpmailer.php');`
- `include_once('PHPMailer/class.smtp.php');`

### 2. PHPMailer 配置
- `IsSMTP()`: 設定使用 SMTP 協議發送郵件。
- `SMTPDebug = 0`: 禁用 SMTP 調試輸出。
- `SMTPAuth = true`: 啟用 SMTP 身份驗證。
- `SMTPSecure = "tls"`: 設定加密協議為 TLS。
- `Host = "smtp.gmail.com"`: 設定 SMTP 伺服器為 Gmail 的 SMTP 伺服器。
- `Port = 587`: 設定 SMTP 端口為 587 (TLS 端口)。
- `Username = _MAIL_ACC`: 使用 `_MAIL_ACC` 常量作為 SMTP 用戶名。
- `Password = _MAIL_PW`: 使用 `_MAIL_PW` 常量作為 SMTP 密碼。
- `SetFrom(_MAIL_ACC, '教育部-因材網' ,TRUE)`: 設定發件人地址和名稱。
- `Subject = $subject`: 設定郵件主題。
- `SMTPOptions`: 設定 SSL 選項，包括 `verify_peer` 和 `verify_peer_name` 為 `true`，但 `allow_self_signed` 也為 `true`，這可能會降低安全性。

### 3. 收件人處理
- 函數接受 `$email` 參數，可以是單個 Email 地址字串，也可以是 Email 地址陣列。它會遍歷陣列並為每個地址添加收件人。

### 4. 郵件內容與附件
- `MsgHTML($body)`: 設定郵件內容為 HTML 格式。
- `AltBody`: 設定純文本格式的郵件內容，用於不支援 HTML 的郵件客戶端。
- `AddAttachment($sAttachmentPath)`: 如果提供了附件路徑，則添加附件。

### 5. 發送郵件
- `Send()`: 發送郵件。

## 關鍵引用與依賴
- `PHPMailer/class.phpmailer.php`
- `PHPMailer/class.smtp.php`
- `_MAIL_ACC` (可能定義在 `include/config.php`)
- `_MAIL_PW` (可能定義在 `include/config.php`)

## 安全考量
- **敏感資訊**: `_MAIL_ACC` 和 `_MAIL_PW` 包含郵件帳號和密碼，這些是敏感資訊。在實際部署中，應確保 `config.php` 或其他配置檔案對這些資訊有嚴格的保護措施，例如不直接硬編碼在程式碼中，而是從環境變數或安全配置服務中讀取。
- **SSL/TLS 配置**: `allow_self_signed` 設置為 `true` 可能會導致安全風險，因為它允許接受自簽名證書，這在生產環境中通常不建議，除非有特殊原因。
- **XSS 防護**: 郵件內容 `$body` 來自外部輸入時，應確保其在傳遞給此函數之前已經過適當的 XSS 防護，以防止惡意 HTML 或腳本注入到郵件中。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解 `_MAIL_ACC` 和 `_MAIL_PW` 常量的具體定義位置和保護方式。
- 重新評估 `SMTPOptions` 中 `allow_self_signed` 的設置，並在生產環境中考慮更嚴格的配置。
