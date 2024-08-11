FROM python:3.11-bookworm

# Install Tini
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini.asc /tini.asc
RUN gpg --batch --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7 \
 && gpg --batch --verify /tini.asc /tini
RUN chmod +x /tini

# I don't intent to remove apt cache.
# If want add '&& apt-get clean && rm -rf /var/lib/apt/lists/*'
RUN apt-get update && apt-get install -y \
      git \
      curl \
      build-essential \
      openssh-server \
      sudo \
      locales

RUN useradd -rm -d /home/ansibleuser1 -s /bin/bash -U ansibleuser1 && \
      echo 'ansibleuser1:ansibleuser1' | chpasswd && \
      systemctl enable ssh && \
      mkdir /run/sshd && \
      echo "ansibleuser1 ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers && \
      localedef -i en_US -f UTF-8 en_US.UTF-8

RUN mkdir -m 700 /home/ansibleuser1/.ssh && \
      chown ansibleuser1:ansibleuser1 /home/ansibleuser1/.ssh

COPY --chown=ansibleuser1:ansibleuser1 --chmod=600 ../ssh-rsa/authorized_keys /home/ansibleuser1/.ssh/authorized_keys

COPY ../ssh-rsa /ssh-rsa

EXPOSE 22

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8 \
    TZ=UTC

ENTRYPOINT ["/tini", "--"]
CMD ["/usr/sbin/sshd", "-D"]

STOPSIGNAL SIGTERM