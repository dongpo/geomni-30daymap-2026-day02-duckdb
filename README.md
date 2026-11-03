# Geomni · Day 02 · 北台灣道路 × DuckDB Spatial

[Open demo](https://dongpo.github.io/geomni-30daymap-2026-day02-duckdb/)

這個 demo 使用 DuckDB Spatial 實際讀取 Overture 2026-10-21.0 GeoParquet，篩選北台灣道路，再執行精確交集及裁切。共 319,935 筆線段。

操作：縮放、拖曳、切換全部／主要道路；點選線段查看名稱、類別與 ID。

處理流程：bbox 粗篩 → ST_Intersects → ST_Intersection → GeoParquet。SQL 與類別統計隨專案提供。

網頁使用 3 公尺容許誤差簡化幾何，保留所有線段 ID。此頁不在瀏覽器執行 DuckDB；它顯示已完成的 DuckDB 查詢結果。線寬不代表交通量，也未驗證導航連通性。

## Run locally

```sh
python3 -m http.server 8000
```

Open http://localhost:8000. Static site; no API keys. Each repository contains its own assets and does not depend on the other demos. Modern browser with WebGL and DecompressionStream required for Points/Lines.

## Evidence and licensing

See `evidence/`, `LICENSE-DATA.txt` and vendor notices. Prepared 2026-11-03. Original release query SQL records the analysis workflow; local file paths in SQL refer to the original analysis workspace. This is a geographic technology demonstration, not navigation or a legal boundary service.
