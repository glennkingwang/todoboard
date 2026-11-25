# 📋 工作看板 TodoBoard（手機版）

個人待辦看板的手機 App，與電腦桌面看板透過雲端同步。

**開啟網址：** https://glennkingwang.github.io/todoboard/

## 功能

- 三個分頁：**待完成**／**即將到來**／**已完成**
- 新增、編輯、刪除、標記完成；可設定日期、時間、提前提醒分鐘數與備註
- 顏色提示：⚠ 已逾期（紅色閃爍）、● 今天（紅）、◆ 3 天內（橘）
- 「即將到來」的項目日期一到，自動移入「待完成」
- 🔄 同步按鈕；背景每 30 分鐘、App 重新開啟、網路恢復時自動同步
- 沒網路時修改先存在手機，連線後自動上傳
- 可加到手機主畫面，以全螢幕 App 方式開啟

## 系統架構

```
手機 App（本 repo，GitHub Pages） ─┐
                                   ├─→ Supabase 雲端資料庫（東京）
電腦桌面看板（Python / Tkinter） ──┘        │
                                   pg_cron 每分鐘檢查 → Telegram Bot 提醒
```

| 元件 | 說明 |
|---|---|
| 資料庫 | Supabase Postgres：`todos`（待辦）、`user_settings`（個人設定） |
| 權限 | Row Level Security：每個帳號只能讀寫自己的資料 |
| 同步規則 | 以 `updated_at` 最後修改者為準；刪除先標記 `deleted` |
| 雲端提醒 | `pg_cron` + `pg_net`，每分鐘執行 `todo_reminders()`，用各帳號自己的 Telegram Bot 發送 |

## Telegram 提醒時段

| 類型 | 通知時間 |
|---|---|
| 有日期＋時間 | 到期前 N 分鐘（預設 30）＋ 到期時 |
| 只有日期 | 當天 09:00 |
| 每日摘要 | 每天 08:30（已逾期／今天／3 天內／未定日期） |

時間規則可在 App 的「⚙ 設定」修改，手機與電腦共用。

## 安裝到手機主畫面

- **iPhone（Safari）：** 分享按鈕 →「加入主畫面」
- **Android（Chrome）：** ⋮ →「加到主畫面」／「安裝應用程式」

## 檔案

| 檔案 | 用途 |
|---|---|
| `index.html` | App 本體（單一檔案，無外部套件） |
| `manifest.json` | 加到主畫面用的 App 資訊 |
| `icon-180/192/512.png` | App 圖示 |

## 安全性說明

`index.html` 內含的 Supabase **Publishable key** 是設計給前端公開使用的金鑰，
資料存取由登入驗證與 Row Level Security 保護。
本 repo **不含**任何密碼、Secret key 或 Telegram Bot Token（Token 只存在各帳號的雲端設定中）。

## 更新 App

修改 `index.html` 後重新上傳（覆蓋）到本 repo，約 1～2 分鐘後 GitHub Pages 自動更新。
手機若仍顯示舊版，關閉 App 後重新開啟即可。
