.. _limitations:

Known limitations
=================

Tokyo Cabinet issues
--------------------

Limited concurrency and scalability on big data sets (>10M records) This is known issue of tokyocabinet.
The problem is caused by many factors:

* No more than 255 concurrent rw-locks can be acquired in context of overall hash buckets space.
  So on large data sets we need to lock very wide bucket areas even in the case of accessing a small data ranges.
* Limited number of hash buckets allocated for B+ underlying storage. (B+ tree data blocks stored in the hash database `tchdb`)
* The `tchdb` free blocks pool implemented as simple linked list of limited size.
  Database blocks defragmented and sorted time to time based on some heuristic which
  may lead to relatively high deviation of data access time.

Multiprocess database access
----------------------------

EJDB can be used by threads within a single process only. EJDB |ejdbversion| is not
allowed for using from different OS processes.


Indexes
-------

* Compound indexes are not supported
* Indexes for nested array objects are not working `#37 <https://github.com/Softmotions/ejdb/issues/37>`_


Misc
----

* The `_id` primary key field of every document must be only of :term:`ObjectId` type.
* The `_id` primary key field cannot be traversed in sorted order in queries.
