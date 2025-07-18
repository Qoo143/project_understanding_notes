# package-lock.json 理解筆記

## 檔案路徑
`C:\Users\User\Desktop\ntcu-nbnat-aial\package-lock.json`

## 概述
`package-lock.json` 檔案是 npm (Node Package Manager) 的**依賴鎖定文件**。它記錄了 `package.json` 中定義的所有依賴項在安裝時的確切版本、來源和雜湊值，以及它們的子依賴項。這個檔案的主要目的是確保在不同環境中安裝專案依賴時，能夠獲得完全一致的依賴樹，從而避免因依賴版本不一致導致的問題。

## 主要功能與職責

### 1. `name` 和 `lockfileVersion`
- `"name": "ntcu-nbnat-AIAL"`: 專案的名稱。
- `"lockfileVersion": 3`: 表示鎖定文件的版本為 3。不同版本的 npm 會生成不同格式的鎖定文件。

### 2. `dependencies` 區塊 (頂層依賴)
- 此區塊列出了專案直接依賴的 JavaScript 函式庫及其版本約束。這些是因材網前端功能的重要組成部分：
    - **`hammerjs`: `^2.0.8`**
        - **用途**: 一個用於處理觸摸手勢的 JavaScript 庫，可能用於實現移動設備上的互動或特殊手勢操作。
    - **`mathjax`: `^2.7.7`**
        - **用途**: 一個用於在網頁上顯示數學公式的 JavaScript 庫。這表明因材網可能包含需要渲染數學內容的功能。
    - **`showdown`: `^1.9.1`**
        - **用途**: 一個用於將 Markdown 轉換為 HTML 的 JavaScript 庫。這可能用於處理用戶輸入的 Markdown 內容或顯示 Markdown 格式的文檔。
    - **`vue`: `^3.3.4`**
        - **用途**: Vue.js 是一個流行的前端 JavaScript 框架，用於構建用戶介面。這表明因材網的前端使用了 Vue.js 3。
    - **`vue-multiselect`: `^3.0.0-beta.2`**
        - **用途**: 一個用於 Vue.js 的多選下拉選單組件。可能用於篩選、選擇多個選項等。
    - **`vue-router`: `^4.2.4`**
        - **用途**: Vue.js 的官方路由庫，用於構建單頁應用程式 (SPA) 中的導航。這表明因材網的前端使用了 Vue Router 進行頁面路由管理。
    - **`vuedraggable`: `^4.1.0`**
        - **用途**: 一個基於 Sortable.js 的 Vue.js 組件，用於實現拖曳排序功能。可能用於列表排序、任務管理等。

### 3. `packages` 區塊 (詳細依賴樹)
- 這個區塊包含了所有直接和間接依賴項的詳細信息，包括它們的版本、解析的 URL、完整性雜湊值 (`integrity`)、以及它們自己的依賴項 (`dependencies`)。
- 例如，`node_modules/@vue/compiler-core`、`node_modules/@vue/compiler-dom`、`node_modules/@vue/compiler-sfc` 等都屬於 Vue.js 生態系統的內部依賴。
- 其他依賴項如 `postcss` (CSS 處理)、`source-map-js` (源碼映射)、`yargs` (命令行參數解析) 等，表明了前端構建和開發工具鏈的複雜性。

## 關鍵作用
- **確保可重現的構建**: 無論何時何地運行 `npm install`，只要 `package-lock.json` 存在，都會安裝完全相同的依賴樹。
- **版本控制**: 鎖定依賴項的確切版本，防止因新版本引入的兼容性問題。
- **性能優化**: 加速 `npm install` 的過程，因為 npm 可以直接從鎖定文件中獲取依賴信息，而無需重新解析依賴樹。

## 閱讀狀態
已閱讀

## 待辦事項
- 了解這些前端函式庫在因材網專案中的具體應用場景，特別是 `hammerjs`、`mathjax`、`showdown`、`vue-multiselect` 和 `vuedraggable`。
- 了解 `package.json` (如果存在) 與 `package-lock.json` 之間的關係。
