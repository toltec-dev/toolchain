## Toltec build toolchain

Set of Docker images for cross-compiling binaries to run on the [the reMarkable tablet](https://remarkable.com/).
It is used to provide a consistent environment for building [Toltec](https://github.com/toltec-dev/toltec) packages, and can be useful for any project that needs to be built for the reMarkable.

### Available images

<table>
<tr>
    <th>Name</th>
    <th>Description</th>
</tr>
<tr>
    <td>
        <a href="https://github.com/orgs/toltec-dev/packages/container/package/base">base</a>
    </td>
    <td>
        Aims at replicating the <a href="https://remarkable.engineering/oecore-x86_64-cortexa9hf-neon-toolchain-zero-gravitas-1.8-23.9.2019.sh">build toolchain provided by the reMarkable company</a>, the difference being that this toolchain is fully built from source, using <a href="http://crosstool-ng.github.io/">Crosstool-NG 1.24.0</a>. Contents: <ul>
            <li>a cross-compiler for the ARMv7 architecture</a>
            <li>glibc 2.27</li>
            <li>configuration for the CMake and Meson build systems</li>
            <li>Linux 4.9 headers</li>
            <li>libcap 2.25</li>
            <li>util-linux 2.32</li>
            <li>libsystemd 237</li>
            <li>zlib 1.2.11</li>
            <li>libpng 1.6.34</li>
            <li>dlib 19.21</li>
        </ul>
    </td>
</tr>
<tr>
    <td>
        <a href="https://github.com/orgs/toltec-dev/packages/container/package/qt">qt</a>
    </td>
    <td>
        Adds Qt 5.11.3 on top of the base image, including the closed-source libqsgepaper library.
    </td>
</tr>
<tr>
    <td>
        <a href="https://github.com/orgs/toltec-dev/packages/container/package/rust">rust</a>
    </td>
    <td>
        Adds Nightly Rust configured to use the ARMv7 cross-compiler on top of the base image.
    </td>
</tr>
<tr>
    <td>
        <a href="https://github.com/orgs/toltec-dev/packages/container/package/python">python</a>
    </td>
    <td>
        Adds a Python 3.7.3 distribution on top of the base image.
    </td>
</tr>
</table>

### Changelog

#### v1.2.1

* Fix missing CMake from final images.

#### v1.2

* Add toolchain configuration for CMake projects to the `base` image.
* Add dlib to the `base` image.

#### v1.1

* Add libcap, util-linux and libsystemd to the `base` image.
* Move libpng and zlib from the `qt` image to the `base` image.

#### v1.0

_(Initial release.)_
