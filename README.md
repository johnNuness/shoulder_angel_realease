# Shoulder Angel 

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/6e66184a-de29-4277-98f4-7907606e5540" />

**Pair editing and approval facade for VS Code language model tools.**

Shoulder Angel turns AI agent file edits into real pair programming: instead of an agent writing directly to disk, every change is submitted as a proposal and opened as an **editable side-by-side diff** for you to review, adjust, and approve — or reject — before anything touches your files.

---

## Why

Agent mode is fast, and that is exactly the problem. The agent rewrites ten files, you skim a wall of green and red after the fact, and the only real choices left are *keep everything* or *undo everything*.

Shoulder Angel puts you back in the loop **at the moment the code is written**, not after:

- The agent never writes to disk. It calls `propose_edit` / `propose_create`, and the proposal opens as a diff.
- The right-hand pane of that diff is **a real, writable editor**. Fix the variable name, delete the function you did not ask for, tighten the logic — right there.
- **`Ctrl+S` accepts.** What lands on disk is exactly the text you left in the pane, your edits included.
- **Closing the tab rejects.** Nothing is written; the file on disk is untouched.
- Reject with a reason and the reason goes **back to the model**, which corrects the content and re-proposes it — so the fix comes back as a new diff, not as a paragraph of chat.

The result is a review loop where you are the one holding the pen, and the agent is the one asking permission.

---

## How it works

| Step | What happens |
| --- | --- |
| 1. Agent proposes | The model calls `propose_edit` (existing file) or `propose_create` (new file) with the full file content and a short explanation. |
| 2. Diff opens | A side-by-side comparison appears: current file on the left, proposal on the right. The right side is editable. |
| 3. You decide | Edit freely, then **Accept** (`Ctrl+S` or the ✓ in the tab title bar) or **Reject** (the ✗, or just close the tab). |
| 4. Disk is written — or not | On accept, the file is written atomically (temp file + rename), preserving the file's dominant line endings. On reject, nothing is written and the model is told why. |

Extra safety along the way:

- **Anti-undo guard** — protects against a stray `Ctrl+Z` in the 500 ms before you save.
- **External modification detection** — warns you if the target file changed on disk while the proposal was under review.
- **Accepted with edits** — when you adjust the proposal before saving, the model receives a unified diff of your corrections, so it learns from them in the rest of the session.

---

## Best way to use it

Shoulder Angel only pays off if the agent has **no other way to write**. Three settings, once, per agent profile:

### 1. Enable the Shoulder Angel tools

In the chat tool picker, make sure both tools are checked:

<img width="633" height="71" alt="image" src="https://github.com/user-attachments/assets/9f7ef7ac-8aec-4bf2-afb2-7b48c13b0679" />

### 2. Disable the built-in CreateFile and EditFiles tools

This is the most important step. Deselect the built-in **CreateFile** and **EditFiles** tools so that the only remaining way for the agent to access your files is via a proposal:

<img width="412" height="144" alt="image" src="https://github.com/user-attachments/assets/7ecc5910-8029-468c-b2ee-93d9f6d45095" />


<img width="600" height="160" alt="image" src="https://github.com/user-attachments/assets/46a097bc-bc86-4a16-a803-7bd1e3ad1401" />

Leave `read`, `search`, `execute` and the rest enabled — Shoulder Angel only replaces *writing*, never reading. If the built-in `edit` tool stays on, the agent will happily use it and bypass review entirely.

### 3. Turn on "Allow all" for tool calls

Counter-intuitive, but correct: once writing is gated behind a human-reviewed diff, per-call approval prompts are pure friction. Let the agent run, and review where it actually counts.

<img width="621" height="496" alt="image" src="https://github.com/user-attachments/assets/430a9d4e-6f2b-4073-8979-8ff65a90e1c9" />


> With this combination the agent works at full speed, and every single byte it wants to write still stops in front of you first.

### 4. Tell the agent the rule (Optional)

Add an `AGENTS.md` (or `.github/copilot-instructions.md`) at the root of your project with something like:

