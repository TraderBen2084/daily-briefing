# Quantimes · AI 交接說明

> 俾任何 AI（Claude、ChatGPT、Gemini、Grok…）讀嘅總說明。讀完呢份 + `STATUS.md` 就識跟進，唔使 Ben 由頭解釋。
> 網站：https://traderben2084.github.io/daily-briefing/
> Repo：`TraderBen2084/daily-briefing`（公開，唔好放任何 token / 密碼）

## 1. 係咩
Ben（獨立交易員，多倫多，美東時間）嘅每日市場報告網站。全部自動產生，喺一部 Mac mini 上跑，發佈去 GitHub Pages。
交易風格：短線 1–5 日，回調入場為主、突破為次（1D / 4H / 15M）。

## 2. 頁面

| 頁 | 網址 | 產生方式 | 主要數據 |
|---|---|---|---|
| 總頁 | `/` | `scripts/build_portal.py` 自動砌 | 讀各頁最新數據 |
| 美股簡報 | `/us/` | Cowork 18:15（Deepvue + WebSearch），只寫美股；順手寫 Crypto 新聞 | `briefing.txt`、`modules/web-news/crypto/` |
| 恒指收市 | `/hsi/` | `hsi-analysis/main.py`（10:00 ET）；港股新聞由 Cowork 04:07 寫 | `hsi-analysis/data/日期.json`、`modules/web-news/hk/` |
| 黃金 XAUUSD | `/gold/` | `gold-analysis/main.py` | `site/gold/history/日期.json` |
| Crypto | `/crypto/` | `crypto-analysis/main.py` | `site/crypto/state/日期.json` |
| AI Cycle | `/ai-cycle/` | `ai_cycle/main.py` + Cowork 讀 Deepvue | `ai_cycle/deepvue_data.json` |
| X 新聞 | `/news/` | Grok 讀 X → `modules/x-news/inbox/日期.md`（測試中） | markdown |
| 總括評論 | `/summary/` | Cowork 20:15 綜合各頁 → `scripts/build_summary.py` | `modules/summary/日期.md` |
| 歷史存檔 | `/archive/` | 自動 | `site/us/archive/`、`site/ai-cycle/日期.html` |

各模組詳細設計：`hsi-analysis/README.md`、`gold-analysis/HANDOFF.md`、`crypto-analysis/HANDOFF.md`（喺 Ben 部 Mac，唔喺 repo）。

### Earnings Gap 候選（美股頁入面嘅區塊）
- `scripts/earnings_gap.py`（17:30 ET 一至五）：TradingView 篩選器股票池（美國普通股、市值 > $2B、唔要 REIT/SPAC、業績喺 2026-09-15 後）+ yfinance 日線 → 業績反應日 gap up ≥ 5% → 旗形四組（已突破 / 近旗頂 / 旗形下半 / 已破位）→ `modules/earnings-gap/latest.json`
- Gap 原因：美股 Cowork 排程用 WebSearch 寫入 `modules/earnings-gap/reasons.json`（key = `代號|gap日期`，查過唔再查）
- `scripts/build_earnings_gap.py`：發佈時插入 `site/us/index.html`（`<!-- EG-START -->` 至 `<!-- EG-END -->`）

## 3. 本機 folder（`~/Documents/dailyreport/`）
```
site/              ← GitHub repo 本身（GitHub Pages 直接 serve）
  assets/theme.css ← 全站共用風格（由 shared/ 抄過嚟）
  STATUS.md        ← 每次發佈自動更新嘅進度表
scripts/           ← publish.sh、build_portal.py、send_telegram.py
shared/theme.css   ← 風格嘅「正本」，改呢個
modules/           ← 新模組：x-news、summary
hsi-analysis/ gold-analysis/ crypto-analysis/ ai_cycle/   ← 各分析模組
logs/              ← 所有 log
_to_delete/        ← 待 Ben 自己刪嘅舊檔
```

## 4. 發佈時間
固定兩次（美東）：**10:15**（恒指出完）同 **20:30**（其餘全部出完）。
`scripts/publish.sh`：整理美股檔案 → 砌總頁 → `git add/commit/push`。各模組自己**唔好** push。

## 5. 規矩（改 code 前一定要睇）
- Mac 嘅 `python3` 係 **3.9**：唔准用 3.10+ 語法，每個 `.py` 頂加 `from __future__ import annotations`。
- **綠升紅跌**，全站一致（包括港股）。
- 風格 = 「D · Heatmap」：黑底、熱力格，顏色深淺代表訊號強度。新頁一律 link `assets/theme.css`，唔好自己再定一套顏色。
- 唔用手動輸入：拎唔到數就標「略過」，唔准用舊數頂替。
- 唔用付費 API（Anthropic API 等）；研究部分用 Claude Cowork / Claude Code 訂閱。
- Ben 唔熟開發術語，解釋要用淺白廣東話。

## 6. 進行中
見 `STATUS.md` 同下面清單：
- [x] 執 folder、共用風格、總頁、固定時間發佈
- [x] 各頁換成 D 風格 + 頂部導航（恒指、黃金、Crypto、AI Cycle 改程式；美股跟 `DESIGN-quantimes.md`）
- [x] X 新聞頁（`scripts/build_news.py`，讀 Grok 輸出）
- [x] 總括評論頁（`scripts/build_summary.py`；Cowork 20:15 跟 `modules/summary/PROMPT.md` 寫 markdown）
- [x] HSI 圖表改用 Vela 互動 K 線（試行，載入失敗退返 PNG）
- [ ] 美股 Cowork prompt 改用 `DESIGN-quantimes.md`（等 Ben 改）
