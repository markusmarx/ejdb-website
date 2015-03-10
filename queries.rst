Queries
========

Create query object.
Sucessfully created queries must be destroyed with ejdbquerydel().

EJDB queries inspired by MongoDB (mongodb.org) and follows same philosophy.
 
Simple matching
****************

Simple matching of String OR Number OR Array value:

.. code-block:: c

    -   {'fpath' : 'val', ...}

$not Negate operation
**********************

.. code-block:: c

    -   {'fpath' : {'$not' : val}} //Field not equal to val
    -   {'fpath' : {'$not' : {'$begin' : prefix}}} //Field not begins with val
 
$begin String starts with prefix
***********************************

.. code-block:: c

    -   {'fpath' : {'$begin' : prefix}}


$gt, $gte (>, >=) and $lt, $lte for number types
**************************************************

.. code-block:: c

    -   {'fpath' : {'$gt' : number}, ...}

$bt Between for number types
*****************************

.. code-block:: c

    -   {'fpath' : {'$bt' : [num1, num2]}}

$in String
**********

`$in` String OR Number OR Array val matches to value in specified array:

.. code-block:: c

    -   {'fpath' : {'$in' : [val1, val2, val3]}}

$nin - Not IN
*************

$strand String tokens
*********************

$strand String tokens OR String array val matches all tokens in specified array:

.. code-block:: c

    -   {'fpath' : {'$strand' : [val1, val2, val3]}}


$stror String tokens
********************

$stror String tokens OR String array val matches any token in specified array:

.. code-block:: c

    -   {'fpath' : {'$stror' : [val1, val2, val3]}}


$exists Field existence matching
*********************************

.. code-block:: c

    -   {'fpath' : {'$exists' : true|false}}


$icase Case insensitive string matching
***************************************

.. code-block:: c

    -    {'fpath' : {'$icase' : 'val1'}} //icase matching

Ignore case matching with '$in' operation:

.. code-block:: c

    -    {'name' : {'$icase' : {'$in' : ['tHéâtre - театр', 'heLLo WorlD']}}}

For case insensitive matching you can create special index of type: `JBIDXISTR`


$elemMatch 
**********

The `$elemMatch` operator matches more than one component within an array element.

.. code-block:: c

    -  { array: { $elemMatch: { value1 : 1, value2 : { $gt: 1 } } } }

Restriction: only one `$elemMatch` allowed in context of one array field.

$and, $or joining:
******************

.. code-block:: c

    -   {..., $and : [subq1, subq2, ...] }
    -   {..., $or  : [subq1, subq2, ...] }


Example: 

.. code-block:: c

    {z : 33, $and : [ {$or : [{a : 1}, {b : 2}]}, {$or : [{c : 5}, {d : 7}]} ] }





Mongodb $(projection) operator supported. (http://docs.mongodb.org/manual/reference/projection/positional/#proj._S_)
Mongodb positional $ update operator supported. (http://docs.mongodb.org/manual/reference/operator/positional/)


**Queries can be used to update records:**


$set Field set operation
*************************

.. code-block:: c

    - {.., '$set' : {'field1' : val1, 'fieldN' : valN}}
$upsert Atomic upsert
*************************

If matching records are found it will be '$set' operation, otherwise new record will be inserted with fields specified by argment object.

.. code-block:: c

    - {.., '$upsert' : {'field1' : val1, 'fieldN' : valN}}

$inc Increment operation
*************************

Only number types are supported.

.. code-block:: c

    - {.., '$inc' : {'field1' : number, ...,  'field1' : number}
   

$dropall
*********

In-place record removal operation

.. code-block:: c

    - {.., '$dropall' : true}


$addToSet
*********

Atomically adds value to the array only if its not in the array already.
If containing array is missing it will be created.

.. code-block:: c

    - {.., '$addToSet' : {'fpath' : val1, 'fpathN' : valN, ...}}

$addToSetAll
************
Batch version if `$addToSet`

.. code-block:: c

    - {.., '$addToSetAll' : {'fpath' : [array of values to add], ...}}

$pull
*****

Atomically removes all occurrences of value from field, if field is an array.

.. code-block:: c

    - {.., '$pull' : {'fpath' : val1, 'fpathN' : valN, ...}}

$pullAll
*********

Batch version of `$pull`

.. code-block:: c

    - {.., '$pullAll' : {'fpath' : [array of values to remove], ...}}

.. note:: Negate operations: `$not` and `$nin` not using indexes so they can be slow in comparison to other matching operations.

.. note:: Only one index can be used in search query operation.


QUERY HINTS
***********

Specified by `hints` argument

* `$max` Maximum number in the result set
* `$skip` Number of skipped results in the result set
* `$orderby` Sorting order of query fields.
* `$fields` Set subset of fetched fields

If a field presented in $orderby clause it will be forced to include in resulting records.
Example:

.. code-block:: c

    hints:    {
    "$orderby" : { //ORDER BY field1 ASC, field2 DESC
    "field1" : 1,
    "field2" : -1
    },
    "$fields" : { //SELECT ONLY {_id, field1, field2}
    "field1" : 1,
    "field2" : 1
    }
    }

.. seealso:: Many query examples can be found in `testejdb/t2.c` test case.

.. code-block:: c

    * @param EJDB database handle.
    * @param qobj Main BSON query object.
    * @param orqobjs Array of additional OR query objects (joined with OR predicate).
    * @param orqobjsnum Number of OR query objects.
    * @param hints BSON object with query hints.
    * @return On success return query handle. On error returns NULL.


.. code-block:: c

    EJDB_EXPORT EJQ* ejdbcreatequery(EJDB *jb, bson *qobj, bson *orqobjs, int orqobjsnum, bson *hints);
