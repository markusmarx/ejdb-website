.. The EJDB documentation root

Welcome to EJDB!
================

Mission
-------
EJDB is the embeddable (in-process) database engine designed for querying collections
of `JSON <http://json.org>`_ documents and persisting data.
EJDB implemented as C library based on `Tokyo Cabinet <http://fallabs.com/tokyocabinet/>`_
key-value storage engine. EJDB design inspired by `Mongodb <http://mongodb.org>`_ and follows the same philosophy ,
especially for the :ref:`query language <ql>`

EJDB is good for:

* In-process database for standalone desktop applications. (The SQLite niche).
* Games development.
* Simple server-side solution.
* Whenever a fast in-process data-communication with storage layer is much preferred over TCP/IP transport.

License
-------
EJDB is the open source project distributed under terms of `GNU LGPL v2.1 <http://opensource.org/licenses/LGPL-2.1>`_
license. Basically this means you can freely use it in your commercial and open source projects, but
if you change some part of EJDB code you should open-source these changes under LGPL licence.

Features
--------

* Generic key-value storage using Hash or B+ tree index.
* Collection level transactions.
* Mongodb-like queries and overall philosophy.
* Collection joins in query language.
* Language bindings: Nodejs, Python, Lua, Java, Ruby, .Net, Go

Documentation
-------------
.. toctree::
    :maxdepth: 1

    news
    doc/snippets
    doc/install/index
    doc/cli
    doc/ql/ql
    doc/ql/joins
    doc/internals/index
    Language bindings <doc/bindings/index>
    doc/iexp/index
    doc/limitations
    doc/mongodb
