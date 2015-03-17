Data Import/Export
==================

EJDB C API
**********
C library implements the following API methods to import/export database data:

::

 /**
 * Exports database collections data to the specified directory.
 * Database read lock will be taken on each collection.
 *
 * NOTE: Only data exported as BSONs can be imported with `ejdbimport()`
 *
 * @param jb EJDB database handle.
 * @param path The directory path in which data will be exported.
 * @param cnames List of collection names to export. `NULL` implies that all existing collections will be exported.
 * @param flags. Can be set to `JBJSONEXPORT` in order to export data as JSON files instead exporting into BSONs.
 * @param log Optional operation string log buffer.
 * @return on sucess `true`
 */

 bool ejdbexport(EJDB *jb, const char *path, TCLIST *cnames, int flags, TCXSTR *log)

::

 /**
 * Imports previously exported collections data into ejdb.
 * Global database write lock will be applied during import operation.
 *
 * NOTE: Only data exported as BSONs can be imported with `ejdbimport()`
 *
 * @param jb EJDB database handle.
 * @param path The directory path in which data resides.
 * @param cnames List of collection names to import. `NULL` implies that all collections found in `path` will be imported.
 * @param flags Can be one of:
 *             `JBIMPORTUPDATE`  Update existing collection entries with imported ones.
 *                               Existing collections will not be recreated.
 *                               For existing collections options will not be imported.
 *
 *             `JBIMPORTREPLACE` Recreate existing collections and replace
 *                               all their data with imported entries.
 *                               Collections options will be imported.
 *
 *             `0`              Implies `JBIMPORTUPDATE`
 * @param log Optional operation log buffer.
 * @return
 */
 bool ejdbimport(EJDB *jb, const char *path, TCLIST *cnames, int flags, TCXSTR *log);

::

 /**
 * Execute ejdb database command.
 *
 * Supported commands:
 *
 *
 *  1) Exports database collections data. See ejdbexport() method.
 *
 *    "export" : {
 *          "path" : string,                    //Exports database collections data
 *          "cnames" : [string array]|null,     //List of collection names to export
 *          "mode" : int|null                   //Values: null|`JBJSONEXPORT` See ejdbexport() method
 *    }
 *
 *    Command response:
 *       {
 *          "log" : string,        //Diagnostic log about executing this command
 *          "error" : string|null, //ejdb error message
 *          "errorCode" : int|0,   //ejdb error code
 *       }
 *
 *  2) Imports previously exported collections data into ejdb.
 *
 *    "import" : {
 *          "path" : string                     //The directory path in which data resides
 *          "cnames" : [string array]|null,     //List of collection names to import
 *          "mode" : int|null                //Values: null|`JBIMPORTUPDATE`|`JBIMPORTREPLACE` See ejdbimport() method
 *     }
 *
 *     Command response:
 *       {
 *          "log" : string,        //Diagnostic log about executing this command
 *          "error" : string|null, //ejdb error message
 *          "errorCode" : int|0,   //ejdb error code
 *       }
 *
 * @param jb    EJDB database handle.
 * @param cmd   BSON command spec.
 * @return Allocated BSON command response object. Caller shoud call `bson_del()` on it.
 */
 bson* ejdbcommand(EJDB *jb, bson *cmdbson);

Other EJDB bindings
********************

The following EJDB bindings implement the ejdbcommand() API method:

* `Lua <https://github.com/Softmotions/ejdb-lua>`_
* `Python <https://github.com/Softmotions/ejdb-python>`_
* `NodeJS <https://github.com/Softmotions/ejdb-node>`_
* `C# <https://github.com/Softmotions/ejdb-csharp>`_
* `Google Go <https://github.com/mkilling/goejdb>`_
* `Ruby <https://github.com/Softmotions/ejdb-ruby>`_
* `Pike <https://github.com/hww3/pike_modules-ejdb>`_
* `Java <https://github.com/Softmotions/ejdb-java>`_


