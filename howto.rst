How-Tos
=======

Use a Unix socket for Postgresql
--------------------------------

If you would like to connect via a Unix socket to Postgresql, set the parameter ``host=/path/to/socket/folder``.

Example:

.. code:: bash

    export DATABASE_URL="user=postgres password=postgres dbname=miniflux2 sslmode=disable host=/path/to/socket/folder"
    ./miniflux

How to run Miniflux on port 443 or 80
-------------------------------------

Ports less than 1024 are reserved for privileged users.
If you have installed Miniflux with the RPM or Debian package, systemd run the process as the `miniflux` user.

To give Miniflux the ability to bind to privileged ports as a non-root user, add the capability `CAP_NET_BIND_SERVICE` to the binary:

.. code:: bash

    setcap cap_net_bind_service=+ep /usr/bin/miniflux

Check that the capability is added:

.. code:: bash

    getcap /usr/bin/miniflux
    /usr/bin/miniflux = cap_net_bind_service+ep

Reverse-Proxy Configuration
---------------------------

You can use the reverse-proxy software of your choice, here an example with Nginx:

.. code:: bash

    server {
        server_name     my.domain.tld;
        listen          80;

        location / {
            proxy_pass http://127.0.0.1:8080;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

This example assumes that you are running the Miniflux daemon on `127.0.0.1:8080`.

Reverse-Proxy with a subfolder
------------------------------

Since the version 2.0.2, you can host your Miniflux instance under a subfolder.

You must define the environment variable ``BASE_URL`` for Miniflux, for example:

.. code:: bash

    export BASE_URL=http://example.org/rss/

Example with Nginx:

.. code:: bash

    server {
        server_name     my.domain.tld;
        listen          80;

        location /rss/ {
            proxy_pass http://127.0.0.1:8080/rss/;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

Alternative Nginx configuration:

.. code:: bash

    server {
        server_name     my.domain.tld;
        listen          80;

        location / {
            proxy_pass http://127.0.0.1:8080;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

This example assumes that you are running the Miniflux daemon on ``127.0.0.1:8080``.

Now you can access your Miniflux instance at `http://example.org/rss/`.
In this configuration, cookies are using the path `/rss`.

Reverse-Proxy with a Unix socket
--------------------------------

If you prefer to use a Unix socket, change the environment variable ``LISTEN_ADDR`` to the path of your socket.

Example to use a Unix socket:

.. code:: bash

    export LISTEN_ADDR=/var/run/miniflux.sock

Example with Nginx as reverse-proxy:

.. code:: bash

    server {
        server_name     my.domain.tld;
        listen          80;

        location / {
            proxy_pass  http://unix:/var/run/miniflux.sock;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

.. note::

    - The Miniflux process must have write access to the socket folder.
    - If you don't set the header X-Forwarded-For, Miniflux won't be able to determine the remote IP address.
    - Listening on Unix socket is available only since Miniflux v2.0.13.

How to use Let's Encrypt
------------------------

You could use Let's Encrypt to manage the SSL certificate automatically and activate HTTP/2.0.

.. code:: bash

    export CERT_DOMAIN=my.domain.tld
    miniflux

- Your server must be reachable publicly on port 443 and port 80 (http-01 challenge)
- In this mode, ``LISTEN_ADDR`` is automatically set to ``:https``
- A cache directory is required, by default ``/tmp/cert_cache`` is used, it could be overrided by using the variable ``CERT_CACHE``

.. note:: Miniflux supports http-01 challenge since the version 2.0.2

Manual HTTPS Configuration
--------------------------

Here an example to generate a self-signed certificate:

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

1. Create a new project in Google Console
2. Create a new OAuth2 client
3. Set an authorized redirect URL, for example ``https://my.domain.tld/oauth2/google/callback``
4. Define the OAuth2 environment variables and start the process

.. code:: bash

    export OAUTH2_PROVIDER=google
    export OAUTH2_CLIENT_ID=replace_me
    export OAUTH2_CLIENT_SECRET=replace_me
    export OAUTH2_REDIRECT_URL=https://my.domain.tld/oauth2/google/callback

    miniflux

Now from the settings page, you can link your existing user to your Google account.

If you would like to authorize anyone to create a user account, you must set ``OAUTH2_USER_CREATION=1``.
Since Google do not have the concept of username, the email address is used as username.
