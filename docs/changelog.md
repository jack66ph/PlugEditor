# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased] - 2026-11-24

### Changed
- **固定工具列與獨立捲動文字編輯區（`editor.html`）**：
  - 單獨開啟 `editor.html`（`window.self === window.top`）時自動套用 `standalone-layout` 類別，將視窗與 `.editor-shell` 鎖定為 `100%` / `100dvh` 高度，改由 `.editor-area-wrap` 獨立垂直捲動，使工具列、搜尋與取代列及手機底部快捷列固定於畫面不隨內容移動。
  - 為 `.toolbars-container` 加上 `position: sticky; top: 0; z-index: 20; flex-shrink: 0;`，同時保留 `<iframe>` 嵌入模式下的 `editor:resize` 自動高度回報機制。
  - 新增 `.editor-scroll-content` 包裹層並調整 `renderFindHighlights` 以 `findHighlightLayer` 計算相對座標，確保文字區捲動時搜尋高亮覆蓋層仍正確隨文字捲動對齊。

### Added
- **搜尋與取代功能（`editor.html`）**：
  - 於基本排版工具列新增 `搜尋與取代`（`findReplace`）按鈕，並支援於設定頁面自訂顯示與排序。
  - 新增 `#findReplaceBar` 面板與鍵盤快捷鍵：`Ctrl+F` / `Cmd+F` 開啟搜尋（自動帶入單行選取文字）、`Ctrl+H` / `Cmd+H` 開啟並聚焦取代欄、`Enter` / `Shift+Enter` 跳至下／上一筆、`Esc` 關閉面板。
  - 支援即時搜尋、符合筆數計數（`1 / N`、`無結果`）、區分大小寫切換（`Aa`）、單筆取代與全部取代（保留原有行內格式，且全部取代採單一 Quill `Delta` 可一次 `Ctrl+Z` 復原）。
  - 新增 `#findHighlightLayer` 非侵入式高亮覆蓋層，不改動文件 Delta、Undo 歷史與匯出結果，並完整支援白天／黑夜／護眼主題與手機版面（`data-layout="mobile"`）。

## [Unreleased] - 2026-11-15

### Added
- **專屬手機版面設計（`data-layout="mobile"`）**：
  - 頂部改為分頁標籤式工具列（`.mobile-toolbar-header`），支援膠囊分頁切換各工具列與收合／展開（`▾` / `▴`）。
  - 基本排版列於手機版改為單列橫向滑動（`36px` 觸控按鈕），符號與注音列改為可捲動格線鍵盤（`mobile-symbol-grid`）。
  - 新增底部拇指快捷列（`.mobile-bottom-bar`），提供復原、重做、縮排與純文字／富文字／Markdown 一鍵複製。
  - 顏色選擇器改為底部置中浮動視窗，設定彈窗改為底部抽屜（Bottom Sheet）樣式，編輯區字級設為 `16px` 防止 iOS Safari 自動縮放。
  - 支援「自動偵測（`<= 640px`）」、「桌面版面」、「手機版面」切換（設定頁、`?layout=` 參數與 `editor:setConfig` 的 `layoutMode`）。
- **示範頁手機寬度模擬（`index.html`）**：新增「模擬手機寬度 (375px)」切換按鈕。
- **全工具列按鈕自訂與分頁設定**：
  - 設定頁面新增分頁（Tabs）架構：「一般設定」、「基本排版」、「中文標點」、「英文標點」、「其他符號」、「注音符號」。
  - 除基本排版外，中文標點、英文標點、其他符號與注音符號工具列亦支援個別按鈕的勾選顯示與拖曳／上下鍵排序，並保留 `localStorage` 設定相容性。
  - 各工具列分頁新增「全選」、「全不選」、「還原預設」快捷按鈕，以及工具列隱藏時的「立即啟用」提示列。
- **GitHub 發布與外部嵌入支援文件**：
  - 新增 LICENSE（MIT 授權，附 Quill 2 BSD-3-Clause 條款宣告）。
  - 新增 README.md，提供 GitHub Pages 嵌入範例與 window.postMessage 雙向通訊 API 規格文件。
  - 新增 index.html，作為 GitHub Pages 預設首頁與 iframe 通訊即時示範頁面。

