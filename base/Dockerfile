# Pre-installed reMarkable libraries
ARG FROM
FROM $FROM

# Build libcap 2.66 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
    # Build libcap
    && cd /root \
    && git clone --depth 1 --branch libcap-2.66 git://git.kernel.org/pub/scm/libs/libcap/libcap.git \
    && cd libcap \
    # Compile binaries that run on the build machine using normal GCC
    && sed -i "s/^\(BUILD_CC\) ?= \$(CC)/\1 := gcc/" Make.Rules \
    && make \
        AR="${CROSS_COMPILE}ar" \
        CC="${CROSS_COMPILE}gcc" \
        RANLIB="${CROSS_COMPILE}ranlib" \
        lib=lib \
				-j$(nproc) \
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

# Build util-linux 2.37.4 targeting armhf
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
    && git clone --depth 1 --branch v2.37.4 https://github.com/karelzak/util-linux.git \
    && cd util-linux \
    && ./autogen.sh \
    && ./configure --host="$CHOST" \
    && make LDFLAGS="-Wl,-rpath-link,.libs" -j$(nproc) \
    && make install DESTDIR="$SYSROOT" \
    && cd .. \
    # Clean up
    && rm -rf util-linux \
    && find "$SYSROOT" -type l,f -name "*.la" -delete \
    && apt-get autoremove -y \
        automake \
        autopoint \
        bison \
        gettext \
        git \
        libtool \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build systemd 250 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        gettext \
        git \
        gperf \
        m4 \
        rsync \
        python3-pip \
    && pip3 install --no-cache-dir --break-system-packages jinja2 \
    # Build systemd
    && cd /root \
    && git clone --depth 1 --branch v250.5 https://github.com/systemd/systemd.git \
    && cd systemd \
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
    # TODO: remove pip packages as well
    && apt-get autoremove -y \
        gettext \
        git \
        gperf \
        m4 \
        rsync \
        python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build zlib 1.2.11 and libpng 1.6.39 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        git \
        libtool \
    # Build static zlib
    && cd /root \
    && git clone --depth 1 --branch v1.2.11 https://github.com/madler/zlib \
    && cd zlib \
    && CC="${CROSS_COMPILE}gcc" CFLAGS=-fPIC ./configure \
        --static \
        --prefix=/usr \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Build dynamic libpng
    && git clone --depth 1 --branch v1.6.39 git://git.code.sf.net/p/libpng/code libpng \
    && cd libpng \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf zlib libpng \
    && find "$SYSROOT" -type l,f -name "*.la" -delete \
    && apt-get autoremove -y \
        automake \
        git \
        libtool \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build libevdev 1.12.1 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build libevdev
    && cd /root \
    && mkdir libevdev \
    && cd libevdev \
    && curl https://www.freedesktop.org/software/libevdev/libevdev-1.12.1.tar.xz -o libevdev.tar.xz \
    && echo "1dbba41bc516d3ca7abc0da5b862efe3ea8a7018fa6e9b97ce9d39401b22426c  libevdev.tar.xz" | sha256sum -c \
    && tar --strip-components=1 -xf libevdev.tar.xz \
    && rm libevdev.tar.xz \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libevdev \
    && find "$SYSROOT" -type l,f -name "*.la" -delete

# Build OpenSSL 3.0.15 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build OpenSSL
    && cd /root \
    && mkdir openssl \
    && cd openssl \
    && curl https://www.openssl.org/source/openssl-3.0.15.tar.gz -Lo openssl.tar.gz \
    && echo "23c666d0edf20f14249b3d8f0368acaee9ab585b09e1de82107c66e1f3ec9533  openssl.tar.gz" | sha256sum -c \
    && tar --strip-components=1 -xf openssl.tar.gz \
    && rm openssl.tar.gz \
    && ./Configure --prefix=/usr --cross-compile-prefix="$CROSS_COMPILE" linux-armv4 \
    && make -j$(nproc) \
    && make DESTDIR="$SYSROOT" install_sw \
    && cd .. \
    # Clean up
    && rm -rf openssl

