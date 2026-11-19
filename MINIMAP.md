# MINIMAP — finn-hands-queue (hand this + the relevant source files to any model)

## What this is
A DATA repo, not a code repo: the task queue for the finn-hands
daemon on Penny's PC. Swarm agents (finn, marley, crystal, candace)
drop task JSON files here; the finn-hands daemon (separate repo,
`finn-hands`) picks them up and executes them on her PC. No code,
no build, no run.bat — the repo IS the queue. All ~290 task files
date from Oct 19–28, 2026; the queue has been quiet since (the
AGENT-LINK Gmail wire superseded it as the live channel).

## Layout map
- `queue.json` — the queue manifest: a `tasks` array. Currently
  holds ONE entry, `https-test-001` (an Oct 15, 2026 HTTPS queue
  test from finn). Note its field names are UPPERCASE
  (`MSG-ID`, `FROM`, `TO`, `OP`, `CMD`, `PROJECT`, `TTL`,
  `CREATED`) — the older envelope style.
- `tasks/` — ~290 task files, one JSON per task, named
  `<sender>-<YYYYMMDD>-<seq>.json` (e.g.
  `candace-20261020-001.json`, `finn-20261019-hands-test-001.json`,
  `marley-20261019-001.json`). Senders: candace (~190 files,
  Oct 19–28), finn (~50, Oct 19–20), marley (~12, Oct 19),
  crystal (2, Oct 19), plus one-offs (`check-fsp-001`,
  `fsp-test-001`–`005`, `https-test-002`, `list-fsp-001`,
  `sub146-diag-8113`, `survey-ai-userdata-001/002`,
  `write-script-001`).
- `README.md` — one line (`# finn-hands-queue`).

## Task file schema (lowercase style, the norm in tasks/)
```json
{
  "task_id": "candace-20261020-001",
  "from": "candace",
  "op": "EXEC",
  "cmd": "C:\\Program Files\\Git\\bin\\git.exe pull",
  "project": "C:\\Users\\llm_development\\goose-qt-coding-apparatus",
  "timeout": 120,
  "ttl": 72,
  "created": "2026-10-20T18:42:21+00:00"
}
```
- `op` is `EXEC` in every file sampled (the daemon also knows
  SELF-UPDATE and other ops — see the finn-hands repo — but this
  queue's files are EXEC tasks).
- `cmd` is a SINGLE command — no shell chaining (`&&`, `;`, `&`
  fuse into one invocation's argv and break). Longer work arrives
  as base64-encoded Python via `py -3.10 -c "import base64;
  exec(base64.b64decode('...'))"` (see
  `tasks/finn-8113-diag-repair2-20261020.json`).
- `project` is the working directory on her PC (always under
  `C:\Users\llm_development\`). `timeout` seconds; `ttl` 72 (hours).
- Filenames ARE the task ids; `task_id` inside matches the filename.

## How to build and run it
Nothing to build or run. To enqueue a task (the historical flow):
write a task JSON into `tasks/` following the schema above, commit
+ push; the daemon polls and executes. The daemon itself lives in
the `finn-hands` repo — this repo is only the mailbox.

## Gotchas
- **This queue is dormant.** The last task files are Oct 28, 2026.
  The live agent channel is the AGENT-LINK Gmail wire, not this
  repo. Do not assume dropping a file here reaches anyone — verify
  the daemon still polls this repo before relying on it.
- **Two field-name styles exist.** `queue.json` uses UPPERCASE
  envelope keys; `tasks/*.json` use lowercase. Don't mix them, and
  don't "normalize" the old files.
- **The queue is append-only history.** ~290 files are the Oct 2026
  operational record (daemon repairs, git pulls, DB probes,
  Tailscale revives, hands tests). Don't delete or rewrite them —
  they are the audit trail.
- Task ids must be unique per sender+date; a reused id is a sender
  bug, not a reason to overwrite.

## Current state
Dormant data repo. No build number exists — none applies. Last
activity Oct 28, 2026.
