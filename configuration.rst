Configuration
=============

Miniflux doesn't use any configuration file, **only environment variables**.

Systemd uses the file ``/etc/miniflux.conf`` to populate environment variables.

+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| Variable Name             | Description                                                          | Default Value                                                                   |
+===========================+======================================================================+=================================================================================+
| ``DEBUG``                 | Enable debug logs if a value is set                                  | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``WORKER_POOL_SIZE``      | Number of background workers                                         | ``5``                                                                           |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``POLLING_FREQUENCY``     | Refresh interval in minutes for feeds                                | ``60`` (minutes)                                                                |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``BATCH_SIZE``            | Number of feeds to send to the queue for each interval               | ``10``                                                                          |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_URL``          | Postgresql connection parameters                                     | ``postgres://postgres:postgres@localhost/miniflux2?sslmode=disable``            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_MAX_CONNS``    | Maximum number of database connections                               | ``20``                                                                          |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DATABASE_MIN_CONNS``    | Minimum number of database connections                               | ``1``                                                                           |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``LISTEN_ADDR``           | HTTP server listening address                                        | ``127.0.0.1:8080``                                                              |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``PORT``                  | If defined, override ``LISTEN_ADDR`` to ``0.0.0.0:$PORT``            | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``BASE_URL``              | Base URL to generate HTML links and base path for cookies            | ``http://localhost/``                                                           |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CLEANUP_FREQUENCY``     | Cleanup job frequency, remove old sessions and archive read entries  | ``24`` (hours)                                                                  |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``HTTPS``                 | Forces cookies to use secure flag and send HSTS headers              | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``DISABLE_HSTS``          | Disable HTTP Strict Transport Security header if HTTPS is set        | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_FILE``             | Path to SSL certificate                                              | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``KEY_FILE``              | Path to SSL private key                                              | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_DOMAIN``           | Use Let's Encrypt to get automatically a certificate for this domain | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CERT_CACHE``            | Let's Encrypt cache directory                                        | ``/tmp/cert_cache``                                                             |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_PROVIDER``       | OAuth2 provider to use, at this time only ``google`` is supported    | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_CLIENT_ID``      | OAuth2 client ID                                                     | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_CLIENT_SECRET``  | OAuth2 client secret                                                 | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_REDIRECT_URL``   | OAuth2 redirect URL                                                  | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``OAUTH2_USER_CREATION``  | Set to ``1`` to authorize OAuth2 user creation                       | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``RUN_MIGRATIONS``        | Set to ``1`` to run database migrations                              | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``CREATE_ADMIN``          | Set to ``1`` to create an admin user from environment variables      | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``ADMIN_USERNAME``        | Admin user login, used only if ``CREATE_ADMIN`` is enabled           | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``ADMIN_PASSWORD``        | Admin user password, used only if ``CREATE_ADMIN`` is enabled        | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``POCKET_CONSUMER_KEY``   | Pocket consumer API key for all users                                | None                                                                            |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+
| ``PROXY_IMAGES``          | Avoids mixed content warnings for external images:                   | ``http-only``                                                                   |
|                           | ``http-only``, ``all``, or ``none``                                  |                                                                                 |
+---------------------------+----------------------------------------------------------------------+---------------------------------------------------------------------------------+

Database Connection Parameters
------------------------------

Miniflux use the `Golang library pq <https://github.com/lib/pq>`_ to communicate with Postgres.
Connection parameters are available `on this page <https://godoc.org/github.com/lib/pq#hdr-Connection_String_Parameters>`_.

The default value for :code:`DATABASE_URL` is :code:`postgres://postgres:postgres@localhost/miniflux2?sslmode=disable`.
You don't necessary need to use the URL format.

If you would like to connect via a Unix socket, you could do:

.. code:: bash

    export DATABASE_URL="user=postgres password=postgres dbname=miniflux2 sslmode=disable host=/path/to/socket/folder"
    ./miniflux

