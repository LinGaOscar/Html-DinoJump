# 開發文件（dev.md）
> 本檔由 /sync-docs 自動生成，請勿手動編輯——手改內容下次會被覆蓋。

## 環境需求
- 任一現代瀏覽器（支援 HTML5 Canvas）
- 無建置流程、無套件管理、無 lint／測試工具
- 選用：Python 3（僅用於本機 HTTP server）

## 快速啟動
最短路徑：clone 後直接用瀏覽器開啟 `index.html`。

若需要本機伺服器（避免 file:// 限制）：

```bash
python -m http.server
```

然後開啟 http://localhost:8000/。

## 修改時的注意事項
- 遊戲數值（重力、跳躍力、地面高度、障礙速度）在 `resize()` 內依畫布高度等比縮放，調整平衡性時請連同係數一起看。
- 遊戲中按 `D` 鍵切換 hitbox 除錯疊圖。
- 畫布顏色為 `js/script.js` 內寫死的 hex，與 `css/style.css` 的 CSS 變數重複，改配色需兩邊同步。
- 沒有自動化測試，修改後請手動遊玩：開始、跳躍、雙跳、撞擊結束、重新開始、飛翔模式（累計過 10 個障礙物）。

## 部署
GitHub Pages，從 `main` 分支根目錄部署，步驟見 [doc/github-pages.md](../doc/github-pages.md)。