### Fixed
- **清單重影**：Quill 2 內建 `.ql-ui` 清單標號與自訂 `li::before` 標號同時顯示，現已隱藏內建標號。
- **複製富文字貼到 Word 格式遺失**：
  - 清單：改為輸出巢狀 `<ul>`/`<ol>`，Word 可辨識為原生清單；數字清單依層級採 `1.` → `a.` → `i.`。
  - 粗體/斜體/底線/刪除線：改輸出 `<b>`/`<i>`/`<u>`/`<s>`。
  - 顏色/字級：顏色轉 hex（並加 `<font color>` 備援），字級 px 轉 pt。
  - 剪貼簿寫入改用 `copy` 事件 + `clipboardData.setData()`，避免 Chromium `ClipboardItem` 清洗樣式；保留 `ClipboardItem` 作為備援。

### Changed
- **手機版工具列精簡與彈窗互動優化**：
  - 頂部分頁標籤改為單字呈現：`基`（基本排版）、`標`（合併中文／英文標點，預設中文）、`他`（其他符號）、`注`（注音符號）。
  - 所有手機版工具列統一為單列排版且間隙縮小為 `1px`，取消左右滑動，改以列尾 `...` 按鈕開啟彈出視窗選取溢出工具。
  - 手機版基本工具列將字型與字體大小合併為 `T` 按鈕（點擊後彈窗同時調整），純文字／富文字按鈕簡化為 `txt`／`rtx`。
  - 修正設定視窗按鈕排序：補齊觸控拖曳（`.drag-handle`）、修正 `↑` / `↓` 按鈕事件與 `.sort-arrows .mini-btn` 樣式範圍。
- **全畫面設定頁面與響應式格線**：將設定彈窗改為全畫面顯示（四周保留 `5px` 邊距），並將 `.sortable-grid` 改為 `auto-fill` 多欄響應式排列以善用畫面空間。
- `getRichHtmlContent()` 改為輸出語意化、自帶行內樣式的 HTML（亦用於 `editor:change` / `editor:getContent`）。

### Notes
- Word「合併格式設定」貼上會移除顏色與字型，需使用「保留原始格式設定」。

## 2026-11-15（初版）

### Added
- **初始建立獨立單檔編輯器 (`editor.html`)**：
  - 整合 Quill 2 (BSD-3-Clause) 富文字編輯核心，採 CDN 引用以利單檔發布與共用。
  - 基本排版工具列：粗體、斜體、底線、字型、字體大小、文字顏色選擇器、清除格式。
  - 自訂 6 層符號清單（`⦁ ◦ ▪ ▫ ▴ ▵`）與 6 層階層編號清單（`1.` 至 `1.1.1.1.1.1.`）。
  - 縮排控制：以兩個半型空白（`2ch`）為單位的多層級縮排。
  - Word 式複製格式（單擊套用一次、雙擊鎖定連續套用）。
  - 複製純文字（空格縮排與符號轉換）、富文字 (HTML) 與 Markdown 功能。
  - 擴充符號工具列：中文標點符號、英文標點符號、其他符號、注音符號，並提供中英標點切換鍵。
  - 支援白天、黑夜、護眼模式三種色彩主題。
  - 支援工具列項目自訂勾選、拖曳與上/下排序，並具備一鍵復原與 `localStorage` 記憶功能。
  - 完整 `window.postMessage` 雙向通訊介面，支援 `iframe` 自適應高度同步 (`editor:resize`) 與內容變更廣播 (`editor:change`)。
- **授權標示合規處理**：
  - 在 `editor.html` 頂部註解加入 Quill 2 的完整 BSD-3-Clause 授權聲明。
  - 在設定介面底部明確標記使用 Quill 2 及其版權宣告。
- **專案管理文件**：
  - 建立 `PROJECT_STATE.md`。
  - 建立 `docs/changelog.md`。
  - 建立 `docs/errorsfixed.md`。
