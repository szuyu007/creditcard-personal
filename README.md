# Hackcard — 個人信用卡管家

一個自用的信用卡管理工具,解決多卡用戶的兩個痛點:

1. 站在付款當下不知道刷哪張卡回饋最高
2. 容易忘記繳費截止日而產生違約金

這不是商業產品,是 vibe coding 練習,單檔 `index.html` + vanilla JavaScript,無後端。

## 網站

👉 [szuyu007.github.io/creditcard-personal](https://szuyu007.github.io/creditcard-personal/)

## 功能

- **帳單看板**:顯示每張卡的帳單 / 繳費截止日倒數,紅橘綠警示,可標記已繳費,方案到期提醒
- **選卡建議**:搜尋常消費通路 + 輸入金額 → 排名各卡回饋金額
- **額度追蹤**:追蹤各卡加碼方案月度使用量,透過 Google Sheets 跨裝置同步
- **行事曆提醒**:下載 .ics 檔,把方案到期日加進行事曆

## 技術

- 單一 `index.html`,HTML + vanilla JavaScript
- 資料儲存:`localStorage`(一般狀態)+ Google Apps Script / Google Sheets(額度同步)
- 部署:GitHub Pages(push 到 `main` 自動部署)

## 檔案結構

```
.
├── index.html                      # 主程式(含所有 UI、邏輯、資料)
├── .github/workflows/deploy.yml    # GitHub Pages 自動部署
├── docs/
│   └── PRD/                        # 產品需求文件
└── assets/
    └── cube-benefits/              # 國泰 CUBE 卡權益資料
                                    # (原始截圖存本地不進版控,文字化 markdown 進版控)
```

## 支援卡片

| 卡片 | 發卡銀行 |
|---|---|
| CUBE 卡 | 國泰世華 |
| Richart 卡 | 台新銀行 |
| 街口聯名卡 | 台新銀行 |
| Unicard | 玉山銀行 |
| U Bear 卡 | 玉山銀行 |
| Pi 拍錢包卡 | 玉山銀行 |
