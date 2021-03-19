# Pre-installed reMarkable libraries
ARG FROM
FROM $FROM

# Build libcap 2.32 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
    # Build libcap
    && cd /root \
    && git clone git://git.kernel.org/pub/scm/libs/libcap/libcap.git \
    && cd libcap \
    && git checkout 13227f9b2f2b2f222e8022e19bd46db6f6f898c6 \
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

# Build util-linux 2.36.1 targeting armhf
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
    && git checkout 35c07c82be1ddc3b1c40f061b59008cac6405499 \
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

# Build systemd 244 targeting armhf
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
    && git checkout db9c5ae73e23d816e2df2a3e10a9a2a60b5b3ed7 \
    # Fix build errors with GCC 10 and -O3
    # (see <https://github.com/systemd/systemd/pull/15762>)
    && git apply /systemd-244-15762.patch \
    && rm /systemd-244-15762.patch \
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

# Build zlib 1.2.11 and libpng 1.6.37 targeting armhf
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
    && git checkout a40189cf881e9f0db80511c382292a5604c3c3d1 \
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

# Build libevdev 1.9.1 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build libevdev
    && cd /root \
    && mkdir libevdev \
    && cd libevdev \
    && curl https://www.freedesktop.org/software/libevdev/libevdev-1.9.1.tar.xz -o libevdev.tar.xz \
    && echo "f5603c48c5afd76b14df7a5124e0a94a102f8da0d45826192325069d1bbc7acb  libevdev.tar.xz" > sha256sums \
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
