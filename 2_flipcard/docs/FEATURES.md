# FEATURES.md

## 功能完成狀態

| # | 功能 | 頁面 | 狀態 |
|---|------|------|------|
| 1 | 多方向翻卡（左/右/上/下） | `index2.html` | ✅ 完成 |
| 2 | 鍵盤控制翻卡與導覽 | `index2.html` | ✅ 完成 |
| 3 | 學習進度追蹤 | `index2.html` | ✅ 完成 |
| 4 | 點點指示器導覽 | `index2.html` | ✅ 完成 |
| 5 | JSON 資料載入 + Fallback | `index2.html` | ✅ 完成 |
| 6 | Hover 觸發翻卡 | `flip-card-1.html` | ✅ 完成 |
| 7 | 點擊翻卡 + 進度條 | `flip-card-2.html` | ✅ 完成 |
| 8 | 3D 立方體旋轉 | `flip-card-3.html` | ✅ 完成 |
| 9 | 自動播放（立方體） | `flip-card-3.html` | ✅ 完成 |
| 10 | TTS 語音播放 | `測試.html` | ✅ 完成 |
| 11 | 語速控制（0.5x ~ 2.0x） | `測試.html` | ✅ 完成 |
| 12 | 例句隨機排序（Shuffle） | `測試.html` | ✅ 完成 |
| 13 | 字符高亮（boundary 事件） | `測試2.html` | ✅ 完成 |
| 14 | 字符高亮（setTimeout 計時） | `特效成功但是尚未檢查.html` | ⚠️ 待驗證 |
| 15 | 翻牌自動 TTS | `index2.html` | ✅ 完成 |
| 16 | 語速滑桿（Header） | `index2.html` | ✅ 完成 |
| 17 | 逐字高亮（`#exJp`） | `index2.html` | ✅ 完成 |

---

## 功能詳細說明

---

### 功能 1-5：多方向翻卡（index2.html）

**概述**：主要學習介面，從 `vocabulary.json` 載入 10 個方位詞，以 3D 翻轉卡片形式呈現。

#### 卡片翻轉行為

- **正面**顯示：平假名（大字 4.2rem）、漢字（副標 1.5rem）、羅馬拼音（小字）、「點擊或按空白鍵翻轉」提示
- **背面**顯示：中文意思（大字 1.9rem）、例句框（日文例句 + 分隔線 + 中文翻譯）
- 點擊卡片 → 向右翻轉（`rotateY(180deg)`）
- 再次點擊同方向 → 翻回正面
- 點擊不同方向（鍵盤）→ 先移除舊 class，下一幀套新 class（使用 `requestAnimationFrame`）

#### 鍵盤對應表

| 按鍵 | 動作 |
|------|------|
| `←` | 上一張卡片 |
| `→` | 下一張卡片 |
| `Space` | 向右翻轉 / 翻回 |
| `↓` | 向下翻轉（`rotateX(180deg)`） |
| `↑` | ~~向上翻轉~~ **已停用**（保留另作他用） |

使用 `document.addEventListener('keydown', ...)` 監聽，呼叫 `e.preventDefault()` 防止捲動頁面。`ArrowUp` 的事件處理已注解（`flipCardUp()` 函式仍存在但未綁定鍵盤）。

#### 進度追蹤

- 進度以 `seen`（Set）記錄已翻開的卡片索引
- 只有從正面**首次**翻到背面時才計入（`seen.add(current)` 在 `flipCard()` 內，翻回不觸發）
- 進度條寬度：`(seen.size / words.length) × 100%`，以 CSS `transition: width 0.4s ease` 動畫
- 文字顯示：`X / 10 已翻開`

#### 點點指示器

- 每個詞對應一個點（36×36px 圓角方形），顯示平假名 + 序號
- 三種狀態：
  - 預設：灰色邊框，文字 `#a0aec0`
  - **已翻（seen）**：綠色邊框 `#68d391`，淺綠背景，文字深綠
  - **當前（active）**：藍色填充 `#4a90d9`，白色文字，有陰影
- 點擊點點可直接跳至該卡片（`goTo(idx)`）

#### Fallback 機制

`index2.html` 的 `loadData()` 以 try/catch 包住 Fetch：

```
fetch('./vocabulary.json') 成功 → 使用 JSON 資料
fetch('./vocabulary.json') 失敗（任何原因）→ 使用 FALLBACK 常數（內嵌相同 10 筆資料）
```

---

### 功能 6：Hover 翻轉（flip-card-1.html）

**概述**：3 個詞的 Hover 示範，移入滑鼠即翻轉，無需點擊。

- 詞彙：うえ（上）、した（下）、みぎ（右）
- 觸發：CSS `:hover` 偽類，`card:hover { transform: rotateY(180deg); }`
- 背面各有不同漸層：藍褐（上）/ 綠褐（下）/ 紫褐（右）
- 正面有閃光（shine）效果：CSS `::after` 偽元素，半透明白色漸層
- 每張卡片有 emoji 裝飾（⬆️ ⬇️ ➡️）
- **無導覽、無進度追蹤**，純示範用途

