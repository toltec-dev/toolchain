# Full Qt toolchain for the reMarkable
ARG FROM
FROM $FROM

# Build Qt 5.15.1 targeting armhf
COPY linux-arm-remarkable-g++ /linux-arm-remarkable-g++
COPY linux-aarch64-remarkable-g++ /linux-aarch64-remarkable-g++
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
    libudev-dev \
    # Build Qt
    && cd /root \
    && git clone https://code.qt.io/qt/qt5.git \
    && cd qt5 \
    && git switch 6.7.3 \
    && GIT_ASKPASS="/bin/echo" perl init-repository --module-subset=qtbase,qtshadertools,qtdeclarative \
    # Setup reMarkable mkspec
    && mv /linux-arm-remarkable-g++ qtbase/mkspecs/devices \
    && mv /linux-aarch64-remarkable-g++ qtbase/mkspecs/devices \
    && cd .. \
    # Build for host machine (required from Qt6+)
    && bash -c  'source /opt/x-tools/switch-aarch64.sh \
    && mkdir qt5-build \
    && cd qt5-build \
    && unset SYSROOT \
    && unset PKG_CONFIG_SYSROOT_DIR \
    && ../qt5/configure \
    -prefix /usr \
    -no-opengl \
    -no-widgets \
    -no-feature-sql \
    -- -DQT_BUILD_EXAMPLES=OFF \
    -DQT_BUILD_TESTS=OFF \
    && cmake --build . --parallel $(nproc) \
    && cmake --install . \
    && cd .. \
    # Clean up
    && rm -rf qt5-build \
    # Build for armhf
    && export SYSROOT=/opt/x-tools/arm-remarkable-linux-gnueabihf/arm-remarkable-linux-gnueabihf/sysroot \
    && export PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
    && mkdir qt5-build \
    && cd qt5-build \
    && ../qt5/configure \
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
    && cmake --build . --parallel $(nproc) \
    && cmake --install . \
    && cd .. \
    # Clean up
    && rm -rf qt5-build \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm' \
    # Now Build AArch64 
    && bash -c 'source /opt/x-tools/switch-aarch64.sh \
    && mkdir qt5-build \
    && cd qt5-build \
    && ../qt5/configure \
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
    && cmake --build . --parallel $(nproc) \
    && cmake --install . \
    && cd .. \
    && rm -rf qt5-build \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm' \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

