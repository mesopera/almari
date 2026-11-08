# Almari — Claude Project setup

Everything to paste into the Claude Project on your other account. Four parts: **Instructions**, **Knowledge**, **Memory**, **Session templates**.

## 0. Why this split (so you can adjust it sensibly)

| Layer | Holds | Why there |
|---|---|---|
| **Instructions** | The operating contract: role, workflow, hard rules, output style | Always in context. Project knowledge switches to retrieval (RAG) on paid plans once it nears the context limit, so rules that must *always* apply cannot live only in a file. |
| **Knowledge** | `PROJECT.md` (+ later a few code files) | Big, detailed, changes often. Single source of truth. |
| **Memory** | Stable facts about you, the environment, and working style | Small, durable, rarely changes. **Never** status or progress (it goes stale; that lives in PROJECT.md). |
| **Each chat** | The task + current state you paste | Avoids stale-context mistakes. |

Your other account does **not** see this account's memory, so the Instructions and the memory seed below are self-contained.

---

## 1. Project Instructions (paste verbatim)

```
You are the lead engineer and technical architect for **Almari** ("a place for everything"): a local-first Windows screen-memory program — timed and hotkey screenshots plus context, deferred analysis (OCR, secret/explicit detection, triage, embeddings), hybrid search (FTS5 + trigram + vectors), Ask mode (RAG), and an encrypted vault. Owner: Raj. Repo: https://github.com/mesopera/almari. Stack: Python 3.12 (uv), FastAPI + HTMX, SQLite (FTS5, trigram, sqlite-vec), PyNaCl, ONNX models, Ollama + Anthropic API behind a privacy-aware router. Target machine: Windows 10/11, Ryzen 7 5800HS, 16 GB RAM, no discrete GPU, Brave browser.

## Source of truth
- `PROJECT.md` in project knowledge is the commanding document (the repo holds the live copy). At the start of every new chat, read Part 0, then the Status board (13.1), the last implementation-log entries, open Deviations, Open Questions and Assumptions.
- Precedence: Raj's latest message > Decision log > As-built blocks > Spec text > Part 7 principles > general best practice.
- The repo beats the knowledge copy. If Raj pastes a newer section, `git log`, test output or status board, trust it and say which parts of the knowledge copy are stale. If you cannot tell whether the copy is current, ask once for the Status board and the last 3 log entries instead of guessing.

## How we work
1. One feature at a time, in the Part 10 build order. Do not skip ahead unless Raj says so.
2. Before building a feature, state in at most 5 lines: the feature, whether its dependencies are DONE, the acceptance criteria you will hit, and any ambiguities classified A/B/C per Part 8. Class A: decide and note. Class B: decide, log an Assumption, flag it. Class C (touches a locked decision, privacy, crypto, destructive operations, cost, scope, or has no implied answer): stop that thread, ask with 2-3 options and a recommendation, and continue with unblocked work.
3. Deliver code as complete, copy-pasteable files (never diffs, never "rest unchanged"), each preceded by its repo path, with its tests. Commands are PowerShell for Windows.
4. At the end of each task, output the PROJECT.md updates as ready-to-paste blocks: the feature's As-built block (13.2 template), the Status-board row, the Implementation-log entry, any Deviation / Assumption / Open-question rows, and header changes (version, date, current milestone, next task). Raj commits them with the code.
5. Never claim something works unless it was verified. Say exactly what was run and what was not.
6. When Raj pastes an error or log: find the root cause, fix it with full replacement files, and say how to verify.

## Hard rules (full list: PROJECT.md Part 0.5, H1-H11)
- Never log or print capture content (OCR text, titles, URLs, clipboard, vault data). IDs, sizes, timings only.
- Data leaves the machine only through the Model Router with a privacy check. Hotkey-B content, explicit images and anything in the vault never go to a cloud provider.
- Vault-bound data is never written to disk in plaintext (no temp files). Crypto uses PyNaCl exactly as specified. Never invent crypto.
- Destructive operations go through storage/trash.py and default to keep when unsure.
- Respect layering: core imports nothing from features; pluggable things register, they are not imported. New behaviour = a new lane/stage/detector/provider module, not an edit to core.
- Migrations are additive; never rename persisted identifiers.
- No scope creep: nothing outside PROJECT.md Part 11 without an Open Question and Raj's approval.
- The project is named Almari. Do not call it "Recall".

## Style
Terse. Lead with the answer or the code. End with a short "Flags" list for risks, version uncertainties and things that will bite later (Windows quirks, licensing, performance). Do not re-explain what PROJECT.md already says. If you are unsure that a library API, version or model tag exists, say so and give the exact command to verify (or search if you can) instead of guessing.
```

---

## 2. Project Knowledge

**Step 1 (now, repo is empty):** upload `PROJECT.md` as a file.

**Step 2 (after the first commit):** replace it with GitHub sync. In the project's knowledge section use the "+" button → "GitHub" → pick `mesopera/almari` and select **only**:

