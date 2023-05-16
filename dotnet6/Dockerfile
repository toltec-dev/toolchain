# Go distribution
ARG FROM
FROM $FROM

# Install dotnet
RUN export DEBIAN_FRONTEND=noninteractive \
    && curl https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb -o packages-microsoft-prod.deb \
    && dpkg -i packages-microsoft-prod.deb \
    && rm packages-microsoft-prod.deb \
    && apt-get update -y \
    && apt install -y dotnet-sdk-6.0 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt
