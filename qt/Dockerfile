# Full Qt toolchain for the reMarkable
ARG FROM
FROM $FROM

# Build Qt 5.15.1 targeting armhf
COPY linux-arm-remarkable-g++ /linux-arm-remarkable-g++
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
        python \
    # Build Qt
    && cd /root \
    && git clone https://code.qt.io/qt/qt5.git \
    && cd qt5 \
    && git checkout dd11f01de7416b650b58c13e18146533d764477c \
    && perl init-repository --module-subset=essential,-qtmultimedia,-qttools,qtgraphicaleffects,qtquickcontrols2,qtsvg,qtwebsockets \
    # Setup reMarkable mkspec
    && mv /linux-arm-remarkable-g++ qtbase/mkspecs/devices \
    && cd .. \
    && mkdir qt5-build \
    && cd qt5-build \
    && ../qt5/configure \
        -confirm-license \
        -opensource \
        -prefix /usr \
        -sysroot "$SYSROOT" \
        -hostprefix /usr \
        -device linux-arm-remarkable-g++ \
        -device-option CROSS_COMPILE="$CROSS_COMPILE" \
        -nomake examples \
        -nomake tests  \
        -no-rpath \
        -no-opengl \
        -no-widgets \
        -no-feature-sql \
        -reduce-exports \
    && make \
    && make install \
    && cd .. \
    # Clean up
    && rm -rf qt5 qt5-build \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm \
    && apt-get autoremove -y \
        git \
        python \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add the closed-source libqsgepaper library
RUN curl --proto '=https' --tlsv1.2 -sSf \
        https://toltec-dev.org/thirdparty/lib/libqsgepaper-5.15.a \
        -o "$SYSROOT/usr/lib/libqsgepaper.a" \
    && curl --proto '=https' --tlsv1.2 -sSf \
        https://toltec-dev.org/thirdparty/include/epframebuffer.h \
        -o "$SYSROOT/usr/include/epframebuffer.h"
