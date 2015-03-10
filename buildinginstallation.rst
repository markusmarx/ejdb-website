Building & Installation
=======================

Installation on Windows
************************


Installation on Debian/Ubuntu
*****************************

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
