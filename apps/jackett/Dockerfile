# hadolint ignore=DL3007
FROM ghcr.io/k8s-at-home/ubuntu:latest

ARG TARGETPLATFORM
ARG VERSION

# Sets Jackett config directory
# https://github.com/Jackett/Jackett#home-directory-1
ENV XDG_CONFIG_HOME=/config

USER root

# hadolint ignore=DL3008,DL3015,SC2086
RUN \
  apt-get -qq update \
  && \
  apt-get -qq install -y \
    libicu66 \
    libssl1.1 \
  && \
  case "${TARGETPLATFORM}" in \
    'linux/amd64') export ARCH='AMDx64' ;; \
    'linux/arm64') export ARCH='ARM64' ;; \
    'linux/arm/v7') export ARCH='ARM32' ;; \
  esac \
  && \
  curl -fsSL -o /tmp/jackett.tar.gz \
    "https://github.com/Jackett/Jackett/releases/download/v${VERSION}/Jackett.Binaries.Linux${ARCH}.tar.gz" \
  && tar ixzf /tmp/jackett.tar.gz -C /app --strip-components 1 \
  && \
  rm -rf \
    /app/JackettUpdater* \
  && \
  printf "UpdateMethod=docker\nPackageVersion=%s\nPackageAuthor=[Team k8s-at-home](https://github.com/k8s-at-home)" "${VERSION}" > /app/package_info \
  && \
  apt-get autoremove -y \
  && apt-get clean \
  && \
  rm -rf \
    /tmp/* \
    /var/lib/apt/lists/* \
    /var/tmp/ \
  && chmod -R u=rwX,go=rX /app \
  && printf "umask %d" "${UMASK}" >> /etc/bash.bashrc \
  && update-ca-certificates

USER kah

EXPOSE 9117

COPY ./apps/jackett/entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]
