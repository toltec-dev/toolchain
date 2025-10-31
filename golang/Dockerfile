# Go distribution
ARG FROM
FROM $FROM

# Install golang
RUN cd /root \
    && curl --proto '=https' --tlsv1.2 -sSf \
    https://dl.google.com/go/go1.24.2.linux-amd64.tar.gz \
    -o go1.24.2.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go1.24.2.linux-amd64.tar.gz \
    && mkdir go \
    && rm go1.24.2.linux-amd64.tar.gz

# Add go binaries to PATH
ENV PATH="$PATH:/usr/local/go/bin"
ENV GOPATH="/root/go"

RUN echo 'export GOARCH=arm64' >> /opt/x-tools/switch-aarch64.sh \
    && echo 'export GOARCH=armv' >> /opt/x-tools/switch-arm.sh
