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

    # All binaries
    make build

    # Only Linux (amd64 architecture)
    make linux

    # Build for ARM architectures (32 and 64 bits)
    make linux-arm

    # Only Mac OS
    make darwin

    # Only FreeBSD
    make freebsd

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
