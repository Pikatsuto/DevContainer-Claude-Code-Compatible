# Claude Code compatible Dev Container
# Base: Debian 13 (Trixie)
FROM debian:trixie-slim

LABEL org.opencontainers.image.source="https://github.com/Pikatsuto/DevContainer-Claude-Code-Compatible"
LABEL org.opencontainers.image.description="Dev Container with Claude Code pre-installed on Debian 13"
LABEL org.opencontainers.image.licenses="MIT"

# Configuration arguments
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/home/vscode/.local/bin:$PATH"

# Install: Claude Code dependencies + analysis and debug tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    # Required for Claude Code
    ca-certificates \
    curl \
    git \
    sudo \
    openssh-client \
    # Shell and plugins
    zsh \
    zsh-autosuggestions \
    zsh-syntax-highlighting \
    # Analysis and debug tools
    ripgrep \
    fd-find \
    jq \
    less \
    procps \
    lsof \
    file \
    tree \
    && rm -rf /var/lib/apt/lists/*

# Install GitHub CLI
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
    && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && apt-get update \
    && apt-get install -y gh \
    && rm -rf /var/lib/apt/lists/*

# Install Starship (modern prompt)
RUN curl -fsSL https://starship.rs/install.sh | sh -s -- -y

# Create non-root user with zsh
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/zsh $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Install Claude Code
RUN curl -fsSL https://claude.ai/install.sh | bash

# Prepare Claude Code directories
RUN mkdir -p /home/$USERNAME/.claude && \
    chown -R $USERNAME:$USERNAME /home/$USERNAME/.claude

# Configure zsh for user
RUN cat <<'EOF' > /home/$USERNAME/.zshrc
# History
HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000
setopt SHARE_HISTORY
setopt HIST_IGNORE_DUPS
setopt HIST_IGNORE_SPACE

# Fish-style navigation
setopt AUTO_CD
setopt AUTO_PUSHD
setopt PUSHD_IGNORE_DUPS
setopt CORRECT

# Advanced completion
autoload -Uz compinit && compinit
zstyle ':completion:*' menu select
zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'
zstyle ':completion:*' list-colors "${(s.:.)LS_COLORS}"

# Plugins (autosuggestions + syntax highlighting)
source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# Suggestion color (light gray)
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=244'

# Fish-style keybindings
bindkey '^[[A' history-search-backward
bindkey '^[[B' history-search-forward
bindkey '^[[1;5C' forward-word
bindkey '^[[1;5D' backward-word
bindkey '^[[H' beginning-of-line
bindkey '^[[F' end-of-line
bindkey '^[[3~' delete-char
bindkey '^ ' autosuggest-accept

# Useful aliases
alias ls='ls --color=auto'
alias ll='ls -lah'
alias la='ls -A'
alias l='ls -CF'
alias grep='grep --color=auto'
alias fd='fdfind'

# Starship prompt
eval "$(starship init zsh)"
EOF

# Configure Starship (minimal informative theme)
RUN mkdir -p /home/$USERNAME/.config && cat <<'EOF' > /home/$USERNAME/.config/starship.toml
# Minimal fish-style prompt
format = """
$directory\
$git_branch\
$git_status\
$character"""

[directory]
style = "cyan bold"
truncation_length = 3
truncate_to_repo = true

[git_branch]
symbol = " "
style = "purple"

[git_status]
style = "red bold"
format = '([$all_status$ahead_behind]($style) )'

[character]
success_symbol = "[❯](green)"
error_symbol = "[❯](red)"
EOF

RUN chown -R $USERNAME:$USERNAME /home/$USERNAME/.zshrc /home/$USERNAME/.config

USER $USERNAME
WORKDIR /workspaces

CMD ["zsh"]
