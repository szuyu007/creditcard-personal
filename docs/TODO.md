# 待辦 & 續接清單

> 這份文件記錄未完成的工作,讓下次開啟新對話時可以快速續接。
> 用法:在新對話開頭貼給 Claude Code,並說「我想繼續做 X」即可。
>
> 最後更新:2026-04-12

---

## 🎯 最優先:手機實測後的細節調整

現在版本(commit `2cdf838`)已部署到 GitHub Pages。需要先用手機走一次完整流程,記錄下列問題:

- [ ] `chip-cashback` 綠色按鈕在手機上**夠大點嗎**?目前 `min-height: 32px`,Apple HIG 建議 44pt。若太小需調整
- [ ] iOS Safari `window.confirm()` 的 OK/Cancel 長相是否符合預期
- [ ] 紅色錯誤 toast(`.error-toast`)會不會被 iOS 網址列/虛擬鍵盤遮住
- [ ] 點 chip 的 `:active { transform: scale(0.97) }` 動畫在手機上是否自然
- [ ] `result-details` 裡 chip 比 tag 大造成的高度不一致,視覺上能接受嗎
- [ ] 金額輸入後手機鍵盤會不會擋住結果列表

---

## 📋 CUBE 權益資料仍待補

### 資料層面(需要使用者補拍截圖/查找)
- [ ] **慶生月適用期間** — 若國泰官網有寫「生日當月 1 日 ~ 月底」以外的規則則需記錄
- [ ] **日本賞、集精選的補充條款** — 目前只有方案卡片截圖,沒有「查看適用商家優惠」詳細頁
  - 例如日本賞的「新光三越 3%」是所有分店都算?還是限日本消費?
  - 集精選「7-ELEVEN 實體門市 2%」有沒有排除項目?
  - 「指定充電站」到底是哪些充電站?

### 結構層面
- [ ] `data/cube-benefits.json` 的 `_meta.notes` 裡記錄了幾項待補項,查看該檔最新狀態

---

## 🏢 集團 aliases 擴充

目前只做了 4 個餐飲集團的 aliases:
- 王品集團、饗賓集團、瓦城集團、漢來美食

**可以擴充的方向:**

