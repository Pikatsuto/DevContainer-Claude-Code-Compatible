# Dev Container compatible avec Claude Code + BMAD-METHOD
# Basé sur l'image base
FROM ghcr.io/pikatsuto/devcontainer-claude-code:latest

LABEL org.opencontainers.image.source="https://github.com/Pikatsuto/DevContainer-Claude-Code-Compatible"
LABEL org.opencontainers.image.description="Dev Container with Claude Code and BMAD-METHOD pre-installed on Debian 13"
LABEL org.opencontainers.image.licenses="MIT"

ARG NODE_VERSION=24

USER root

# Installation de Node.js (requis pour BMAD-METHOD)
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*

USER vscode
WORKDIR /workspaces

CMD ["bash"]
