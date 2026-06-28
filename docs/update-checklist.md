# 回饋資料更新待查清單 & 填寫範本

> 這份文件是「**查到新資料後照著填**」的操作手冊。
> 每半年（CUBE 換期）或各銀行公告新活動時，照著對應章節更新。
>
> ⚠️ **行號會隨改動漂移**，文件裡寫的行號僅供參考，請用編輯器的「搜尋」找**變數名**（例如搜 `rewardExpiry`、`recommendations`、`quotaConfig`）。
>
> 改完一定要跑一次 [tests/validate.html](../tests/validate.html) 確認沒填錯（見最後一節）。

---

## 0. 總覽：哪個資料改哪裡

| 要更新的東西 | 改哪個檔 | 找哪個變數 / 欄位 |
|---|---|---|
| CUBE 卡方案期間、適用商家、回饋率 | `data/cube-benefits.json` | `plans[].period` / `plans[].merchants[]` / `_meta.lastUpdated` |
| 各卡「方案到期日」（帳單頁的彩色 tag、到期通知、行事曆匯出） | `index.html` | `rewardExpiry` 陣列 |
| 其他卡（Richart / 街口 / Unicard / U Bear / Pi）的回饋率與活動 | `index.html` | `recommendations` 陣列 |
| 額度追蹤上限（每月/每期可累積多少回饋） | `index.html` | `quotaConfig` 陣列 |

> 📌 **到期日有兩處要顧**：
> - **選卡頁**的 CUBE「方案剩 N 天」標記，是直接讀 `cube-benefits.json` 的 `period`，**改 JSON 就會自動跟著對**，不用另外動。
> - 但**帳單頁的方案到期 tag、到期通知、行事曆 .ics** 仍是讀 `index.html` 的 `rewardExpiry`。
> - 所以更新 CUBE 到期日時，**`cube-benefits.json` 的 `period` 和 `rewardExpiry` 裡 CUBE 那筆都要改**。其他 5 張卡只在 `rewardExpiry` 改。

---

## 1. CUBE 卡（每期到期就要換，目前到 6/30）

### 1-1. 去哪截圖
國泰世華 App → 「**CUBE 卡**」→ 「**權益方案**」→ 每個方案點進「**查看適用商家優惠**」逐頁截圖。
（截圖可放 `assets/cube-benefits/`，本地保留即可，不進版控。命名沿用既有 `IMG_xxxx.PNG`。）

### 1-2. 改 `data/cube-benefits.json`

**(a) 每個方案的期間 `period`**

```json
{
  "id": "le-xiang-gou",
  "name": "樂饗購",
  "period": "2026/07/01 ~ 2026/12/31",   ← 改成新一期日期
  ...
}
```

⚠️ **格式務必維持 `YYYY/MM/DD ~ YYYY/MM/DD`**（月、日補零，中間用 ` ~ `）。
程式用正則解析這個格式來判斷方案是否還有效，格式跑掉方案就會整個消失。

**(b) 適用商家 `merchants[]`**

每一筆店家長這樣，新增 / 下架就增刪這個陣列裡的物件：

```json
{ "name": "遠東 SOGO 百貨", "category": "國內指定百貨", "rate": 3, "type": "merchant" }
```

| 欄位 | 說明 | 注意 |
|---|---|---|
| `name` | 店家名 | 使用者搜尋會比對這個 |
| `category` | 分類（如「國內指定百貨」「餐飲」） | 自由文字，給人看的 |
| `rate` | 回饋率 | **一定是數字** `3`，不是字串 `"3"` |
| `type` | `"merchant"`（具體店家）或 `"category"`（類別） | 只能這兩個值 |
| `note` | （選填）附加說明 | |

**(c) 整期停售的方案** → 加 `"active": false`（參考「日本賞」那筆），程式就會自動把它從選卡結果排除，不用刪。

**(d) 慶生月** → `period` 維持 `null` 是正常的，它靠 `birthMonth` 欄位（數字，例如 `7` = 7 月）判斷，生日月份若有變改這個。

**(e) 最後** → 把 `_meta.lastUpdated` 改成今天日期（`YYYY-MM-DD`）。若有看不清楚 / 待補的項目，記到 `_meta.notes` 陣列。

### 1-3. 同步改 `index.html` 的 `rewardExpiry`

搜 `rewardExpiry`，找到 CUBE 那筆，把 `expiryDate` 改成新 period 的**結束日**：

```js
{ cardName: "CUBE 卡", planName: "玩數位/樂饗購/趣旅行/集精選/慶生月", expiryDate: "2026/12/31" },
```

⚠️ 這裡格式是 `YYYY/M/D`（**不補零**，例如 `2026/6/30`）。validate.html 會幫你檢查這筆跟 JSON 對不對得上。

