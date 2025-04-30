# Essential cross-compiling build tools for the reMarkable
FROM debian:bookworm-slim
ARG NGCONFIG="arm-remarkable-linux-gnueabihf"
ARG NGCONFIG_AARCH64="aarch64-remarkable-linux-gnu"
ARG CHOST="arm-linux-gnueabihf"
ARG CHOST_AARCH64="aarch64-remarkable-linux-gnu"

# Install common building tools
RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        cmake \
        curl \
        gcc-11 \
        g++-11 \
        meson \
        pkg-config \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log /var/log/apt \
    # Force use of gcc and g++ 11
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 11 \
        --slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-11  \
        --slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-11  \
        --slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-11 \
        --slave /usr/bin/g++ g++ /usr/bin/g++-11

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
        rsync \
        texinfo \
        unzip \
    # Build crosstool-ng itself
    && cd /root \
    && git clone https://github.com/crosstool-ng/crosstool-ng \
    && cd crosstool-ng \
    && git checkout cf25b4edb096c9b815daa202968c15906b8047e5 \
    && mv "/$NGCONFIG" samples \
    && cp "samples/$NGCONFIG/0004-fix-glibc-dso-link.patch" packages/glibc/2.35/ \
    && ./bootstrap \
    && ./configure --enable-local \
    && make -j$(nproc) \
    # Build the toolchain and install it in the following folder
    && export CT_PREFIX="/opt/x-tools" \
    && ./ct-ng "$NGCONFIG" \
    && ./ct-ng build.$(nproc) \
    && cd .. \
    # Clean up
    && rm -rf crosstool-ng \
    && find "/opt/x-tools/$NGCONFIG/$NGCONFIG" -type l,f -name "*.la" -delete \
    && rm "/opt/x-tools/$NGCONFIG/build.log.bz2" \
    # Build the aarch64 toolchain
		&& cd /root \
		&& git clone https://github.com/NiLuJe/crosstool-ng \
		&& cd crosstool-ng \
		&& git checkout 7396dbf0a0c0883b1ead50dd442b45a646c21618 \
		&& echo "CT_ALLOW_BUILD_AS_ROOT=y" >> samples/$NGCONFIG_AARCH64/crosstool.config \
		&& echo "CT_ALLOW_BUILD_AS_ROOT_SURE=y" >> samples/$NGCONFIG_AARCH64/crosstool.config \
		&& echo "CT_GCC_V_11=y" >> samples/$NGCONFIG_AARCH64/crosstool.config \
		&& echo "CT_LOG_PROGRESS_BAR=n" >> samples/$NGCONFIG_AARCH64/crosstool.config \
		&& ./bootstrap \
		&& ./configure --enable-local \
		&& make -j$(nproc) \
		# Build the toolchain and install it in the following folder
		&& export CT_PREFIX="/opt/x-tools" \
		&& ./ct-ng "$NGCONFIG_AARCH64" \
		&& ./ct-ng oldconfig \
		&& ./ct-ng upgradeconfig \
    # Disable because GNU Savannah is very unstable
		# && ./ct-ng updatetools \
    && ./ct-ng build.$(nproc) \
		&& cd .. \
    # Clean up
		&& rm -rf crosstool-ng \
		&& find "/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64" -type l,f -name "*.la" -delete \
		&& rm "/opt/x-tools/$NGCONFIG_AARCH64/build.log.bz2" \
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
        rsync \
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
    && git clone --depth 1 --branch v0.4.5 https://git.yoctoproject.org/git/opkg \
    && cd opkg \
    && ./autogen.sh \
    && ./configure \
        --enable-sha256 \
        --prefix=/ \
        --exec-prefix=/usr \
    && make -j$(nproc) \
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
		SYSROOT_AARCH64="/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot" \
    NGCONFIG="$NGCONFIG" \
    TARGET_PREFIX="$CHOST"

RUN touch /opt/x-tools/switch-aarch64.sh && chmod +x /opt/x-tools/switch-aarch64.sh && cat <<EOF > /opt/x-tools/switch-aarch64.sh
export ARCH=aarch64
export CHOST="$CHOST_AARCH64"
export CROSS_COMPILE="$CHOST_AARCH64-"
export PATH="$PATH:/opt/x-tools/$NGCONFIG_AARCH64/bin"
export PKG_CONFIG_LIBDIR="/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot/usr/lib/pkgconfig:/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot/lib/pkgconfig:/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot/opt/lib/pkgconfig"
export PKG_CONFIG_SYSROOT_DIR="/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot"
export SYSROOT="/opt/x-tools/$NGCONFIG_AARCH64/$NGCONFIG_AARCH64/sysroot"
export NGCONFIG="$NGCONFIG_AARCH64"
export TARGET_PREFIX="$CHOST_AARCH64"
EOF

# Configure Opkg
COPY opkg /opt/x-tools/$NGCONFIG/$NGCONFIG/sysroot/etc/opkg
RUN mv /usr/bin/opkg /usr/bin/opkg-bin \
    && printf "#!/bin/bash\n/usr/bin/opkg-bin --offline-root \"$SYSROOT\" --host-cache-dir \"\$@\"\n" > /usr/bin/opkg \
		&& printf "#!/bin/bash\n/usr/bin/opkg-bin --offline-root \"$SYSROOT_AARCH64\" --host-cache-dir \"\$@\"\n" > /usr/bin/opkg-aarch64 \
    && chmod +x /usr/bin/opkg \
    && mkdir /repo \
    && touch /repo/Packages.gz

# Add cross-compiling configuration for common build systems
COPY "meson/$CHOST" "/usr/share/meson/cross/$CHOST"
COPY "meson/$CHOST_AARCH64" "/usr/share/meson/cross/$CHOST_AARCH64"
COPY "cmake/$CHOST.cmake" "/usr/share/cmake/$CHOST.cmake"
COPY "cmake/$CHOST_AARCH64.cmake" "/usr/share/cmake/$CHOST_AARCH64.cmake"
