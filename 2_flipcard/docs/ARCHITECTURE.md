# ARCHITECTURE.md

## 目錄結構

```
0404_方位詞/
├── index2.html                         # 主要互動翻卡頁面（多方向翻轉）
├── flip-card-1.html                   # Hover 翻轉示範（3 個詞）
├── flip-card-2.html                   # 點擊翻轉 + 進度條（5 個詞）
├── flip-card-3.html                   # 3D 立方體旋轉（4 個詞）
├── 測試.html                          # 例句卡 + TTS 語音（50 句）
├── 測試2.html                         # 例句卡 + TTS + boundary 事件高亮
├── 特效成功但是尚未檢查.html           # 例句卡 + TTS + setTimeout 高亮（待驗證）
├── vocabulary.json                    # 核心詞彙資料（10 個方位詞，單一來源）
├── 方位詞中日.txt                     # 中日對照參考表 + 10 詞 × 5 句
├── 方位詞日.txt                       # 空白佔位檔（未使用）
├── N4_方位詞例句集_完整版.pptx        # 完整例句簡報
├── CLAUDE.md                          # AI 協作說明
└── docs/
    ├── README.md                      # 項目介紹與快速開始
    ├── ARCHITECTURE.md                # 本文件
    ├── DEVELOPMENT.md                 # 開發規範
    ├── FEATURES.md                    # 功能清單
    ├── TESTING.md                     # 測試指南
    ├── CHANGELOG.md                   # 更新日誌
    └── plans/                         # 開發計畫（進行中）
        └── archive/                   # 已完成計畫歸檔
```

---

## 啟動流程

### index2.html（唯一使用 Fetch 的頁面）

```
瀏覽器開啟 index2.html
  └─ loadData() 執行
       ├─ fetch('./vocabulary.json')
       │    ├─ 成功 → words = data.words（10 個詞）
       │    └─ 失敗（CORS / 離線）→ words = FALLBACK.words（內嵌相同資料）
       └─ render()
            ├─ 動態建立 DOM（進度列、卡片場景、導覽按鈕、點點指示器）
            ├─ buildDots()  ← 根據 words 陣列產生點點
            ├─ showCard(0) ← 顯示第一張
            └─ addEventListener('keydown', ...) ← 鍵盤支援
```


---

## 資料結構

### vocabulary.json（唯一真實來源）

```json
{
  "title": "方位詞單字集",
  "category": "位置・方向",
  "words": [
    {
      "id": 1,
      "japanese": "うえ",        // 平假名讀音（字卡正面大字顯示）
      "kanji": "上",             // 漢字（字卡正面副標）
      "romaji": "ue",            // 羅馬拼音（字卡正面小字）
      "chinese": "上面・上方",   // 中文釋義，以 ・ 分隔替代詞（字卡背面大字）
      "example_jp": "机の上に本があります。",  // 日文例句（字卡背面）
      "example_zh": "桌子上面有書。"           // 中文翻譯（字卡背面）
    }
    // ... 共 10 筆
  ]
}
```

**10 個詞彙清單**：うえ(上)、した(下)、みぎ(右)、ひだり(左)、まえ(前)、うしろ(後ろ)、なか(中)、そと(外)、となり(隣)、ちかく(近く)

### index2.html 內部狀態物件

```javascript
let words = [];               // vocabulary.json 載入後的詞彙陣列
let current = 0;              // 當前顯示的卡片索引（0-based）
let flipped = false;          // 卡片是否處於翻面狀態
let flipDirection = null;     // 當前翻轉方向："left" | "right" | "up" | "down" | null
let seen = new Set();         // 已翻開過的卡片索引集合（用於進度計算）
let highlightTimers = [];     // highlightExJp() 排程的 setTimeout ID 陣列，切換卡片時清除
```

### 測試系列頁面的例句資料結構

`測試.html`、`測試2.html`、`特效成功但是尚未檢查.html` 使用相同的資料格式：

```javascript
const data = [
  {
    w: "上",                         // 漢字詞（卡片左側大字）
    r: "うえ",                       // 平假名讀音（卡片左側小字）
    s: "棚（たな）の上に写真（しゃしん）が飾ってあります。",  // 例句，括號內為假名標注
    t: "架子上面擺著重要的照片。"    // 中文翻譯
  }
  // 10 個詞 × 5 句 = 50 筆
];
```

**`s` 欄位的括號語法**：正文漢字後緊接全形括號 `（よみかた）`。`speak()` 函式會用 `text.replace(/（.*?）/g, '')` 過濾括號及其內容，再送給 TTS 引擎。字符高亮邏輯也依此語法解析主字與假名的對應關係。

---

## CSS 3D Transform 機制

### 共用概念

所有翻卡頁面都依賴以下三個 CSS 屬性：

```css
.scene {
  perspective: 1000px;       /* 設定觀察者距離，值越小 3D 效果越誇張 */
}
.card {
  transform-style: preserve-3d;  /* 子元素保留 3D 空間，不攤平 */
  transition: transform 0.6s ease;
}
.face {
  backface-visibility: hidden;   /* 翻到背面時不顯示正面的鏡像 */
}
```

### index2.html 的四方向翻轉

卡片翻轉方向由 CSS class 控制：

