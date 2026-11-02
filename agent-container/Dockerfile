FROM node:22-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    python3-venv \
    git \
    ca-certificates \
    nano \
    && rm -rf /var/lib/apt/lists/*

# Claude Code refuses --dangerously-skip-permissions when run as root, so
# everything after this point runs as a non-root user.
RUN npm install -g @anthropic-ai/claude-code \
    && useradd -m agent

WORKDIR /workspace
RUN chown -R agent:agent /workspace

USER agent
ENV HOME=/home/agent

CMD ["bash"]