# Build libcurl 7.82.0 targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Build libcurl
    && cd /root \
    && mkdir libcurl \
    && cd libcurl \
    && curl https://curl.se/download/curl-7.82.0.tar.xz -o curl.tar.xz \
    && echo "0aaa12d7bd04b0966254f2703ce80dd5c38dbbd76af0297d3d690cdce58a583c  curl.tar.xz" | sha256sum -c \
    && tar --strip-components=1 -xf curl.tar.xz \
    && rm curl.tar.xz \
    && ./configure --prefix=/usr --host="$CHOST" --with-openssl \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libcurl \
    && find "$SYSROOT" -type l,f -name "*.la" -delete

# Build breakpad 0.1 (f88a1aa2af) targeting armhf
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
        zlib1g-dev \
        libssl-dev \
    # breakpad's build tools require python2 to be available as python on the path
    && cd /root \
    && git clone --depth 1 https://github.com/pyenv/pyenv.git .pyenv \
    && export PYENV_ROOT="$(pwd)/.pyenv" \
    && export PATH="$PYENV_ROOT/bin:$PATH" \
    && eval "$(pyenv init --path)" \
    && pyenv install 2.7 \
    && pyenv global 2.7 \
    # Fetch and activate depot_tools
    && git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git \
    && export PATH=$PATH:/root/depot_tools \
    # Build static breakpad
    && mkdir breakpad \
    && cd breakpad \
    && fetch breakpad \
    && cd src \
    # fetch updates default.xml with seemingly useless diffs that break checkout later
    && git checkout HEAD -- default.xml \
    && git checkout f88a1aa2af1d1fd795716365245761b89041139d \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
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

# Build libcap 2.66 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    . /opt/x-tools/switch-aarch64.sh \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
    # Build libcap
    && cd /root \
    && git clone --depth 1 --branch libcap-2.66 git://git.kernel.org/pub/scm/libs/libcap/libcap.git \
    && cd libcap \
    # Compile binaries that run on the build machine using normal GCC
    && sed -i "s/^\(BUILD_CC\) ?= \$(CC)/\1 := gcc/" Make.Rules \
    && make \
        AR="${CROSS_COMPILE}ar" \
        CC="${CROSS_COMPILE}gcc" \
        RANLIB="${CROSS_COMPILE}ranlib" \
        lib=lib \
				-j$(nproc) \
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

# Build util-linux 2.37.4 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    . /opt/x-tools/switch-aarch64.sh \
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
    && git clone --depth 1 --branch v2.37.4 https://github.com/karelzak/util-linux.git \
    && cd util-linux \
    && ./autogen.sh \
    && ./configure --host="$CHOST" \
    && make LDFLAGS="-Wl,-rpath-link,.libs" -j$(nproc) \
    && make install DESTDIR="$SYSROOT" \
    && cd .. \
    # Clean up
    && rm -rf util-linux \
    && find "$SYSROOT" -type l,f -name "*.la" -delete \
    && apt-get autoremove -y \
        automake \
        autopoint \
        bison \
        gettext \
        git \
        libtool \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build systemd 250 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        gettext \
        git \
        gperf \
        m4 \
        rsync \
        python3-pip \
    && pip3 install --no-cache-dir --break-system-packages jinja2 \
    # Build systemd
    && cd /root \
    && git clone --depth 1 --branch v250.5 https://github.com/systemd/systemd.git \
    && cd systemd \
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
    # TODO: remove pip packages as well
    && apt-get autoremove -y \
        gettext \
        git \
        gperf \
        m4 \
        rsync \
        python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build zlib 1.2.11 and libpng 1.6.39 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        git \
        libtool \
    # Build static zlib
    && cd /root \
    && git clone --depth 1 --branch v1.2.11 https://github.com/madler/zlib \
    && cd zlib \
    && CC="${CROSS_COMPILE}gcc" CFLAGS=-fPIC ./configure \
        --static \
        --prefix=/usr \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Build dynamic libpng
    && git clone --depth 1 --branch v1.6.39 git://git.code.sf.net/p/libpng/code libpng \
    && cd libpng \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf zlib libpng \
    && find "$SYSROOT" -type l,f -name "*.la" -delete \
    && apt-get autoremove -y \
        automake \
        git \
        libtool \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Build libevdev 1.12.1 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Build libevdev
    && cd /root \
    && mkdir libevdev \
    && cd libevdev \
    && curl https://www.freedesktop.org/software/libevdev/libevdev-1.12.1.tar.xz -o libevdev.tar.xz \
    && echo "1dbba41bc516d3ca7abc0da5b862efe3ea8a7018fa6e9b97ce9d39401b22426c  libevdev.tar.xz" | sha256sum -c \
    && tar --strip-components=1 -xf libevdev.tar.xz \
    && rm libevdev.tar.xz \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libevdev \
    && find "$SYSROOT" -type l,f -name "*.la" -delete

