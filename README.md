psycopg2 Python Library for DigitalOcean Functions
======================================

This is a custom compiled psycopg2 C library for Python. As DigitalOcean Functions runtime image
is missing the required PostgreSQL libraries, we have to
compile psycopg2 with the PostgreSQL `libpq.so` library statically linked
libpq library instead of the default dynamic link.

This project is forked from https://github.com/jkehler/awslambda-psycopg2 and will diverge to accommodate more cloud providers.

### How to use

#### Python 3.11
1. Copy the `psycopg2-3.11` directory into your DigitalOcean function's directory, [reference](https://docs.digitalocean.com/products/functions/reference/runtimes/python/#include-files-in-built-function).
2. Rename the directory as `pyscopg` and import the module from the relative path in your project's main function -
```python
import sys, os
sys.path.append(os.path.join(os.path.dirname(__file__), '.', 'psycopg2'))
import psycopg2
```

### Instructions on compiling this package from scratch

Here was the process that was used to build this package. You will need to
perform these steps if you want to build a newer version of the psycopg2
library.

1. Download the
  [PostgreSQL source code](https://ftp.postgresql.org/pub/source/v9.4.3/postgresql-9.4.3.tar.gz) and extract into a directory.
2. Download the
  [psycopg2 source code](http://initd.org/psycopg/tarballs/PSYCOPG-2-6/psycopg2-2.6.1.tar.gz) and extract into a directory.
3. Go into the PostgreSQL source directory and execute the following commands:
  - `./configure --prefix {path_to_postgresql_source} --without-readline --without-zlib`
  - `make`
  - `make install`
4. Go into the psycopg2 source directory and edit the `setup.cfg` file with the following:
  - `pg_config={path_to_postgresql_source/bin/pg_config}`
  - `static_libpq=1`
5. Execute `python setup.py build` in the psycopg2 source directory.

After the above steps have been completed you will then have a build directory
and the custom compiled psycopg2 library will be contained within it. Copy this
directory into your DigitalOcean Functions package and you will now be able to access
PostgreSQL from within DigitalOcean Functions using the psycopg2 library!

#### Compiling with ssl support

To compile with ssl support steps 3 and 4 above become.

3. Go into the PostgreSQL source directory and execute the following commands:
  - `./configure --prefix {path_to_postgresql_source} --without-readline --without-zlib --with-openssl`
  - `make`
  - `make install`
4. Go into the psycopg2 source directory and edit the `setup.cfg` file with the following:
  - `pg_config={path_to_postgresql_source/bin/pg_config}`
  - `static_libpq=1`
  - `libraries=ssl crypto`

All other steps are identical.
