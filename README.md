## Toltec build toolchain

This is a set of Docker images used for cross-compiling binaries for the [the reMarkable tablet](https://remarkable.com/).
They are primarily used to provide reproducible environments for building [Toltec](https://github.com/toltec-dev/toltec) packages.
They can also be useful for any project that needs to be built for the reMarkable.

### Paths

Name                | Location   | Purpose
------------------- | ---------- | -------
Build system root   | `/`        | A basic Debian install which provides the CMake and Meson build systems, the `opkg` package manager for installing packages in the host system root, and other useful building tools.
Cross-compiler root | `/opt/x-tools/arm-remarkable-linux-gnueabihf/bin` | Provides a build of GCC which targets the ARMv7 architecture. This directory is in the image’s `$PATH` by default.
Host system root    | `$SYSROOT` | Provides a set of libraries similar to the one available on the reMarkable, compiled for the ARMv7 architecture. Included libraries are: glibc 2.27, Linux 4.9 headers, libcap 2.25, util-linux 2.32, libsystemd 237, zlib 1.2.11, libpng 1.6.34, and dlib 19.21.

### Images

Name | Purpose
---- | -------
[base](https://github.com/orgs/toltec-dev/packages/container/package/base) | Only provides the tools mentioned above.
[qt](https://github.com/orgs/toltec-dev/packages/container/package/qt) | Adds Qt 5.11.3 on top of the base image, including the closed-source libqsgepaper library.
[rust](https://github.com/orgs/toltec-dev/packages/container/package/rust) | Adds Nightly Rust configured to use the ARMv7 cross-compiler on top of the base image.
[python](https://github.com/orgs/toltec-dev/packages/container/package/python) | Adds a Python 3.7.3 distribution on top of the base image.

### Why not use the reMarkable-provided toolchain?

reMarkable does [provide an OpenEmbedded-Core-based toolchain](https://remarkable.engineering/oecore-x86_64-cortexa9hf-neon-toolchain-zero-gravitas-1.8-23.9.2019.sh) which can be used for cross-compiling binaries targeting the tablet.
However, no way of building this toolchain from source is provided, which limits the possibilities of evolving the build environment.
Building the toolchain from source also allows us to know that the binaries have not been tampered with compared to their source.

### Changelog

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
