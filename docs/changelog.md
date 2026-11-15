# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased] - 2026-11-15

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
