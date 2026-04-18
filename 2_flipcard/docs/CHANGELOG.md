# CHANGELOG.md

版本格式遵循 [Semantic Versioning](https://semver.org/)。

---

## [0.1.0] — 2026-04-18

### 初始版本

建立 JLPT N4 方位詞學習工具，包含 7 個互動學習頁面與完整文件體系。

#### 新增頁面

- **index.html** — 多方向翻卡主介面
  - 從 `vocabulary.json` 載入 10 個方位詞（含 CORS Fallback 機制）
  - 支援左/右/上/下四方向 CSS 3D 翻轉
  - 鍵盤控制（← → Space ↑ ↓）
  - 進度追蹤（`seen` Set + 進度條 + 百分比）
  - 點點指示器（active / seen / unseen 三狀態）

- **flip-card-1.html** — Hover 翻轉示範
  - 3 個詞：うえ / した / みぎ
  - CSS `:hover` 觸發翻轉，無需點擊

- **flip-card-2.html** — 點擊翻轉 + 進度條
  - 5 個詞，霓虹配色
  - 點擊翻轉 + 頁面頂部進度條

- **flip-card-3.html** — 3D 立方體旋轉
  - 4 個面（なか / そと / よこ / ちかく）
  - 星空動畫背景（80 個隨機閃爍星點）
  - 自動播放（2 秒/面）
  - 鍵盤 ← → 控制

- **測試.html** — 例句卡 + TTS 語音
  - 10 詞 × 5 句 = 50 筆例句
  - Web Speech API TTS（lang: ja-JP）
  - 語速控制滑桿（0.5x ~ 2.0x）
  - Shuffle 隨機排序

- **測試2.html** — 例句卡 + TTS + boundary 事件字符高亮
  - 同測試.html 功能，加上即時字符高亮
  - 使用 `SpeechSynthesisUtterance.onboundary` 事件
  - 假名括號字元與主字同步高亮

- **特效成功但是尚未檢查.html** — 例句卡 + TTS + setTimeout 計時高亮
  - 同測試.html 功能，使用 setTimeout 替代 boundary 事件
  - 每主字 200ms 計時基準（隨語速縮放）
  - ⚠️ 尚未完整驗證

#### 新增資料

- **vocabulary.json** — 核心詞彙資料
  - 10 個 JLPT N4 方位詞，每詞含：平假名、漢字、羅馬拼音、中文釋義、日文例句、中文翻譯

#### 新增參考資料

- **方位詞中日.txt** — 中日對照參考表（10 詞 × 5 句）
- **N4_方位詞例句集_完整版.pptx** — 完整例句簡報

#### 新增文件

- **CLAUDE.md** — AI 協作說明
- **docs/README.md** — 項目介紹與快速開始
- **docs/ARCHITECTURE.md** — 架構、目錄結構、CSS 3D 機制、字符高亮實作比較
- **docs/DEVELOPMENT.md** — 開發規範、命名規則、新增詞彙步驟、計畫歸檔流程
- **docs/FEATURES.md** — 功能清單與詳細行為說明
- **docs/TESTING.md** — 手動測試清單、瀏覽器相容性指南
- **docs/CHANGELOG.md** — 本文件

---

## [0.2.0] — 2026-04-18

### 新增

- **index2.html** — 重寫主要翻卡學習頁面
  - 從 `vocabulary.json` 載入 10 個方位詞（含 CORS Fallback 機制）
  - 支援左/右/下三方向 CSS 3D 翻轉（↑ 保留 CSS，鍵盤停用）
  - 鍵盤控制：← → Space ↓（↑ 已停用，另作他用）
  - 進度追蹤（`seen` Set + 進度條 + 點點指示器三狀態）
  - Header 語速滑桿（`#rateSlider`）：範圍 0.5x~1.5x，步進 0.2，預設 1.0x

- **翻牌自動 TTS**（`speak()` 函式）
  - 右翻/左翻 → 朗讀 `japanese` 欄位（平假名讀音）
  - 下翻 → 朗讀 `example_jp` 欄位（日文例句）
  - 切換卡片時自動停止前次語音

- **`highlightExJp()` 逐字高亮**
  - 翻牌後將 `#exJp` 例句文字逐字變紅（由左至右）
  - 間隔公式：`200 / rate` ms/字，與語速滑桿連動
  - 新增 `highlightTimers` 狀態變數：追蹤計時器 ID，切換卡片時全部清除

### 變更

- `ArrowUp` 鍵盤翻牌停用（`flipCardUp()` 函式與 `flip-up` CSS class 保留，鍵盤綁定已注解）
- 所有 JavaScript 函式上方新增繁體中文說明備註

### 更新文件

- **docs/FEATURES.md** — 新增功能 15（翻牌 TTS）、16（語速滑桿）、17（字符高亮）
- **docs/ARCHITECTURE.md** — 新增 `highlightTimers` 狀態變數、`speak()` 與 `highlightExJp()` 實作說明
- **docs/TESTING.md** — 新增 index2.html 的 TTS、語速滑桿、逐字高亮測試項目
- **docs/DEVELOPMENT.md** — 更新環境變數表（語速滑桿規格、高亮計時基準、翻卡動畫時間）

---

## 版本規劃

> 未來版本待計畫，計畫文件置於 `docs/plans/`。

| 預期功能 | 說明 |
|----------|------|
| 驗證 `特效成功但是尚未檢查.html` | 確認 setTimeout 字符高亮在各語速/句長的準確度，修正後重命名為正式頁面 |
| 擴充詞彙 | 新增 `間（あいだ）`、`よこ（横）` 等詞至 vocabulary.json |
| 行動端優化 | 改善觸控翻卡手勢（Swipe → 翻卡） |