.. warning:: Password that contains special characters like ``^`` might be rejected since Miniflux 2.0.3.
             Golang v1.10 is `now validating the password <https://go-review.googlesource.com/c/go/+/87038>`_ and will return this error: ``net/url: invalid userinfo``.
             To avoid this issue, do not use the URL format for ``DATABASE_URL`` or make sure the password is URL encoded.

Running Miniflux on port 443 or 80
----------------------------------

Ports less than 1024 are reserved for privileged users.
If you have installed Miniflux with the RPM or Debian package, systemd run the process as the `miniflux` user.

To give Miniflux the ability to bind to privileged ports as a non-root user, add the capability `CAP_NET_BIND_SERVICE` to the binary:

.. code:: bash

    setcap cap_net_bind_service=+ep /usr/bin/miniflux

Check that the capability is added:

.. code:: bash

    getcap /usr/bin/miniflux
    /usr/bin/miniflux = cap_net_bind_service+ep

Here, we assume you installed the Miniflux binary into /usr/bin.

Let's Encrypt Integration
-------------------------

You could use Let's Encrypt to handle the SSL certificate automatically and activate HTTP/2.0.

.. code:: bash

    export CERT_DOMAIN=my.domain.tld
    miniflux

- Your server must be reachable publicly on port 443 and port 80 (http-01 challenge)
- In this mode, :code:`LISTEN_ADDR` is automatically set to :code:`:https`
- A cache directory is required, by default :code:`/tmp/cert_cache` is used, it could be overrided by using the variable :code:`CERT_CACHE`

.. note:: Miniflux supports http-01 challenge since the version 2.0.2

Manual HTTPS Configuration
--------------------------

Here an example to generate your self-signed certificate:

.. code:: bash

    # Generate the private key:
    openssl genrsa -out server.key 2048
    openssl ecparam -genkey -name secp384r1 -out server.key

    # Generate the certificate:
    openssl req -new -x509 -sha256 -key server.key -out server.crt -days 3650

Start the server like this:

.. code:: bash

    # Configure the environment variables:
    export CERT_FILE=/path/to/server.crt
    export KEY_FILE=/path/to/server.key
    export LISTEN_ADDR=":https"

    # Start the server:
    miniflux

Then you can access to your server by using an encrypted connection with the HTTP/2.0 protocol.

OAuth2 Authentication
---------------------

OAuth2 allows you to sign in with an external provider.
At this time, only Google is supported.

Google
~~~~~~

1. Create a new project in Google Console
2. Create a new OAuth2 client
3. Set an authorized redirect URL: :code:`https://my.domain.tld/oauth2/google/callback`
4. Define the OAuth2 environment variables and start the process

.. code:: bash

    export OAUTH2_PROVIDER=google
    export OAUTH2_CLIENT_ID=replace_me
    export OAUTH2_CLIENT_SECRET=replace_me
    export OAUTH2_REDIRECT_URL=https://my.domain.tld/oauth2/google/callback

    miniflux

Now from the settings page, you can link your existing user to your Google account.

If you would like to authorize anyone to create user account, you must set :code:`OAUTH2_USER_CREATION=1`.
Since Google do not have the concept of username, the email address is used as username.

Reverse-Proxy Configuration with Subfolder
------------------------------------------

Since the version 2.0.2, you can host your Miniflux instance under a subfolder.

You must define the environment variable :code:`BASE_URL` for Miniflux, for example:

.. code:: bash

    export BASE_URL=http://example.org/rss/

You can use the reverse-proxy software of your choice, here an example with Nginx:

.. code:: bash

    location /rss/ {
        proxy_pass http://127.0.0.1:8080/rss/;
        proxy_set_header Host $host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

This example assumes that you are running the Miniflux daemon on `127.0.0.1:8080`.

Now you can access your Miniflux instance at `http://example.org/rss/`.
In this configuration, cookies are using the path `/rss`.
