# Dev Container compatible avec Claude Code
# Base: Debian 13 (Trixie)
FROM debian:trixie-slim

LABEL org.opencontainers.image.source="https://github.com/Pikatsuto/DevContainer-Claude-Code-Compatible"
LABEL org.opencontainers.image.description="Dev Container with Claude Code pre-installed on Debian 13"
LABEL org.opencontainers.image.licenses="MIT"

# Arguments pour la configuration
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG NODE_VERSION=24

ENV DEBIAN_FRONTEND=noninteractive

# Installation: Claude Code + outils d'analyse et debug
RUN apt-get update && apt-get install -y --no-install-recommends \
    # Requis pour Claude Code
    ca-certificates \
    curl \
    git \
    sudo \
    # Outils d'analyse et debug
    ripgrep \
    fd-find \
    jq \
    less \
    procps \
    lsof \
    file \
    tree \
    && rm -rf /var/lib/apt/lists/*

# Installation de Node.js
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*

# Création de l'utilisateur non-root
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Configuration npm global pour l'utilisateur
ENV NPM_CONFIG_PREFIX=/home/$USERNAME/.npm-global
ENV PATH=$NPM_CONFIG_PREFIX/bin:$PATH

# Installation de Claude Code
RUN npm install -g @anthropic-ai/claude-code && \
    npm cache clean --force

# Préparer les répertoires Claude Code
RUN mkdir -p /home/$USERNAME/.claude && \
    chown -R $USERNAME:$USERNAME /home/$USERNAME/.claude && \
    chown -R $USERNAME:$USERNAME /home/$USERNAME/.npm-global

USER $USERNAME
WORKDIR /workspaces

CMD ["bash"]