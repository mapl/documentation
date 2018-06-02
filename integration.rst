Integration with External Services
==================================

Fever API
---------

Miniflux implements the Fever API in addition to its own REST API.
The Fever API allows you to use existing mobile applications to read your feeds instead of the web user interface.

To activate the Fever API, go into the integration section and choose a username/password.

.. image:: _static/fever.png

The API endpoint is :code:`https://example.org/fever/`

Compatible Apps
~~~~~~~~~~~~~~~

- `Reeder <http://reederapp.com/>`_ (iOS/Mac OS)
- `Unread <https://www.goldenhillsoftware.com/unread/>`_ (iOS)

.. note::
    - Saving an entry will add a new bookmark and save the article
    - Only the JSON format is supported
    - Refreshing feeds is not possible with Reeder because no user information is sent
    - Links, sparks, and kindlings are not supported

Pinboard
--------

You could save articles to `Pinboard <https://pinboard.in/>`_.

.. image:: _static/pinboard.png

To activate this service, go to the integration section and enter your Pinboard API credentials.
You must use the API token, not your password.

Instapaper
----------

You could save articles to `Instapaper <https://www.instapaper.com/>`_.

.. image:: _static/instapaper.png

To activate this service, go to the integration section and enter your Instapaper credentials.

Pocket
------

Configuration
~~~~~~~~~~~~~

To configure the Pocket integration on your own Miniflux instance, you need to create an application on Pocket's website.

Go to `<https://getpocket.com/developer/apps/new>`_ and create a new application.

- You can define the *Pocket Consumer Key* for all users by using the environment variable ``POCKET_CONSUMER_KEY``.
- Or, you can set the *Pocket Consumer Key* only for your user by using the form.

.. image:: _static/pocket_1.png

If the environment variable is defined, the text field for the *Pocket Consumer API key* will be hidden.

Make sure the environment variable ``BASE_URL`` is defined properly to allow the authorization flow to work afterward.

Usage
~~~~~

Once the consumer key is configured, you need to get a *Pocket Access Token*.
This token could be fetched automatically by using the authorization flow or manually by making some HTTP calls.

.. image:: _static/pocket_2.png

The simplest solution is to use the link, **"Connect your Pocket account"**.
This method redirects the end user to Pocket's website and ask for authorization.

.. image:: _static/pocket_3.png

After the authorization is given, Miniflux will fetch the *Pocket Access Token* for you.

If you prefer to fetch *Pocket Access Token* manually, the process is described in `Pocket's developer documentation <https://getpocket.com/developer/docs/authentication>`_.

Wallabag
--------

`Wallabag <https://wallabag.org/>`_ is a self-hosted application for saving web pages.

.. image:: _static/wallabag.png

- The API URL is the root URL of your instance, for example, if you have the hosted version use: :code:`https://app.wallabag.it/`.
- To create a new API client, go to the section "API clients management" and choose "Create a new client".

Nunux Keeper
------------

`Nunux Keeper <https://keeper.nunux.org/>`_ is a *"personal content curation service"*.
It's an alternative to Pocket or Wallabag.

.. image:: _static/nunux_reader.png

- The API URL is the root URL of your instance, for example, if you are using the hosted version: :code:`https://api.nunux.org/keeper`.
- To create a new API key, go to the settings tab: "API key" and choose "Regenerate API key".

.. image:: _static/nunux_reader_api_key.png
