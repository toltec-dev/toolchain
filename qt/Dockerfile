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
        git \
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
    && cmake --build . --parallel $(nproc) \
    && cmake --install . \
    && cd .. \
    # Clean up
    && rm -rf qt5-build \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm \
    # Now Build AArch64 
		&& bash -c 'source /opt/x-tools/switch-aarch64.sh \
		&& mkdir qt5-build \
		&& cd qt5-build \
		&& ../qt5/configure \
        -confirm-license \
        -opensource \
        -prefix /usr \
        -sysroot "$SYSROOT" \
        -hostprefix /usr \
        -device linux-aarch64-remarkable-g++ \
        -device-option CROSS_COMPILE="$CROSS_COMPILE" \
        -nomake examples \
        -nomake tests  \
        -no-rpath \
        -no-opengl \
        -no-widgets \
        -no-feature-sql \
        -reduce-exports \
		&& cmake --build . --parallel $(nproc) \
		&& cmake --install . \
		&& cd .. \
    # Clean up
    && rm -rf qt5-build \
		&& find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm' \
    && apt-get autoremove -y \
        git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Add the closed-source libqsgepaper library
#RUN curl --proto '=https' --tlsv1.2 -sSf \
#       https://toltec-dev.org/thirdparty/lib/libqsgepaper-5.15-v2.a \
#       -o "$SYSROOT/usr/lib/libqsgepaper.a" \
#   && echo "acd55568a81e7b67bf9c8bc9b55f918373554b236ca1da58bd8ac663e0a9ab47 $SYSROOT/usr/lib/libqsgepaper.a" | sha256sum -c \
#   && curl --proto '=https' --tlsv1.2 -sSf \
#       https://toltec-dev.org/thirdparty/include/epframebuffer.h \
#       -o "$SYSROOT/usr/include/epframebuffer.h" \
#   && echo "c28e9ff825f67766ae86b57859377aa0111622030a029cfc53c6926a7063aa7f $SYSROOT/usr/include/epframebuffer.h" | sha256sum -c
