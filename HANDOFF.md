# 西區交易室・交接說明

最後更新：2026-11-08。這份文件讓接手的人（或另一個 Claude 對話）不用翻舊對話就能維護西區交易室，或照它開下一間辦公室。

## 一句話

一個 three.js 等角 3D 辦公室，Q 版貓咪員工每個交易日由排程任務自動上網整理台股資料、寫成 JSON 報告 push 進這個 repo；網頁（GitHub Pages）讀這些檔案顯示，不用登入。

## 入口

| 東西 | 位置 |
| --- | --- |
| 公開網址（主要） | https://nnilbig.github.io/alphaoffice/ （需在 repo Settings → Pages 開啟：main / root） |
| Claude 頁面（私人，可「問牠」） | https://claude.ai/artifact/C5ZaF97ePJZDzY2LNDJaVD |
| 開新辦公室的公版（5 項填寫） | https://claude.ai/artifact/Bfaf744GqZbCycGhmSgVoY |
| 舊版 2D 交易室（已停用，只剩搬家公告） | https://claude.ai/artifact/C4Q7xr2X8onCWDCjTRaEVN |

## 檔案

| 路徑 | 內容 |
| --- | --- |
| `index.html` | 整個網頁（單檔，three.js 0.160.0 從 jsDelivr 載入） |
| `icon.png` | 方塊風灰貓 Earl 圖示（logo、分頁、手機主畫面） |
| `reports/index.json` | 所有報告清單：`{updated, reports:[{agent,date,time,closeTime?}]}` |
| `reports/YYYY-MM-DD/<agent>.json` | 單份報告 |
| `prompts/*.md` | 5 個排程任務的完整指令與 ID |
| `README.md` | 對外說明 |

`index.html` 內的區塊（搜尋註解 `/* ---------- xxx ---------- */`）：

| 區塊 | 要改什麼時看這裡 |
| --- | --- |
| `crew data` | `CREW`：每隻貓的英文名、職位、說明、毛色、眼睛、上衣、領帶、褲子、名單顏色 |
| `floor` / `outer walls` / `interior walls` | 大理石地板、地毯、落地窗、玻璃隔間、實牆 |
| `furniture` | `desk(x,z,scheme,agent,flip)` 6 席對拼桌、`SCHEMES` 螢幕配色、伺服器機櫃、會議桌、主管室、茶水間 |
| `cats` | `makeCat()` 方塊貓模型、`placeCat()` 位置與動作（work / tea / meet / read） |
| `panel` | 右側名單 |
| `reports` | `SCHED` 交稿時間、報告三種版型、主管交稿清單、資料來源切換（Claude 內讀資料庫，其他地方讀 `reports/`） |
| `tools` / `loop` | 像素感切換（預設高清）、動畫 |

## 成員

| agent key | 名字 | 職位 | 交稿 | 螢幕 |
| --- | --- | --- | --- | --- |
| `brown` | Mocha 棕貓 | 國際事件與數據 | 07:30 | 黃白 |
| `black` | Onyx 黑貓 | 產業數據（坐對面那排） | 07:45 | 紅黑 |
| `orange` | Tango 橘貓 | 盤勢數據 | 08:00，14:00 收盤補充 | 藍黃 |
| `white` | Snow 白貓 ×4 | 會議室：今日重點觀察 | 08:20 | 會議室電視 |
| `tabby` | Miso 斑紋貓 | 茶水間：情緒價值・交易修心 | 08:20（與白貓同一輪） | — |
| — | Earl 灰貓 | 主管室看書；點牠看交稿狀態 | 不交稿 | — |

還沒交稿的貓螢幕是暗的；名單顯示「今日 HH:MM 已交」「今日預計 HH:MM」或「週末休市」。

## 報告格式

```json
{
 "agent": "brown",
 "date": "2026-11-09",
 "time": "07:32",
 "closeTime": "14:05",
 "summary": "一到兩句最重要的結論",
 "cols": ["項目", "數值", "變化"],
 "kpis": [{"label": "", "value": "", "chg": "", "dir": "up|down|flat"}],
 "days": {"prev": [], "today": [], "next": []},
 "obs": [{"text": "", "from": "Tango・盤勢"}],
 "quote": "",
 "tips": [],
 "sources": [{"t": "媒體｜標題", "u": "https://"}]
}
```

- 情報員用 `summary cols kpis days sources`；白貓用 `summary obs sources`；Miso 用 `quote tips`。
- `dir`：`up` 紅、`down` 綠（台股慣例）。
- `closeTime` 只有 Tango 的 14:00 補充會寫。

## 排程任務

週一至週五（Asia/Taipei），休市日由任務自己判斷跳過。每個任務：clone 這個 repo → 寫 `reports/<date>/<agent>.json` → 更新 `reports/index.json` → commit 並 push 到 `main`；再順手寫一份到 Claude 頁面的資料庫（失敗可忽略）。完整指令在 `prompts/`。

| 時間 | 任務名稱 | ID |
| --- | --- | --- |
| 07:30 | 西區交易室｜Mocha 國際 | `trig_01DWZkRWLbsrbURm5taTQtDo` |
| 07:45 | 西區交易室｜Onyx 產業 | `trig_01Q8BDU5d3dQZfoDw8qYQgma` |
| 08:00 | 西區交易室｜Tango 盤前 | `trig_01DSq6JWRnRokEYA7C1uUCpX` |
| 08:20 | 西區交易室｜白貓會議＋Miso | `trig_014PAZxbZo4ygScB7dFTruQt` |
| 14:00 | 西區交易室｜Tango 收盤補充 | `trig_01Dw5euFeLTyCFPaCBMWkUoZ` |

改指令時，用 `update_trigger` 改 prompt，並同步更新 `prompts/` 對應檔案。

## 改網頁的流程

1. 改 `index.html`（Claude 頁面版本的原始檔是同一份內容，只少了 `<!doctype>…<head>` 外殼）。
2. Claude 頁面：以同一檔案重新發布到 `C5ZaF97ePJZDzY2LNDJaVD`，並附上 `icon.png`。
3. Repo：包上完整 HTML 外殼後 commit / push 到 `main`。
4. 不要手動改 `reports/`，那是排程寫的；修正某天報告時，同時改該檔與 `index.json` 的時間。

## 已知限制

- GitHub Pages 只能由 repo 擁有者在 Settings 開啟；這邊的 GitHub API 不允許代開。
- repo 是公開的，報告內容任何人看得到。
- GitHub Pages 有快取，新報告約 1–10 分鐘後才出現。
- 「問牠」對話只在 Claude 頁面裡能用。
- 早上排程多時容易碰到使用量上限（11/7 早上幾個舊排程就因此失敗）；需要時可把 3 位情報員合併成一輪。

## 開下一間辦公室

照公版文件填 5 項（名稱與主題、3 位情報員各看什麼、茶水間貓提供什麼、主管貓在做什麼、交稿時間），然後：

1. 開新 repo，複製 `index.html`、`icon.png`，建空的 `reports/index.json`（`{"updated":"","reports":[]}`）。
2. 改 `CREW`、`SCHED`、`SCHEMES` 與 `desk(...)` 的配色、HUD 標題、`<title>`。
3. 複製 `prompts/` 五份，換掉 repo 名稱、頁面網址、職位段與欄位段，建立新的排程任務。
4. 擁有者在新 repo 開 GitHub Pages。
