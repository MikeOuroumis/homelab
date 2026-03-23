FROM kasmweb/ubuntu-jammy-desktop:1.17.0

USER root

# Install Google Chrome
RUN wget -q -O /tmp/chrome.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb && \
    apt-get update && \
    apt-get install -y /tmp/chrome.deb || true && \
    apt-get install -fy && \
    rm /tmp/chrome.deb

# Install Node.js 22 (needed for Claude Code and OpenClaw)
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs

# Install Claude Code
RUN npm install -g @anthropic-ai/claude-code

# Install OpenClaw
RUN npm install -g openclaw

# Install common dev tools
RUN apt-get install -y \
    git \
    curl \
    wget \
    vim \
    htop \
    socat \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Auto-start script: start OpenClaw gateway + socat forwarder on boot
RUN printf '#!/bin/bash\n\
# Start OpenClaw gateway\n\
nohup openclaw gateway run > /tmp/openclaw-gateway.log 2>&1 &\n\
# Wait for gateway, then forward port\n\
(while true; do\n\
  if ss -tlnp 2>/dev/null | grep -q 127.0.0.1:18789; then\n\
    socat TCP-LISTEN:18790,fork,reuseaddr,bind=0.0.0.0 TCP:127.0.0.1:18789 &\n\
    break\n\
  fi\n\
  sleep 2\n\
done) &\n' > /home/kasm-default-profile/.openclaw-autostart.sh && \
    chmod +x /home/kasm-default-profile/.openclaw-autostart.sh

# Add autostart to xstartup
RUN mkdir -p /home/kasm-default-profile/.vnc && \
    printf '#!/bin/bash\n/home/kasm-user/.openclaw-autostart.sh &\n' \
    > /home/kasm-default-profile/.vnc/user-startup.sh && \
    chmod +x /home/kasm-default-profile/.vnc/user-startup.sh

# Create desktop shortcut for Chrome (in default profile so it gets copied on first start)
RUN mkdir -p /home/kasm-default-profile/Desktop && \
    printf '[Desktop Entry]\nVersion=1.0\nName=Google Chrome\nExec=/usr/bin/google-chrome-stable --no-sandbox %%U\nIcon=google-chrome\nType=Application\nCategories=Network;WebBrowser;\n' \
    > /home/kasm-default-profile/Desktop/google-chrome.desktop && \
    chmod +x /home/kasm-default-profile/Desktop/google-chrome.desktop

USER 1000
