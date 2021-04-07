## Toltec Build Toolchain

This is a set of Docker images used for cross-compiling binaries for the [the reMarkable tablet](https://remarkable.com/).
They are primarily aimed at providing reproducible build environments for [Toltec](https://github.com/toltec-dev/toltec) packages.
They can also be useful for any project that needs to be built for the reMarkable.

_Note: reMarkable also used to provide an official OpenEmbedded-Core-based toolchain on its <https://remarkable.engineering> website._
_However, the company seems to have phased out the  website as of January 2021, so its status is currently unknown._

### Paths

Name                  | Location   | Contents
--------------------- | ---------- | -------
Build system root     | `/`        | Minimal Debian install with usual build tools, the CMake and Meson build systems, and the `opkg` package manager for installing packages in the host system root.
Cross-compiler root   | `/opt/x-tools/arm-remarkable-linux-gnueabihf/bin` | GCC cross-compiler and assorted tools which target the ARMv7 architecture. This directory is in `$PATH` by default.
Host system root      | `$SYSROOT` | This is where binaries targeting the reMarkable are installed. By default, it only contains a minimal set of libraries and executables (glibc and Linux headers).
Local Opkg repository | `/repo`    | Packages stored in this directory can be installed to the host system root using the `opkg` command.

### Images

Name | Contents
---- | -------
[toolchain](https://github.com/orgs/toltec-dev/packages/container/package/toolchain) | Only the tools mentioned above.
[base](https://github.com/orgs/toltec-dev/packages/container/package/base) | Adds to the host system root a set of libraries similar to those that come pre-installed on the reMarkable: libcap, util-linux, libsystemd, zlib, libpng, and libevdev.
[qt](https://github.com/orgs/toltec-dev/packages/container/package/qt) | Adds Qt to the host system root, including the closed-source libqsgepaper plugin. Includes the `qmake` build tool in the build system root.
[rust](https://github.com/orgs/toltec-dev/packages/container/package/rust) | Adds Nightly Rust configured to use the ARMv7 cross-compiler to the build system root.
[python](https://github.com/orgs/toltec-dev/packages/container/package/python) | Adds a Python 3.7.3 distribution to the build system root.
[golang](https://github.com/orgs/toltec-dev/packages/container/package/golang) | Adds a Go 1.16.2 distribution to the build system root.

### Version Matrix

The toolchain images aim to follow the library versions available on the stock reMarkable system.
The major version number of the toolchain is increased each time an official reMarkable update changes the available libraries.
The table below lists the version number of each library for each major toolchain release.

<table>
    <tr>
        <th align="left">↓ Library \ Toolchain →</th>
        <th align="left"><a href="https://github.com/toltec-dev/toolchain/tree/v1.x">1.x</a></td>
        <th align="left"><a href="https://github.com/toltec-dev/toolchain/tree/v2.x">2.x</a></td>
    </tr>
    <tr>
        <th>Compatible reMarkable updates</td>
        <td>⩽ 2.5.0.26</td>
        <td>⩾ 2.6.1.71</td>
    </tr>
    <tr>
        <th colspan="3" align="left">
            <a href="https://github.com/orgs/toltec-dev/packages/container/package/toolchain">toolchain</a> Image
        </th>
    </tr>
    <tr>
        <td>Linux headers</td>
        <td>4.9</td>
        <td>4.14</td>
    </tr>
    <tr>
        <td>glibc</td>
        <td>2.27</td>
        <td>2.31</td>
    </tr>
    <tr>
        <th colspan="3" align="left">
            <a href="https://github.com/orgs/toltec-dev/packages/container/package/base">base</a> Image
        </th>
    </tr>
    <tr>
        <td>libcap</td>
        <td>2.25</td>
        <td>2.32</td>
    </tr>
    <tr>
        <td>util-linux</td>
        <td>2.32</td>
        <td>2.36.1</td>
    </tr>
    <tr>
        <td>libsystemd</td>
        <td>237</td>
        <td>244</td>
    </tr>
    <tr>
        <td>zlib</td>
        <td colspan="2">1.2.11</td>
    </tr>
    <tr>
        <td>libpng</td>
        <td>1.6.34</td>
        <td>1.6.37</td>
    </tr>
    <tr>
        <td>libevdev</td>
        <td>1.5.8</td>
        <td>1.9.1</td>
    </tr>
    <tr>
        <th colspan="3" align="left">
            <a href="https://github.com/orgs/toltec-dev/packages/container/package/qt">qt</a> Image
        </th>
    </tr>
    <tr>
        <td>Qt</td>
        <td>5.11.3</td>
        <td>5.15.1</td>
    </tr>
</table>

### Installing Opkg Packages

Opkg is installed on every toolchain image and is configured to install packages in _offline_ mode, i.e., without executing any [maintainer script](https://www.debian.org/doc/debian-policy/ch-maintainerscripts.html).
By default, it is configured to install packages from Entware and from the local repository stored in `/repo`.
Opkg stores its cache in `/var/cache/opkg`.

### Changelog

[See the changelog →](CHANGELOG.md)
