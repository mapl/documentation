REST API
========

Authentication
--------------

The Miniflux API uses HTTP Basic authentication.
The credentials are the username/password of your account.

Clients
-------

There are 2 official API clients, one written in Go and another one written in Python.

Golang Client
~~~~~~~~~~~~~

- Repository: `<https://github.com/miniflux/miniflux/tree/master/client>`_
- Reference: `<https://godoc.org/miniflux.app/client>`_

Installation:

.. code:: bash

    go get -u miniflux.app/client


Usage Example:

.. code:: go

    package main

    import (
        "fmt"

        miniflux "miniflux.app/client"
    )

    func main() {
        client := miniflux.New("https://miniflux.example.org", "admin", "secret")

        // Fetch all feeds.
        feeds, err := client.Feeds()
        if err != nil {
            fmt.Println(err)
            return
        }
        fmt.Println(feeds)
    }

Python Client
~~~~~~~~~~~~~

- Repository: `<https://github.com/miniflux/miniflux-python>`_
- PyPi: `<https://pypi.org/project/miniflux/>`_

Installation:

.. code:: bash

    pip install miniflux

Usage example:

.. code:: python

    import miniflux

    client = miniflux.Client("https://miniflux.example.org", "my_username", "my_secret_password")

    # Get all feeds
    feeds = client.get_feeds()

    # Refresh a feed
    client.refresh_feed(123)

    # Discover subscriptions from a website
    subscriptions = client.discover("https://example.org")

    # Fetch 10 starred entries
    entries = client.get_entries(starred=True, limit=10)

    # Fetch last 5 feed entries
    feed_entries = client.get_feed_entries(123, direction='desc', order='published_at', limit=5)

    # Update a feed category
    client.update_Feed(123, category_id=456)

API Reference
-------------

Status Codes
~~~~~~~~~~~~

- :code:`200`: Everything is OK
- :code:`201`: Resource created/modified
- :code:`204`: Resource removed/modified
- :code:`400`: Bad request
- :code:`401`: Unauthorized (bad username/password)
- :code:`403`: Forbidden (access not allowed)
- :code:`500`: Internal server error

Error Response
~~~~~~~~~~~~~~

.. code:: json

    {
        "error_message": "Some error"
    }

Discover Subscriptions
~~~~~~~~~~~~~~~~~~~~~~

Request:

.. code::

    POST /v1/discover
    Content-Type: application/json

    {
        "url": "http://example.org"
    }

Response:

.. code:: json

    [
        {
            "url": "http://example.org/feed.atom",
            "title": "Atom Feed",
            "type": "atom"
        },
        {
            "url": "http://example.org/feed.rss",
            "title": "RSS Feed",
            "type": "rss"
        }
    ]

Get Feeds
~~~~~~~~~

Request:

.. code::

    GET /v1/feeds

Response:

.. code:: json

    [
        {
            "id": 42,
            "user_id": 123,
            "title": "Example Feed",
            "site_url": "http://example.org",
            "feed_url": "http://example.org/feed.atom",
            "rewrite_rules": "",
            "scraper_rules": "",
            "crawler": false,
            "checked_at": "2017-12-22T21:06:03.133839-05:00",
            "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
            "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
            "parsing_error_count": 0,
            "parsing_error_message": "",
            "category": {
                "id": 793,
                "user_id": 123,
                "title": "Some category"
            },
            "icon": {
                "feed_id": 42,
                "icon_id": 84
            }
        }
    ]

Notes:

- :code:`icon` is :code:`null` when the feed doesn't have any favicon.

Get Feed
~~~~~~~~

Request:

.. code::

    GET /v1/feeds/42

Response:

.. code:: json

    {
        "id": 42,
        "user_id": 123,
        "title": "Example Feed",
        "site_url": "http://example.org",
        "feed_url": "http://example.org/feed.atom",
        "rewrite_rules": "",
        "scraper_rules": "",
        "crawler": false,
        "checked_at": "2017-12-22T21:06:03.133839-05:00",
        "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
        "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
        "parsing_error_count": 0,
        "parsing_error_message": "",
        "category": {
            "id": 793,
            "user_id": 123,
            "title": "Some category"
        },
        "icon": {
            "feed_id": 42,
            "icon_id": 84
        }
    }

Notes:

- :code:`icon` is :code:`null` when the feed doesn't have any favicon.

Get Feed Icon
~~~~~~~~~~~~~

Request:

.. code::

    GET /v1/feeds/42/icon

Response:

.. code:: json

    {
        "id": 262,
        "data": "image/png;base64,iVBORw0KGgoAAA....",
        "mime_type": "image/png"
    }

