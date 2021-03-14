## Toltec build toolchain

This is a set of Docker images used for cross-compiling binaries for the [the reMarkable tablet](https://remarkable.com/).
They are primarily aimed at providing reproducible build environments for [Toltec](https://github.com/toltec-dev/toltec) packages.
They can also be useful for any project that needs to be built for the reMarkable.

Note that reMarkable also used to provide an OpenEmbedded-Core-based toolchain on its <https://remarkable.engineering> website.
However, the company seems to have phased out the  website as of January 2021, so its status is currently unknown.

### Paths

Name                  | Location   | Contents
--------------------- | ---------- | -------
Build system root     | `/`        | Minimal Debian install with usual build tools, the CMake and Meson build systems, and the `opkg` package manager for installing packages in the host system root.
Cross-compiler root   | `/opt/x-tools/arm-remarkable-linux-gnueabihf/bin` | GCC cross-compiler and assorted tools which target the ARMv7 architecture. This directory is in `$PATH` by default.
Host system root      | `$SYSROOT` | This is where binaries targeting the reMarkable are installed. By default, it only contains a minimal set of libraries and executables (glibc 2.27, Linux 4.9 headers).
Local Opkg repository | `/repo`    | Packages stored in this directory can be installed to the host system root using the `opkg` command.

### Images

Name | Contents
---- | -------
[toolchain](https://github.com/orgs/toltec-dev/packages/container/package/toolchain) | Only the tools mentioned above.
[base](https://github.com/orgs/toltec-dev/packages/container/package/base) | Adds to the host system root a set of libraries similar to those that come pre-installed on the reMarkable: libcap 2.25, util-linux 2.32, libsystemd 237, zlib 1.2.11, libpng 1.6.34, and libevdev 1.5.8.
[qt](https://github.com/orgs/toltec-dev/packages/container/package/qt) | Adds Qt 5.11.3 to the host system root, including the closed-source libqsgepaper plugin. Includes the `qmake` build tool in the build system root.
[rust](https://github.com/orgs/toltec-dev/packages/container/package/rust) | Adds Nightly Rust configured to use the ARMv7 cross-compiler to the build system root.
[python](https://github.com/orgs/toltec-dev/packages/container/package/python) | Adds a Python 3.7.3 distribution to the build system root.
[golang](https://github.com/orgs/toltec-dev/packages/container/package/golang) | Adds a Go 1.16.2 distribution to the build system root.

### Installing Opkg packages

Opkg is installed on every toolchain image and is configured to install packages in _offline_ mode, i.e., without executing any [maintainer script](https://www.debian.org/doc/debian-policy/ch-maintainerscripts.html).
By default, it is configured to install packages from Entware and from the local repository stored in `/repo`.
Opkg stores its cache in `/var/cache/opkg`.

### Changelog

#### v1.4 — 2021-03-04

* Automatically set the `offline-root` and `host-cache-dir` flags when invoking `opkg`.
* Prevent qmake from setting rpath on built binaries.
* Remove Qt libraries that are not available on the default reMarkable system.

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