| Class | CSS Transform | 鍵盤 | 說明 |
|-------|---------------|------|------|
| `flip-right`（預設） | `rotateY(180deg)` | `Space` | 向右翻，背面初始 `rotateY(180deg)` |
| `flip-left` | `rotateY(-180deg)` | — | 向左翻，背面改為 `rotateY(-180deg)` |
| `flip-down` | `rotateX(180deg)` | `↓` | 向下翻，背面改為 `rotateX(180deg)` |
| `flip-up` | `rotateX(-180deg)` | ~~`↑`~~ 已停用 | CSS class 保留，但鍵盤綁定已注解 |

**關鍵細節**：`.back` 的初始 transform 是 `rotateY(180deg)`（面朝背面）。當翻轉方向改為左/上/下時，`flipCard()` 函式會動態更換 `.back` 的對應 class，確保背面在翻轉後正確朝向觀察者。切換方向時使用 `requestAnimationFrame` 隔開 class 移除與新增，避免動畫跳幀。

### flip-card-3.html 的 3D 立方體

立方體由 4 個面組成（前/右/後/左），每面預先定位：

```css
.face-front  { transform: translateZ(130px); }          /* 正面推出 130px */
.face-right  { transform: rotateY( 90deg) translateZ(130px); }
.face-back   { transform: rotateY(180deg) translateZ(130px); }
.face-left   { transform: rotateY(-90deg) translateZ(130px); }
```

旋轉時只改變外層 `.cube` 的 Y 軸旋轉角度：

```javascript
const angles = [0, -90, -180, 90]; // step 0~3 對應的 rotateY 角度
document.getElementById('cube').style.transform = `rotateY(${angles[step]}deg)`;
```

自動播放使用 `setInterval(() => rotate(1), 2000)`（2 秒切換一面）。

---

## Web Speech API 整合

### index2.html 的實作

```javascript
function speak(text) {
  window.speechSynthesis.cancel();  // 停止當前播放，避免多個 utterance 堆疊
  const uttr = new SpeechSynthesisUtterance(text);
  uttr.lang = 'ja-JP';
  uttr.rate = parseFloat(document.getElementById('rateSlider').value);  // 0.5 ~ 1.5
  window.speechSynthesis.speak(uttr);
}
```

`example_jp` 欄位不含假名括號，因此不需要 regex 過濾即可直接送 TTS。

呼叫時機（在 `flipCard()` 情況三）：

```javascript
speak(dir === 'down' ? words[current].example_jp : words[current].japanese);
highlightExJp();
```

### 逐字高亮（index2.html `highlightExJp()`）

```javascript
function highlightExJp() {
  highlightTimers.forEach(t => clearTimeout(t));
  highlightTimers = [];
  const el = document.getElementById('exJp');
  const text = el.textContent;
  const rate = parseFloat(document.getElementById('rateSlider').value);
  const msPerChar = 200 / rate;
  el.innerHTML = text.split('').map((ch, i) => `<span id="hc-${i}">${ch}</span>`).join('');
  text.split('').forEach((_, i) => {
    const t = setTimeout(() => {
      const span = document.getElementById(`hc-${i}`);
      if (span) span.style.color = 'red';
    }, i * msPerChar);
    highlightTimers.push(t);
  });
}
```

### 基本用法（測試.html）

```javascript
function speak(text) {
  window.speechSynthesis.cancel();  // 停止當前播放，避免多個 utterance 堆疊
  const rate = document.getElementById('rate-slider').value;  // 0.5 ~ 2.0
  const cleanText = text.replace(/（.*?）/g, '');  // 移除假名標注
  const uttr = new SpeechSynthesisUtterance(cleanText);
  uttr.lang = 'ja-JP';
  uttr.rate = parseFloat(rate);
  window.speechSynthesis.speak(uttr);
}
```

### 字符高亮的實作

#### 方法：setTimeout 計時（特效成功但是尚未檢查.html）

```
原理：
  1. 遍歷原始句子每個字元
  2. 括號內的假名字元（inParen=true）放入 pendingIndices 暫存
  3. 遇到主字（非括號內）時，將 pendingIndices + 當前索引一起排程，批次高亮
  4. 高亮延遲時間：delay += msPerChar，其中 msPerChar = 200 / rate（rate=1 時每字 200ms）
  5. 每批高亮後安排 clearHighlight（在預估的總播放時間後）

狀態：尚未驗證，特別是長句子和極端語速（0.5x / 2.0x）的情況。
```

---

## 進度追蹤邏輯（index2.html）

```javascript
// 翻開一張卡片時
seen.add(current);         // 加入已看集合
updateProgress();

// 計算進度
const pct = (seen.size / words.length) * 100;
document.getElementById('progFill').style.width = pct + '%';
document.getElementById('progText').textContent = `${seen.size} / ${words.length} 已翻開`;
```

進度不會因切換卡片而重設，只會累加。切換到已翻過的卡片不會重複計入（Set 自動去重）。切換卡片時會重置翻轉狀態（`card.classList.remove('flip-*')`），但 `seen` 集合不清空。

---

## 配色主題一覽

| 頁面 | 主色調 | 背景 |
|------|--------|------|
| `index2.html` | 藍 `#4a90d9` + 暖橘背面 | 淺灰 `#f5f7fa` |

