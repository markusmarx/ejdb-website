Development FAQ
===============

bson_destroy is not bson_del (bson.h)
*************************************

* `bson_destroy()` clears bson object and frees internal memory buffers held by bson object but does not delete bson itself
* `bson_del()` performs `bson_destroy()` then frees bson object itself.
