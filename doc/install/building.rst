.. _building:

Building from sources
=====================

Prerequisites
-------------

* git
* GNU make
* cmake >= `2.8.12`
* gcc >= `4.7` or clang >= `3.4` C compiler
* zlib-dev


Make
----

.. code-block:: sh

    git clone https://github.com/Softmotions/ejdb.git
    cd ejdb
    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ../
    make
    make install
    # Or you can create tgz package:
    make package


CMake basic build(-D) options
-----------------------------

.. code-block:: sh

    // Build ejdb sample projects
    BUILD_SAMPLES:BOOL=ON

    // Build shared libraries
    BUILD_SHARED_LIBS:BOOL=ON

    // Build test cases
    BUILD_TESTS:BOOL=OFF

    // Choose the type of build, options are:
    // - None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used)
    // - Debug
    // - Release
    // - RelWithDebInfo.
    CMAKE_BUILD_TYPE:STRING=Release

    // Install path prefix,
    // prepended onto install directories.
    CMAKE_INSTALL_PREFIX:PATH=/usr/local

    // Enable PPA package build
    ENABLE_PPA:BOOL=OFF

    // Build .deb instalation packages
    PACKAGE_DEB:BOOL=OFF

    // Build .tgz package archive
    PACKAGE_TGZ:BOOL=ON

    // Upload debian packages to the launchpad ppa repository
    UPLOAD_PPA:BOOL=OFF


.. _building_windows:

Building native Windows libs
----------------------------

EJDB binaries for `Windows` need to be build on `Linux`
using cross-compilation within the MXE cross build environment.
First of all you need to checkout the `MXE cross build environment <http://mxe.cc>`_

.. code-block:: sh

    git clone -b stable https://github.com/mxe/mxe.git

Then create/edit MXE settings file:

.. code-block:: sh

    cd <mxe checkout dir>
    nano ./settings.mk


Save the following content in  `settings.mk`:

.. code-block:: sh

    JOBS := 1
    MXE_TARGETS := x86_64-w64-mingw32.static i686-w64-mingw32.static
    LOCAL_PKG_LIST := winpthreads pcre zlib lzo bzip2 cunit
    .DEFAULT local-pkg-list:
    local-pkg-list: $(LOCAL_PKG_LIST)

Build MXE packages:

.. code-block:: sh

     cd <mxe checkout dir>
     make


Finally build `libejdb` windows binaries:

.. code-block:: sh

    export MXE_HOME=<mxe checkout dir>
    export MXE_CFG=<x86_64-w64-mingw32.static|i686-w64-mingw32.static>

    cd <ejdb checkout dir>
    mkdir build-win32
    cd build-wind32
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_TOOLCHAIN_FILE=../win64-tc.cmake ..
    make package