# Essential cross-compiling build tools for the reMarkable
FROM debian:bullseye-slim
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
        gcc-10 \
        g++-10 \
        meson \
        pkg-config \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt \
    # Force use of gcc and g++ 10
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 10 \
        --slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-10  \
        --slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-10  \
        --slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-10  \
        --slave /usr/bin/g++ g++ /usr/bin/g++-10

# Build latest crosstool-ng and use it to build the toolchain
COPY crosstool-ng "/$NGCONFIG"
RUN export DEBIAN_FRONTEND=noninteractive \
    # Install build dependencies
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        autoconf \
        automake \
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
    && git checkout a21748bd5342b72ddf5bfef782e058c3245161f7 \
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
        automake \
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
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

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
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt

# Save to the environment values useful for setting up cross-compiling
ENV ARCH=arm \
    CHOST="$CHOST" \
    CROSS_COMPILE="$CHOST-" \
    PATH="$PATH:/opt/x-tools/$NGCONFIG/bin" \
    PKG_CONFIG_LIBDIR="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/usr/lib/pkgconfig:/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/lib/pkgconfig:/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/opt/lib/pkgconfig" \
    PKG_CONFIG_SYSROOT_DIR="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot" \
    SYSROOT="/opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot" \
    NGCONFIG="$NGCONFIG" \
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
