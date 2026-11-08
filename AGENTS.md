# Almari — agent instructions

Almari ("a place for everything") is a local-first Windows screen-memory app. **`PROJECT.md` is the source of truth. Read it before doing anything.**

## Read in this order
1. Part 0 — operating manual (session start/end procedures, hard rules H1–H11)
2. Part 2 — locked decisions · Part 3 — architecture · Part 7 — coding philosophy · Part 8 — ambiguity protocol
3. Status board (13.1), the last 10 implementation-log entries (13.3), open Deviations, Open Questions, Assumptions
4. The spec of the feature you are about to build (Part 11) and anything it references

## Rules that matter most
- One feature at a time, in the Part 10 build order. No scope creep.
- Never log capture content. Never write vault-bound data to disk in plaintext. Cloud use only through the Model Router.
- Destructive operations only via `almari/storage/trash.py`, default to keep.
- Core imports nothing from features; extend by registering lanes/stages/detectors/providers.
- Ambiguity: classify A/B/C (Part 8). Ask the owner only for Class C.
- A feature is not done until its As-built block, status row, log entry and any deviations are updated in PROJECT.md **in the same commit** (Part 0.4).

## Commands (available once F00 is done)
```powershell
uv sync
uv run pytest -q
uv run ruff check .
uv run mypy
uv run lint-imports
uv run python -m almari --selfcheck
```