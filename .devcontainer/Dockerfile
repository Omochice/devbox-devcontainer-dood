FROM debian:12.12

RUN apt-get update -y \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        sudo \
    && rm -rf /var/lib/apt/lists/*

ARG DEVBOX_VERSION=0.16.0
RUN ARCH=$(uname -m | sed "s/aarch64/arm64/" | sed "s/x86_64/amd64/") \
    && curl -fsSL -o /devbox.tar.gz https://github.com/jetify-com/devbox/releases/download/${DEVBOX_VERSION}/devbox_${DEVBOX_VERSION}_linux_${ARCH}.tar.gz \
    && tar xf /devbox.tar.gz \
    && chmod +x /devbox \
    && mv /devbox /usr/local/bin/ \
    && rm /devbox.tar.gz \
    && yes | devbox cache info

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL >/etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && chown -R $USERNAME /nix
USER $USERNAME

WORKDIR /tmp/devbox
RUN mkdir -p /tmp/devbox && chown ${USERNAME}:${USERNAME} /tmp/devbox
COPY --chown=${USERNAME}:${USERNAME} devbox.json devbox.json
COPY --chown=${USERNAME}:${USERNAME} devbox.lock devbox.lock

RUN devbox shellenv >>/home/${USERNAME}/.bashrc
