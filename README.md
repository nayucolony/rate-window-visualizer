# rate-window-visualizer

A tiny web app to visualize sliding-window API rate limits.

## MVP
- Input: limit, window seconds, request timestamps
- Output: timeline showing accepted/rejected requests

## Current behavior
- Accepts timestamp input separated by commas, spaces, or newlines
- Prints per-request decision logs
- Renders a detail table with in-window members **before/after** each decision

## Edge-case quick checks
- **同時刻リクエスト**（`limit=2, window=10, events=1,1,1`）
  - 1つ目: ACCEPT / 2つ目: ACCEPT / 3つ目: REJECT
- **窓境界ちょうど**（`limit=2, window=10, events=0,5,10`）
  - `t=10` の時点で `t=0` は窓外（`10 - 0 >= 10`）として除外されるため ACCEPT
- **空入力**（`events=`）
  - シミュレーション結果は空、テーブルは `No valid timestamps.` を表示

## Run
```bash
python3 -m http.server 8000
# open http://localhost:8000/index.html
```
