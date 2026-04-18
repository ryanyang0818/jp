# DEVELOPMENT.md

## 開發環境

本專案為純靜態網頁，無需安裝任何工具。建議搭配以下工具：

- **編輯器**：VS Code（可安裝 Live Server 擴充套件，解決 CORS 問題）
- **瀏覽器**：Chrome 或 Edge（Web Speech API 支援最完整）
- **本地伺服器**：VS Code Live Server 或 `python -m http.server 8080`

---

## 命名規則

### 檔案命名

| 情境 | 規則 | 範例 |
|------|------|------|
| 正式功能頁面 | 使用英文小寫 + 連字號 | `flip-card-1.html`、`index2.html` |

### JavaScript 命名

| 類型 | 規則 | 範例 |
|------|------|------|
| 函式 | camelCase | `flipCard()`, `renderCards()`, `updateProgress()` |
| 變數 | camelCase | `current`, `flipDirection`, `msPerChar` |
| 常數（固定值） | UPPER_SNAKE_CASE | `FALLBACK` |
| DOM 元素 ID | camelCase | `progFill`, `jpKana`, `curJp` |
| CSS class | kebab-case | `flip-right`, `card-left`, `btn-speak` |

### CSS 命名

使用語意化命名，描述用途而非樣式：

```css
/* 好 */
.card-left { ... }      /* 卡片左側區域 */
.btn-speak { ... }      /* 語音播放按鈕 */
.progress-fill { ... }  /* 進度條填充 */

/* 避免 */
.red-text { ... }       /* 描述外觀，不描述用途 */
.big { ... }            /* 過於籠統 */
```

---

## 新增詞彙

新增詞彙需同步修改**兩個地方**：

### 步驟 1：更新 vocabulary.json

```json
{
  "words": [
    // 現有 10 筆...
    {
      "id": 11,
      "japanese": "あいだ",
      "kanji": "間",
      "romaji": "aida",
      "chinese": "之間・中間",
      "example_jp": "駅と図書館の間に公園があります。",
      "example_zh": "車站和圖書館之間有公園。"
    }
  ]
}
```


---


### 例句格式

```javascript
{ w: "漢字詞", r: "よみかた", s: "例句（括號假名）。", t: "中文翻譯。" }
```

**`s` 欄位的假名標注語法**：在每個複雜漢字後以全形括號 `（）` 標注讀音。

```
正確：棚（たな）の上（うえ）に写真（しゃしん）があります。
錯誤：棚の上に写真があります。（無假名，TTS 不受影響，但高亮功能無法正確對應字元）
```

`speak()` 函式會自動去掉括號及內容再送 TTS，因此原始句子保留假名標注不影響語音發音。

---

## 新增 HTML 頁面

新增頁面時，建議遵循以下慣例：

1. **配色主題**：選擇與現有 7 個頁面不同的配色，或在 `:root` 定義 CSS Variables：
   ```css
   :root {
     --primary: #your-color;
     --bg: #your-bg;
   }
   ```

2. **資料來源**：
   - 若需要全部 10 個詞彙，可使用 Fetch 載入 `vocabulary.json`（參考 `index2.html` 的 `loadData()` 模式）
   - 若只需少數詞彙，直接內嵌 `const data = [...]`

3. **語音功能**：複製 `測試.html` 的 `speak()` 函式，確保包含：
   - `window.speechSynthesis.cancel()` 先停止前次播放
   - `text.replace(/（.*?）/g, '')` 過濾假名標注
   - `uttr.lang = 'ja-JP'`

4. **RWD**：加入至少針對 600px 以下的媒體查詢

---

## 環境變數

本專案為純靜態網頁，**無任何環境變數**。所有設定均硬碼於各 HTML 檔案中：

| 設定項 | 位置 | 預設值 | 說明 |
|--------|------|--------|------|
| TTS 語言 | 各 `speak()` 函式 | `ja-JP` | 語音合成的語言代碼 |
| TTS 語速範圍（index2.html） | `#rateSlider` 元素 | min=0.5, max=1.5, step=0.2, value=1.0 | index2.html Header 語速滑桿 |
| TTS 語速範圍（測試系列） | `rate-slider` 元素 | min=0.5, max=2.0, step=0.1, value=1.0 | 測試.html / 測試2.html 語速滑桿 |
| 自動播放間隔 | `flip-card-3.html` | 2000ms | 立方體自動旋轉間隔 |
| 逐字高亮基準時間 | `index2.html` `highlightExJp()` | `200 / rate` ms/char | setTimeout 計時基準 |
| 字符高亮基準時間 | `特效成功版` `speak()` | `200 / rate` ms/char | setTimeout 計時基準 |
| 進度動畫時間 | `index2.html` CSS | `transition: width 0.4s ease` | 進度條動畫 |
| 立方體翻轉動畫 | `flip-card-3.html` CSS | `transition: transform 0.8s cubic-bezier(...)` | 立方體旋轉動畫 |
| 翻卡動畫時間 | `index2.html` CSS | `transition: transform 0.2s ease` | 卡片翻轉動畫（使用者已改為 0.2s） |

---

## 計畫歸檔流程

### 1. 建立計畫檔案

檔名格式：`YYYY-MM-DD-<feature-name>.md`，放在 `docs/plans/`

```
docs/plans/2026-04-18-character-highlight.md
```

### 2. 計畫文件結構

```markdown
# 2026-04-18 字符高亮驗證與修正

## User Story
作為學習者，我希望語音播放時能即時看到被唸到的字元高亮，
以便跟上發音節奏。

## Spec
- 支援所有 50 句例句（10 詞 × 5 句）
- 在語速 0.5x ~ 2.0x 均正確對應
- 括號內假名字元與前一主字同步高亮

## Tasks
- [ ] 驗證現有 `特效成功但是尚未檢查.html` 的高亮時機準確度
- [ ] 對比 `測試2.html` 的 boundary 事件實作
- [ ] 修正時機偏差（若發現問題）
- [ ] 更新 docs/FEATURES.md 與 docs/CHANGELOG.md
```

### 3. 完成後

1. 將計畫檔移至 `docs/plans/archive/`
2. 更新 `docs/FEATURES.md` 對應功能的狀態
3. 在 `docs/CHANGELOG.md` 新增版本記錄

---

## JSDoc 格式

本專案函式較簡短，一般不需要 JSDoc。若函式邏輯較複雜（如字符高亮計時），可加入單行說明：

```javascript
// msPerChar：rate=1 時每個主字平均發音時間；依語速縮放
const msPerChar = 200 / rate;
```

避免多行說明塊，保持程式碼簡潔。
