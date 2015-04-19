Building & Installation
=======================

Installation on Windows
************************

Precompiled windows binaries
############################

`tcejdb-1.1.26-mingw32-x86_64 <tcejdb-1.1.26-mingw32-x86_64.zip>`

`tcejdb-1.1.26-mingw32-i686 <tcejdb-1.1.26-mingw32-i686.zip>`

Building EJDB and TokyoCabinet for windows
##########################################

All TokyoCabinet and EJDB API are ported to windows!

Prerequsties
-------------

* Install Linux x64 OS (tested on debian/ubuntu)
* Install all these packages:

::

    autoconf automake bash bison bzip2 cmake
    flex gcc intltool libtool make openssl
    patch perl pkg-config scons sed unzip
    wget xz-utils yasm zip wine git

Creating build environment
---------------------------

You need to checkout specific branch of `MXE cross build environment <http://mxe.cc/>`_ to build win32 and win64 binaries:

::

    git clone https://github.com/mxe/mxe.git

Then create MXE settings file:

::

    cd <mx checkout dir>
    nano ./settings.mk

And enter the following settings into `./settings.mk`:

::

    JOBS := 1
    MXE_TARGETS := x86_64-w64-mingw32 i686-w64-mingw32
    #SOURCEFORGE_MIRROR := downloads.sourceforge.net
    LOCAL_PKG_LIST := winpthreads pcre zlib cunit
    .DEFAULT local-pkg-list:
    local-pkg-list: $(LOCAL_PKG_LIST)

Then make MXE build env:

::

    cd <mx checkout dir>
    make

Building EJDB and TokyoCabinet
------------------------------

::

    git clone https://github.com/Softmotions/ejdb.git
    cd ejdb

To make win build we have to run this script: `tcejdb/mxe/mxe-build.sh`

::

    mxe-build.sh <mxe home path> w32|w64 [optional ./configure options]

Example: building win32 binaries + EJDB test cases

::

    tcejdb/mxe/mxe-build.sh ${HOME}/mxe w32 "--enable-tests"

Example: building win64 binaries:

::

    tcejdb/mxe/mxe-build.sh ${HOME}/mxe w64

As result you will get zip archive in the folder `./tcejdb: tcejdb-1.1.25-mingw32-i686.zip or tcejdb-1.1.25-mingw32-x86_64.zip`

In those archives you will found:

* TC/EJDB header files `./include/tcejdb`
* library DLL: `tcejdbdll.dll`  **(Exported All TokyoCabinet and EJDB API)**
* DLL Import library for MSVC: `tcejdbdll.lib(.exp)` and for GCC: `libtcejdbdll.a`
* Library for static linking: `libtcejdb.a`

Sample code
------------

Sample code can be found in `./tcejdb/samples/`

* sample1_mingw Netbeans project for MinGW build env
* sample1_win32 Visual studio express 2010 solution

Notes
-----

* In order to build MSVC import library we need Microsoft's `lib.exe` tool, it automatically downloaded by `mxe-build.sh` from my dropbox account. So do not be surprised =)

* Database files size on windows 32-bit are limited to 2GB, because on win32 all data is memory mapped.

* HDB `tchdbsetxmsiz()` takes no effect because all file data is memory mapped on windows platform.


------------------------------------



Installation on Debian/Ubuntu
*****************************

**EJDB C library** can be installed as debian packages

**Packages:**

::

    libtcejdb9 - Embedded JSON Database engine [runtime]
    libtcejdb9-dbg - Embedded JSON Database engine [runtime]
    libtcejdb9-dev - Embedded JSON Database engine [development]
    python3-ejdb - EJDB Python3 binding
    lua51-ejdb - EJDB Lua binding

**Ubuntu installation:**

::

    sudo add-apt-repository ppa:adamansky/ejdb
    sudo apt-get update
    sudo apt-get install libtcejdb9 libtcejdb9-dev libtcejdb9-dbg

.. note:: Debian packages published for ubuntu raring and quantal.


Your own debian packages
########################

You can build custom debian packages for your debian based system by invoking

::

    make deb-packages
    or
    make deb-source-packages


EJDB CLI
########

In order to use `EJDB CLI <https://github.com/Softmotions/ejdb/wiki/EJDB-Command-line-interface>`_ or `EJDB NodeJS binding <https://github.com/Softmotions/ejdb-node>`_ you still have to install the *nodejs* and *npm* (`https://launchpad.net/~chris-lea/+archive/node.js/ <https://launchpad.net/~chris-lea/+archive/ubuntu/node.js>`_)

::

    npm install -g ejdb




------------------------------------



Manual installation
***********************

Prerequisites
##############

**System libraries:**

* gcc
* zlib-dev

Build and install
##################

::

   cd ./tcejdb
   ./configure --prefix=<installation prefix> && make && make check
   make install

* library name: **tcejdb** (with pkgconfig)
* main include header: <tcejdb/ejdb.h>
