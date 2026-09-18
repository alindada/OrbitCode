# OrbitCode — AI Pair Programmer

![version](https://img.shields.io/badge/version-1.0.1-blue)
![license](https://img.shields.io/github/license/alindada/OrbitCode)
![VS Code](https://img.shields.io/badge/VS%20Code-%5E1.80.0-007ACC)
![release](https://img.shields.io/github/v/release/alindada/OrbitCode?include_prereleases&label=download)

An agentic AI coding assistant for **VS Code / Cursor**: sidebar chat, streaming output, inline diff, a built-in tool runtime (file I/O, terminal, LSP, REPL, search) and MCP integration.

Just sign in with a **LLM Gateway** account — models are provided by the gateway, so **you don't need to supply API keys for each vendor yourself**.

---

## Features

| Capability | Description |
|------------|-------------|
| **Multi-model chat** | Sidebar chat with the model list served by the gateway's `/v1/models`;  Qwen / DeepSeek / Ollama and more, depending on your account's permissions |
| **Streaming output** | Token-by-token rendering, interruptible and retryable |
| **Agentic tool chain** | File read/write, terminal commands, REPL, LSP diagnostics/references, web search, skills and subagents — all invoked by the model on its own |
| **Inline diff & checkpoints** | A diff is generated before any file is modified and can be **Keep** or **Discard**'d; AI edits can also be rolled back in a batch per turn |
| **Plan mode** | Read-only toolchain — scout first, then act, so your repo is never modified by accident |
| **Inline edit / explain selection** | Edit selected code directly with `Ctrl+I`; explain it with `Ctrl+Alt+E` |
| **Subagents & background tasks** | Split long tasks to run in parallel; track progress from the lightbulb panel; scheduled (Cron) tasks supported |
| **Project memory / rules / Skills** | Centralize conventions under `.orbitcode/`, loaded on demand by the model to save tokens |
| **MCP support** | Connect Model Context Protocol servers; their tools are registered automatically |
| **Codebase index** | Local full-text index plus `@codebase` retrieval (optional, no embeddings required) |
| **Bilingual UI** | One-click switch between Chinese and English in the sidebar |

---

## Installation

**Requirement**: VS Code / Cursor **1.80+**

### Option 1: Install from VSIX (recommended)

1. Download the latest `orbitcode-<version>.vsix` from the [**Releases**](https://github.com/alindada/OrbitCode/releases/latest) page (this repository is the release channel).
2. VS Code → Extensions (`Ctrl+Shift+X`) → `…` in the top right → **Install from VSIX…** → select the file.
3. Once the success notification appears in the bottom right, **reload the window**.

Command-line install (replace the version with the one you downloaded):

```bash
code --install-extension orbitcode-1.0.1.vsix
```

### Option 2: Install from the Marketplace

If the extension is published to the Marketplace, just search for **OrbitCode** in the Extensions panel and click **Install**. If it isn't published yet, use the VSIX method above.

---

## Quick Start

1. **Open the sidebar**: click the **OrbitCode** icon in the activity bar; or press `Ctrl+Shift+P` → run **OrbitCode: Open Chat** (you can also use **OrbitCode: Open Chat in Editor Panel** to open a large panel in the editor area).
2. **Sign in**: a sign-in page appears on first use — click **Sign in with browser**, complete the sign-in in your browser, and it returns to the extension automatically. The status bar and **Settings → My Account** show the current account.
3. **Pick a model**: choose a model from the selector above the input box (the list comes from the gateway). Then just ask a question, or let the AI edit code directly in the conversation.

> Type `@` in the input box to reference the currently open file and workspace files; `@codebase` runs a local codebase index search.

---

## Day-to-Day Usage

### Chat and context

- **`@` references**: typing `@` opens a picker (open tabs, workspace files). The selection is attached as context and the host reads its content on demand (size-limited to avoid blowing up the context window).
- **Add selection/file**: `Ctrl+Shift+L`, or right-click in the Explorer/editor and choose **Add to OrbitCode Chat**.
- **Images**: paste or drag in a screenshot; multimodal models will read it.
- **Chat history**: the history button in the top bar, or **OrbitCode: Show Chat History**.

### Plan Mode

When enabled, the model may only use read-only tools (read file, search, LSP, `ask_user`, `todo_write`, etc.) — ideal for "research first, edit later". Toggle it with the tree icon in the top bar or the command **OrbitCode: Toggle Plan Mode**; the status bar icon turns into a lock. You can also simply tell the AI "enter plan mode first".

### Checkpoints and undo

Before writing files, the AI leaves backups in `.orbitcode/checkpoints/`, one checkpoint group per user turn. The **Rollback** button in the top bar reverts all AI edits for a turn in one batch (with a confirmation prompt).

> This is a different axis from the Diff **Keep / Discard**: the diff handles a **single file**, while checkpoint rollback handles an **entire turn of the conversation**.

### Inline edit / explain selection

| Action | Shortcut |
|--------|----------|
| Inline-edit the selected code | `Ctrl+I` (macOS `Cmd+I`) |
| Explain the selected code | `Ctrl+Alt+E` |
| Keep changes (in diff) | `Ctrl+Enter` |
| Discard changes (in diff) | `Ctrl+Shift+Enter` |

### Subagents and background tasks

When the AI tackles a long-running task, it may call `spawn_subagent` to run work in parallel in the background and push a summary back into the main conversation. The **lightbulb** panel in the top bar shows recent subagents and scheduled tasks; you can also ask the AI to run `list_background_tasks`, `cron_create` or `cron_delete` to manage them.

### Project memory, rules and Skills

Under the project root's **`.orbitcode/`** (legacy `.ycode/` and `.claudecode/` are also supported):

| Path | Purpose |
|------|---------|
| `.orbitcode/memory.md` | Project memory: conventions, test commands, past decisions. Let the AI fill it in with `ingest_project_memory` or append notes with `append_project_memory` |
| `.orbitcode/rules/` | Project rules, injected into the system prompt |
| `.orbitcode/skills/*.md` | Skill templates, loaded on demand by the AI via `use_skill` |
| `.orbitcode/mcp.json` | Project-level MCP server configuration |
| `.orbitcode/hooks.json` | Lifecycle hooks for before/after tool calls and subagents |

### MCP (Model Context Protocol)

- Add servers (command, arguments, environment variables, enable/disable) under **Settings → MCP** in the sidebar.
- You can also write to the VS Code setting `orbitcode.mcpServers`, or place `.orbitcode/mcp.json` in the workspace (governed by `orbitcode.enableProjectMcp`, on by default and merged).
- If local context is tight, disable `orbitcode.injectMcpTools` to save the per-request schema overhead.

---

## Common Commands

| Command | Description |
|---------|-------------|
| `OrbitCode: Open Chat` | Open the sidebar chat |
| `OrbitCode: Open Chat in Editor Panel` | Open a large panel in the editor area |
| `OrbitCode: New Chat` / `Show Chat History` | New / past conversations |
| `OrbitCode: Focus Chat Input` | Focus the input box (`Ctrl+L`) |
| `OrbitCode: Inline Edit Selection` | Inline-edit the selection (`Ctrl+I`) |
| `OrbitCode: Explain Selection` | Explain the selection (`Ctrl+Alt+E`) |
| `OrbitCode: Toggle Plan Mode` | Toggle plan mode |
| `OrbitCode: Rebuild Codebase Index` | Rebuild the local codebase index |
| `OrbitCode: Open Output Log` | View the runtime log (Output panel → OrbitCode) |
| `OrbitCode: Sign In / Sign Out` | Sign in / sign out |

If a shortcut conflicts, search for `orbitcode` in **Keyboard Shortcuts** and rebind it.

---

## Settings

Most options can be changed under **Settings** (gear icon) in the sidebar; you can also search for `orbitcode.` in VS Code settings.

| Setting | Default | Description |
|---------|---------|-------------|
| `orbitcode.defaultModel` | empty | Gateway model to use; leave empty for the backend default — you can also switch in the model selector |
| `orbitcode.streaming` | `true` | Streaming output |
| `orbitcode.contextCompression` | `normal` | Compression strength for long chat history (`relaxed` keeps more; `off` only truncates overly long tool output) |
| `orbitcode.confirmDangerousTools` | `true` | Require confirmation before running bash / PowerShell / REPL / rename / screenshot, etc. |
| `orbitcode.preferMcp` | `true` | Prefer MCP tools for external services such as databases |
| `orbitcode.injectMcpTools` | `true` | Inject MCP tool schemas into every request |
| `orbitcode.enableProjectMcp` | `true` | Load the workspace's `.orbitcode/mcp.json` |
| `orbitcode.mcpServers` | `{}` | MCP server configuration |
| `orbitcode.webSearchProvider` | `google` | Web search: `google` (Serper) / `duckduckgo` / `tavily`; falls back to DuckDuckGo when no key is set |
| `orbitcode.serperApiKey` / `orbitcode.tavilyApiKey` | empty | Search keys — best entered in the sidebar settings (stored in the local keyring) |
| `orbitcode.codebaseIndexEnabled` | `false` | Enable the local codebase index and `codebase_search` / `@codebase` |
| `orbitcode.codebaseIndexWatch` | `true` | Update the index automatically on file changes |
| `orbitcode.hooksEnabled` | `true` | Run hooks from `.orbitcode/hooks.json` |
| `orbitcode.subagentMaxParallel` / `MaxTurns` / `TimeoutMinutes` | 3 / 25 / 15 | Subagent concurrency, turn count and timeout |

---

## Data and Privacy

- **Account credentials**: the token issued after sign-in is stored in the VS Code local keyring (`ExtensionContext.secrets`) and never written in plain text to `settings.json`.
- **Search keys** (Serper / Tavily): entering them in the sidebar settings also stores them in the keyring; the settings panel offers a "clear stored key" action. If plain-text configuration is detected after upgrading from an older version, it is migrated to the keyring and the plain text is cleared.
- **Code and conversations**: only sent on demand to the selected gateway/model service when making a request. The local codebase index, checkpoints and project memory are all kept inside your own workspace (`.orbitcode/`).

---

## FAQ

| Symptom | What to do |
|---------|------------|
| Still shown as signed out after signing in | Run **OrbitCode: Sign In with LLM Gateway** again to redo the browser callback; make sure the browser can jump back to VS Code |
| Model list is empty | The gateway must be reachable over the network; retry later or click refresh in the model settings panel |
| Sidebar is blank / commands not found | Incomplete installation package. Uninstall, then reinstall with the official `.vsix` (do not repackage with options that exclude dependencies) |
| Want to undo AI edits | Discard a single file in its diff; use **Rollback** (checkpoints) in the top bar for a whole turn |
| AI ignores project conventions | Put the conventions in `.orbitcode/rules/` or `.orbitcode/memory.md` |
| Troubleshooting | **OrbitCode: Open Output Log**, or select **OrbitCode** in the Output panel |

---

## Feedback

- Bugs and feature requests: file them at **https://github.com/alindada/OrbitCode/issues** — please attach the **OrbitCode** output log (**OrbitCode: Open Output Log**) and reproduction steps.
- Downloads and version history: [**Releases**](https://github.com/alindada/OrbitCode/releases).

## License

[MIT](./LICENSE) © 2026 alindada
