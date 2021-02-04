## Toltec build toolchain

This is a set of Docker images used for cross-compiling binaries for the [the reMarkable tablet](https://remarkable.com/).
They are primarily aimed at providing reproducible build environments for [Toltec](https://github.com/toltec-dev/toltec) packages.
They can also be useful for any project that needs to be built for the reMarkable.

### Paths

Name                | Location   | Contents
------------------- | ---------- | -------
Build system root   | `/`        | Minimal Debian install with usual build tools, the CMake and Meson build systems, and the `opkg` package manager for installing packages in the host system root.
Cross-compiler root | `/opt/x-tools/arm-remarkable-linux-gnueabihf/bin` | GCC cross-compiler and assorted tools which target the ARMv7 architecture. This directory is in `$PATH` by default.
Host system root    | `$SYSROOT` | This is where binaries targeting the reMarkable are installed. By default, it only contains a minimal set of libraries and executables (glibc 2.27, Linux 4.9 headers).

### Images

Name | Contents
---- | -------
[toolchain](https://github.com/orgs/toltec-dev/packages/container/package/toolchain) | Only the tools mentioned above.
[base](https://github.com/orgs/toltec-dev/packages/container/package/base) | Adds to the host system root a set of libraries similar to those that come pre-installed on the reMarkable: libcap 2.25, util-linux 2.32, libsystemd 237, zlib 1.2.11, libpng 1.6.34, and libevdev 1.5.8.
[qt](https://github.com/orgs/toltec-dev/packages/container/package/qt) | Adds Qt 5.11.3 to the host system root, including the closed-source libqsgepaper plugin. Includes the `qmake` build tool in the build system root.
[rust](https://github.com/orgs/toltec-dev/packages/container/package/rust) | Adds Nightly Rust configured to use the ARMv7 cross-compiler to the build system root.
[python](https://github.com/orgs/toltec-dev/packages/container/package/python) | Adds a Python 3.7.3 distribution to the build system root.

### Why not use the reMarkable-provided toolchain?

reMarkable does [provide an OpenEmbedded-Core-based toolchain](https://web.archive.org/web/20201129102245/https://remarkable.engineering/oecore-x86_64-cortexa9hf-neon-toolchain-zero-gravitas-1.8-23.9.2019.sh) which can be used for cross-compiling binaries targeting the tablet.
However, no way of building this toolchain from source is provided, which limits the possibilities of evolving the build environment.
Building the toolchain from source also allows us to know that the binaries have not been tampered with compared to their source.

### Changelog

#### v1.3.2 — 2021-02-04

* Fix incorrect `opkg` root installation dir.

#### v1.3.1 — 2021-01-31

* Create the `toolchain` image which does not contain the pre-installed reMarkable libraries.
* Add `$SYSROOT/opt/lib/pkg-config` to the pkgconfig search path.

#### v1.3 — 2021-01-26

* Add `opkg` to the `base` image.
* Remove dlib from the `base` image.
* Add libevdev to the `base` image.
* Cleanup all remaining `*.la` files from the host system root.
* Fix systemd install location in the host system root.
* Remove references to the system root location in the host system root’s libs.

#### v1.2.2 — 2021-01-06

* Fix wrong `libqsgepaper.a` and `epframebuffer.h` files in the `qt` image due to outdated link.

#### v1.2.1 — 2020-10-31

* Fix missing CMake from final images.

#### v1.2 — 2020-09-30

* Add toolchain configuration for CMake projects to the `base` image.
* Add dlib to the `base` image.

#### v1.1 — 2020-09-23

* Add libcap, util-linux and libsystemd to the `base` image.
* Move libpng and zlib from the `qt` image to the `base` image.

#### v1.0 — 2020-09-12

_(Initial release.)_
