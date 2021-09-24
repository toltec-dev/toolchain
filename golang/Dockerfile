# Go distribution
ARG FROM
FROM $FROM

# Install golang
RUN cd /root \
    && curl --proto '=https' --tlsv1.2 -sSf \
        https://dl.google.com/go/go1.17.1.linux-amd64.tar.gz \
        -o go1.17.1.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go1.17.1.linux-amd64.tar.gz \
    && mkdir go \
    && rm go1.17.1.linux-amd64.tar.gz

# Add go binaries to PATH
ENV PATH="$PATH:/usr/local/go/bin"
ENV GOPATH="/root/go"
