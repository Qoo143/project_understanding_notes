# Auth/Auth.php (兼容性檔案) 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PEAR\Auth\Auth.php`

## 概述
這個檔案是 PEAR Auth 庫的一個兼容性檔案，其主要作用是為了向後兼容舊的引入路徑。它本身不包含實際的身份驗證邏輯，而是簡單地 `require_once` 了同目錄下的 `Auth.php`。根據檔案註解，它在 PEAR Auth 庫的 1.2.0 版本後已被棄用。

## 主要功能
- 提供舊的引入路徑兼容性。
- 引入實際的 `Auth.php` 核心檔案。

## 關鍵引用
- `require_once 'Auth.php';` (指向 `C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PEAR\Auth.php`)

## 閱讀狀態
已閱讀

## 待辦事項
- 繼續閱讀 `C:\Users\User\Desktop\ntcu-nbnat-aial\classes\PEAR\Auth.php`，這應該是實際的 PEAR Auth 庫核心檔案。
