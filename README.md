# rate-window-visualizer

A tiny web app to visualize sliding-window API rate limits.

## MVP
- Input: limit, window seconds, request timestamps
- Output: timeline showing accepted/rejected requests

## Current behavior
- Accepts timestamp input separated by commas, spaces, or newlines
- Prints per-request decision logs
- Renders a detail table with in-window members **before/after** each decision

## Run
```bash
python3 -m http.server 8000
# open http://localhost:8000/index.html
```
