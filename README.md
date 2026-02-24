# rate-window-visualizer

A tiny web app to visualize sliding-window API rate limits.

## MVP
- Input: limit, window seconds, request timestamps
- Output: timeline showing accepted/rejected requests

## Current behavior
- Accepts timestamp input separated by commas, spaces, or newlines
- Prints per-request decision logs
- Renders a detail table with in-window members **before/after** each decision
- Shows an inline legend above the table (`boundary-hit`, `ACCEPT`, `REJECT`) so row color/decision meaning is self-explanatory
- Preset buttons (`normal`, `boundary`, `burst`) can auto-fill inputs and run simulation instantly

## Presets
- `normal`: a balanced sample flow for baseline behavior checks
- `boundary`: values around window boundaries to confirm exclusion rules (`now - t >= window`)
- `burst`: concentrated requests to verify rejection behavior when the limit is exceeded

Tip: after opening `index.html`, click any preset first, then tweak `limit/window/events` manually for quick what-if testing.

## Detail table legend
- `boundary-hit` row (light orange): one or more existing events were excluded at this step due to boundary crossing (`t - head >= window`).
- `ACCEPT`: active in-window count was below `limit`, so the request was added.
- `REJECT`: active in-window count had already reached `limit`, so the request was not added.

## How to read `excluded` (boundary exclusion count)
- `excluded` shows how many existing in-window requests were removed **at that step** because they fell outside the window (`now - t >= window`).
- A larger `excluded` value indicates boundary-driven cleanup happened before judging ACCEPT/REJECT.
- The per-request log line in `index.html` follows `... (excluded=X, before=Y, after=Z)`: `excluded` is the count removed at the boundary, `before` is active in-window size right before current request judgment, and `after` is active in-window size right after the ACCEPT/REJECT result is applied.

### Boundary preset quick verification
1. Click `boundary` preset.
2. Check rows where timestamp moves across a window edge (for example around `t=10` with `window=10`).
3. Confirm `excluded` becomes `1` when the oldest event leaves the window exactly at the boundary.
4. Confirm decision stays consistent with the cleaned window size (`active(before)` and `active(after)`).
5. Confirm rows with `excluded > 0` are highlighted with the `boundary-hit` style (light orange background) so boundary cleanup points are immediately visible.

### Screenshot checklist (boundary highlight)
- Include at least one highlighted `boundary-hit` row where `excluded=1`.
- Keep `active(before)`, `decision`, and `active(after)` in frame to show judgment consistency after cleanup.
- Prefer a capture that includes preset buttons so the scenario (`boundary`) is reproducible.

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