Notes:

- If the feed doesn't have any favicon, a 404 is returned.

Create Feed
~~~~~~~~~~~

Request:

.. code::

    POST /v1/feeds
    Content-Type: application/json

    {
        "feed_url": "http://example.org/feed.atom",
        "category_id": 22
    }

Response:

.. code:: json

    {
        "feed_id": 262,
    }

Update Feed
~~~~~~~~~~~

Request:

.. code::

    PUT /v1/feeds/42
    Content-Type: application/json

    {
        "title": "New Feed Title",
        "category": {
            "id": 22
        }
    }

Response:

.. code:: json

    {
        "id": 42,
        "user_id": 123,
        "title": "New Feed Title",
        "site_url": "http://example.org",
        "feed_url": "http://example.org/feed.atom",
        "rewrite_rules": "",
        "scraper_rules": "",
        "crawler": false,
        "checked_at": "2017-12-22T21:06:03.133839-05:00",
        "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
        "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
        "parsing_error_count": 0,
        "parsing_error_message": "",
        "category": {
            "id": 22,
            "user_id": 123,
            "title": "Another category"
        },
        "icon": {
            "feed_id": 42,
            "icon_id": 84
        }
    }

Available fields:

- ``feed_url``: (string)
- ``site_url``: (string)
- ``title``: (string)
- ``scraper_rules``: (string)
- ``rewrite_rules``: (string)
- ``crawler``: (boolean)
- ``username``: (string)
- ``password``: (string)
- ``category_id``: (int)

Refresh Feed
~~~~~~~~~~~~

Request:

.. code::

    PUT /v1/feeds/42/refresh

.. note::

    - Returns :code:`204` status code for success.
    - This API call is synchronous and can takes hundred of milliseconds.

Remove Feed
~~~~~~~~~~~

Request:

.. code::

    DELETE /v1/feeds/42

Get Feed Entry
~~~~~~~~~~~~~~

Request:

.. code::

    GET /v1/feeds/42/entries/888

Response:

.. code:: json

    {
        "id": 888,
        "user_id": 123,
        "feed_id": 42,
        "title": "Entry Title",
        "url": "http://example.org/article.html",
        "comments_url": "",
        "author": "Foobar",
        "content": "<p>HTML contents</p>",
        "hash": "29f99e4074cdacca1766f47697d03c66070ef6a14770a1fd5a867483c207a1bb",
        "published_at": "2016-12-12T16:15:19Z",
        "status": "read",
        "starred": false,
        "feed": {
            "id": 42,
            "user_id": 123,
            "title": "New Feed Title",
            "site_url": "http://example.org",
            "feed_url": "http://example.org/feed.atom",
            "rewrite_rules": "",
            "scraper_rules": "",
            "crawler": false,
            "checked_at": "2017-12-22T21:06:03.133839-05:00",
            "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
            "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
            "parsing_error_count": 0,
            "parsing_error_message": "",
            "category": {
                "id": 22,
                "user_id": 123,
                "title": "Another category"
            },
            "icon": {
                "feed_id": 42,
                "icon_id": 84
            }
        }
    }

.. note::

    - The field ``comments_url`` is available since Miniflux v2.0.5.

Get Entry
~~~~~~~~~

Request:

.. code::

    GET /v1/entries/888

Response:

.. code:: json

    {
        "id": 888,
        "user_id": 123,
        "feed_id": 42,
        "title": "Entry Title",
        "url": "http://example.org/article.html",
        "comments_url": "",
        "author": "Foobar",
        "content": "<p>HTML contents</p>",
        "hash": "29f99e4074cdacca1766f47697d03c66070ef6a14770a1fd5a867483c207a1bb",
        "published_at": "2016-12-12T16:15:19Z",
        "status": "read",
        "starred": false,
        "feed": {
            "id": 42,
            "user_id": 123,
            "title": "New Feed Title",
            "site_url": "http://example.org",
            "feed_url": "http://example.org/feed.atom",
            "rewrite_rules": "",
            "scraper_rules": "",
            "crawler": false,
            "checked_at": "2017-12-22T21:06:03.133839-05:00",
            "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
            "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
            "parsing_error_count": 0,
            "parsing_error_message": "",
            "category": {
                "id": 22,
                "user_id": 123,
                "title": "Another category"
            },
            "icon": {
                "feed_id": 42,
                "icon_id": 84
            }
        }
    }

Get Feed Entries
~~~~~~~~~~~~~~~~

Request:

