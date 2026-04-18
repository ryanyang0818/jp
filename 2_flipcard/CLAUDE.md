# CLAUDE.md

## 專案概述

N4 方位詞學習工具 — 純靜態 HTML5 / CSS3 / Vanilla JS，無建構流程，直接以瀏覽器開啟使用。提供 7 種不同視覺風格的互動學習介面，涵蓋翻卡、3D 旋轉、TTS 語音等學習模式。

## 常用指令

| 動作 | 方式 |
|------|------|
| 啟動主程式 | 瀏覽器開啟 `index2.html` |
| 查看全部學習模式 | 依序開啟各 HTML 檔案（見下表） |
| 更新核心詞彙 | 編輯 `vocabulary.json`，同步更新 `index2.html` 內的 `FALLBACK` 常數 |

## 各頁面快速對照

| 檔案 | 學習模式 | 特色 |
|------|----------|------|
| `index2.html` | 主要翻卡（多方向） | 進度追蹤、鍵盤控制、Fetch JSON |

## 關鍵規則

1. **無建構流程**：所有 HTML 直接可用，不需 npm install / build；無 node_modules
2. **詞彙雙來源**：`vocabulary.json` 是主要來源；`index2.html` 的 `FALLBACK` 常數是備份，兩者必須保持同步
3. **TTS 假名語法**：例句中用全形括號標記假名，格式為 `漢字（よみかた）`；`speak()` 函式會以 `text.replace(/（.*?）/g, '')` 過濾括號及內容後再送 TTS
4. **字符高亮未驗證**：`特效成功但是尚未檢查.html` 的 setTimeout 計時高亮功能尚未完整驗證，使用前需測試各種句子長度與語速組合
5. **功能開發流程**：計畫檔放 `docs/plans/`（格式 `YYYY-MM-DD-<feature>.md`），功能完成後移至 `docs/plans/archive/`，並更新 `docs/FEATURES.md` 和 `docs/CHANGELOG.md`

## 詳細文件

- [docs/README.md](./docs/README.md) — 項目介紹與快速開始
- [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) — 架構、目錄結構、資料流、CSS 3D 機制
- [docs/DEVELOPMENT.md](./docs/DEVELOPMENT.md) — 開發規範、命名規則、新增詞彙步驟
- [docs/FEATURES.md](./docs/FEATURES.md) — 功能列表與行為說明
- [docs/TESTING.md](./docs/TESTING.md) — 手動測試清單與驗證指南
- [docs/CHANGELOG.md](./docs/CHANGELOG.md) — 更新日誌
