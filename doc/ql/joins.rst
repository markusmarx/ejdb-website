.. _joins:

Collection joins
================

Joins can be specified in query within the :ref:`$do`:

``{..., $do : {fieldpath1: {$join: collection1}, fieldpath2 : {$join: collection2}} }``

Where a value specified by `fieldpath` should point to `OID`s` of objects stored
in the specific `collection`. Its value can be OID, string representation of OID, or array of OIDs.
Upon query execution OID value stored in `fieldpath` will be replaced by
joined objects fetched from `collection`.


Simple joins showcase
---------------------

This showcase based on trivial `rent a car` data model.

Create/open the database
************************

.. code-block:: js

    $ ~/tmp$ ejdb rentacar
    Welcome to EJDB CLI v1.0.55
    ejdb> db.open('rentacar')
    { file: '/home/adam/tmp/rentacar', collections: [] }
    ejdb>

Save our cars fleet
*******************

.. code-block:: js

    ejdb> var fleet = [
    ... {model:'Honda Accord', year:2005},
    ... {model:'Toyota Corolla', year:2011},
    ... {model:'Toyota Camry', year:2008}
    ... ];

    ejdb> db.save('cars', fleet);
    [ { model: 'Honda Accord',
        year: 2005,
        _id: '50fe0a9935cf1e0300000000' },
      { model: 'Toyota Corolla',
        year: 2011,
        _id: '50fe0a9935cf1e0300000001' },
      { model: 'Toyota Camry',
        year: 2008,
        _id: '50fe0a9935cf1e0300000002' } ]
    ejdb>


Then register a rent orders
***************************

.. code-block:: js

    ejdb> var orders = [
    ...  {car:'50fe0a9935cf1e0300000000', pickUpDate: new Date(2013, 05, 20, 07),
    ...   returnDate: new Date(2013, 05, 27, 18), customer: 'andy'},
    ...  {car:'50fe0a9935cf1e0300000002', pickUpDate: new Date(2013, 05, 11, 16),
    ...   returnDate: new Date(2013, 05, 15, 13), customer: 'john'},
    ...  {car:'50fe0a9935cf1e0300000002', pickUpDate: new Date(2013, 05, 15, 17),
    ...   returnDate: new Date(2013, 05, 21, 12), customer: 'antony'}
    ... ];

    ejdb> db.save('orders', orders);
    [ { car: '50fe0a9935cf1e0300000000',
        pickUpDate: Thu Jun 20 2013 07:00:00 GMT+0700 (NOVT),
        returnDate: Thu Jun 27 2013 18:00:00 GMT+0700 (NOVT),
        customer: 'andy',
        _id: '50fe0f3735cf1e0300000003' },
      { car: '50fe0a9935cf1e0300000002',
        pickUpDate: Tue Jun 11 2013 16:00:00 GMT+0700 (NOVT),
        returnDate: Sat Jun 15 2013 13:00:00 GMT+0700 (NOVT),
        customer: 'john',
        _id: '50fe0f3735cf1e0300000004' },
      { car: '50fe0a9935cf1e0300000002',
        pickUpDate: Sat Jun 15 2013 17:00:00 GMT+0700 (NOVT),
        returnDate: Fri Jun 21 2013 12:00:00 GMT+0700 (NOVT),
        customer: 'antony',
        _id: '50fe0f3735cf1e0300000005' } ]
    ejdb>


Find all rent orders with joined cars
*************************************

.. code-block:: js

    ejdb> db.find('orders', {$do: {car: {$join:'cars'}}});
    Found 3 records
    { _id: '50fe0f3735cf1e0300000003',
      car:
       { _id: '50fe0a9935cf1e0300000000',
         model: 'Honda Accord',
         year: 2005 },
      pickUpDate: Thu Jun 20 2013 07:00:00 GMT+0700 (NOVT),
      returnDate: Thu Jun 27 2013 18:00:00 GMT+0700 (NOVT),
      customer: 'andy' }
    { _id: '50fe0f3735cf1e0300000004',
      car:
       { _id: '50fe0a9935cf1e0300000002',
         model: 'Toyota Camry',
         year: 2008 },
      pickUpDate: Tue Jun 11 2013 16:00:00 GMT+0700 (NOVT),
      returnDate: Sat Jun 15 2013 13:00:00 GMT+0700 (NOVT),
      customer: 'john' }
    { _id: '50fe0f3735cf1e0300000005',
      car:
       { _id: '50fe0a9935cf1e0300000002',
         model: 'Toyota Camry',
         year: 2008 },
      pickUpDate: Sat Jun 15 2013 17:00:00 GMT+0700 (NOVT),
      returnDate: Fri Jun 21 2013 12:00:00 GMT+0700 (NOVT),
      customer: 'antony' }


We have top cars monthly rating
*******************************

.. code-block:: js

    ejdb> var topcars =
               {month: 'June',
                cars: ['50fe0a9935cf1e0300000002',
                       '50fe0a9935cf1e0300000000']};
    ejdb> db.save('topcars', topcars);
    [ { month: 'June',
        cars:
         [ '50fe0a9935cf1e0300000002',
           '50fe0a9935cf1e0300000000' ],
        _id: '50fe106b35cf1e0300000006' } ]
    ejdb>


Get top cars **June** rating with joined array field
****************************************************

.. code-block:: js

    ejdb> db.find('topcars',
                  {month: 'June',
                   $do: {'cars': {$join: 'cars'}}});
    Found 1 records
    { _id: '50fe106b35cf1e0300000006',
      month: 'June',
      cars:
       [ { _id: '50fe0a9935cf1e0300000002',
           model: 'Toyota Camry',
           year: 2008 },
         { _id: '50fe0a9935cf1e0300000000',
           model: 'Honda Accord',
           year: 2005 } ] }


