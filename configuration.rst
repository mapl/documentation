Configuration
=============

Miniflux doesn't use any configuration file, **only environment variables**.

Systemd uses the file ``/etc/miniflux.conf`` to populate environment variables.

+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| Variable Name                  | Description                                                          | Default Value                                                                   |
+================================+======================================================================+=================================================================================+
| ``DEBUG``                      | Set the value to ``1`` to enable debug logs                          | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``WORKER_POOL_SIZE``           | Number of background workers                                         | 5                                                                               |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``POLLING_FREQUENCY``          | Refresh interval in minutes for feeds                                | 60 (minutes)                                                                    |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``BATCH_SIZE``                 | Number of feeds to send to the queue for each interval               | 10                                                                              |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_URL``               | Postgresql connection parameters                                     | ``user=postgres password=postgres dbname=miniflux2 sslmode=disable``            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_MAX_CONNS``         | Maximum number of database connections                               | 20                                                                              |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_MIN_CONNS``         | Minimum number of database connections                               | 1                                                                               |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``LISTEN_ADDR``                | Address to listen on (use absolute path for Unix socket)             | ``127.0.0.1:8080``                                                              |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``PORT``                       | Override ``LISTEN_ADDR`` to ``0.0.0.0:$PORT`` (PaaS)                 | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``BASE_URL``                   | Base URL to generate HTML links and base path for cookies            | ``http://localhost/``                                                           |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CLEANUP_FREQUENCY``          | Cleanup job frequency, remove old sessions and archive read entries  | 24 (hours)                                                                      |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``HTTPS``                      | Forces cookies to use secure flag and send HSTS headers              | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DISABLE_HSTS``               | Disable HTTP Strict Transport Security header if HTTPS is set        | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DISABLE_HTTP_SERVICE``       | Disable HTTP service                                                 | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DISABLE_SCHEDULER_SERVICE``  | Disable scheduler service                                            | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_FILE``                  | Path to SSL certificate                                              | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``KEY_FILE``                   | Path to SSL private key                                              | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_DOMAIN``                | Use Let's Encrypt to get automatically a certificate for this domain | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_CACHE``                 | Let's Encrypt cache directory                                        | ``/tmp/cert_cache``                                                             |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_PROVIDER``            | OAuth2 provider to use, at this time only ``google`` is supported    | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_CLIENT_ID``           | OAuth2 client ID                                                     | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_CLIENT_SECRET``       | OAuth2 client secret                                                 | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_REDIRECT_URL``        | OAuth2 redirect URL                                                  | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_USER_CREATION``       | Set to ``1`` to authorize OAuth2 user creation                       | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``RUN_MIGRATIONS``             | Set to ``1`` to run database migrations                              | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CREATE_ADMIN``               | Set to ``1`` to create an admin user from environment variables      | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``ADMIN_USERNAME``             | Admin user login, used only if ``CREATE_ADMIN`` is enabled           | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``ADMIN_PASSWORD``             | Admin user password, used only if ``CREATE_ADMIN`` is enabled        | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``POCKET_CONSUMER_KEY``        | Pocket consumer API key for all users                                | None                                                                            |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``PROXY_IMAGES``               | Avoids mixed content warnings for external images:                   | ``http-only``                                                                   |
|                                | ``http-only``, ``all``, or ``none``                                  |                                                                                 |
+--------------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+

Accepted boolean values are ``1``, ``yes``, ``true``, and ``on``.

Database Connection Parameters
------------------------------

Miniflux uses the `Golang library pq <https://github.com/lib/pq>`_ to communicate with Postgres.
Connection parameters are available `on this page <https://godoc.org/github.com/lib/pq#hdr-Connection_String_Parameters>`_.

The default value for ``DATABASE_URL`` is ``user=postgres password=postgres dbname=miniflux2 sslmode=disable``.

You could also use the URL format ``postgres://postgres:postgres@localhost/miniflux2?sslmode=disable``.

.. warning:: Password that contains special characters like ``^`` might be rejected since Miniflux 2.0.3.
             Golang v1.10 is `now validating the password <https://go-review.googlesource.com/c/go/+/87038>`_ and will return this error: ``net/url: invalid userinfo``.
             To avoid this issue, do not use the URL format for ``DATABASE_URL`` or make sure the password is URL encoded.

Enabling HSTORE extension for Postgresql
----------------------------------------

.. _migrations-superuser:

Creating Postgresql extensions requires the ``SUPERUSER`` privilege.
Several solutions are available:

1) Give :code:`SUPERUSER` privileges to miniflux user only during the schema migration:

.. code:: sql

    ALTER USER miniflux WITH SUPERUSER;
    -- Run the migrations (miniflux -migrate)
    ALTER USER miniflux WITH NOSUPERUSER;

2) You could `create the hstore extension <https://www.postgresql.org/docs/current/static/sql-createextension.html>`_ with another user that have the ``SUPERUSER`` privileges before to run the migrations.

.. code::

    sudo -u postgres psql $MINIFLUX_DATABASE
    > CREATE EXTENSION hstore;