.. code::

    GET /v1/feeds/42/entries?limit=1&order=id&direction=asc

Available filters:

- ``status``: Entry status (read, unread or removed)
- ``offset``
- ``limit``
- ``order``: "id", "status", "published_at", "category_title", "category_id"
- ``direction``: "asc" or "desc"
- ``before`` (unix timestamp, available since Miniflux 2.0.9)
- ``after`` (unix timestamp, available since Miniflux 2.0.9)
- ``before_entry_id`` (int64, available since Miniflux 2.0.9)
- ``after_entry_id`` (int64, available since Miniflux 2.0.9)
- ``starred`` (boolean, available since Miniflux 2.0.9)

Response:

.. code:: json

    {
        "total": 10,
        "entries": [
            {
                "id": 888,
                "user_id": 123,
                "feed_id": 42,
                "title": "Entry Title",
                "url": "http://example.org/article.html",
                "comments_url": "",
                "author": "Foobar",
                "content": "<p>HTML contents</p>",
                "hash": "29f99e4074cdacca1766f47697d03c66070ef6a14770a1fd5a867483c207a1bb",
                "published_at": "2016-12-12T16:15:19Z",
                "status": "read",
                "starred": false,
                "feed": {
                    "id": 42,
                    "user_id": 123,
                    "title": "New Feed Title",
                    "site_url": "http://example.org",
                    "feed_url": "http://example.org/feed.atom",
                    "rewrite_rules": "",
                    "scraper_rules": "",
                    "crawler": false,
                    "checked_at": "2017-12-22T21:06:03.133839-05:00",
                    "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
                    "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
                    "parsing_error_count": 0,
                    "parsing_error_message": "",
                    "category": {
                        "id": 22,
                        "user_id": 123,
                        "title": "Another category"
                    },
                    "icon": {
                        "feed_id": 42,
                        "icon_id": 84
                    }
                }
            }
        ]

Get Entries
~~~~~~~~~~~

Request:

.. code::

    GET /v1/entries?status=unread&direction=desc

Available filters:

- ``status``: Entry status (read, unread or removed)
- ``offset``
- ``limit``
- ``order``: "id", "status", "published_at", "category_title", "category_id"
- ``direction``: "asc" or "desc"
- ``before`` (unix timestamp, available since Miniflux 2.0.9)
- ``after`` (unix timestamp, available since Miniflux 2.0.9)
- ``before_entry_id`` (int64, available since Miniflux 2.0.9)
- ``after_entry_id`` (int64, available since Miniflux 2.0.9)
- ``starred`` (boolean, available since Miniflux 2.0.9)

Response:

.. code:: json

    {
        "total": 10,
        "entries": [
            {
                "id": 888,
                "user_id": 123,
                "feed_id": 42,
                "title": "Entry Title",
                "url": "http://example.org/article.html",
                "comments_url": "",
                "author": "Foobar",
                "content": "<p>HTML contents</p>",
                "hash": "29f99e4074cdacca1766f47697d03c66070ef6a14770a1fd5a867483c207a1bb",
                "published_at": "2016-12-12T16:15:19Z",
                "status": "unread",
                "starred": false,
                "feed": {
                    "id": 42,
                    "user_id": 123,
                    "title": "New Feed Title",
                    "site_url": "http://example.org",
                    "feed_url": "http://example.org/feed.atom",
                    "rewrite_rules": "",
                    "scraper_rules": "",
                    "crawler": false,
                    "checked_at": "2017-12-22T21:06:03.133839-05:00",
                    "etag_header": "KyLxEflwnTGF5ecaiqZ2G0TxBCc",
                    "last_modified_header": "Sat, 23 Dec 2017 01:04:21 GMT",
                    "parsing_error_count": 0,
                    "parsing_error_message": "",
                    "category": {
                        "id": 22,
                        "user_id": 123,
                        "title": "Another category"
                    },
                    "icon": {
                        "feed_id": 42,
                        "icon_id": 84
                    }
                }
            }
        ]

Update Entries
~~~~~~~~~~~~~~

Request:

.. code::

    PUT /v1/entries
    Content-Type: application/json

    {
        "entry_ids": [1234, 4567],
        "status": "read"
    }

.. note::

    - Returns :code:`204` status code for success.

Toggle Entry Bookmark
~~~~~~~~~~~~~~~~~~~~~

Request:

.. code::

    PUT /v1/entries/1234/bookmark

.. note::

    - Returns :code:`204` status code for success.

Get Categories
~~~~~~~~~~~~~~

Request:

.. code::

    GET /v1/categories

Response:

.. code:: json

    [
        {"title": "All", "user_id": 267, "id": 792},
        {"title": "Engineering Blogs", "user_id": 267, "id": 793}
    ]

Create Category
~~~~~~~~~~~~~~~

Request:

.. code::

    POST /v1/categories
    Content-Type: application/json

    {
        "title": "My category"
    }

Response:

.. code:: json

    {
        "id": 802,
        "user_id": 267,
        "title": "My category"
    }

Update Category
~~~~~~~~~~~~~~~

Request:

.. code::

    PUT /v1/categories/802
    Content-Type: application/json

    {
        "title": "My new title"
    }

Response:

.. code:: json

    {
        "id": 802,
        "user_id": 267,
        "title": "My new title"
    }

Delete Category
~~~~~~~~~~~~~~~

Request:

.. code::

    DELETE /v1/categories/802

OPML Export
~~~~~~~~~~~

Request:

.. code::

    GET /v1/export

The response is a XML document (OPML file).

.. note:: This API call is available since Miniflux v2.0.1.

OPML Import
~~~~~~~~~~~

Request:

.. code::

    POST /v1/import

    XML data

- The body is your OPML file (XML).
- Returns ``201 Created`` if imported successfully.

Response:

.. code:: json

    {
      "message": "Feeds imported successfully"
    }

.. note:: This API call is available since Miniflux v2.0.7.

Create User
~~~~~~~~~~~

Request:

.. code::

    POST /v1/users
    Content-Type: application/json

    {
        "username": "bob",
        "password": "test123",
        "is_admin": false
    }

Response:

.. code:: json

    {
        "id": 270,
        "username": "bob",
        "language": "en_US",
        "timezone": "UTC",
        "theme": "default",
        "entry_sorting_direction": "asc"
    }

.. note::

    - You must be an administrator to create users.

Update User
~~~~~~~~~~~

Request:

.. code::

    PUT /v1/users/270
    Content-Type: application/json

    {
        "username": "joe"
    }

Available fields:

- :code:`username`: (string)
- :code:`password`: (string)
- :code:`is_admin`: (boolean)
- :code:`theme`: (string)
- :code:`language`: (string)
- :code:`timezone`: (string)
- :code:`entry_sorting_direction`: "desc" or "asc" (available since Miniflux 2.0.9)

Response:

.. code:: json

    {
        "id": 270,
        "username": "joe",
        "language": "en_US",
        "timezone": "UTC",
        "theme": "default",
        "entry_sorting_direction": "asc"
    }

.. note::

    - You must be an administrator to update users.

Get Current User
~~~~~~~~~~~~~~~~

Request:

.. code::

    GET /v1/me

Response:

.. code:: json

    {
        "id": 1,
        "username": "admin",
        "is_admin": true,
        "theme": "default",
        "language": "en_US",
        "timezone": "America/Vancouver",
        "entry_sorting_direction": "desc",
        "last_login_at": "2018-06-01T19:54:30.723051-07:00",
        "extra": {}
    }

.. note:: This API endpoint is available since Miniflux v2.0.8.

Get User
~~~~~~~~

Request:

.. code::

    # Get user by user ID
    GET /v1/users/270

    # Get user by username
    GET /v1/users/foobar

Response:

.. code:: json

    {
        "id": 270,
        "username": "bob",
        "is_admin": false,
        "language": "en_US",
        "timezone": "UTC",
        "theme": "default",
        "entry_sorting_direction": "asc",
        "last_login_at": "2017-12-27T16:40:58.841841-05:00",
        "extra": {
            "google_id": "42424242424242"
        }
    }

.. note::

    - You must be an administrator to fetch users.
    - The extra field is a dictionary of optional values.

Get Users
~~~~~~~~~

Request:

.. code::

    GET /v1/users

Response:

.. code:: json

    [
        {
            "id": 270,
            "username": "bob",
            "is_admin": false,
            "language": "en_US",
            "timezone": "UTC",
            "theme": "default",
            "entry_sorting_direction": "asc",
            "last_login_at": "2017-12-27T16:40:58.841841-05:00",
            "extra": {}
        }
    ]

.. note::

    - You must be an administrator to fetch users.
    - The extra field is a dictionary of optional values.

Delete User
~~~~~~~~~~~

Request:

.. code::

    DELETE /v1/users/270

.. note::

    - You must be an administrator to delete users.

Healthcheck
~~~~~~~~~~~

The healthcheck endpoint is useful for monitoring and load-balancer configuration.

Request:

.. code::

    GET /healthcheck

Response:

.. code::

    OK

Return a status code 200 when the service is up.
