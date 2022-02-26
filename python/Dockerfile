# Python distribution
ARG FROM
FROM $FROM

RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && cd /root \
    && apt-get install -y \
        python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt
