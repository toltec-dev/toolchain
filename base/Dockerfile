# Pre-installed reMarkable libraries
ARG FROM
FROM $FROM

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
        lib=lib \
    && make install \
        RAISE_SETFCAP=no \
        lib=lib \
        DESTDIR="$SYSROOT" \
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
    && ./configure --host="$CHOST" \
    && make LDFLAGS="-Wl,-rpath-link,.libs" \
    && make install DESTDIR="$SYSROOT" \
    && cd .. \
    # Clean up
    && rm -rf util-linux \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm \
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
        --cross-file="$CHOST" \
        --prefix=/ \
        -Drootprefix=/ \
        --datadir=usr/share \
        --includedir=usr/include \
        --libdir=lib \
    && ninja -C build \
    && DESTDIR="$SYSROOT" ninja -C build install \
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
        --prefix=/usr \
    && make \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Build dynamic libpng
    && git clone git://git.code.sf.net/p/libpng/code libpng \
    && cd libpng \
    && git checkout b78804f9a2568b270ebd30eca954ef7447ba92f7 \
    && ./autogen.sh \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf zlib libpng \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm \
    && apt-get autoremove -y \
        automake \
        git \
        libtool \
        python \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Build libevdev 1.5.8 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build libevdev
    && cd /root \
    && mkdir libevdev \
    && cd libevdev \
    && curl https://www.freedesktop.org/software/libevdev/libevdev-1.5.8.tar.xz -o libevdev.tar.xz \
    && echo "6083d81e46609da8ba80cb826c02d9080764a6dec33c8267ccb7e158833d4c6d  libevdev.tar.xz" > sha256sums \
    && sha256sum -c sha256sums \
    && tar --strip-components=1 -xf libevdev.tar.xz \
    && rm libevdev.tar.xz sha256sums \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libevdev \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm
