Development
===========

Requirements
------------

- Git
- Go >= 1.10
- Dep ``go get -u github.com/golang/dep/cmd/dep`` (`<https://github.com/golang/dep>`_)

.. _checkout-sources:

Checkout the source code
------------------------

.. code:: bash

    go get -u miniflux.app
    cd $GOPATH/src/miniflux.app

The default value for ``$GOPATH`` if not set is ``$HOME/go``.

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