- [ ] 其他餐飲集團(例如:乾杯、莆田、爭鮮、三商等)
- [ ] **「指定百貨購物中心」** 條目([index.html:794](../index.html#L794))已有 keywords 但沒 aliases,可以把具體百貨名稱(Global Mall、夢時代、統一時代、宏匯、三創...) 從 keywords 搬到 aliases,讓「搜子店名 → 跳到具體店名頁」更一致
- [ ] **蝦皮 vs 蝦皮購物、momo vs momo 購物網** 可能需要 aliases 統一

**注意:** 不建議把所有卡的權益都做成 JSON。
CUBE 卡做成 JSON 是因為它有結構化的「方案 → 商家清單」,Richart/街口/Pi 的結構不一樣(有的是分類,有的是活動期間制),強行 JSON 化會變成維護災難。擴充 `recommendations` 的 `aliases`/`keywords` 比較實在。

---

## 💰 額度追蹤擴充

目前只追蹤 4 個項目:

```
quotaConfig:
  - unicard-elite      Unicard 百大加碼 (1000 點/月)
  - ubear-digital      U Bear 數位訂閱 (100 元/期)
  - ubear-online       U Bear 網購加碼 (150 元/期)
  - jkopay-select      街口聯名卡 精選通路 (10000 街口幣/月)
```

「加入額度」按鈕點在其他卡(CUBE、Richart、Pi 等)會跳紅色 toast 「尚未追蹤額度」。如果你想擴充:

- [ ] **CUBE 各方案的月額度** — 目前國泰 CUBE 卡的每個方案有沒有月上限?(似乎無,或很高)。需要使用者查國泰 App 確認
- [ ] **Richart 各方案的月額度** — Pay 著刷、好饗刷、大筆刷、數趣刷、天天刷...各有什麼上限?
- [ ] **Pi 拍錢包卡** — 有很多限時活動,活動型的上限(例如「媽咪女神節 每月 300 P幣」)要怎麼進 quotaConfig?
- [ ] 擴充後需更新 `matchQuotaForCard()` 函式([index.html](../index.html) 中找 `matchQuotaForCard`)的 match 規則

**擴充流程範本:**
1. 在 `quotaConfig` 陣列新增一筆 `{ id, card, name, limit, unit, resetDay, note }`
2. 在 `matchQuotaForCard()` 加一條 `if (card === "X" && note.includes("Y")) return "new-id"`
3. 按鈕會自動在對應的推薦卡出現

---

## 🎨 UI / UX 迭代清單

### 確認對話框(OK/Cancel)
目前用 `window.confirm()` 原生對話框。iOS Safari 上 OK 是 primary(藍字),已符合 UX 標準。
- [ ] **如果要完全自訂樣式**(控制按鈕顏色、動畫、字體),需要寫自訂 modal
  - 工程量約 20 分鐘
  - 要處理:背景遮罩、ESC 關閉、點背景關閉、焦點管理、動畫進退
  - **不急,保留原生 confirm 也很 OK**

### 紅色錯誤 toast
- [ ] 手機上位置會不會被遮住(等手機實測結論)
- [ ] 3 秒自動消失,可能太短,可考慮加「點擊關閉」或延長至 4~5 秒

### chip-cashback 按鈕
- [ ] 若手機實測覺得太小,調 `min-height: 32px` → `44px`
- [ ] `chip-cashback` 跟其他 tag 並列造成高度不一致,可考慮把它獨立一行放在 tag 區下方

---

## 🔐 安全性(之前討論過的,未做的方案)

### SHEETS_SECRET 的根本解法
當時換了新 secret(`j7sn0BdhDPvVWYAdVQTx-6Css_B9IBU2`)但這還是公開在前端。
- [ ] **方案 C 的徹底做法**:把 Apps Script 驗證改成「檢查 Referer 是不是來自 `szuyu007.github.io`」,把前端 secret 整個拿掉
- [ ] 或在 Apps Script 加速率限制(每個 IP 每分鐘最多 N 次)

**不急**,目前用長亂數 secret 已經擋掉 99% 的人。

---

## 🐛 已知小問題 / 技術債

- [ ] `findCubeBenefitsForRecEntry()` 函式目前是**死碼**,寫了但沒被呼叫。可以刪,或保留等未來需要「集團所有 alias 聯集查詢」時再用
- [ ] `onclick` 字串裡塞參數有潛在 escape 風險。目前有處理單引號和雙引號,但如果 note 含反斜線或其他特殊字元可能會壞。長期建議改用 `addEventListener` + `data-*` attributes
- [ ] 漢來的「焰」是單字 alias,目前走子字串比對,打「火焰」「焰火」等會誤命中。可考慮加「精確比對 vs 子字串比對」的 alias 分類
- [ ] `recommendations` 裡「蝦皮購物」的 U Bear 加油站那筆 note 含「網購」,我用「網路消費 3%」精確匹配避開誤擊。若以後 note 文字改動,matcher 要同步更新

---

## 🚀 較大的新功能點子(可選)

- [ ] **選卡介面搜尋歷史** — 記住使用者最近搜過/點過的 3~5 家店,放在快選按鈕上方
- [ ] **額度預警通知** — 當某額度達到 80%、100% 時,在選卡頁該卡旁邊顯示警告標記
- [ ] **月結報表** — 額度頁底部顯示「本期累計實際回饋 X 元」
- [ ] **日本賞等限時方案到期倒數** — 快到期前在相關搜尋結果旁加「剩 N 天」標記
- [ ] **多使用者 / 分享** — 現在 Google Sheet 是單人用,若要給家人一起用,需要考慮權限

---

## 📂 檔案結構參考

```
creditcard-personal/
├── index.html                 # 主檔,所有邏輯都在這
├── data/
│   └── cube-benefits.json     # CUBE 權益結構化資料(178 筆 merchant)
├── docs/
│   ├── PRD/
│   │   └── creditcard-personal v0.1.md
│   ├── cube-benefits.md       # 人類閱讀版的 CUBE 方案摘要
│   └── TODO.md                # 本檔
└── assets/
    └── cube-benefits/         # 原始截圖(.gitignore 不進版控)
```

**關鍵函式位置**(行號會隨改動漂移,建議用 Grep 找函式名)

- `loadCubeBenefits()` — 載入 cube-benefits.json
- `findCubeBenefitsByStore()` — 搜尋用(模糊比對,過濾過期方案)
- `findCubeBenefitsForExactStore()` — 精確比對(融合用)
- `isCubePlanActive()` — 方案期間/生日月檢查
- `isCubeMerchantCoveredByRec()` — 去重判斷(避免下拉重複出現)
- `resolveStoreEntry()` — 從店名或 alias 找到 rec 條目
- `onSearchInput()` — 搜尋下拉 render
- `showRecommendationFor()` — 主推薦結果 render(含 CUBE 融合 + 集團總覽)
- `showCubeOnlyRecommendation()` — CUBE-only 店家結果 render
- `matchQuotaForCard()` — 推薦 option → quotaConfig id
- `onCashbackChipClick()` — 可回饋金額按鈕點擊處理
- `showErrorToast()` — 紅色錯誤提示

---

## 💡 下次對話開場建議

你可以這樣貼給我:

> 我想繼續做信用卡專案的 X 項(例如:手機細節調整 / 擴充 Richart 額度追蹤 / 改自訂 modal)。
> 專案在 `/Users/seeyou/Developer/Claude/Claude Code/creditcard-personal/`,
> 詳細未完成項目看 `docs/TODO.md`。

我會讀 TODO.md 然後接著做。
