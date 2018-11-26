Upgrading to a New Version
==========================

.. warning:: Please, do not update the software blindly without reading the `ChangeLog <https://github.com/miniflux/miniflux/blob/master/ChangeLog>`_.
             Always check for breaking changes if any.

Procedure
---------

1. Export Environment Variable of Miniflux's DATABASE_URL if not already done: :code:`export DATABASE_URL=postgres:// ...` 
2. Disconnect all users by flushing all sessions: :code:`miniflux -flush-sessions` 
3. Stop the process
4. Backup your database
5. Check that your backup is really working
6. Run database migrations: :code:`miniflux -migrate`
7. Start the process

Debian Package
--------------

Follow instructions mentioned above and run: :code:`dpkg -i miniflux_2.x.x_amd64.deb`.

RPM Package
-----------

Follow instructions mentioned above and run: :code:`rpm -Uvh miniflux-2.x.x-1.0.x86_64.rpm`.

Docker Image
------------

- Pull the new image with the new tag: :code:`docker pull miniflux/miniflux:2.x.x`
- Stop and remove the old container: :code:`docker stop <container_name> && docker rm <container_name>`
- Start a new container with the latest tag: :code:`docker run -d -p 80:8080 miniflux/miniflux:2.x.x`

If you use Docker Compose, define the new tag in the YAML file and restart the container.
