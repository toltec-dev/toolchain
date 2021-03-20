## Changelog

### v2.x

#### v2.0 — 2021-03-20

* Update libraries for reMarkable 2.6.1.71

***

### v1.x

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
