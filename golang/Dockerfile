# Go distribution
ARG FROM
FROM $FROM

# Install rustup
RUN cd /root \
    && curl --proto '=https' --tlsv1.2 -sSf https://golang.org/dl/go1.16.2.linux-amd64.tar.gz \
    -o "go1.16.2.linux-amd64.tar.gz" \
    && tar -C /usr/local -xzf "go1.16.2.linux-amd64.tar.gz" \
    && mkdir -p ./go

# Add go binaries to PATH
ENV PATH="$PATH:/usr/local/go/bin"
ENV GOPATH="/root/go"