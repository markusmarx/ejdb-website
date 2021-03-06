.. The EJDB documentation root

Welcome to EJDB!
================

EJDB v1.2.8 released
--------------------
2015-01-06

**Changelog:**

* Fix: Problem with the bson2json conversion dealing with doubles `#135 <https://github.com/Softmotions/ejdb/issues/135>`_
*	$push and $pushAll operations are implemented. `#130 <https://github.com/Softmotions/ejdb/issues/130>`_
* Fix: $rename can operate on nested json objects `#107 <https://github.com/Softmotions/ejdb/issues/107>`_
* Fix: $inc doesn't create key if it doesn't exist `#120 <https://github.com/Softmotions/ejdb/issues/120>`_
* Source code style fixes
* A data format version info now stored in the database meta header `#139 <https://github.com/Softmotions/ejdb/issues/139>`_

Mission
-------
EJDB is the embeddable (in-process) database engine designed for querying collections
of `JSON <http://json.org>`_ documents and persisting data.
EJDB is implemented as a C library based on `Tokyo Cabinet <http://fallabs.com/tokyocabinet/>`_
key-value storage engine. EJDB design is inspired by `Mongodb <http://mongodb.org>`_ and follows the same philosophy ,
especially for the :ref:`query language <ql>`

EJDB is good for:

* In-process database for standalone desktop applications. (The SQLite niche);
* Games development;
* Simple server-side solution;
* Whenever a fast in-process data-communication with storage layer is much preferred over TCP/IP transport;

License
-------
EJDB is the open source project distributed under the terms of `GNU LGPL v2.1 <http://opensource.org/licenses/LGPL-2.1>`_
license. Basically, that means you can freely use it in your commercial and open source projects, but
if you change some part of EJDB code you should open-source these changes under LGPL licence.

Features
--------

* Generic key-value storage using Hash or B+ tree index.
* Collection level transactions.
* Mongodb-like queries and overall philosophy.
* Collection joins in query language.
* Language bindings: Nodejs, Python, Lua, Java, Ruby, .Net, Go


Community
=========

* `EJDB on Github <https://github.com/Softmotions/ejdb>`_
* `@ejdblab Twitter <https://twitter.com/ejdblab>`_
* `EJDB on Gitter <https://gitter.im/Softmotions/ejdb>`_
* `Google groups <https://groups.google.com/forum/#!forum/ejdb>`_

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