> Modify and create files exclusively through the `#propose_edit` and `#propose_create` tools. Do not use native tools that write directly to disk. Always send the **complete** file content, never a patch or snippet. If a proposal is rejected **with a reason**, apply the reason and propose the same file again so the reviewer can see the corrected version.

### Recommended settings

```json
{
  "shoulderAngel.autosave": true,
  "github.copilot.chat.editing.confirmEdit": true,
  "chat.editing.autoAcceptDelay": 0
}
```

---

## Contributions

**Language model tools**

- `propose_edit` — propose replacing the full contents of an existing workspace file.
- `propose_create` — propose creating a new workspace file.

Both can be referenced explicitly in a prompt (`#propose_edit`) or invoked by the model on its own.

**Commands**

- `Accept Proposal` — accept the pending proposal (visible only in a review tab).
- `Reject Proposal` — reject it, with an optional reason sent back to the model.
- `Shoulder Angel: Check for Updates` — check the release repository for a newer version right now.

**Settings**

| Setting | Default | What it does |
| --- | --- | --- |
| `shoulderAngel.autosave` | `true` | Save pending changes to the target file before opening the proposal diff. |
| `shoulderAngel.checkForUpdates` | `true` | Periodically check the distribution repository for a new version. |
| `shoulderAngel.autoUpdate` | `true` | Download and install a newer version automatically, then offer a window reload. When `false`, the update is only announced. |
| `shoulderAngel.checkForPrereleaseUpdates` | `false` | Include pre-release versions in the check. |

---

This repository hosts only the packaged, ready-to-install artifact (`.vsix`) for manual installation. It is distributed here free of charge. The extension's source code is maintained in a separate, private repository.

📦 [Browse all releases](https://github.com/johnNuness/shoulder_angel_realease/releases)

## Install

### Option A — One command (recommended)

Downloads the latest `.vsix` to a temp file and installs it into VS Code in a single step, without opening a browser or handling the download yourself:

```bash
curl -fsSL -o /tmp/shoulder-angel.vsix \
  https://github.com/johnNuness/shoulder_angel_realease/releases/latest/download/shoulder-angel.vsix \
&& code --install-extension /tmp/shoulder-angel.vsix
```

> ℹ️ VS Code's `--install-extension` flag only accepts a local file path or a Marketplace extension ID — it cannot install directly from a URL. This command works around that by downloading the file to a temporary path first and immediately handing it to `code`, so it still takes a single copy-pasted command with no manual download step in the browser.

If you use VS Code Insiders, replace `code` with `code-insiders`.

> ⚠️ **Maintainer note**: the command above always points at `shoulder-angel.vsix` under `/releases/latest/download/`. GitHub's "latest" alias resolves by exact asset filename, so every release must attach the package under that same, unversioned filename (`shoulder-angel.vsix`) for this link to keep working across versions — do not name the asset with the version number embedded.

### Option B — Manual download

1. Go to the [Releases page](https://github.com/johnNuness/shoulder_angel_realease/releases) and download the `.vsix` asset from the latest release.
2. In VS Code, open the Command Palette (`Ctrl+Shift+P`) and run **Extensions: Install from VSIX...**.
3. Select the downloaded file and reload the window when prompted.

## Staying up to date

Since this extension isn't distributed through the VS Code Marketplace, it checks this repository's releases in the background (at most once every 24 hours) and notifies you in-editor when a newer version is published here, with a direct download link. You can also trigger a check anytime via the **Shoulder Angel: Check for Updates** command in the Command Palette.

To reinstall over an existing version, just re-run the install command above — `code --install-extension` upgrades in place.

## Requirements

VS Code `^1.95.0` or later.

## License

This artifact is provided free of charge.

Shoulder Angel is proprietary software: this repository distributes only the compiled, packaged extension (`.vsix`) for free personal and commercial use as an installed VS Code extension.

You are free to:

- Download and install this artifact at no cost.
- Use it in personal and commercial projects.

You may not, without prior written authorization from the copyright holder:

- Redistribute, sell, or sublicense this artifact.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY ARISING FROM THE USE OF THIS SOFTWARE.

See [LICENSE](LICENSE) for the full text.

Copyright (c) 2026 Shoulder Angel Contributors.