---

### 功能 7：點擊翻轉 + 進度條（flip-card-2.html）

**概述**：5 個詞，點擊翻轉，頁面頂部有進度條，霓虹配色。

- 詞彙：5 個（上、下、右、左、前）
- 觸發：`onclick` 切換 `.flipped` class
- 進度條：顯示「已翻 X / 5」，以已翻過的卡片數計算
- 底部點點：當前卡片指示
- 有上/下一張導覽按鈕
- 霓虹配色：深背景 + 霓虹綠 `#00ff88` / 霓虹青 邊框發光

---

### 功能 8-9：3D 立方體旋轉（flip-card-3.html）

**概述**：4 個詞排列在立方體的四個面，旋轉切換詞彙。

#### 立方體結構

- **4 個面**（硬碼於 HTML，非從 JSON 載入）：
  - Front：なか（中）裡面・中間
  - Right：そと（外）外面・外部
  - Back：よこ（横）旁邊・側面（**注意**：vocabulary.json 中無此詞）
  - Left：ちかく（近く）附近・近處

- **旋轉角度映射**：`step` 0~3 對應 `rotateY` 角度 `[0, -90, -180, 90]`

#### 操作方式

| 方式 | 動作 |
|------|------|
| 點擊 ◀ 按鈕 | `rotate(-1)` 向前旋轉 |
| 點擊 ▶▶ 按鈕 | `rotate(1)` 向後旋轉 |
| 點擊 ▶ 按鈕 | 切換自動播放（2 秒/面） |
| `←` / `→` 鍵 | 同上，鍵盤控制 |
| 點擊底部小點 | 直接跳至對應面（`goTo(idx)`） |

#### 自動播放

```javascript
// 開啟
autoTimer = setInterval(() => rotate(1), 2000);
// 關閉
clearInterval(autoTimer); autoTimer = null;
```

按鈕文字在 ▶ / ⏸ 之間切換。

#### 星空背景

JavaScript 動態產生 80 個 `<div class="star">` 元素，每個有隨機大小（0.5~3px）、位置、閃爍動畫持續時間（2~6s）與延遲。

---

### 功能 10-12：例句卡 + TTS（測試.html）

**概述**：以卡片 Grid 呈現 50 句例句，每句可點擊播放日語 TTS。

#### 卡片佈局

```
┌──────────┬─────────────────────────────────────────────┐
│    上    │  棚（たな）の上に写真が飾ってあります。 🔊  │
│   うえ   │  架子上面擺著重要的照片。                   │
└──────────┴─────────────────────────────────────────────┘
```

- 左側：漢字詞（2.8rem 粗體）+ 平假名讀音
- 右側：例句文字 + 🔊 播放按鈕 + 中文翻譯（淺藍左邊框）

#### TTS 播放行為

1. `speechSynthesis.cancel()` 先停止當前播放
2. 讀取滑桿語速值
3. 用 regex 去掉假名括號：`cleanText = text.replace(/（.*?）/g, '')`
4. 建立 `SpeechSynthesisUtterance`，設 `lang='ja-JP'`，`rate=parseFloat(rate)`
5. 呼叫 `speechSynthesis.speak(uttr)`

#### 語速控制

- `<input type="range" min="0.5" max="2.0" step="0.1" value="1.0">`
- 拖曳時即時更新顯示文字（`speed-display`）
- 下次點擊 🔊 時才套用新語速（不會中途更改當前播放）

#### Shuffle 功能

```javascript
function shuffleCards() {
  const shuffled = [...data].sort(() => Math.random() - 0.5);
  renderCards(shuffled);
}
```

使用 Fisher-Yates 近似法（`Math.random() - 0.5`），重新 `renderCards()` 整個容器。

#### Grid 佈局

```css
.card-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 25px;
}
```

寬螢幕自動兩欄，窄螢幕（<600px）強制單欄。

---

### 功能 13：字符高亮 boundary 事件（測試2.html）

**概述**：在 `測試.html` 基礎上，語音播放時高亮當前被唸到的字元（紅色 + 粗體）。

#### 實作機制

**渲染階段（renderCards）**：

1. `sentenceToSpans(sentence)` 遍歷原始句子每個字元，計算其在 `cleanText` 中的索引位置（`pos`）：
   - 括號 `（`、`）` 及括號內的假名 → `pos = -1`（不對應 cleanText）
   - 其餘字元（漢字、平假名、標點）→ `pos = cleanIdx++`（對應 cleanText 索引）
2. 每個字元包成 `<span data-pos="N">字元</span>`

**播放階段（speak）**：

1. 建立 `SpeechSynthesisUtterance(cleanText)`（已去掉假名括號）
2. 綁定 `uttr.onboundary(e)` 事件：
   - `e.name === 'word'` 或 `'sentence'` 才處理
   - 找出 `data-pos` 在 `[e.charIndex, e.charIndex + e.charLength)` 範圍內的 `span`
   - 設定 `style.color = '#FF0000'`，`style.fontWeight = 'bold'`（累積，不清除前一詞）
