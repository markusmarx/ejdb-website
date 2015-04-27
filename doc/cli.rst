.. _cli:

Command line interface
======================

EJDB command line interface is provided as the part of :ref:`ejdb-node module <nodejs>` published in
`Nodejs Package Manager(NPM) repository <https://www.npmjs.com/package/ejdb>`_.

Installation
------------

You need the :ref:`ejdb-node module <nodejs>` being installed on your system, follow
the :ref:`ejdb-node installation guide <nodejs_installation>` to do this.

Program options
---------------

The :program:`ejdb` program has the following options:

.. program:: ejdb

.. option:: -h, --help

    Display usage summary and ejdb version.

.. option:: -n, --no-colors

    Do not use colored output.

.. option:: -q, --quiet

    Run in quiet output mode

.. option:: -c, --cmd

    Run the specified javascript command


Usage demo
----------

.. code-block:: sh

    $ ejdb
    Welcome to EJDB CLI v1.2.6-1
    ejdb>


This is javascript interpreter with db and EJDB objects in the context, to describe db just type: `db <ENTER>`

.. code-block:: js

    ejdb> db
    { open: [Function] (dbFile, [openMode], [cb]) Open database,
      status: [Function] Get current database status,
      close: [Function] ([cb]) Close database }
    ejdb>

Let's open/create db:

.. code-block:: js

    ejdb> db.open('mydb');
    { file: '/home/adam/mydb', collections: [] }

For opened database we have more methods to operate:

.. code-block:: js

    { close: [Function] ([cb]) Close database,
      status: [Function] Get current database status,
      find: [Function] (cname, [qobj], [qobjarr], [hints], [cb])
            Execute query on collection,
      ensureCollection: [Function] (cname, [copts], [cb])
            Creates new collection if it does't exists,
      dropCollection: [Function] (cname, [prune], [cb])
                      Drop collection, if `prune` is true collection
                      db files will be erased from disk.,
      save: [Function] (cname, object|array of object, [opts], [cb])
                        Save/update specified JSON objects in the collection.,
      update: [Function] (cname, [qobj], [qobjarr], [hints], [cb])
                         Perform update query on collection,
      isOpen: [Function] Check if database in opened state,
      removeCollection: [Function],
      load: [Function] (cname, oid, [cb])
                        Loads object identified by OID from the collection,
      remove: [Function] (cname, oid, [cb]) Removes object from the collection,
      findOne: [Function] (cname, [qobj], [qobjarr], [hints], [cb])
                          Retrive one object from the collection,
      command: [Function],
      count: [Function] (cname, [qobj], [qobjarr], [hints], [cb])
                        Convenient count(*) operation,
      sync: [Function] Synchronize entire EJDB database with disk,
      dropIndexes: [Function] (cname, path, [cb])
                              Drop indexes of all types for JSON field path,
      optimizeIndexes: [Function] (cname, path, [cb])
                                  Optimize indexes of all types for JSON field path,
      ensureStringIndex: [Function] (cname, path, [cb])
                                    Ensure String index for JSON field path,
      rebuildStringIndex: [Function] (cname, path, [cb]),
      dropStringIndex: [Function] (cname, path, [cb]),
      ensureIStringIndex: [Function] (cname, path, [cb])
                                     Ensure case insensitive String index for JSON field path,
      rebuildIStringIndex: [Function] (cname, path, [cb]),
      dropIStringIndex: [Function] (cname, path, [cb]),
      ensureNumberIndex: [Function] (cname, path, [cb])
                                    Ensure index presence of Number type for JSON field path,
      rebuildNumberIndex: [Function] (cname, path, [cb]),
      dropNumberIndex: [Function] (cname, path, [cb]),
      ensureArrayIndex: [Function] (cname, path, [cb])
                                    Ensure index presence of Array type for JSON field path,
      rebuildArrayIndex: [Function] (cname, path, [cb]),
      dropArrayIndex: [Function] (cname, path, [cb]),
      getDBMeta: [Function] Get description of EJDB database and its collections,
      beginTransaction: [Function] Begin collection transaction,
      commitTransaction: [Function] Commit collection transaction,
      rollbackTransaction: [Function] Rollback collection transaction,
      getTransactionStatus: [Function] Get collection transaction status }
    ejdb>


Then save something:

.. code-block:: js

    ejdb> db.save('mycollection', {foo : 'bar'});
    [ { foo: 'bar',
        _id: '50d2f2fa7075995500000000' } ]
    ejdb> db.save('mycollection', {foo : 'bar2'});
    [ { foo: 'bar2',
        _id: '50d2f3007075995500000001' } ]
    ejdb> db.save('mycollection', {foo : 'bar3'});
    [ { foo: 'bar3',
        _id: '50d2f3037075995500000002' } ]
    ejdb>

Show db status and its collections:

.. code-block:: js

    ejdb> db.getDBMeta()
    { file: '/home/adam/mydb',
      collections:
       [ { name: 'mycollection',
           file: '/home/adam/mydb_mycollection',
           records: 3,
           options:
            { buckets: 131071,
              cachedrecords: 0,
              large: false,
              compressed: false },
           indexes: [] } ] }
    ejdb>


Select first record:

.. code-block:: js

    ejdb> db.findOne('mycollection');
    { _id: '50d2f2fa7075995500000000',
      foo: 'bar' }

All records:

.. code-block:: js

    var cursor = db.find('mycollection');
    Found 3 records
    { _id: '50d2f2fa7075995500000000',
      foo: 'bar' }
    { _id: '50d2f3007075995500000001',
      foo: 'bar2' }
    { _id: '50d2f3037075995500000002',
      foo: 'bar3' }


We can do more with saved `cursor` object:


.. code-block:: js

    ejdb> cursor
    { pos: 0,
      length: 3,
      close: [Function: close],
      reset: [Function: reset],
      hasNext: [Function: hasNext],
      next: [Function: next],
      field: [Function: field],
      object: [Function: object] }
    ejdb>
    ejdb> while (cursor.next()) console.log("foo" + cursor.field("foo"));
    foobar
    foobar2
    foobar3
    undefined
    ejdb>

Finish this little demo:

.. code-block:: js

    ejdb> db.close();
    undefined
    ejdb> Bye!