# Build OpenSSL 3.0.15 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Build OpenSSL
    && cd /root \
    && mkdir openssl \
    && cd openssl \
    && curl https://www.openssl.org/source/openssl-3.0.15.tar.gz -Lo openssl.tar.gz \
    && echo "23c666d0edf20f14249b3d8f0368acaee9ab585b09e1de82107c66e1f3ec9533  openssl.tar.gz" | sha256sum -c \
    && tar --strip-components=1 -xf openssl.tar.gz \
    && rm openssl.tar.gz \
    && ./Configure --prefix=/usr --cross-compile-prefix="$CROSS_COMPILE" linux-aarch64 \
    && make -j$(nproc) \
    && make DESTDIR="$SYSROOT" install_sw \
    && cd .. \
    # Clean up
    && rm -rf openssl

# Build libcurl 7.82.0 targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Build libcurl
    && cd /root \
    && mkdir libcurl \
    && cd libcurl \
    && curl https://curl.se/download/curl-7.82.0.tar.xz -o curl.tar.xz \
    && echo "0aaa12d7bd04b0966254f2703ce80dd5c38dbbd76af0297d3d690cdce58a583c  curl.tar.xz" | sha256sum -c \
    && tar --strip-components=1 -xf curl.tar.xz \
    && rm curl.tar.xz \
    && ./configure --prefix=/usr --host="$CHOST" --with-openssl \
    && make -j$(nproc) \
    && DESTDIR="$SYSROOT" make install \
    && cd .. \
    # Clean up
    && rm -rf libcurl \
    && find "$SYSROOT" -type l,f -name "*.la" -delete

# Build breakpad 0.1 (f88a1aa2af) targeting aarch64
RUN export DEBIAN_FRONTEND=noninteractive \
    && . /opt/x-tools/switch-aarch64.sh \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        git \
        zlib1g-dev \
        libssl-dev \
    # breakpad's build tools require python2 to be available as python on the path
    && cd /root \
    && git clone --depth 1 https://github.com/pyenv/pyenv.git .pyenv \
    && export PYENV_ROOT="$(pwd)/.pyenv" \
    && export PATH="$PYENV_ROOT/bin:$PATH" \
    && eval "$(pyenv init --path)" \
    && pyenv install 2.7 \
    && pyenv global 2.7 \
    # Fetch and activate depot_tools
    && git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git \
    && export PATH=$PATH:/root/depot_tools \
    # Build static breakpad
    && mkdir breakpad \
    && cd breakpad \
    && fetch breakpad \
    && cd src \
    # fetch updates default.xml with seemingly useless diffs that break checkout later
    && git checkout HEAD -- default.xml \
    && git checkout f88a1aa2af1d1fd795716365245761b89041139d \
    && ./configure --prefix=/usr --host="$CHOST" \
    && make -j$(nproc) \
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

