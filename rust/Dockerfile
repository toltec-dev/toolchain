# Rust nightly targeting the armv7-hf and aarch64 architecture
ARG FROM
FROM $FROM

RUN echo "bumping on 2025-04-28"
# Install rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y -q \
    --default-toolchain none \
		&& rustup toolchain install nightly --profile nightly --target armv7-unknown-linux-gnueabihf \
		&& rustup target add aarch64-unknown-linux-gnu

# Add rust binaries to PATH
ENV PATH="$PATH:/root/.cargo/bin"

# Target reMarkable architecture by default
COPY config /root/.cargo/config
