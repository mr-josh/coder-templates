FROM ubuntu:22.04

# Common system tools
RUN apt-get update && \
    apt-get upgrade -y && \
    DEBIAN_FRONTEND="noninteractive" apt-get install -y \
    bash \
    build-essential \
    python-is-python3 \
    ca-certificates \
    curl \
    htop \
    locales \
    man \
    software-properties-common \
    sudo \
    unzip \
    vim \
    wget && \
    # Install latest Git using their official PPA
    add-apt-repository ppa:git-core/ppa && \
    DEBIAN_FRONTEND="noninteractive" apt-get install --yes git

# Install Nodejs
RUN curl -fsSL https://deb.nodesource.com/setup_19.x | sudo bash -

RUN sudo apt-get update && \
    sudo apt-get upgrade -y && \
    sudo apt-get install nodejs -y

# Add a user `coder` so that you're not developing as the `root` user
RUN useradd coder \
    --create-home \
    --shell=/bin/bash \
    --uid=1000 \
    --user-group && \
    echo "coder ALL=(ALL) NOPASSWD:ALL" >>/etc/sudoers.d/nopasswd

# Get temp install files for later
ADD https://github.com/mr-josh/coder-templates/raw/main/static/aaronYoung.dark-synthwave-vscode-1.0.3.vsix /tmp/build/aaronYoung.dark-synthwave-vscode-1.0.3.vsix

# Set build files permissions
RUN chmod -R 777 /tmp/build
RUN chown -R coder. /tmp/build

# Add some shell helpers
ADD ./10-coder.sh /etc/profile.d/
RUN chmod u=rw,g=r,o=r /etc/profile.d/10-coder.sh
RUN chown root. /etc/profile.d/10-coder.sh

# Add setup script for easy code desktop setup
ADD ./setup.sh /home/coder/
RUN chmod u=rwx,g=rx,o=r /etc/profile.d/10-coder.sh
RUN chown coder. /home/coder/setup.sh

USER coder

# Install coder server
RUN curl -fsSL https://code-server.dev/install.sh | sh

RUN code-server --install-extension streetsidesoftware.code-spell-checker
RUN code-server --install-extension exodiusstudios.comment-anchors
RUN code-server --install-extension pkief.material-icon-theme
RUN code-server --install-extension esbenp.prettier-vscode
RUN code-server --install-extension /tmp/build/aaronYoung.dark-synthwave-vscode-1.0.3.vsix

# Configure settings
ADD ./settings.json /home/coder/.local/share/code-server/User/settings.json
RUN sudo chown coder. /home/coder/.local/share/code-server/User/settings.json

# Clean up build files
RUN rm -rf /tmp/build