# Rust nightly targeting the armv7-hf and aarch64 architectures
ARG FROM
FROM $FROM

RUN echo "bumping on 2025-04-28"
# Install rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y -q \
    --default-toolchain none \
    && /root/.cargo/bin/rustup toolchain install nightly --profile minimal --target armv7-unknown-linux-gnueabihf \
    && /root/.cargo/bin/rustup target add aarch64-unknown-linux-gnu

# Add rust binaries to PATH
ENV PATH="$PATH:/root/.cargo/bin"

# Target reMarkable architecture by default
COPY config /root/.cargo/config
