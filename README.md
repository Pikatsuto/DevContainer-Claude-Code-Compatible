# DevContainer Claude Code Compatible

Dev Container template with Claude Code pre-installed on Debian 13 (Trixie).

## Features

- **Debian 13 (Trixie) base**: Lightweight and up-to-date image
- **Claude Code pre-installed**: Ready to use out of the box
- **Shared authentication**: Reuses your host's Claude Code credentials
- **Analysis tools included**: ripgrep, fd, jq, tree, etc.
- **Multi-architecture**: Supports amd64 and arm64
- **Auto-update**: Daily rebuild if changes detected

## Usage

### Option 1: Use the image directly

Copy the `.devcontainer/devcontainer.json` file to your project and update the `image` field with your GitHub username:

```json
{
  "image": "ghcr.io/Pikatsuto/devcontainer-claude-code:latest"
}
```

### Option 2: Extend the image

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
claude login
```

The container will automatically reuse this authentication.

### GitHub Package Permissions

To use the image, make sure the package is public or you are authenticated:

```bash
echo $GITHUB_TOKEN | docker login ghcr.io -u USERNAME --password-stdin
```

## Create Your Own Template

1. Use this repository as a template (click "Use this template" button)
2. Replace `Pikatsuto` with your username in these files:
   - `.devcontainer/Dockerfile`: `org.opencontainers.image.source` label
   - `.devcontainer/devcontainer.json`: `image` field
3. Enable GitHub Actions
4. Make the package public in Settings > Packages

## Structure

```
.
├── .devcontainer/
│   ├── Dockerfile          # Docker image: Debian 13 + Claude Code
│   └── devcontainer.json   # VS Code Dev Container configuration
├── .github/
│   └── workflows/
│       └── build-container.yml  # Automatic build every 24h
└── README.md
```

## Automatic Updates

The GitHub Action checks every 24 hours for:

- New Claude Code version
- Changes in the Dockerfile

Rebuild only occurs if changes are detected.

## Image Contents


| Package     | Description             |
| ----------- | ----------------------- |
| Claude Code | Anthropic CLI           |
| git         | Version control         |
| ripgrep     | Fast search             |
| fd-find     | File finder             |
| jq          | JSON parser             |
| tree        | Directory visualization |
| procps      | Process tools (ps, top) |
| lsof        | File/network debugging  |

## License

MIT
