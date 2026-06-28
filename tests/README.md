# 測試 / 驗證

這個專案沒有用 npm / 測試框架（刻意保持「雙擊就能跑」）。測試分兩種：

## 1. 自動資料驗證 — `validate.html`

檢查回饋資料有沒有填錯（JSON 格式、rate 是不是數字、兩套到期日是否一致、額度對映有沒有失效…）。

**怎麼跑：**

- **最簡單**：直接雙擊 `tests/validate.html`，看是不是**全綠**。
- 若打開後顯示「**載入失敗**」（瀏覽器在 `file://` 下擋了讀取本機檔），改用本機伺服器：

  ```bash
  # 在專案根目錄執行
  python3 -m http.server
  ```

  然後瀏覽器開 <http://localhost:8000/tests/validate.html>

**看結果：**
- ✅ 綠 = 通過
- ⚠️ 黃 = 警告（可接受，但值得看一眼，例如「兩套到期日對不上」「store 名重複」）
- ❌ 紅 = 要修（例如 rate 寫成字串、JSON 壞掉）

每次照 [`docs/update-checklist.md`](../docs/update-checklist.md) 改完資料，都跑一次這個。

## 2. 手動測試 — 照清單點

App 的互動（選卡、常用按鈕、到期提醒、額度、手機 UX）要人工點。
照 [`docs/test-report-template.md`](../docs/test-report-template.md) 一項一項點、記錄結果，就是一份測試報告。
