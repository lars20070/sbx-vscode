FROM docker.io/docker/sandbox-templates:claude-code-docker

USER root

RUN curl -Lo vscode.deb 'https://update.code.visualstudio.com/latest/linux-deb-arm64/stable' && \
  apt-get update && apt-get install --yes --no-install-recommends ./vscode.deb && \
  rm ./vscode.deb && \
  rm -rf /var/lib/apt/lists/*

USER agent

RUN code --extensions-dir ~/.vscode-server/extensions/ --install-extension anthropic.claude-code

# SSH to GitHub is blocked inside the sandbox, so rewrite GitHub remotes to HTTPS.
# The first rule covers git@github.com: URLs; the second covers ssh://git@github.com/
# URLs. Both are needed because some repositories use one form and some use the other.
RUN git config --global url."https://github.com/".insteadOf "git@github.com:" && \
  git config --global --add url."https://github.com/".insteadOf "ssh://git@github.com/"

# Liveness check
HEALTHCHECK --interval=30s --timeout=10s --start-period=20s --retries=3 \
  CMD code --version || exit 1