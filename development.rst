Development
===========

Requirements
------------

- Git
- Go >= 1.11

.. _checkout-sources:

Checkout the source code
------------------------

Fork the project and clone the repository locally.

Since Go 1.11, you don't need to work inside the ``$GOPATH``.
You can checkout the source code anywhere on your filesystem.

Miniflux is using `Go Modules <https://github.com/golang/go/wiki/Modules>`_ to manage dependencies.

Build a binary of the application
---------------------------------

.. code:: bash

    # Build all binaries for all supported platforms
    make build

    # Build Linux binary for amd64 architecture
    make linux-amd64

    # ARM 64 bits (arm64v8)
    make linux-armv8

    # ARM 32 bits variant 7 (arm32v7)
    make linux-armv7

    # ARM 32 bits variant 6 (arm32v6)
    make linux-armv6

    # ARM 32 bits variant 5 (arm32v5)
    make linux-armv5

    # Mac OS (amd64)
    make darwin

    # FreeBSD (amd64)
    make freebsd

Remove precompiled binaries
---------------------------

.. code:: bash

    make clean

Run the software locally
------------------------

.. code:: bash

    make run

This command execute :code:`go generate` and :code:`go run main.go`.

Regenerate embedded files
-------------------------

To avoid any dependencies, all assets (Javascript, CSS, images, translations) are automatically included in the source code.

.. code:: bash

    go generate

Linter
------

.. code:: bash

    make lint

Unit tests
----------

.. code:: bash

    make test

Integration tests
-----------------

Integration tests are testing API endpoints with a real database.

You need to have Postgresql installed locally preconfigured with the user "postgres" and the password "postgres".

To run integration tests, execute the following command:

.. code:: bash

    make integration-test ; make clean-integration-test

If the test suite fail, you will see the logs of Miniflux.