3. `uttr.onend`：還原所有 span 顏色
4. `uttr.onerror`：同上（確保播放失敗時也清除高亮）

**已知限制**：
- `onboundary` 事件的 `charIndex` / `charLength` 在不同瀏覽器/OS 的 TTS 引擎行為不一致
- Chrome on Windows 通常觸發 `word` 邊界，Safari on macOS 行為不同
- 某些環境完全不觸發 boundary 事件

---

### 功能 14：字符高亮 setTimeout 計時（特效成功但是尚未檢查.html）

**概述**：不依賴 boundary 事件，改用計時器估算每個字元的發音時機。

#### 實作機制

```
msPerChar = 200 / rate   // rate=1 時每個主字 200ms；語速越快間隔越短
```

遍歷原始句子：
- `（` → `inParen = true`，加入 `pendingIndices`
- `）` → `inParen = false`，加入 `pendingIndices`  
- 括號內字元 → 加入 `pendingIndices`（積累，等主字一起亮）
- 主字（非括號內）→ 批次排程：`pendingIndices + [i]` 一起在 `delay` ms 後高亮

```javascript
const batch = [...pendingIndices, i];
pendingIndices = [];
const d = delay;
timers.push(setTimeout(() => {
  batch.forEach(idx => {
    const span = document.getElementById(`${elId}-${idx}`);
    if (span) { span.style.color = 'red'; span.style.fontWeight = 'bold'; }
  });
}, d));
delay += msPerChar;
```

**待驗證項目**：
1. 長句子（>20 個主字）的累積時差
2. 極端語速（0.5x, 2.0x）的對齊準確度
3. 連續播放多句時的計時器清理（`clearHighlight()`）
4. TTS 實際開始時間的起始延遲（目前用 100ms 常數）

**狀態**：⚠️ 已實作但尚未系統性驗證，使用前請參考 TESTING.md。

---

### 功能 15：翻牌自動 TTS（index2.html）

**概述**：卡片首次翻開時，依翻轉方向自動以日語朗讀對應欄位的文字，無需手動點擊播放按鈕。

#### 觸發條件

只在 `flipCard()` 的**情況三**（卡片在正面、首次翻開）觸發，翻回正面或切換翻轉方向時不重複朗讀。

#### 朗讀內容對照

| 翻牌方向 | 朗讀內容 | JSON 欄位 | 範例 |
|----------|----------|-----------|------|
| 右翻（點擊 / Space） | 平假名讀音 | `japanese` | `うえ` |
| 左翻 | 平假名讀音 | `japanese` | `うえ` |
| 下翻（↓） | 日文例句 | `example_jp` | `机の上に本があります。` |

#### 實作

```javascript
speak(dir === 'down' ? words[current].example_jp : words[current].japanese);
```

`speak()` 函式取 `#rateSlider` 的值作為 `uttr.rate`，語速由功能 16 的滑桿控制。

---

### 功能 16：語速滑桿（index2.html Header）

**概述**：Header 區域內嵌語速控制滑桿，控制 index2.html 所有 TTS 的播放速度。

#### 規格

| 屬性 | 值 |
|------|----|
| 元素 ID | `rateSlider` |
| 最小值 | 0.5x |
| 最大值 | 1.5x |
| 步進 | 0.2 |
| 預設值 | 1.0x |
| 顯示元素 ID | `rateVal` |

拖曳時透過 `oninput` 即時更新 `#rateVal` 的文字顯示（格式：`1.0x`）。語速在**下次翻牌觸發 `speak()` 時**才套用，不影響進行中的播放。

---

### 功能 17：逐字高亮（index2.html `#exJp`）

**概述**：翻牌後將 `#exJp`（日文例句）的每個字元依序變為紅色，與 TTS 朗讀速度同步。

#### 實作機制

```
msPerChar = 200 / rate    // rate=1 時每字 200ms；語速越快間隔越短
```

1. 清除 `highlightTimers` 陣列中的舊計時器
2. 將 `#exJp` 的 `textContent` 拆成個別字元，各包成 `<span id="hc-{i}">`
3. 對每個 span 排程 `setTimeout`，依序將 `style.color` 設為 `'red'`
4. 計時器 ID 存入 `highlightTimers`，供下次切換卡片時清除

#### 清理時機

`showCard()` 開頭執行 `highlightTimers.forEach(t => clearTimeout(t))`，確保切換卡片時舊的高亮序列立即終止，不干擾新卡片內容。

#### 與功能 14（特效版）的差異

| 項目 | index2.html `highlightExJp()` | 特效版 |
|------|-------------------------------|--------|
| 對象文字 | `example_jp`（無假名括號） | 含括號假名的例句 |
| 逐字方式 | 無括號，直接逐字 | 批次：主字 + 前面積累的假名括號 |
| 驗證狀態 | ✅ 已完成 | ⚠️ 待驗證 |
