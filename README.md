# DevContainer Claude Code Compatible

Dev Container templates with Claude Code pre-installed on Debian 13 (Trixie).

## Available Templates

| Template | Image | Description |
|----------|-------|-------------|
| **base** | `ghcr.io/pikatsuto/devcontainer-claude-code:latest` | Claude Code only |
| **base-with-bmad** | `ghcr.io/pikatsuto/devcontainer-claude-code-bmad:latest` | Claude Code + Node.js + BMAD-METHOD |

## Features

- **Debian 13 (Trixie) base**: Lightweight and up-to-date image
- **Claude Code pre-installed**: Ready to use out of the box
- **Shared authentication**: Reuses your host's Claude Code and SSH credentials
- **Analysis tools included**: ripgrep, fd, jq, tree, etc.
- **Multi-architecture**: Supports amd64 and arm64
- **Auto-update**: Daily rebuild if changes detected

## Usage

### Option 1: Copy the devcontainer folder

Copy the desired template folder from `.devcontainer/` to your project:

```bash
# For base template
cp -r .devcontainer/base /your-project/.devcontainer

# For BMAD template
cp -r .devcontainer/base-with-bmad /your-project/.devcontainer
```

### Option 2: Use the image directly

Create a `.devcontainer/devcontainer.json` in your project:

```json
{
  "name": "Claude Code Dev Container",
  "image": "ghcr.io/pikatsuto/devcontainer-claude-code:latest",
  "mounts": [
    "source=${localEnv:HOME}/.claude,target=/home/vscode/.claude,type=bind,consistency=cached",
    "source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,type=bind,readonly"
  ],
  "remoteUser": "vscode"
}
```

### Option 3: Extend the image

Create your own Dockerfile based on this image:

```dockerfile
FROM ghcr.io/pikatsuto/devcontainer-claude-code:latest

# Add your additional packages
RUN sudo apt-get update && sudo apt-get install -y \
    python3 \
    python3-pip \
    && sudo rm -rf /var/lib/apt/lists/*
```

## Requirements

### Claude Code Authentication

Authentication is shared from your host machine via the `~/.claude` mount.

**Before using the container**, authenticate on your host machine:

```bash
curl -fsSL https://claude.ai/install.sh | bash
claude login
```

The container will automatically reuse this authentication.

### SSH Keys (for git push)

Your SSH keys are mounted read-only from `~/.ssh` for GitHub/GitLab access.

### GitHub Package Permissions

To use the image, make sure the package is public or you are authenticated:

```bash
echo $GITHUB_TOKEN | docker login ghcr.io -u USERNAME --password-stdin
```

## Structure

```
.
├── .devcontainer/
│   ├── base/
│   │   ├── Dockerfile
│   │   ├── devcontainer.json
│   │   └── devcontainer-template.json
│   └── base-with-bmad/
│       ├── Dockerfile
│       ├── devcontainer.json
│       └── devcontainer-template.json
├── .github/
│   └── workflows/
│       └── build-container.yml
├── devcontainer-collection.json
└── README.md
```

## Automatic Updates

The GitHub Action checks every 24 hours for:

- New Claude Code version
- Changes in any Dockerfile

Rebuild only occurs if changes are detected. All templates are built in parallel.

## Image Contents

### Base

| Package     | Description             |
| ----------- | ----------------------- |
| Claude Code | Anthropic CLI           |
| zsh         | Shell with fish-like UX |
| starship    | Modern prompt           |
| gh          | GitHub CLI              |
| git         | Version control         |
| ripgrep     | Fast search             |
| fd-find     | File finder             |
| jq          | JSON parser             |
| tree        | Directory visualization |
| procps      | Process tools (ps, top) |
| lsof        | File/network debugging  |

Zsh is configured with:
- **Autosuggestions**: history-based suggestions
- **Syntax highlighting**: command colorization
- **Starship prompt**: modern prompt with git status
- **Advanced completion**: Tab navigation, case-insensitive
- **Fish-like keybindings**: Ctrl+Space to accept, arrows for history search

### Base with BMAD

All base packages plus:

| Package      | Description                        |
| ------------ | ---------------------------------- |
| Node.js 24   | JavaScript runtime                 |
| BMAD-METHOD  | AI-driven agile development framework |

## License

MIT
