FROM phusion/baseimage:0.11

# Use baseimage-docker's init system.
CMD ["/sbin/my_init"]

# Packages
RUN apt-get update && apt-get install --no-install-recommends -y \
    sudo \
    gpg \
    pkg-config \
    build-essential \
    curl \
    wget \
    lsb-release \
    add-apt-key \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Node 14.x SDK
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*


RUN ln -s /usr/bin/node /usr/local/bin/node

# Golang 1.13 SDK
RUN curl -sL https://dl.google.com/go/go1.13.linux-amd64.tar.gz | tar -zx -C /usr/local

# Code-Server
RUN apt-get update && apt-get install --no-install-recommends -y \
    bsdtar \
    openssl \
    locales \
    net-tools \
    && rm -rf /var/lib/apt/lists/*

RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8
ENV DISABLE_TELEMETRY true

ENV CODE_VERSION="3.2.0"
RUN curl -sL https://github.com/cdr/code-server/releases/download/${CODE_VERSION}/code-server-${CODE_VERSION}-linux-x86_64.tar.gz | \
                tar --strip-components=1 -zx -C /usr/local/bin # code-server-${CODE_VERSION}-linux-x86_64/code-server

#RUN ln -s /usr/local/bin/code-server /usr/bin/code-server

# Setup User
RUN groupadd -r awesome_people \
    && useradd -m -r pragma -g awesome_people -s /bin/bash \
    && echo "pragma ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd
USER pragma

# Setup User Go Environment
ENV PATH "${PATH}:/usr/local/go/bin:/home/pragma/go/bin"

# Setup User Visual Studio Code Extentions
ENV VSCODE_USER "/home/pragma/.local/share/code-server/User"
ENV VSCODE_EXTENSIONS "/home/pragma/.local/share/code-server/extensions"

RUN mkdir -p ${VSCODE_USER}
COPY --chown=pragma:awesome_people settings.json /home/pragma/.local/share/code-server/User/

# Setup User Visual Studio Code Extentions
ENV VSCODE_USER "/home/pragma/.local/share/code-server/User"
ENV VSCODE_EXTENSIONS "/home/pragma/.local/share/code-server/extensions"

# Setup Go Extension
RUN mkdir -p ${VSCODE_EXTENSIONS}/go \
    && curl -JLs https://marketplace.visualstudio.com/_apis/public/gallery/publishers/ms-vscode/vsextensions/Go/latest/vspackage | bsdtar --strip-components=1 -xf - -C ${VSCODE_EXTENSIONS}/go extension

# Setup User Workspace
RUN mkdir -p /home/pragma/project

WORKDIR /home/pragma/project

EXPOSE 8080

CMD ["code-server", "--disable-telemetry", "--bind-addr=0.0.0.0:8080"]
