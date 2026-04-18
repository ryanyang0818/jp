# N4 方位詞學習工具

JLPT N4 級別方位詞（位置・方向語彙）互動學習應用。提供 7 種不同視覺風格與互動模式的靜態網頁，無需安裝任何套件，直接用瀏覽器開啟即可使用。

---

## 技術棧

| 層次 | 技術 |
|------|------|
| 標記 | HTML5（語意化標籤） |
| 樣式 | CSS3（Grid、3D Transform、CSS Variables、keyframes 動畫、媒體查詢） |
| 邏輯 | Vanilla JavaScript（無框架、無打包工具） |
| 資料 | JSON（`vocabulary.json`） |
| 語音 | Web Speech API — `SpeechSynthesisUtterance`（lang: `ja-JP`） |
| 資料載入 | Fetch API（僅 `index2.html`） |

**無外部依賴**：不使用 jQuery、React、Vue、Bootstrap、Tailwind 等任何第三方函式庫。

---

## 快速開始

```bash
# 1. 下載或複製專案資料夾
# 2. 直接用瀏覽器開啟任意 HTML 檔案
# Windows
start index2.html

# macOS
open index2.html

# Linux
xdg-open index2.html
```

> **注意**：若使用 `file://` 協定開啟 `index2.html`，部分瀏覽器的 CORS 安全策略可能阻擋 `vocabulary.json` 的 Fetch 請求，此時 `index2.html` 會自動切換為內嵌的 `FALLBACK` 資料，功能不受影響。建議使用本地靜態伺服器（如 VS Code Live Server、Python `http.server`）以避免此問題。

```bash
# 使用 Python 啟動本地伺服器（Python 3）
python -m http.server 8080
# 然後開啟 http://localhost:8080
```

---

## 學習頁面一覽

| 檔案 | 標題 | 學習模式 | 詞彙數 | 特色功能 |
|------|------|----------|--------|----------|
| `index2.html` | 方位詞學習卡片 | 多方向翻卡 | 10（from JSON） | 鍵盤控制、進度追蹤、點點導覽 |
| `flip-card-1.html` | 翻卡特效 #1 | Hover 翻轉 | 3 | 漸層背景、閃光效果 |
| `flip-card-2.html` | 翻卡特效 #2 | 點擊翻轉 | 5 | 進度條、霓虹配色 |
| `flip-card-3.html` | 3D 立方旋轉單字卡 | 3D 立方體 | 4 | 星空背景、自動播放 |
| `測試.html` | 日文方位詞學習卡 Pro | 例句卡 + TTS | 10 詞 × 5 句 | 語速控制、Shuffle |
| `測試2.html` | 日文方位詞學習工具 Pro | 例句卡 + TTS + 高亮 | 10 詞 × 5 句 | boundary 事件字符高亮 |
| `特效成功但是尚未檢查.html` | （同上） | 例句卡 + TTS + 高亮 | 10 詞 × 5 句 | setTimeout 計時高亮（待驗證） |

---

## 常用指令

| 動作 | 方式 |
|------|------|
| 啟動主要翻卡介面 | 瀏覽器開啟 `index2.html` |
| 切換到下一張卡片 | `→` 鍵 或 點擊「下一張」按鈕 |
| 切換到上一張卡片 | `←` 鍵 或 點擊「上一張」按鈕 |
| 翻轉卡片（向右） | 點擊卡片 或 按 `Space` |
| 翻轉卡片（向下） | 按 `↓` 鍵 |
| 翻轉卡片（向上） | 按 `↑` 鍵 |
| 播放例句語音 | 點擊 🔊 按鈕（測試系列頁面） |
| 調整語速 | 拖曳語速滑桿（0.5x ~ 2.0x） |
| 打亂例句順序 | 點擊「打亂順序」按鈕 |
| 旋轉 3D 立方體 | `←` / `→` 鍵 或 點擊 ◀ ▶▶ 按鈕 |

---

---

## 文件索引

| 文件 | 說明 |
|------|------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | 目錄結構、資料結構、CSS 3D 機制、字符高亮實作比較 |
| [DEVELOPMENT.md](./DEVELOPMENT.md) | 新增詞彙步驟、命名規則、計畫歸檔流程 |
| [FEATURES.md](./FEATURES.md) | 每個頁面的功能行為詳細說明 |
| [TESTING.md](./TESTING.md) | 手動測試清單、瀏覽器相容性、TTS 驗證方法 |
| [CHANGELOG.md](./CHANGELOG.md) | 版本更新日誌 |

---

## 參考資料

| 檔案 | 內容 |
|------|------|
| `方位詞中日.txt` | 中日對照參考表 + 每詞 5 句例句 |
| `N4_方位詞例句集_完整版.pptx` | 完整例句簡報（PowerPoint 格式） |
| `方位詞日.txt` | 空白佔位檔（尚未使用） |
