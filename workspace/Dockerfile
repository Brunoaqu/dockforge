###########################################################################
# Image Setup
###########################################################################

FROM phusion/baseimage:focal-1.0.0

LABEL maintainer="Bruno Aquino <bruno.gl.aquino@gmail.com>"

# Set Environment Variable
ENV DEBIAN_FRONTEND noninteractive

# Start as root
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

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

# always run apt update when start and after add new source list, then clean up at end.
#RUN set -xe; \
#    apt-get update -yqq \
#    groupadd -g ${PGID} dockforge && \
#    useradd -l -u ${PGID} -g dockforge -m dockforge -G docker_env && \
#    usermod -p "*" dockforge -s /bin/bash

###########################################################################
# Final Touch:
###########################################################################

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm -f /var/log/lastlog /var/log/faillog

# Set default work directory
WORKDIR /usr/app
