# dotfiles

Personal dotfiles managed by [chezmoi](https://chezmoi.io). Works on any machine — Arch Linux, Ubuntu, macOS, or inside a DevContainer.

> **Related repo:** [DevContainer](https://github.com/AhsanRahat12/DevContainer) — project-specific tooling and dev environment setup.

---

## Overview

This repo manages everything personal and portable:

- **Chezmoi Externals** — downloads the `mise` binary automatically on any machine
- **Global Mise Config** — defines personal tools available everywhere (chezmoi, bat, neovim, etc.)
- **Chezmoi Scripts** — automatically runs `mise install` when the tool list changes
- **Dotfiles** — `.bashrc`, `.vimrc`, and other personal configs

The system is built on two tools:
- [chezmoi](https://chezmoi.io) — manages and applies dotfiles across machines
- [mise](https://mise.jdx.dev) — installs and manages personal tools and binaries

---

## Directory Structure

```
~/.local/share/chezmoi/
├── dot_bashrc                          → ~/.bashrc
├── dot_vimrc                           → ~/.vimrc
├── dot_config/                         → ~/.config/
│   └── mise/
│       └── config.toml                → global mise tools
├── .chezmoiexternals/
│   └── mise.toml                      → downloads mise binary to ~/.local/bin/mise
├── .chezmoiscripts/
│   └── run_onchange_after_install_packages.sh.tmpl  → auto-runs mise install on changes
└── setup                              → DevPod dotfiles hook script
```

> `dot_config/` maps to `~/.config/` on apply. To manage any app config, create a folder for it inside `dot_config/` and chezmoi will place it at the correct path automatically.

**Example — adding a Hyprland config:**
```
dot_config/
├── mise/
│   └── config.toml         → ~/.config/mise/config.toml
└── hypr/
    └── hyprland.conf       → ~/.config/hypr/hyprland.conf
```

---

## How to Use on a New Machine

```bash
# Install chezmoi and apply dotfiles in one command
sh -c "$(curl -fsLS get.chezmoi.io)" -- -b $HOME/.local/bin init --apply git@github.com:AhsanRahat12/dotfiles.git
```

This will:
1. Install chezmoi to `~/.local/bin/`
2. Clone this repo to `~/.local/share/chezmoi/`
3. Run `chezmoi apply` — which downloads mise, places all configs, and installs all global tools automatically

---

## How to Add a New Global Tool

Global tools are personal tools available everywhere, regardless of project or directory.

### Method 1 — Edit the source directly (recommended)

1. Open the global mise config in your chezmoi source:
```bash
chezmoi cd
vim dot_config/mise/config.toml
```

2. Add the tool:
```toml
[tools]
bat = "latest"
chezmoi = "latest"
neovim = "latest"   # ← add new tools here
```

3. Apply and push:
```bash
chezmoi apply
git add .
git commit -m "add neovim to global tools"
git push
```

### Method 2 — Use mise then sync back to chezmoi

1. Add the tool via mise:
```bash
mise use --global neovim
```

2. Check what changed:
```bash
chezmoi diff
```

3. Pull the change back into the chezmoi source:
```bash
chezmoi add ~/.config/mise/config.toml
```

4. Push to GitHub:
```bash
chezmoi cd
git add .
git commit -m "add neovim to global tools"
git push
```

The chezmoi script detects changes and automatically runs `mise install`. To find available tool names, run `mise registry` or visit [mise.jdx.dev](https://mise.jdx.dev).

---

## Two-Tier Tooling System

| Layer | Location | Scope | Examples |
|---|---|---|---|
| **Personal global** | `dot_config/mise/config.toml` (this repo) | You, everywhere | chezmoi, bat, neovim |
| **Project tools** | `mise.toml` in project root | Whole team, project directory only | kubectl, helm, terraform |

Project-specific tooling lives in the [DevContainer repo](https://github.com/AhsanRahat12/DevContainer).
