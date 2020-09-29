# Essential cross-compiling build tools for the reMarkable
FROM debian:buster

# Install common building tools
RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        cmake \
        curl \
        meson \
        pkg-config \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build crosstool-ng 1.24.0 and use it to build the toolchain
COPY crosstool-ng /arm-remarkable-linux-gnueabihf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y \
        autoconf \
        bison \
        file \
        flex \
        gawk \
        git \
        help2man \
        libncurses-dev \
        libtool-bin \
        texinfo \
        unzip \
    # Build crosstool-ng itself
    && cd /root \
    && git clone https://github.com/crosstool-ng/crosstool-ng \
    && cd crosstool-ng \
    && git checkout b2151f1dba2b20c310adfe7198e461ec4469172b \
    && mv /arm-remarkable-linux-gnueabihf samples \
    && ./bootstrap \
    && ./configure --enable-local \
    && make \
    # Build the toolchain and install it in the following folder
    && export CT_PREFIX="/opt/x-tools" \
    && ./ct-ng arm-remarkable-linux-gnueabihf \
    && ./ct-ng build \
    && cd .. \
    # Clean up
    && rm -rf crosstool-ng \
    && rm /opt/x-tools/arm-remarkable-linux-gnueabihf/build.log.bz2 \
    && apt-get autoremove -y \
        autoconf \
        bison \
        file \
        flex \
        gawk \
        git \
        help2man \
        libncurses-dev \
        libtool-bin \
        texinfo \
        unzip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add cross-compiling tools to PATH and configurations for common build systems
ENV ARCH=arm \
    CHOST="arm-linux-gnueabihf" \
    CROSS_COMPILE="arm-linux-gnueabihf-" \
    PATH="$PATH:/opt/x-tools/arm-remarkable-linux-gnueabihf/bin" \
    PKG_CONFIG_LIBDIR="/opt/x-tools/arm-remarkable-linux-gnueabihf/arm-remarkable-linux-gnueabihf/sysroot/usr/lib/pkgconfig" \
    SYSROOT="/opt/x-tools/arm-remarkable-linux-gnueabihf/arm-remarkable-linux-gnueabihf/sysroot"

COPY meson/arm-linux-gnueabihf /usr/share/meson/cross/arm-linux-gnueabihf
COPY cmake/arm-linux-gnueabihf.cmake /usr/share/cmake/arm-linux-gnueabihf.cmake

# Build libcap 2.25 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
    # Build libcap
    && cd /root \
    && git clone git://git.kernel.org/pub/scm/libs/libcap/libcap.git \
    && cd libcap \
    && git checkout 1048a44a4a6eecb6d43d94913b0bd065ea6d0601 \
    && sed -i "s/^BUILD_GPERF/#\0/" Make.Rules \
    # Compile binaries that run on the build machine using normal GCC
    && sed -i "s/^\(BUILD_CC\) := \$(CC)/\1 := gcc/" Make.Rules \
    && make \
        AR="${CROSS_COMPILE}ar" \
        CC="${CROSS_COMPILE}gcc" \
        RANLIB="${CROSS_COMPILE}ranlib" \
        lib="lib" \
        prefix="$SYSROOT/usr" \
    && make install \
        RAISE_SETFCAP=no \
        lib="lib" \
        prefix="$SYSROOT/usr" \
    && cd .. \
    # Clean up
    && rm -rf libcap \
    && apt-get autoremove -y \
        git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build util-linux 2.32 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        autopoint \
        bison \
        gettext \
        git \
        libtool \
    # Build util-linux
    && cd /root \
    && git clone https://github.com/karelzak/util-linux.git \
    && cd util-linux \
    && git checkout 475ecbad15943d6831fc508ce72016d581763b2b \
    && ./autogen.sh \
    && ./configure \
        --prefix="$SYSROOT/usr" \
        --host="$CHOST" \
    && make LDFLAGS="-Wl,-rpath-link,.libs" \
    && make install \
    && cd .. \
    # Clean up
    && rm -rf util-linux \
    && apt-get autoremove -y \
        automake \
        autopoint \
        bison \
        gettext \
        git \
        libtool \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build systemd 237 targeting armhf
COPY patch/*.patch /
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        gettext \
        git \
        gperf \
        m4 \
    # Build systemd
    && cd /root \
    && git clone https://github.com/systemd/systemd.git \
    && cd systemd \
    && git checkout 46659f7deb962f55c728e70597e37c2a3ab6326d \
    # Rename 'debug' build option to 'debug-extra'
    # (see <https://github.com/systemd/systemd/issues/9883>)
    && sed -i "s/\(get_option('debug\)'/\1-extra'/" meson.build \
    && sed -i "s/\(option('debug\)'/\1-extra'/" meson_options.txt \
    # Patch fixing conflicts on MS_* macros
    # (see <https://github.com/systemd/systemd/issues/8507>)
    && git apply /systemd-237-eb50b6d.patch \
    && rm /systemd-237-eb50b6d.patch \
    # Patch fixing format truncation errors
    # (see <https://github.com/systemd/systemd/pull/8250>)
    && git apply /systemd-237-5547c12.patch \
    && rm /systemd-237-5547c12.patch \
    && ./configure \
        --buildtype=release \
        --strip \
        --cross-file="$CHOST" \
        --prefix="$SYSROOT/usr" \
        --libdir="lib" \
        -Drootprefix="$SYSROOT" \
    && ninja -C build \
    && DESTDIR="/" ninja -C build install \
    && cd .. \
    # Clean up
    && rm -rf systemd \
    && apt-get autoremove -y \
        gettext \
        git \
        gperf \
        m4 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build zlib 1.2.11 and libpng 1.6.34 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        git \
        libtool \
        python \
    # Build static zlib
    && cd /root \
    && git clone https://github.com/madler/zlib \
    && cd zlib \
    && git checkout cacf7f1d4e3d44d871b605da3b647f07d718623f \
    && CC="${CROSS_COMPILE}gcc" CFLAGS=-fPIC ./configure \
        --static \
        --prefix="$SYSROOT/usr" \
    && make \
    && make install \
    && cd .. \
    # Build dynamic libpng
    && git clone git://git.code.sf.net/p/libpng/code libpng \
    && cd libpng \
    && git checkout b78804f9a2568b270ebd30eca954ef7447ba92f7 \
    && ./autogen.sh \
    && ./configure \
        --prefix="$SYSROOT/usr" \
        --host="$CHOST" \
    && make \
    && make install \
    && cd .. \
    # Clean up
    && rm -rf zlib libpng \
    && apt-get autoremove -y \
        automake \
        git \
        libtool \
        python \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build dlib 19.21
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        cmake \
        git \
    # Build static zlib
    && cd /root \
    && git clone https://github.com/davisking/dlib \
    && cd dlib \
    && git checkout 9117bd784328d9ac40ffa1f9cf487633a8a715d7 \
    && mkdir build \
    && cd build \
    && cmake -DCMAKE_TOOLCHAIN_FILE="/usr/share/cmake/$CHOST.cmake" .. \
    && cmake --build . \
    && cmake --install . --prefix "$SYSROOT/usr" \
    && cd .. \
    # Clean up
    && rm -rf dlib \
    && apt-get autoremove -y \
        cmake \
        git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
