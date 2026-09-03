# Getting Started — Death and Taxes

> Engine: **Unreal Engine 5.8**  |  Language: **C++ + Blueprints**  |  Platform: **Windows (DX12)**

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Unreal Engine | **5.8** | Install via Epic Games Launcher |
| Visual Studio | **2022** (v17+) | Include **Game development with C++** workload |
| Git | **2.x** | [git-scm.com](https://git-scm.com) |
| Git LFS | **3.x** | `git lfs install` — required for binary assets |

---

## Cloning the Repo

```bash
# Clone with LFS (binary assets download automatically)
git clone git@github.com:JosePlascencia/DeathAndTaxes.git
cd DeathAndTaxes
git lfs pull
```

> **Important:** If you skip `git lfs pull`, Unreal will open but all `.uasset` and `.umap` files will be broken pointer stubs. Always pull LFS objects before opening the editor.

---

## First-time Setup

1. **Generate project files**
   Right-click `DeathAndTaxes.uproject` → *Generate Visual Studio project files*

2. **Open the solution**
   Open `DeathAndTaxes.sln` in Visual Studio 2022.

3. **Build**
   Set configuration to `Development Editor | Win64`, then build (`Ctrl+Shift+B`).

4. **Launch the editor**
   Press `F5` in Visual Studio, or double-click `DeathAndTaxes.uproject`.

---

## Submodule — VibeUE Plugin

The project uses [VibeUE](https://github.com/kevinpbuckley/VibeUE) as a submodule on branch `5-8`.

```bash
git submodule update --init --recursive
```

Run this after cloning or pulling if the `Plugins/VibeUE` folder is empty.

---

## LFS Notes

Binary asset types tracked via LFS (see `.gitattributes`):
- `.uasset`, `.umap` — all Unreal Engine assets
- `.fbx`, `.png`, `.tga`, `.wav`, `.mp3` — source art and audio

**GitHub LFS storage limit**: Free accounts get 1 GB. If you hit the limit, a Git LFS data pack ($5/month = 50 GB) is needed before pushing large asset batches.

---

## Branch Strategy

| Branch | Purpose |
|---|---|
| `main` | Stable — always compiles and opens in editor |
| `feature/*` | Individual feature work (Lane A world, Lane B systems) |
| `hotfix/*` | Critical fixes merged directly to main |

Never commit directly to `main`. Open a PR.

---

## Committing

```bash
git add Source/ Config/                 # text files — normal git
git add Content/                        # binary assets — auto-routed to LFS
git commit -m "feat: add extraction zone actor"
git push origin feature/extraction-zone
```

Use [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:` new feature
- `fix:` bug fix
- `chore:` build/config changes
- `content:` Blueprint or asset work
- `docs:` documentation only
