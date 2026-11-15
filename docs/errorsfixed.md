# Errors & Fixes Log

此文件記錄開發過程中所遭遇的錯誤、潛在法律/授權問題以及相應的解決方案。

## 2026-11-15

### 1. 開源授權合規注意事項 (Quill 2 BSD-3-Clause)
- **問題 / 潛在風險**：
  使用者計畫將此單檔編輯器公開發佈至 GitHub 作為開源或共用元件。Quill 2 採用 BSD-3-Clause 授權，其要求不得移除其版權宣告，且不得擅自以其原創者/組織名義為衍生產品背書。原本僅在 HTML 頂端放簡略註解，未附完整授權與版權聲明。
- **修正方式**：
  1. 在 `editor.html` 頂部 HTML 註解加入完整的 BSD-3-Clause 條款與官方版權聲明（包含 Slab、Jason Chen、salesforce.com）。
  2. 在設定視窗（Settings Modal）底部加上低調的技術標示：「核心編輯器採用 Quill 2 (BSD-3-Clause 授權)」，兼顧合規與乾淨的使用者介面。

### 2. 編輯器內清單標號重影
- **問題**：清單項目出現兩組重疊的標號（如 `1.` 疊在 `1.1.` 上）。
- **原因**：Quill 2 會在每個 `<li>` 內插入 `<span class="ql-ui">`，由 `quill.snow.css` 以 `.ql-ui:before` 畫出內建標號；專案又以 `li::before` 畫自訂標號，兩者同時渲染。
- **修正**：加入 `.ql-editor li > .ql-ui { display: none !important; }`，只保留自訂標號。

### 3. 複製富文字貼到 Word：清單錯誤、格式遺失
- **問題**：貼到 Word 後清單變成連續 1~9 編號或純文字，顏色、粗體、縮排消失。
- **原因**：
  1. 自訂清單標號用 CSS `::before` 產生，Word 不解析偽元素。
  2. Quill 2 的清單 DOM 為平面結構（階層只靠 `ql-indent-N` class），Word 無法判斷層級。
  3. 以 `navigator.clipboard.write(ClipboardItem)` 寫入 HTML 時，Chromium 會清洗內容，行內樣式易遺失。
- **第一次修正（失敗）**：把清單轉成「段落 + 文字標號」。結果 Word 不再視為清單，且格式仍因剪貼簿清洗而遺失；同時影響了 `editor:change` 輸出的 HTML。
- **最終修正**：
  1. 自行序列化 DOM：重建巢狀 `<ul>`/`<ol>`；強調格式改用 `<b>`/`<i>`/`<u>`/`<s>`；顏色 `rgb()` 轉 hex 並加 `<font color>`；字級 px 轉 pt。
  2. 剪貼簿改用 `copy` 事件 + `clipboardData.setData('text/html', …)` 寫入原始 HTML，`ClipboardItem` 僅作備援。

### 4. Word 巢狀數字清單每層都顯示 `1.`
- **問題**：第二層之後顯示為 `1.`，而非階層編號。
- **原因**：巢狀 `<ol>` 未指定類型時，Word 每層皆使用十進位數字。
- **修正**：依層級設定 `type="1" / "a" / "i"`（同時加 `list-style-type`），對應 Word 預設多層清單樣式。若要 `1.1.1.` 格式需改用 Word 專用 `mso-list`，列為後續選項。

### 5. 貼到 Word 後顏色仍未顯示（環境設定）
- **問題**：粗體/斜體/底線正常，但顏色消失。
- **原因**：Word 貼上選項為「合併格式設定」時，只保留強調格式，會移除顏色與字型。
- **處理**：貼上後選「保留原始格式設定」，或在「檔案 → 選項 → 進階 → 剪下、複製和貼上 →『從其他程式貼上』」改為「保留原始格式設定」。

### 6. 開發工具指令錯誤（非產品程式碼）
- **問題**：在 PowerShell 中以 `node -e "..."` 執行含跳脫雙引號的腳本時出現 `ParserError`；Python 輸出含 `⦁` 等字元時出現 `UnicodeEncodeError (cp950)`。
- **修正**：node 改用單引號包住腳本；Python 改用 `sys.stdout.buffer.write(...encode('utf-8'))` 輸出。

### 7. 版面模式卡片與外觀主題卡片 `.theme-card` 選取器衝突
- **問題**：新增「版面模式」卡片（`#layoutOptions`）並沿用 `.theme-card` 樣式時，`applyTheme()` 原先以 `document.querySelectorAll('.theme-card')` 更新 `active` 狀態，會誤將 `#layoutOptions` 內卡片的 `active` 樣式清除。
- **修正**：將 `applyTheme()` 的選取器限縮為 `#themeOptions .theme-card`，並由 `applyLayout()` 獨立維護 `#layoutOptions .theme-card` 的 `active` 狀態。

---

*本次開發過程無其他語法報錯或執行期錯誤。*
