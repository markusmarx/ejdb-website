.. _ql:

Query language
--------------
The form of EJDB queries inspired by `Mongodb <http://mongodb.org>`_ and follows the same philosophy. In many cases
the EJDB queries are fully comparable with mongodb counterparts. Every EJDB query object can be considered as
JSON document which specifies the way how to retrieve or update a set of documents stored in particular database collection.
The query objects can be joined together by `AND` or `OR` logical conjunctions. The way how to actually specify
queries depends on an API of particular EJDB :ref:`language binding <bindings>`, anyway we will use an abstract
queries represented as plain JSONs with some showcases where the :ref:`EJDB Nodejs CLI API <cli>` used.

First we define the following terms: :term:`abstract document` as a metadocument for all
documents containing in particular collection without an actual field values and with all
possible document fields and their types belonging to any document in the collection.
You can consider `abstract document` as a prototype for all possible documents in the collection.
In the context of an `abstract document` a :term:`fieldpath` is the path consisting of JSON field names traversed
from the document's root to the particular document field.


.. contents::


Query operators
***************

Simple matching
^^^^^^^^^^^^^^^

.. code-block:: js

    {'fpath' : val, ...}

Select all documents there their `fieldpath` values exactly matched to the values provided in query.

**Example:**

.. code-block:: js

    ejdb> db.addressbook.find({'firstName': 'Andy', age: 39});
    Found 1 records
    { _id: '55352c20a462ec6800000000',
      firstName: 'Andy',
      age: 39 }


As argument of simple matching query values you can use a regular expressions:

.. code-block:: js

    db.addressbook.find({'firstName': /An.*/});
    Found 1 records
    { _id: '55352c20a462ec6800000000',
      firstName: 'Andy',
      age: 39 }

Negation logical operation ($not)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Any part of query can be wrapped by `$not` negation operator:

.. code-block:: js

    //The field is not equal to val
    {'fpath' : {'$not' : val}}

    //The field is not equal to the provided subquery condition `{...}`
    {'fpath' : {'$not' : {...}}}


Negation not in ($nin)
^^^^^^^^^^^^^^^^^^^^^^

Negation of `$in` operator.
The field value is not equal to any of provided alternatives.

**Example:**

.. code-block:: js

    db.find('name' : {"$nin" : ['John Travolta', 'Ivanov']});


**Example:**

.. code-block:: js


    //Name not begins with 'Andy'
    {'name' : {'$not' : {'$begin' : 'Andy'}}}


.. note::
    Negation operations: `$not` and `$nin` are not using collection indexes
    so they can be slower in comparison to other matching operations.


Case insensitive string matching ($icase)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: js

    {'fpath' : {'$icase' : '...'}}

**Example:**
Case insensitive matching within `$in` operator:

.. code-block:: js

    db.building.find({'name' : {'$icase' : {'$in' : ['théâtre - театр', 'hello world']}}

In order to perform effective case insensitive queries consider creating `JBIDXISTR` index on fields:

**Nodejs API:**

.. code-block:: js

    ejdb> db.ensureIStringIndex
    [Function] (cname, path, [cb]) Ensure case insensitive String index for JSON field path


**C API:**

.. code-block:: c

    flags = flags | JBIDXISTR;
    EJDB_EXPORT bool ejdbsetindex(EJCOLL *coll, const char *ipath, int flags);


String starts with prefix ($begin)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fieldpath starts with specified prefix:

.. code-block:: js

    'fpath' : {'$begin' : prefix}}


Simple projections
^^^^^^^^^^^^^^^^^^

You can select only specific document fields by providing `$fields` query :ref:`hints <qhints>`:

.. code-block:: js

    db.addressbook.find({'firstName': /An.*/}, {$fields: {age:1}});


.. _qhints:

Query hints
***********

$max
^^^^

The maximum number of documents retrieved.


$skip
^^^^^

The number of skipped results in the result set


$orderby
^^^^^^^^

The sorting order of query fields specified as JSON mapping of document `fieldpaths`
to its orderby modes:

.. code-block:: js

    {$orderby: {'fpath': mode, ...}

Where `mode` is and integer specified sort order:

* `-1` Descending sort
* `1` Ascending sort

**Example:**

.. code-block:: js

   db.addressbook.find({}, {$orderby: {age:1, name:-1}});


$fields
^^^^^^^

The document fields projection.

.. code-block:: js

    {$fields: {'fpath': mode, ...}

Where `mode` is an integer specified the field inclusion mode:

* `-1` Exclude field
* `1` Include field

.. note::
    `$fields` hint cannot mix include and exclude fields together

The mongodb `$ (projection) <http://docs.mongodb.org/manual/reference/operator/projection/positional/#proj._S_>`_ is also supported.
Our implementation overcomes the mongodb restriction:
`Only one array field can appear in the query document`


Glossary
--------

.. glossary::

    abstract document
        Abstract document is a  metadocument for all documents containing in particular collection without an actual
        field values and with all possible document fields and their types belonging to any document the collection.
        You can consider `abstract document` as a prototype for all possible documents in the collection.

    fieldpath
        In the context of an `abstract document` a :term:`fieldpath`
        is the path consisting of JSON field names traversed from the document's
        root to the particular document field.



.. Create query object.
 * Sucessfully created queries must be destroyed with ejdbquerydel().
 *
 * EJDB queries inspired by MongoDB (mongodb.org) and follows same philosophy.
 *
 *  - Supported queries:
 *      - Simple matching of String OR Number OR Array value:
 *          -   {'fpath' : 'val', ...}
 *      - $not Negate operation.
 *          -   {'fpath' : {'$not' : val}} //Field not equal to val
 *          -   {'fpath' : {'$not' : {'$begin' : prefix}}} //Field not begins with val
 *      - $begin String starts with prefix
 *          -   {'fpath' : {'$begin' : prefix}}
 *      - $gt, $gte (>, >=) and $lt, $lte for number types:
 *          -   {'fpath' : {'$gt' : number}, ...}
 *      - $bt Between for number types:
 *          -   {'fpath' : {'$bt' : [num1, num2]}}
 *      - $in String OR Number OR Array val matches to value in specified array:
 *          -   {'fpath' : {'$in' : [val1, val2, val3]}}
 *      - $nin - Not IN
 *      - $strand String tokens OR String array val matches all tokens in specified array:
 *          -   {'fpath' : {'$strand' : [val1, val2, val3]}}
 *      - $stror String tokens OR String array val matches any token in specified array:
 *          -   {'fpath' : {'$stror' : [val1, val2, val3]}}
 *      - $exists Field existence matching:
 *          -   {'fpath' : {'$exists' : true|false}}
 *      - $icase Case insensitive string matching:
 *          -    {'fpath' : {'$icase' : 'val1'}} //icase matching
 *          Ignore case matching with '$in' operation:
 *          -    {'name' : {'$icase' : {'$in' : ['théâtre - театр', 'hello world']}}}
 *          For case insensitive matching you can create special index of type: `JBIDXISTR`
 *      - $elemMatch The $elemMatch operator matches more than one component within an array element.
 *          -    { array: { $elemMatch: { value1 : 1, value2 : { $gt: 1 } } } }
 *          Restriction: only one $elemMatch allowed in context of one array field.
 *      - $and, $or joining:
 *          -   {..., $and : [subq1, subq2, ...] }
 *          -   {..., $or  : [subq1, subq2, ...] }
 *          Example: {z : 33, $and : [ {$or : [{a : 1}, {b : 2}]}, {$or : [{c : 5}, {d : 7}]} ] }
 *
 *      - Mongodb $(projection) operator supported. (http://docs.mongodb.org/manual/reference/projection/positional/#proj._S_)
 *      - Mongodb positional $ update operator supported. (http://docs.mongodb.org/manual/reference/operator/positional/)
 *
 *  - Queries can be used to update records:
 *
 *      $set Field set operation.
 *          - {.., '$set' : {'fpath1' : val1, 'fpathN' : valN}}
 *      $upsert Atomic upsert. If matching records are found it will be '$set' operation,
 *              otherwise new record will be inserted
 *              with fields specified by argment object.
 *          - {.., '$upsert' : {'fpath1' : val1, 'fpathN' : valN}}
 *      $inc Increment operation. Only number types are supported.
 *          - {.., '$inc' : {'fpath1' : number, ...,  'fpath2' : number}
 *      $dropall In-place record removal operation.
 *          - {.., '$dropall' : true}
 *      $addToSet Atomically adds value to the array only if its not in the array already.
 *                If containing array is missing it will be created.
 *          - {.., '$addToSet' : {'fpath' : val1, 'fpathN' : valN, ...}}
 *      $addToSetAll Batch version if $addToSet
 *          - {.., '$addToSetAll' : {'fpath' : [array of values to add], ...}}
 *      $pull  Atomically removes all occurrences of value from field, if field is an array.
 *          - {.., '$pull' : {'fpath' : val1, 'fpathN' : valN, ...}}
 *      $pullAll Batch version of $pull
 *          - {.., '$pullAll' : {'fpath' : [array of values to remove], ...}}
 * 		$rename Rename field operation
 * 			- {.., '$rename' : {'oldfname1' : 'newfname1', 'oldfnameN' : 'newfnameN'}}
 *      $unset Unset the specified fields
 *          - { $unset: { 'fpath1' : "", ... } }
 *		$slice Array field slice operator (like a mongodb $slice) implemented
 * 			within $do operation.
 * 			- ${..., $do : {'fpath1' : {$slice : <limit>}}
 *  		- ${..., $do : {'fpath1' : {$slice : [<offset>, <limit>]}}
 *
 *
 * - Collection joins supported in the following form:
 *      {..., $do : {fpath : {$join : 'collectionname'}} }
 *      Where 'fpath' value points to object's OIDs from 'collectionname'. Its value
 *      can be OID, string representation of OID or array of this pointers.
 *
 *  NOTE: It is better to execute update queries with `JBQRYCOUNT`
 *        control flag to avoid unnecessarily data fetching.
 *
 *  NOTE: Negate operations: $not and $nin not using indexes
 *  so they can be slow in comparison to other matching operations.
 *
 *  NOTE: Only one index can be used in search query operation.
 *
 *  QUERY HINTS (specified by `hints` argument):
 *      - $max Maximum number in the result set
 *      - $skip Number of skipped results in the result set
 *      - $orderby Sorting order of query fields.
 *      - $fields Set subset of fetched fields
 *          If a field presented in $orderby clause it will be forced to include in resulting records.
 *          Example:
 *          hints:    {
 *                      "$orderby" : { //ORDER BY field1 ASC, field2 DESC
 *                          "field1" : 1,
 *                          "field2" : -1
 *                      },
 *                      "$fields" : { //SELECT ONLY {_id, field1, field2}
 *                          "field1" : 1,
 *                          "field2" : 1
 *                      }
 *                    }
 *
 * Many query examples can be found in `testejdb/t2.c` test case.
 *
 * @param jb EJDB database handle.
 * @param qobj Main BSON query object.
 * @param orqobjs Array of additional OR query objects (joined with OR predicate).
 * @param orqobjsnum Number of OR query objects.
 * @param hints BSON object with query hints.
 * @return On success return query handle. On error returns NULL.
 */