### 1-4. 常見錯誤

- ❌ `rate` 寫成字串 `"3"` → 排序會壞。要寫數字 `3`。
- ❌ 日期用了全形斜線「／」或漏補零 → 方案解析失敗、整個方案消失。
- ❌ 改完忘了改 `_meta.lastUpdated`。
- ❌ JSON 少一個逗號或多一個逗號 → 整份載入失敗（validate.html 第 1 項會紅）。

---

## 2. 其他卡新活動（Richart / 街口 / Unicard / U Bear / Pi）

這些卡的回饋寫死在 `index.html` 的 `recommendations` 陣列（搜 `const recommendations`）。

### 2-1. 去哪查

| 卡 | 查哪裡 |
|---|---|
| Richart 卡 | 台新銀行官網 →「信用卡」→「最新優惠 / Richart 卡」 |
| 街口聯名卡 | 台新銀行「街口聯名卡」活動頁 |
| Unicard | 玉山銀行「Unicard 百大指定通路」頁 |
| U Bear 卡 | 玉山銀行「U Bear 卡」活動頁（熊任務 / 熊好刷 / 熊潮流） |
| Pi 拍錢包卡 | 玉山銀行「Pi 拍錢包信用卡」活動頁（常有限時加碼） |

### 2-2. 怎麼改

一個店家／通路是一個物件，裡面 `options[]` 是各張卡的回饋：

```js
{
  store: "全聯",
  keywords: ["pxmart"],          // 選填：搜尋用的別名關鍵字（顯示時仍跳主店名）
  aliases: ["小全聯", "PX Pay"], // 選填：集團子品牌（顯示別名本身）
  options: [
    { card: "CUBE 卡", bank: "國泰世華", rate: 2, type: "小樹點",
      pay: "直刷 / Apple Pay", cubePlan: "集精選", note: "不可用 LINE Pay" },
    // ...其他卡
  ]
}
```

- **回饋率 / 條件變了** → 改對應 option 的 `rate` / `note` / `pay`。
- **新增一個通路** → 複製一整個 `{ store, options: [...] }` 區塊。
- **集團（一個按鈕代表多個品牌）** → 用 `aliases`（例如「王品集團」底下放「西堤」「陶板屋」…）。
- **同義詞 / 英文名 / 別稱**（搜得到但顯示主店名） → 用 `keywords`。

⚠️ **Pi 的限時活動**（例如「媽咪女神節 每月 300 P幣」）通常直接寫在某個 option 的 `note` 字串裡。**活動過期後要手動把那段文字拿掉**（搜 `note` 裡的活動關鍵字）。

---

## 3. 額度追蹤上限變動（`quotaConfig`）

搜 `const quotaConfig`。目前追蹤 4 項：

```js
{ id: "unicard-elite",  card: "Unicard",   name: "百大加碼",     limit: 1000,  unit: "點",     resetDay: 15, note: "..." },
{ id: "ubear-digital",  card: "U Bear 卡", name: "數位訂閱加碼", limit: 100,   unit: "元",     resetDay: 15, note: "..." },
{ id: "ubear-online",   card: "U Bear 卡", name: "網購加碼",     limit: 150,   unit: "元",     resetDay: 15, note: "..." },
{ id: "jkopay-select",  card: "街口聯名卡", name: "精選通路",    limit: 10000, unit: "街口幣", resetDay: 7,  note: "..." },
```

- **上限數字變了** → 改該筆的 `limit`（和 `note` 裡的說明文字）。
- **新增一個要追蹤的額度**（參考 `docs/TODO.md`「額度追蹤擴充」段）：
  1. 在 `quotaConfig` 加一筆 `{ id, card, name, limit, unit, resetDay, note }`（`id` 不可跟現有重複）。
  2. 搜 `matchQuotaForCard`，加一條對映規則，例如：
     ```js
     if (card === "新卡名" && n.includes("新方案關鍵字")) return "新的-id";
     ```
  3. 對應的推薦卡片就會自動出現「＋可回饋 $XX」按鈕。

---

## 4. 改完一定要驗證

1. **雙擊打開** [tests/validate.html](../tests/validate.html)（若 `file://` 下載不到資料，見 [tests/README.md](../tests/README.md) 用本機伺服器開）。
2. 看是不是**全綠**。重點看：
   - JSON 格式有沒有壞、`rate` 是不是數字。
   - **「rewardExpiry ↔ CUBE period 一致性」**這項：若你只改了 JSON 忘了改 `rewardExpiry`（或反過來），這裡會變黃／紅提醒你。
3. 全綠後 `open index.html`，照 [docs/test-report-template.md](./test-report-template.md) 點一輪，確認沒有副作用。
