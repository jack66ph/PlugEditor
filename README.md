# PlugEditor

輕量、獨立單檔（`editor.html`）的可嵌入式富文字編輯器元件，專為透過 `<iframe>` 嵌入外部網站或應用程式，並透過 `window.postMessage` 進行雙向通訊而設計。

## 特色

- **零建置單檔架構**：所有樣式、SVG 圖示與邏輯皆封裝於 [`editor.html`](editor.html)，無需編譯或打包即可部署。
- **Word 相容富文字匯出**：自動將平面清單轉換為巢狀 `<ul>` / `<ol>`，支援純文字、語意化 HTML（相容 Word 貼上）與 Markdown 匯出。
- **進階階層清單與格式刷**：支援 6 層符號/數字清單、Word 操作體驗的格式刷（單擊套用一次、雙擊鎖定連續套用）。
- **多語系與符號工具列**：內建中文標點、英文標點、數學/特殊符號與繁體中文注音符號列。
- **三種外觀主題**：支援白天（`light`）、黑夜（`dark`）、護眼（`eyecare`）模式。
- **使用者自訂記憶**：支援工具列按鈕勾選與拖曳排序，自動儲存於瀏覽器 `localStorage`。

---

## 快速開始（嵌入其他 App）

透過 GitHub Pages 部署後，即可在任何網頁或 App 中使用 `<iframe>` 直接嵌入 `editor.html`：

```html
<iframe
  id="plug-editor"
  src="https://<你的帳號>.github.io/<Repo名稱>/editor.html"
  style="width: 100%; min-height: 360px; border: 1px solid #cbd5e1; border-radius: 8px;"
></iframe>

<script>
  const iframe = document.getElementById('plug-editor');
  const EDITOR_ORIGIN = 'https://<你的帳號>.github.io';

  window.addEventListener('message', (event) => {
    if (event.origin !== EDITOR_ORIGIN) return;
    const data = event.data;
    if (!data || typeof data.type !== 'string') return;

    switch (data.type) {
      case 'editor:ready':
        // 編輯器就緒，可寫入初始內容
        iframe.contentWindow.postMessage(
          { type: 'editor:setContent', format: 'html', content: '<p>Hello PlugEditor!</p>' },
          EDITOR_ORIGIN
        );
        break;

      case 'editor:resize':
        // 自動同步編輯器高度
        iframe.style.height = `${data.height}px`;
        break;

      case 'editor:change':
        console.log('HTML:', data.html);
        console.log('Text:', data.text);
        console.log('Markdown:', data.markdown);
        break;
    }
  });
</script>
```

> **提示**：若主網站有設定 `Content-Security-Policy`（CSP），請將 `https://<你的帳號>.github.io` 加入 `frame-src` 白名單。

---

## `window.postMessage` 通訊介面

### 父層網站 → 編輯器

| `type` | 參數 | 說明 |
| :--- | :--- | :--- |
| `editor:setContent` | `format`: `'html' \| 'text'`（預設 `'html'`）<br>`content`: `string` | 設定編輯器內容（靜默更新，不觸發 `editor:change`） |
| `editor:getContent` | `requestId`?: `string` | 主動請求目前內容，編輯器將回傳 `editor:content` |
| `editor:setTheme` | `theme`: `'light' \| 'dark' \| 'eyecare'` | 切換編輯器外觀主題並儲存設定 |
| `editor:setConfig` | `readOnly`?: `boolean`<br>`placeholder`?: `string` | 動態設定唯讀狀態或提示文字 |
| `editor:focus` | — | 將游標聚焦至編輯器 |

### 編輯器 → 父層網站

| `type` | 回傳欄位 | 說明 |
| :--- | :--- | :--- |
| `editor:ready` | — | 編輯器初始化完成時觸發 |
| `editor:resize` | `height`: `number` | 編輯器外框高度改變時觸發（可用於自適應 `iframe` 高度） |
| `editor:change` | `html`: `string`<br>`text`: `string`<br>`markdown`: `string` | 使用者編輯內容時觸發（內建 150ms debounce） |
| `editor:content` | `requestId`: `string \| null`<br>`html`, `text`, `markdown`: `string` | 回應 `editor:getContent` 請求 |

---

## 本地開發與示範

- 直接在瀏覽器開啟 [`editor.html`](editor.html) 即可單獨使用編輯器。
- 透過本地 HTTP 伺服器開啟 [`index.html`](index.html)（例如 `npx serve .` 或 VS Code Live Server）可測試完整的 `iframe` 雙向通訊示範。

---

## 授權條款

本專案採用 [MIT License](LICENSE) 授權。  
核心編輯器基於 [Quill 2](https://github.com/slab/quill)（BSD-3-Clause License）。
