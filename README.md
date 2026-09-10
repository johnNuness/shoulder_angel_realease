# Shoulder Angel 

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/6e66184a-de29-4277-98f4-7907606e5540" />


**Pair editing and approval facade for VS Code language model tools.**

Shoulder Angel turns AI agent file edits into real pair programming: instead of an agent writing directly to disk, every change is submitted as a proposal and opened as an editable side-by-side diff for you to review, adjust, and approve — or reject — before anything touches your files.

This repository hosts **only the packaged, ready-to-install artifact (`.vsix`)** for manual installation. It is distributed here **free of charge**. The extension's source code is maintained in a separate, private repository.

📦 **[Browse all releases](https://github.com/johnNuness/shoulder_angel_realease/releases)**

---

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

> ⚠️ **Maintainer note:** the command above always points at `shoulder-angel.vsix` under `/releases/latest/download/`. GitHub's "latest" alias resolves by exact asset filename, so every release must attach the package under that **same, unversioned** filename (`shoulder-angel.vsix`) for this link to keep working across versions — do not name the asset with the version number embedded.

### Option B — Manual download

1. Go to the [**Releases**](https://github.com/johnNuness/shoulder_angel_realease/releases) page and download the `.vsix` asset from the latest release.
2. In VS Code, open the Command Palette (`Ctrl+Shift+P`) and run **Extensions: Install from VSIX...**.
3. Select the downloaded file and reload the window when prompted.

---

## Staying up to date

Since this extension isn't distributed through the VS Code Marketplace, it checks this repository's releases in the background (at most once every 24 hours) and notifies you in-editor when a newer version is published here, with a direct download link. You can also trigger a check anytime via the **Shoulder Angel: Verificar Atualizações** command in the Command Palette.

To reinstall over an existing version, just re-run the install command above — `code --install-extension` upgrades in place.

---

## Requirements

- VS Code `^1.95.0` or later.

---

## License

**This artifact is provided free of charge.**

Shoulder Angel is proprietary software: this repository distributes only the compiled, packaged extension (`.vsix`) for free personal and commercial use as an installed VS Code extension.

You are free to:
- Download and install this artifact at no cost.
- Use it in personal and commercial projects.

You may **not**, without prior written authorization from the copyright holder:
- Redistribute, sell, or sublicense this artifact.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY ARISING FROM THE USE OF THIS SOFTWARE.

See [LICENSE](./LICENSE) for the full text.

Copyright (c) 2026 Shoulder Angel Contributors.
