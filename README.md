# 591 店面地圖

591 店面出租監看 LINE Bot 的網頁版：地圖＋完整名單＋篩選，可以把物件標成 ⭐ 想約看／✖ 不考慮。

這個 repo 只有畫面（`index.html`，GitHub Pages 直接提供），**不含任何資料**：
物件資料、設定、標記都存在 LINE Bot 的 Google Apps Script，網頁開啟時才去讀。

## 連結

```
https://<帳號>.github.io/<repo 名稱>/#k=<網頁密鑰>&s=<GAS 部署代號>
```

- 完整連結在 LINE 傳「網頁」取得，推播最後也會附上。
- 用完整連結開過一次，連結會記在那台瀏覽器（localStorage），之後直接開網址也能用；換裝置、換瀏覽器或清掉網站資料要再用完整連結開一次。密鑰換過時會自動清掉記住的舊連結。
- `#` 後面的參數不會送到 GitHub；沒有正確密鑰讀不到資料。
- 拿到連結的人都能看，也都能標記；標「不考慮」的物件，LINE 名單也不再列出。
- 連結外流時，到 GAS「專案設定 → 指令碼屬性」刪掉 `WEB_KEY`，下次會產生新的，舊連結失效。

## 運作方式

- 讀資料：`GET <GAS>/exec?k=<密鑰>` → `{ ok, data: { updated, sections, items }, settings, marks }`
- 標記：`POST <GAS>/exec`，body `{ action: "mark", key, id, mark: "want" | "skip" | "" }`，
  Content-Type 用 `text/plain`（簡單請求，瀏覽器不會送 GAS 不處理的 OPTIONS 預檢）。
- 重新查詢：`POST {action: "refresh", key}`，GAS 觸發 GitHub Actions 爬一次（5 分鐘內只觸發一次）；網頁每 15 秒讀一次，
  `data.updated` 晚於這次觸發時間就重新載入；`refresh.failed` 有值代表失敗。
- 篩選預設跟 LINE 上的設定一樣；在網頁改的只記在這台瀏覽器（localStorage），不影響 LINE。
- 地圖：Leaflet 1.9.4（cdnjs）＋ OpenStreetMap 圖資。

## 更新

改完 `index.html` 推上 GitHub，Pages 約 1 分鐘後更新。
