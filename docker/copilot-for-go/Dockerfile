# 使用官方最新版的 Golang 映像檔
FROM golang:latest

# 1. 安裝 Linux 必要工具 (Agent 執行任務所需的手腳)
RUN apt-get update && apt-get install -y \
    ca-certificates \
    curl \
    git \
    jq \
    procps \
    vim \
    build-essential \
    ripgrep \
    fd-find \
    tree \
    universal-ctags \
    iproute2 \
    file \
    && rm -rf /var/lib/apt/lists/*

# 修正 fd 與 rg 的指令別名 (Debian 套件名稱與指令有時不同)
RUN ln -s $(which fdfind) /usr/local/bin/fd

# 2. 依照官方要求，使用 curl 安裝 Copilot CLI
RUN curl -fsSL https://gh.io/copilot-install | bash

# 3. 修正後的別名與權限設定
# 根據你的 help 文件，--yolo 已經是全權限開啟
RUN echo 'alias c="copilot --yolo"' >> /root/.bashrc

# 4. 設定開發環境變數
WORKDIR /workspace

# 預設啟動 Bash
CMD ["bash"]