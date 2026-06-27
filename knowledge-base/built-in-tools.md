# Built-in (non-MCP) harness tools available to Tony

The native Claude Code / Agent SDK tools, separate from the MCP servers in `mcp/`. One-liner + when to use. (MCP capability map lives in `mcp/`; this is the harness layer.)

## Core file + shell
- **Bash** — run shell commands (absolute paths; cwd resets between calls in agent threads; `run_in_background` for long jobs). Use for git, builds, tests, CLI tools. Avoid `cat`/`sed`/`echo` for file I/O — use the file tools.
- **Read** — read a file (also images/PDF pages/notebooks). Use before editing; read only the slice you need on big files.
- **Write** — create or fully overwrite a file. Must Read first if it already exists.
- **Edit** — exact-string replace in a file (`replace_all` for global). Must Read the file first.
- **NotebookEdit** — edit a Jupyter `.ipynb` cell (deferred tool). Use only for notebooks.

## Agents / orchestration
- **Agent** — spawn a sub-agent/worker. Types: `claude` (default catch-all), `Explore` (read-only broad search, returns conclusions not file dumps), `Plan` (architect; returns step plans, no edits), `general-purpose` (multi-step research/search), `worker` (the Metro background worker — all hands-on work on Opus 4.8 deep-think), `codex:codex-rescue` (hand a stuck/second-opinion task to Codex), `claude-code-guide` (Q&A about Claude Code/SDK/API), `statusline-setup`. `isolation: worktree` = isolated git copy; `run_in_background` = async. **This is how the Metro main session offloads work and stays free to reply.**
- **SendMessage** — continue a previously-spawned agent with its context intact (vs a fresh `Agent` call). Deferred.
- **TaskStop** — stop a running background agent. Deferred.
- **EnterWorktree / ExitWorktree** — move the session into/out of an isolated git worktree. Deferred.

## Discovery / deferred-tool loading
- **ToolSearch** — load schemas for deferred tools before calling them (`select:<name,...>` for exact, or keyword search). REQUIRED before any deferred MCP/tool can be invoked (e.g. the Snapshot/Better Stack/Metro tools all arrive deferred).
- **Skill** — invoke an available skill by name (see list below). Blocking requirement: if a skill matches the request, call it before doing the task manually.

## Web
- **WebSearch** — search the web (deferred). Use for current info / unknowns; prefer over memory for anything time-sensitive.
- **WebFetch** — fetch + read a specific URL (deferred).

## Scheduling / monitoring (deferred)
- **Monitor** — wait on a condition / watch a background job (e.g. until-loop for a process to finish). Use instead of foreground `sleep`.
- (Cron / Schedule / Loop are exposed as **Skills**, below — they create recurring cloud agents / interval runs. The hourly loop + the 15-min SX-Ledger poll are jobs of this kind.)

## AskUserQuestion
- **AskUserQuestion** — ask the user a structured multiple-choice question when genuinely blocked on a decision. Use sparingly; not for a worker (no interactive user attached).

## Available Skills (via the Skill tool)
- **metro** — run the Metro train-supervisor (launch `metro`, watch stdout JSON events, act on each). The comms backbone.
- **discord** — control Discord via the discord tool (send/react/poll/threads/pins/search/mod/member+role info).
- **stage-loops** — recreate the recurring Stage/Metro orchestrator loops (daily Stage announcement, daily Snapshot dev digest).
- **new-issue** — Less's issue-intake workflow (pick repo, create labelled XMTP channel, work via background workers, open PR off main).
- **sekhmet-review** — review a PR/diff in Sekhmet's rigorous, selective voice. (Used for checkpoint#390 review.)
- **code-review** — review the working diff for correctness bugs + cleanups; `--comment` to post inline, `--fix` to apply.
- **simplify** — quality-only cleanup pass (reuse/simplify/efficiency), applies fixes; not a bug hunt.
- **security-review** — security review of pending branch changes.
- **review** — review a GitHub PR (vs `code-review` for your local diff).
- **verify** / **run** — actually launch the app to confirm a change works (verify = observe behavior; run = launch/screenshot).
- **deep-research** — fan-out multi-source, adversarially-verified, cited research report. Ask 2-3 clarifying Qs first if scope is vague.
- **loop** — run a prompt/slash-command on a recurring interval (or self-paced). For recurring tasks/polling.
- **schedule** — create/manage scheduled cloud agents (cron routines) + one-off timed runs.
- **codex:rescue / codex:setup / codex:* ** — delegate to / configure the Codex rescue subagent + Codex CLI runtime.
- **interview-goal** — interview the user to surface the project's underlying goal.
- **update-config** — configure the harness via settings.json (permissions, env vars, hooks for automated behaviors).
- **keybindings-help**, **claude-api**, **init**, **fewer-permission-prompts** — keybinding edits; Claude API reference; CLAUDE.md init; auto-allowlist common read-only calls.

> Diagnosis-specific knowledge lives in `skills/` (repo skills, e.g. `diagnose-sx-vote-pipeline.md`), distinct from these harness Skills.
