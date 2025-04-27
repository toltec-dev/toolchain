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
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

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
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

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
        rsync \
    # Build systemd
    && cd /root \
    && git clone https://github.com/systemd/systemd.git \
    && cd systemd \
    && git checkout db9c5ae73e23d816e2df2a3e10a9a2a60b5b3ed7 \
    # Fix build errors with GCC 10 and -O3
    # (see <https://github.com/systemd/systemd/pull/15762>)
    && git apply /systemd-244-15762.patch \
    # Fix Meson syntax and semantic changes in recent versions
    # (see <https://github.com/systemd/systemd/pull/20633>)
    && git apply /systemd-244-20633.patch \
    && rm /systemd-244-*.patch \
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
        rsync \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build zlib 1.2.11 and libpng 1.6.37 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        git \
        libtool \
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
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

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

# Build OpenSSL 1.1.1g targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build OpenSSL
    && cd /root \
    && mkdir openssl \
    && cd openssl \
    && curl https://www.openssl.org/source/openssl-1.1.1g.tar.gz -Lo openssl.tar.gz \
    && echo "ddb04774f1e32f0c49751e21b67216ac87852ceb056b75209af2443400636d46  openssl.tar.gz" > sha256sums \
    && sha256sum -c sha256sums \
    && tar --strip-components=1 -xf openssl.tar.gz \
    && rm openssl.tar.gz sha256sums \
    && ./Configure --prefix=/usr --cross-compile-prefix="$CROSS_COMPILE" linux-armv4 \
    && make \
    && make DESTDIR="$SYSROOT" install_sw \
    && cd .. \
    # Clean up
    && rm -rf openssl

# Build libcurl 7.69.1 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build libcurl
    && cd /root \
    && mkdir libcurl \
    && cd libcurl \
    && curl https://curl.se/download/curl-7.69.1.tar.xz -o curl.tar.xz \
    && echo "03c7d5e6697f7b7e40ada1b2256e565a555657398e6c1fcfa4cb251ccd819d4f  curl.tar.xz" > sha256sums \
    && sha256sum -c sha256sums \
    && tar --strip-components=1 -xf curl.tar.xz \
    && rm curl.tar.xz sha256sums \
    && ./configure --prefix=/usr --host="$CHOST" --with-openssl \
    && make \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libcurl \
    && find "$SYSROOT" -type l,f -name "*.la" | xargs --no-run-if-empty rm

# Build breakpad 0.1 (db1cda2653) targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
        zlib1g-dev \
        libssl-dev \
    # breakpad's build tools require python2 to be available as python on the path
    && git clone https://github.com/pyenv/pyenv.git .pyenv \
    && export PYENV_ROOT="$(pwd)/.pyenv" \
    && export PATH="$PYENV_ROOT/bin:$PATH" \
    && eval "$(pyenv init --path)" \
    && pyenv install 2.7 \
    && pyenv global 2.7 \
    # Fetch and activate depot_tools
    && cd /root \
    && git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git \
    && export PATH=$PATH:/root/depot_tools \
    # Build static breakpad
    && cd /root \
    && mkdir breakpad \
    && cd breakpad \
    && fetch breakpad \
    && cd src \
    && git checkout -f db1cda26539c711c3da7ed4d410dfe8190e89b8f \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make \
    && DESTDIR="$SYSROOT" make install \
    && cd ../.. \
    # Clean up
    && rm -rf \
        .vpython-root \
        .vpython_cipd_cache \
        /tmp/* \
        breakpad \
        depot_tools \
        .pyenv \
    && apt-get autoremove -y \
        git \
        zlib1g-dev \
        libssl-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt
