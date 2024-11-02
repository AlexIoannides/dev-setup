# Start with an official Ubuntu image from Canonical 
FROM ubuntu:24.04

# Install APT packages required for core development tools
RUN apt update &&\
    apt install -y build-essential zsh git curl jq &&\
    apt-get clean &&\
    rm -rf /var/lib/apt/lists/*

# Install Docker
RUN curl -fsSL https://get.docker.com | sh

# Install Go
# --> Relies on `export PATH=/usr/local/go/bin:$PATH` in .zshrc file
RUN curl -sO https://dl.google.com/go/go1.23.2.linux-arm64.tar.gz &&\
    tar -xzvf go1.23.2.linux-arm64.tar.gz -C /usr/local &&\
    rm go1.23.2.linux-arm64.tar.gz

# Create a new non-root user and switch to them
RUN useradd --create-home dev &&\
    usermod -aG docker dev
USER dev
WORKDIR /home/dev
RUN mkdir .ssh &&\
    mkdir workspace

# Install user specific tools:
# - Oh My Zsh
# - uv + Python
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
RUN curl -LsSf https://astral.sh/uv/install.sh | sh &&\
    export PATH=$HOME/.cargo/bin:$PATH &&\
    uv python install 3.12 3.13 &&\
    uv python pin 3.12

# Copy Zsh shell config
COPY .zshrc .zshrc

# Make the Zsh shell the default command
CMD ["tail", "-f", "/dev/null"]
