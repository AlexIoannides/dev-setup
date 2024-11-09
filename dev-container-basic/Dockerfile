# Start with an official Ubuntu image from Canonical 
FROM ubuntu:24.04

# Install APT packages required for core development tools
RUN apt update &&\
    apt install -y build-essential sudo zsh git curl jq &&\
    apt-get clean &&\
    rm -rf /var/lib/apt/lists/*

# Install Go
# --> Relies on `export PATH=/usr/local/go/bin:$PATH` in .zshrc file
RUN curl -sO https://dl.google.com/go/go1.23.2.linux-arm64.tar.gz &&\
    tar -xzvf go1.23.2.linux-arm64.tar.gz -C /usr/local &&\
    rm go1.23.2.linux-arm64.tar.gz

# Install uv and Python
RUN curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/usr/local/bin" sh &&\
    uv python install 3.12 3.13 &&\
    uv python pin 3.12

# Create a new:
# --> give them password-less sudo privilages
# --> create directories for binding local OS directories
RUN useradd --create-home dev &&\
    usermod -aG sudo dev &&\
    echo "dev ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers &&\
    mkdir /home/dev/.ssh &&\
    mkdir /home/dev/workspace

# login as new user and change working directory
USER dev
WORKDIR /home/dev

# Install user specific tools:
# - Oh My Zsh
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

# Copy Zsh shell config
COPY .zshrc .zshrc

# Make the Zsh shell the default command
CMD ["tail", "-f", "/dev/null"]
