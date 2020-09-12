## Toltec build toolchain

Set of Docker images for cross-compiling binaries to run on the [the reMarkable tablet](https://remarkable.com/).
In particular, it is used to provide a consistent environment for building [Toltec](https://github.com/toltec-dev/toltec) packages, but can be used by any project that needs to build to the reMarkable.

### Available images

Name                                                                   | Description
---------------------------------------------------------------------- | ------------
[base](https://github.com/orgs/toltec-dev/packages/container/base)     | Contains a cross-compiler for the ARMv7 architecture, with glibc 2.27 and Linux 4.9 headers. It is used as a base image for all other images in this repository.
[qt](https://github.com/orgs/toltec-dev/packages/container/qt)         | Qt 5.11.3, zlib 1.2.11, libpng 1.6.34 and libqsgepaper plugin. This image closely mimics [the original build toolchain provided by the reMarkable company](https://remarkable.engineering/oecore-x86_64-cortexa9hf-neon-toolchain-zero-gravitas-1.8-23.9.2019.sh).
[rust](https://github.com/orgs/toltec-dev/packages/container/rust)     | Nightly Rust install configured to cross-compile to ARMv7.
[python](https://github.com/orgs/toltec-dev/packages/container/python) | Python 3.7.3 distribution with pip.
