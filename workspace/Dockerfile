###########################################################################
# Image Setup
###########################################################################

FROM phusion/baseimage:focal-1.0.0

LABEL maintainer="Bruno Aquino <bruno.gl.aquino@gmail.com>"

ENV DEBIAN_FRONTEND noninteractive

USER root

###########################################################################
# Set Timezone
###########################################################################

ARG TZ=UTC
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

###########################################################################
# Non-root user:
###########################################################################

ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN set -xe && \
  apt-get update -yqq && \
  groupadd -g ${PGID} dockforge && \
  useradd -l -u ${PUID} -g dockforge -m dockforge -G docker_env && \
  usermod -p "*" dockforge -s /bin/bash

###########################################################################
# Node / NVM:
###########################################################################
ARG NODE_VERSION=node
ENV NODE_VERSION ${NODE_VERSION}
ARG INSTALL_NODE=false
ARG INSTALL_NPM_CHECK_UPDATES_CLI=false

ARG NPM_REGISTRY
ENV NPM_REGISTRY ${NPM_REGISTRY}
ARG NPM_FETCH_RETRIES
ENV NPM_FETCH_RETRIES ${NPM_FETCH_RETRIES}
ARG NPM_FETCH_RETRY_FACTOR
ENV NPM_FETCH_RETRY_FACTOR ${NPM_FETCH_RETRY_FACTOR}
ARG NPM_FETCH_RETRY_MINTIMEOUT
ENV NPM_FETCH_RETRY_MINTIMEOUT ${NPM_FETCH_RETRY_MINTIMEOUT}
ARG NPM_FETCH_RETRY_MAXTIMEOUT
ENV NPM_FETCH_RETRY_MAXTIMEOUT ${NPM_FETCH_RETRY_MAXTIMEOUT}
ENV NVM_DIR /home/dockforge/.nvm
ARG NVM_NODEJS_ORG_MIRROR
ENV NVM_NODEJS_ORG_MIRROR ${NVM_NODEJS_ORG_MIRROR}

RUN if [ ${INSTALL_NODE} = true ]; then \
  mkdir -p $NVM_DIR && \
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install ${NODE_VERSION} \
    && nvm use ${NODE_VERSION} \
    && nvm alias ${NODE_VERSION} \
    && npm cache clear --force \
    && npm config set fetch-retries ${NPM_FETCH_RESOURCES} \
    && npm config set fetch-retry-factor ${NPM_FETCH_RETRY_FACTOR} \
    && npm config set fetch-retry-mintimeout ${NPM_FETCH_RETRY_MINTIMEOUT} \
    && npm config set fetch-retry-maxtimeout ${NPM_FETCH_RETRY_MAXTIMEOUT} \
    && corepack enable pnpm \
    && if [ ${INSTALL_NPM_CHECK_UPDATES_CLI} = true ]; then \
    npm install -g npm-check-updates \
    && if [ ${INSTALL_PNPM} = true ]; then \
    corepack use pnpm@latest \
    ;fi \
;fi

RUN if [ ${INSTALL_NODE} = true ]; then \
  echo "" >> ~/.bashrc && \
  echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
  echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"' >> ~/.bashrc \
;fi

RUN if [ ${INSTALL_NODE} = true ]; then \
  echo "" >> ~/.bashrc && \
  echo 'export NVM_DIR="/home/dockforge/.nvm"' >> ~/.bashrc && \
  echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"' >> ~/.bashrc \
;fi

###########################################################################
# Golang :
###########################################################################
ARG INSTALL_GOLANG=true

RUN if [ ${INSTALL_GOLANG} = true ]; then \
  curl -o /usr/local/go.tar.gz -L https://go.dev/dl/go1.23.2.linux-amd64.tar.gz && \
  tar -xfz /usr/local/go.tar.gz -C /usr/local/go \
;fi

RUN if [ ${INSTALL_GOLANG} = true]; then \
  echo "" >> ~/.bashrc && \
  echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc \
;fi

###########################################################################
# Final Touch:
###########################################################################

USER root

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm -f /var/log/lastlog /var/log/faillog

WORKDIR /usr/app
