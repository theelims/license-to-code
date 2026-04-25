# uat-agents

User Acceptance Testing workflow for web apps via Chrome DevTools MCP.

This plugin provides three skills and one subagent that operate against a self-contained `./uat/` folder created in the project where you invoke them.

## What you get

| Skill | Purpose |
|---|---|
| `/uat-agents:plan` | Author a test plan from authoritative docs through Q&A. Auto-runs the reviewer subagent. Produces a `plan.md` requiring manual approval. |
| `/uat-agents:plan-review` | Re-run the reviewer against an existing plan after hand-edits. Demotes status to `in-review`. |
| `/uat-agents:setup` | One-time project setup: writes the `chrome-devtools` MCP entry into `.mcp.json`, copies `./tools/chrome-debug`, and walks you through WSL2 prerequisites. |
| `/uat-agents:test` | Execute an approved plan via Chrome DevTools MCP. Logs structured bugs, supports resume/restart/reverify/subset, aborts on critical bugs. |

Plus the `uat-plan-reviewer` subagent, which is invoked automatically by the two planning skills and never directly by you.

## Prerequisites

- Claude Code v2.0.73 or higher
- Node.js ≥20.19 (required to run `chrome-devtools-mcp` via `npx`)
- WSL2 with `networkingMode=mirrored` in `%USERPROFILE%\.wslconfig` (makes Windows `localhost` reachable from WSL)
- Google Chrome installed on Windows

Run `/uat-agents:setup` to handle the automated parts. See [Quick start](#quick-start) for the full setup sequence.

## Installation

```bash
# 1. Add the marketplace
/plugin marketplace add <github-user>/<marketplace-repo>

# 2. Install the plugin
/plugin install uat-agents@<marketplace-name>

# 3. Reload
/reload-plugins
```

The skills will appear as `/uat-agents:plan`, `/uat-agents:plan-review`, and `/uat-agents:test`.

## Quick start

### First-time setup (once per machine)

```text
> /uat-agents:setup
```

The skill writes `.mcp.json` and copies `./tools/chrome-debug`, then tells you to:

1. Press `Win + R` → type `notepad %USERPROFILE%\.wslconfig` → Enter. Add:
   ```ini
   [wsl2]
   networkingMode=mirrored
   ```
   Save and close.
2. From Windows PowerShell: `wsl --shutdown` — then reopen your WSL terminal.
3. In Claude Code: `/restart` to load the MCP server.

### Every test session

```bash
# Start Chrome on Windows with remote DevTools
./tools/chrome-debug start

# Plan (interactive Q&A → auto-review → you approve)
> /uat-agents:plan

> /clear

# Execute the approved plan
> /uat-agents:test 2026-04-25-my-feature

# If a critical bug aborts:
> /clear
# Invoke your debug agent against the bug file the tester pointed you at.

# After fix:
> /clear
> /uat-agents:test 2026-04-25-my-feature           # resume (default)
# or:
> /uat-agents:test 2026-04-25-my-feature reverify  # re-run from start

# When done for the day
./tools/chrome-debug stop
```

## Folder structure created in your project

The first time you run `/uat-agents:plan` in a project, this gets created at the repo root:

```
uat/
├── README.md                       # bootstrapped from this plugin
├── ux-report.md                    # cumulative idea repository
└── sessions/
    └── 2026-04-25-checkout-flow/
        ├── plan.md                 # approved test plan with checkboxes
        ├── review.md               # reviewer subagent's findings
        ├── session.md              # tester's run state (created on first /uat-agents:test)
        └── bugs/
            ├── INDEX.md
            ├── BUG-001.md
            └── evidence/
                └── BUG-001-spinner.png
```

The `./uat/` folder is self-contained and can be committed to the project repo or published independently — it tells a complete story of what was tested, what broke, and what the UX feedback was.

Templates ship with the plugin (in `${CLAUDE_PLUGIN_ROOT}/templates/`) and are read at runtime. You don't need to vendor them into your project.

## Severity rubric (default)

| Tier | Definition | Tester action |
|---|---|---|
| **critical** | Crash, hang, infinite loop, unresponsive UI, page won't load, data loss | Abort the session |
| **high** | Feature broken; this test cannot pass | Log; skip strictly-dependent tests; continue |
| **medium** | Incorrect behavior with workaround, or non-blocking spec violation | Log; continue |
| **low** | Cosmetic, copy issues, minor polish gaps | Log; continue |

You can override the rubric per session in the plan's `severity_overrides` frontmatter field.

## Approval gate (hard rule)

The tester refuses to run any plan whose `status` field is not exactly `approved`. This is intentional: the review phase is the safety net for ordering bugs and unmet preconditions. Skipping approval defeats the design.

If you hand-edit an approved plan, run `/uat-agents:plan-review` — it demotes the plan to `in-review` and forces a fresh review pass.

## Hand-off to a debug agent

When the tester aborts on a critical bug, it points you at a structured `BUG-NNN.md` file. The bug format is designed to be the input contract for a debugging agent: stable frontmatter (id, severity, status), explicit reproduction steps, expected vs. actual, evidence paths, and a "For the debug agent" section with re-test instructions.

This plugin does not include a debug agent; bring your own and have it write status updates back into the same `BUG-NNN.md` file (`status: fixed-pending-verify` → re-run UAT → `verified`).

## Versioning

Skill descriptions are stable; behavior may change between minor versions. Check `CHANGELOG.md` (when added) before upgrading mid-cycle.

## License

MIT
