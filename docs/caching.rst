.. _caching:

=======
Caching
=======

To speed up subsequent queries involving similar data, pydov uses a caching
mechanism where raw DOV XML data is cached locally for later reuse.

By default, this is a global cache shared by all usages of pydov on the same
system. This means subsequent calls in the same script, multiple runs of
the same script over time and multiple implementations or applications
using pydov on the same system all use the same cache.

The default cache will reuse cached data for up to two weeks: if cached data
for an object is available and has been downloaded less than two weeks ago,
it will be reused in favor of downloading from the DOV services.

As mentioned below some convenient utility methods are provided to handle
disk usage. However, pydov does not change any files present on disk. This
holds true for all files, also those in the cache directory. It is up to the
user to keep track of disk usage etc.

Disabling the cache
*******************
You can (temporarily!) disable the caching mechanism by issuing::

    import pydov

    pydov.cache = None

This disables both the saving of newly downloaded data in the cache, as well
as reusing existing data in the cache. It remains valid for the time being of
the instantiated pydov.cache object.
It does not delete existing data in the cache.

Changing the location of cached data
************************************

By default, pydov stores the cache in a temporary directory provided by the
user's operating system. On Windows, the cache is usually located in::

    C:\Users\username\AppData\Local\Temp\pydov\

If you want the cached xml files to be saved in another location you can define
your own cache, as follows::

    import pydov.util.caching

    pydov.cache = pydov.util.caching.TransparentCache(
        cachedir=r'C:\temp\pydov'
    )

Besides controlling the cache's location, this also allows using a different
cache in different scripts or projects.

Mind that xmls are stored by search type because permalinks are not unique
across types. Therefore, the dir structure of the cache will look like, e.g.::

    ...\pydov\boring\filename.xml
    ...\pydov\filter\filename.xml


Changing the maximum age of cached data
***************************************

If you work with rapidly changing data or want to control when cached data
is renewed, you can do so by changing the maximum age of cached data to
be considered valid for the current runtime::

    import pydov.util.caching
    import datetime

    pydov.cache = pydov.util.caching.TransparentCache(
        max_age=datetime.timedelta(days=1)
    )

If a cached version exists and is younger than the maximum age, it is used
in favor of renewing the data from DOV services. If no cached version
exists or is older than the maximum age, the data is renewed and saved
in the cache.

Note that data older than the maximum age is not automatically deleted from
the cache.

Cleaning the cache
******************

During normal use the cache only grows by adding new objects and overwriting
existing ones with a new version. Should you want clean the cache of old
items or remove the cache entirely, you can do so manually by calling the
respective functions.

To clean the cache, removing all records older than the maximum age, you can
issue::

    import pydov

    pydov.cache.clean()


Since we use a temporary directory provided by the operating system, we rely
on the operating system to clean the folder when it deems necessary.

Should you want to remove the pydov cache from code yourself, you can do so
by issuing::

    import pydov

    pydov.cache.remove()


This will erase the entire cache, not only the records older than the
maximum age.
