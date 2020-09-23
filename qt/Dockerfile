# Full Qt toolchain for the reMarkable
ARG BASE
FROM $BASE

# Build Qt 5.11.3 targeting armhf
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
    && git checkout 1deea3debce6bfd0fd45545b3a8e1de80380a9a5 \
    && perl init-repository --module-subset=default,-qtwebengine \
    # Create a qmake configuration for the current setup
    && cp -R qtbase/mkspecs/linux-arm-gnueabi-g++ \
             qtbase/mkspecs/linux-arm-gnueabihf-g++ \
    && sed -i 's/arm-linux-gnueabi-/arm-linux-gnueabihf-/' \
              qtbase/mkspecs/linux-arm-gnueabihf-g++/qmake.conf \
    # Fix compiling error of old Qt with recent GCC
    # (see <https://bugreports.qt.io/browse/QTBUG-74196>)
    && find . -type f \( -name "*.cpp" -o -name "*.h" \) -print0 | xargs -0 sed -ri 's/asm volatile ?\(/asm (/g' \
    && cd .. \
    && mkdir qt5-build \
    && cd qt5-build \
    && ../qt5/configure \
        -confirm-license \
        -opensource \
        # Place armhf libraries in the cross-compiling toolchain folder
        -prefix "$SYSROOT/usr" \
        -xplatform linux-arm-gnueabihf-g++ \
        # Place x86 binaries (e.g. qmake) and libraries in the normal folder
        -hostprefix /usr \
        -platform linux-g++ \
        # Disable unused features
        -nomake examples \
        -nomake tests  \
        -no-opengl \
        -reduce-exports \
    && make \
    && make install \
    && cd .. \
    # Clean up
    && rm -rf qt5 qt5-build \
    && apt-get autoremove -y \
        git \
        python \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add the closed-source libqsgepaper library
RUN curl --proto '=https' --tlsv1.2 -sSf \
        https://toltec.delab.re/thirdparty/lib/libqsgepaper.a \
        -o "$SYSROOT/usr/lib/libqsgepaper.a" \
    && curl --proto '=https' --tlsv1.2 -sSf \
        https://toltec.delab.re/thirdparty/include/epframebuffer.h \
        -o "$SYSROOT/usr/include/epframebuffer.h"
