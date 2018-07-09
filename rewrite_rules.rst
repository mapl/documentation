Rewrite Rules
=============

To improve the reading experience, it's possible to alter the content of feed items.

For example, if you are reading a popular comic website like XKCD, it's nice to have to image title (the `alt` attribute) added under the image.
Especially on mobile devices when there is no hover event.

List of Rules
-------------

- :code:`add_dynamic_image`: Tries to add the highest quality images from sites that use JavaScript to load images (e.g. either lazily when scrolling or based on screen size).
- :code:`add_image_title`: Add each image's title as a caption under the image.
- :code:`add_youtube_video`: Insert Youtube video inside the article (automatic for Youtube.com)

Adding Rewrite Rules
--------------------

Miniflux includes a set of default rules for some websites, but you could define your own rules.

On the feed edit page, enter your custom rules in the field "Rewrite Rules" like this:

.. code::

    rule1,rule2

Separate each rule by a comma.
As of now, only :code:`add_dynamic_image` and :code:`add_image_title` are available.
Of course, other rules could be added later.
