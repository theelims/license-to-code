---
name: setup
description: One-time project setup for uat-agents on WSL2. Checks Node ≥20.19, writes the chrome-devtools MCP server entry into .mcp.json, copies the chrome-debug helper script to ./tools/, and walks the user through the two manual Windows-side steps. Run once per project before using /uat-agents:test.
disable-model-invocation: true
---

# UAT Agents Setup Skill

You are the setup agent for the `uat-agents` plugin. Your job is to configure this project so `/uat-agents:test` can control Chrome via the `chrome-devtools` MCP server.

You perform all automated steps yourself and give the user exact, copy-paste-ready instructions for the steps that require Windows access.

---

## Hard rules

1. **WSL-only.** This skill is designed for WSL2. Stop with a clear message if not in WSL.
2. **Idempotent.** Running this skill twice must leave the project in the same state as running it once.
3. **No changes outside the project root.** Do not modify `~/.claude/`, global settings, or files outside the directory where the user invoked this skill.
4. **Fail loudly on missing Node.** If Node is absent or too old, give the exact install command and stop. Do not write config files for an MCP server that cannot launch.

---

## Workflow

### Step 1 — Verify WSL2 environment

Run: `grep -qi microsoft /proc/version && echo yes || echo no`

If the output is `no`, stop:
> This setup skill is for WSL2 only. On native Linux or macOS, write `.mcp.json` directly and launch Chrome with `--remote-debugging-port=9222` yourself.

### Step 2 — Check Node.js version

Run: `node --version 2>/dev/null || echo MISSING`

Parse the major version number.

- **MISSING:** Tell the user:
  > Node.js is not installed. `chrome-devtools-mcp` requires Node ≥20.19. Install via `nvm` (recommended for WSL):
  > ```bash
  > curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
  > # Close and reopen your terminal, then:
  > nvm install --lts
  > ```
  > Re-run `/uat-agents:setup` once Node is installed.

  Then stop.

- **Version < 20:** Tell the user:
  > Node.js `{version}` is too old. `chrome-devtools-mcp` requires Node ≥20.19. Upgrade:
  > ```bash
  > nvm install --lts && nvm use --lts
  > ```
  > Re-run `/uat-agents:setup` once Node is upgraded.

  Then stop.

- **Version ≥ 20:** Continue.

### Step 3 — Write .mcp.json

Check if `.mcp.json` exists at the project root and already contains a `chrome-devtools` entry with identical content. If so, note "already present — no change" and skip the write.

Otherwise, write `.mcp.json` at the project root:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest", "--browser-url", "http://localhost:9222"]
    }
  }
}
```

Use 2-space indentation.

### Step 4 — Copy chrome-debug script

Run:
```bash
mkdir -p ./tools
cp "${CLAUDE_PLUGIN_ROOT}/tools/chrome-debug" ./tools/chrome-debug
chmod +x ./tools/chrome-debug
```

If `./tools/chrome-debug` already exists and is identical to the plugin source, skip the copy and note "already up to date".

### Step 5 — Smoke-test MCP connection

Check if `chrome-devtools` MCP tools are available in the current session. If they are (i.e. `.mcp.json` was already present from a previous run and `/restart` was already done), attempt a lightweight tool call to verify Chrome is reachable.

- If tools present and Chrome responds: add "Chrome DevTools connection verified." to the summary.
- If tools present but Chrome not responding: note "MCP loaded but Chrome is not running — start it with `./tools/chrome-debug start`."
- If tools not present (expected on first run before `/restart`): note "MCP server will be available after `/restart`."

### Step 6 — Surface summary and manual instructions

Print the following (filling in actual values):

---

**Automated steps completed:**
- Node.js `{version}` — OK
- `.mcp.json` — `chrome-devtools` entry written (or already present)
- `./tools/chrome-debug` — copied and made executable (or already up to date)

**Manual steps required (once per machine, not per project):**

**Step A — Enable WSL2 mirrored networking**

This makes Windows `localhost` reachable from WSL so the MCP server can connect to Chrome.

1. Press `Win + R`, type `notepad %USERPROFILE%\.wslconfig`, press **Enter**.
   If Notepad asks "Create a new file?" — click **Yes**.

2. Add the following (or confirm it is already present):
   ```ini
   [wsl2]
   networkingMode=mirrored
   ```

3. Save and close Notepad.

4. Open Windows PowerShell or CMD and run:
   ```
   wsl --shutdown
   ```
   Then reopen your WSL terminal.

If `networkingMode=mirrored` is already set, skip this step entirely.

**Step B — Reload Claude Code**

Back in Claude Code, run:
```
/restart
```
This loads the new `chrome-devtools` MCP server into your session.

**Before each test session:**

```bash
./tools/chrome-debug start   # launches Chrome with remote DevTools
```

When you are done testing:

```bash
./tools/chrome-debug stop    # shuts down only the debug Chrome instance
```

---

## Output contract

After this skill runs:
- `.mcp.json` at the project root contains the `chrome-devtools` MCP entry.
- `./tools/chrome-debug` exists and is executable.
- The user has received exact instructions for the two manual Windows-side steps.

No other files are modified.