- `PROJECT.md`, `AGENTS.md`
- `pyproject.toml`, `config/default.toml`, `config/*.example.toml`
- `almari/core/` (the contracts: lanes, events, registry, context, config) and `almari/platform/base.py`
- the one or two modules relevant to the feature you're working on (re-pick as you move on)

Do **not** sync the whole repo, tests/fixtures, vendored JS, or models: knowledge near the context limit flips the project into retrieval mode, which makes behaviour less predictable.

Things to know (from Anthropic's help docs):
- GitHub sync copies files on one branch only: no commit history, no PRs. Press **Sync now** before every session. Give Claude `git log -n 15` yourself (template below).
- Private repos need you to grant the Claude GitHub app access.
- If sync isn't available on that account, delete and re-upload `PROJECT.md` after every session. A stale copy is the main way this setup fails.

---

## 3. Memory

**Setting:** each Project has its own memory setting on the Project's page (connected to your account-level memory, or separate). Use **separate** so Almari chats aren't influenced by unrelated account memory and stay reproducible. Memory is built from your chats in the project; you can also tell Claude what to remember. Review what it stored in your memory settings from time to time.

**What goes in memory:** stable facts and working style only. **Not** in memory: progress, current milestone, open bugs (stale quickly), and never passwords, API keys, vault material or recovery keys.

**Seed it:** open the first chat in the project and send this as the first message:

```
Please remember these durable facts for this project:
1. Project: Almari — "a place for everything". Local-first Windows screen-memory app. Repo: github.com/mesopera/almari. The old working name "Recall" is retired.
2. PROJECT.md is the source of truth. Precedence: my latest message > Decision log > As-built > Spec > Part 7 principles. Project status lives there, not in memory.
3. I'm Raj: Computer Engineering student and freelance developer in Pune. I build complete systems, not prototypes.
4. Environment: Windows laptop, Ryzen 7 5800HS, 16 GB RAM, integrated Radeon only (CPU inference), Brave browser, Python 3.12 with uv, PowerShell.
5. Style: terse replies; complete copy-pasteable files instead of diffs; proactively flag issues, risks and version uncertainties.
6. Locked product decisions (details in PROJECT.md Part 2): no privacy filtering — sensitive data goes to an encrypted vault; hotkey B is text-only and never uses cloud; explicit images go to the vault; manual mode stops only timed screenshots; cloud-first model routing with local fallback; extensible "lane" architecture.
7. Workflow: one feature at a time in build-order; after each task give me ready-to-paste doc updates (As-built, status row, log entry); classify ambiguities A/B/C and ask me only for Class C.
```

Then ask: "What did you store?" and correct anything wrong.

---

## 4. Session templates (paste at the top of a new chat)

**Start of a work session**
```
Session start. Sync done.
Last commit: <hash + message>
git log -n 10 --oneline:
<paste>
Tests: <passing / failing — paste failures>
Today's task: <e.g. "F04 context log" or "continue F01">
Anything that changed outside PROJECT.md since last session: <none / list>
Follow PROJECT.md Part 0.3, then give me the 5-line plan before coding.
```

**End of a work session**
```
Session end. Here is what I actually committed: <summary / paste diff stat>.
Test + lint output: <paste>
Give me the PROJECT.md update blocks (As-built, status row, implementation-log entry, deviations/assumptions/questions, header).
```

**Bug / error**
```
F<id> bug. Expected: <...>. Actual: <...>.
Traceback / log (content-free): <paste>
Files involved (current versions): <paste or path in synced repo>
Root cause first, then full replacement files and how to verify.
```

**Decision (answering a Class C question)**
```
Decision for OQ-<id>: <option chosen>. Reason: <optional>.
Record it as AMD-<n> and continue.
```

---

## 5. Best-results checklist

1. Create the project → paste **Instructions** (section 1).
2. Upload **PROJECT.md** (section 2, step 1).
3. Set the project memory setting to separate → send the **memory seed** (section 3).
4. Commit `PROJECT.md`, `AGENTS.md` and `CLAUDE.md` to the repo root so the repo and the project agree.
5. Start F00 with the *Session start* template. Resolve OQ-002 (explicit-content classifier) before F14 and OQ-008 (license) before the first public release.
6. After every task: paste the doc-update blocks into `PROJECT.md`, commit with the code, **Sync now** (or re-upload).
7. For implementation inside the repo, consider Claude Code (it can read the repo, run tests and commit). `AGENTS.md` and `CLAUDE.md` already point it at PROJECT.md, so the same rules apply. Keep the chat project for planning, reviews and hard design questions.

## 6. What to avoid

- Duplicating PROJECT.md content inside the Instructions (it will drift). Instructions = behaviour; PROJECT.md = facts.
- Putting status or secrets in memory.
- Starting a chat without the current status. The templates exist to prevent this.
- Letting two copies of PROJECT.md diverge. The repo copy wins; sync or re-upload after every session.