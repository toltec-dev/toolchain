# Essential cross-compiling build tools for the reMarkable
FROM debian:buster
ARG NGCONFIG="arm-remarkable-linux-gnueabihf"
ARG CHOST="arm-linux-gnueabihf"

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

# Build latest crosstool-ng and use it to build the toolchain
COPY crosstool-ng "/$NGCONFIG"
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
    && git checkout 6729a76d0c8ecd5d3f347ec31cc1c093b8e20b8e \
    && mv "/$NGCONFIG" samples \
    && ./bootstrap \
    && ./configure --enable-local \
    && make \
    # Build the toolchain and install it in the following folder
    && export CT_PREFIX="/opt/x-tools" \
    && ./ct-ng "$NGCONFIG" \
    && ./ct-ng build \
    && cd .. \
    # Clean up
    && rm -rf crosstool-ng \
    && find "/opt/x-tools/$NGCONFIG/$NGCONFIG" -type l,f -name "*.la" \
        | xargs --no-run-if-empty rm \
    && rm "/opt/x-tools/$NGCONFIG/build.log.bz2" \
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

# Build Opkg for installing and removing ipk packages in the sysroot
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        automake \
        git \
        libarchive-dev \
        libcurl4-gnutls-dev \
        libgpgme-dev \
        libssl-dev \
        libtool-bin \
    && cd /root \
    && git clone https://git.yoctoproject.org/git/opkg \
    && cd opkg \
    && git checkout 532bdf544ae598aa68a8c6dfd05c280d2a04e837 \
    && ./autogen.sh \
    && ./configure \
        --enable-sha256 \
        --prefix=/ \
        --exec-prefix=/usr \
    && make \
    && make install \
    && cd .. \
    # Clean up
    && rm -rf opkg \
    && rm /usr/lib/libopkg.la \
    && apt-get autoremove -y \
        automake \
        git \
        libarchive-dev \
        libssl-dev \
        libtool-bin \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Configure the environment for cross-compiling
ENV ARCH=arm \
    CHOST="$CHOST" \
    CROSS_COMPILE="$CHOST-" \
    PATH="$PATH:/opt/x-tools/$NGCONFIG/bin" \
    PKG_CONFIG_LIBDIR="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/usr/lib/pkgconfig:/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/lib/pkgconfig:/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/opt/lib/pkgconfig" \
    PKG_CONFIG_SYSROOT_DIR="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot" \
    SYSROOT="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot" \
    TARGET_PREFIX="$CHOST"

# Configure Opkg
COPY opkg /opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/etc/opkg
RUN mv /usr/bin/opkg /usr/bin/opkg-bin \
    && printf "#!/bin/bash\n/usr/bin/opkg-bin --offline-root \"$SYSROOT\" --host-cache-dir \"\$@\"\n" > /usr/bin/opkg \
    && chmod +x /usr/bin/opkg \
    && mkdir /repo \
    && touch /repo/Packages.gz

# Add cross-compiling configuration for common build systems
COPY "meson/$CHOST" "/usr/share/meson/cross/$CHOST"
COPY "cmake/$CHOST.cmake" "/usr/share/cmake/$CHOST.cmake"
