# Geomni · 雲端地理資料，如何變成可探索的道路地圖？

[操作地圖](https://dongpo.github.io/geomni-30daymap-2026-day02-duckdb/)

## 這張地圖要回答什麼？

從大型雲端道路資料選出需要的區域，完成空間處理，再交給網頁呈現。本 demo 展示 GeoParquet → DuckDB Spatial → Web GIS 的完整分工。

## 地圖怎麼看？

共 319,935 筆道路線段。金色為 motorway、trunk；青綠色為 primary、secondary、tertiary；灰藍色為其他類別。可縮放、切換全部／主要道路，點選線段查看名稱、分類及 ID。顏色與線寬表示分類，不代表交通量。

## 資料來源與範圍

Overture Maps 2026-10-21.0 的 Transportation segment，選取 subtype=road。研究範圍為 120.80–121.95°E、24.55–25.35°N。原始資料以 GeoParquet 存放於雲端物件儲存。

## 資料做了什麼處理？

本機 DuckDB Spatial 讀取遠端 GeoParquet：先用 bbox 交疊粗篩，再用 ST_Intersects 確認相交，以 ST_Intersection 裁切研究範圍。保存選取及裁切後的 GeoParquet，另輸出 GeoJSON。依類別統計時，先轉至 EPSG:3826，再計算線段幾何長度。

## 網頁圖資如何準備？

網頁版本另在 TWD97／TM2（EPSG:3826）以 3 公尺容許誤差簡化幾何，再轉回經緯度，保留線段 ID。簡化與 gzip 壓縮用於減少傳輸及顯示負擔；這不是原始精度的測量圖。

## 網頁用了什麼架構？

處理階段：雲端物件儲存 → 本機 DuckDB Spatial → 裁切、統計與網頁圖資整理。展示階段：GitHub Pages → gzip GeoJSON → 瀏覽器解壓縮 → MapLibre GL JS 的 WebGL 線圖層。瀏覽器不執行 DuckDB，也沒有空間資料庫或後端分析 API。

## Cloud-Native GIS 的意義

這次讓雲端檔案直接成為查詢來源，減少先匯入完整資料庫的步驟。查詢仍需要傳輸資料與運算資源；本次未量測網路流量節省比例，也不是與 PostGIS 的效能比較。

## 這次實作的界線

319,935 是線段記錄數，包含服務道路、步道等類別，不能當作道路條數。彙總幾何長度不等於去重道路里程；本次未驗證導航連通性。互動網頁使用 MapLibre，供下載的靜態地圖由 GeoPandas／Matplotlib 製作。

## 資料與技術來源

- [Overture Transportation 資料結構](https://docs.overturemaps.org/schema/reference/transportation/segment/)
- [Overture 的 DuckDB 讀取方法](https://docs.overturemaps.org/getting-data/duckdb/)
- [DuckDB Spatial](https://duckdb.org/docs/stable/core_extensions/spatial/overview.html)

## 本機執行

```sh
python3 -m http.server 8000
```

開啟 http://localhost:8000。各 repo 資產獨立，不需要 API key。Points／Lines 需要支援 WebGL 與 DecompressionStream 的現代瀏覽器。

## 證據與授權

查詢 SQL、核對表或結果見 `evidence/`；資料授權與歸屬見 `LICENSE-DATA.txt`，第三方程式授權保留於 `vendor/`。SQL 中的輸出路徑記錄原分析工作目錄，可依重現環境調整。內容更新：2026-11-03。
