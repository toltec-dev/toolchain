# Full Qt toolchain for the reMarkable
ARG FROM
FROM $FROM AS qt6base

# Install build dependencies
RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
    libudev-dev

# Build Qt
RUN cd /root \
    && git clone https://code.qt.io/qt/qt5.git qt6 \
    && cd qt6 \
    && git switch 6.7.3 \
    && GIT_ASKPASS="/bin/echo" perl init-repository --module-subset=qtbase,qtshadertools,qtdeclarative

COPY linux-arm-remarkable-g++ /root/qt6/qtbase/mkspecs/devices/linux-arm-remarkable-g++
COPY linux-aarch64-remarkable-g++ /root/qt6/qtbase/mkspecs/devices/linux-aarch64-remarkable-g++

# Build host
RUN mkdir qt6-build \
    && cd qt6-build \
    && /root/qt6/configure \
    -prefix /usr \
    -no-opengl \
    -no-widgets \
    -no-feature-sql \
    -- -DQT_BUILD_EXAMPLES=OFF \
    -DQT_BUILD_TESTS=OFF \
    && cmake --build . --parallel $(nproc) \
    && cmake --install .

FROM qt6base AS arm

# Build arm
RUN rm -r qt6-build \
    && mkdir qt6-build \
    && cd qt6-build \
    && bash -c 'source /opt/x-tools/switch-arm.sh \
    && /root/qt6/configure \
    -prefix /usr \
    -extprefix $SYSROOT/usr \
    -device linux-arm-remarkable-g++ \
    -device-option CROSS_COMPILE="$CROSS_COMPILE" \
    -no-opengl \
    -no-widgets \
    -no-feature-sql \
    -reduce-exports \
    -- -DQT_HOST_PATH=/usr \
    -DQT_BUILD_EXAMPLES=OFF \
    -DQT_BUILD_TESTS=OFF \
    -DCMAKE_TOOLCHAIN_FILE=/usr/share/cmake/arm-linux-gnueabihf.cmake \
    && cmake --build . --parallel $(nproc)'

FROM qt6base AS aarch64

# Build aarch64
RUN rm -r qt6-build \
    && mkdir qt6-build \
    && cd qt6-build \
    && bash -c 'source /opt/x-tools/switch-aarch64.sh \
    && /root/qt6/configure \
    -prefix /usr \
    -extprefix $SYSROOT/usr \
    -device linux-aarch64-remarkable-g++ \
    -device-option CROSS_COMPILE="$CROSS_COMPILE" \
    -no-rpath \
    -no-opengl \
    -no-widgets \
    -no-feature-sql \
    -reduce-exports \
    -- -DQT_HOST_PATH=/usr \
    -DQT_BUILD_EXAMPLES=OFF \
    -DQT_BUILD_TESTS=OFF \
    -DCMAKE_TOOLCHAIN_FILE=/usr/share/cmake/aarch64-remarkable-linux-gnu.cmake \
    && cmake --build . --parallel $(nproc)'

FROM $FROM AS final

RUN --mount=target=/root/qt6,from=qt6base,src=/root/qt6 \
    --mount=target=qt6-host,from=qt6base,src=qt6-build \
    --mount=target=qt6-arm,from=arm,src=qt6-build \
    --mount=target=qt6-aarch64,from=aarch64,src=qt6-build \
    # Install build dependenciesh
    export DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
    libudev-dev \
    && cp -r qt6-host qt6-build \
    && cd qt6-build \
    && cmake --install . \
    && cd .. \
    && rm -r qt6-build \
    && cp -r qt6-arm qt6-build \
    && bash -c 'source /opt/x-tools/switch-arm.sh \
    && cd qt6-build \
    && cmake --install . \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm' \
    && rm -r qt6-build \
    && cp -r qt6-aarch64 qt6-build \
    && bash -c 'source /opt/x-tools/switch-aarch64.sh \
    && cd qt6-build \
    && cmake --install . \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm' \
    && rm -r qt6-build \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt
