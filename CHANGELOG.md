## Changelog

### v3.x

#### v3.0 — 2023-07-25

* Update crosstool-ng to assume linux kernel 5.4.70
* Update Qt to use reMarkable's qtbase

### v2.x

#### v2.3.2

* Add dotnet6 image

#### v2.3.1

* Bump rust to latest nightly

#### v2.3 — 2022-02-27

* Update base image to use Debian unstable 2022-01-25
* Pin gcc/g++ major version to v10
* Update crosstool-ng to a21748bd
* Add NGCONFIG environment variable
* Add OpenSSL to the `base` image
* Add libcurl to the `base` image
* Add libbreakpad to the `base` image

#### v2.2 — 2021-09-25

* Update base image to use Debian unstable 2021-09-02
* Update Go to 1.17.1
* Update Rust to latest nightly

#### v2.1 — 2021-04-07

* Update all images to be based on Debian unstable (2021-03-29 snapshot)

#### v2.0.1 — 2021-04-02

* Fix crashes for some apps due to libqsgepaper

#### v2.0 — 2021-03-20

* Update libraries for reMarkable 2.6.1.71

***

### v1.x

#### v1.6 — 2021-04-07

* Update all images to be based on Debian unstable (2021-03-29 snapshot)

#### v1.5 — 2021-03-18

* Create the `golang` image.

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
