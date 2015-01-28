The theming module
==================

TLDR
----

- either
    - attach ``@theme(default_theme_name)`` decorator to your controller function
    - or set the `theme` handler option to True or the theme name
    to enable theming.
- either
    - attach ``@breadcrumbs`` to your controller function
    - or set ``bradcrumbs`` handler option to True
    to get the ``breadcrumbs`` variable in the template

Package
-------

The module is called `theming` and can be found in the `dyc.modules` directory.

Theming with theming
--------------------

Effect on context
^^^^^^^^^^^^^^^^^

``@theme()`` adds a ``'theme_config'`` key with a dictionary containing information about, you guessed it, the specified theme to the ``DynamicContent.config`` dict. This 'theme_config' is basically a copy of the config.json file from the theme directory and is used to configure behaviour of the ``@theme`` decorator and others, such as ``@commons.Regions``.

Additionally the decorator compiles a list of ``'scripts'``, ``'stylesheets'`` and ``'meta'``. Which are rendered into strings and attached to the DynamicContent.context dict with those keys to later be inserted into the template.

Basically it adds stylesheets based on the theme configuration.

Effect on templates
^^^^^^^^^^^^^^^^^^^

The `@theme` decorator does not directly influence the name of the view, however it adds the themes ``'template_directory'`` as the first path in which to search for a matching template.

Since every theme defines its own classes etc. you should use one of the themes boilerplate templates when designing your own template.

Enable theming
^^^^^^^^^^^^^^

Decorator
"""""""""

As you may have guessed already, theming a view, like just about anything else, requires a decorator. The decorator is called `dyc.modules.theming.theme`.

Full decorator signature:

.. code:: python

    def theme(default_theme=dyc.includes.settings.DEFAULT_THEME):
        # ...
        pass # return wrapper function


The default_theme argument indicates which theme to attach if you don't attach a theme yourself. As that argument has a default value you can omit it when decorating the function **however** since the decorator expects this argument you still have to add empty parens like so:

.. code:: pyton
    @mvc.controller_function(**options)
    @theming.theme() # <- note the empty parens
    def controller(dc_obj):
        pass


Middleware
""""""""""

Alternative to using a decorator you can set the 'theme' option in the handler options. The ``dyc.modules.theming.Middleware`` will then take care of compiling the theme information.

.. code:: python
    @mvc.controller_function(
        # ...
        theme='my_theme_name'
    )
    def controller(...):
        pass


The argument here can either be a bool or a theme name which to use as default theme. It works very similar to the decorator as in a previously set ``'theme'`` key takes precedence, the argument provided will be used as default, if the ``'theme'`` key has not been set by the controller function (if the argument is `True` the default theme from settings will be used) and if ``'theme_config'`` has been previously set no changes at all are made.

Attach function
"""""""""""""""

Theme also provides a raw attach function called ``theme_dc_obj``. Which takes takes an instance of ``dyc.util.structures.DynamicContent`` and a default theme to use as an argument and will compile theme information and attach context variables as described above to the ``DynamicContent`` instance.

This is mostly intended to be used by other decorators and/or middleware that depend on the theme information or template variables.

Assigning themes to views
^^^^^^^^^^^^^^^^^^^^^^^^^

Inside a controller function that has been decorated with ``@theme()`` you can set the theme it should use by setting the ``'theme'`` key in the ``config`` dict of the ``DynamicContent`` instance.

Alternatively/Additionally you can provide a ``default_theme`` to the ``@theme()`` decorator, which will automatically set the ``'theme'`` key of the ``config`` dict, provided it has not been set already.

Breadcrumbs
-----------

You can use this part of the package to attach whats called breadcrumbs to your context, which you can then use in the template with the ``breadcrumbs`` key.

Breadcrumbs are essentially links to parent pages. Currently these are obtained by simply splitting the request url in sections and rendering a series of links to each section.

Enabling breadcrumbs
^^^^^^^^^^^^^^^^^^^^

Decorator
"""""""""

As with the theme, there is a decorator which can be used to enable breadcrumbs, and it is very imaginatively called ``@breadcrumbs``.

The ``@breadcrumbs`` decorator does not require any arguments and can be used on basically any controller function.

.. code:: python

    from dyc.modules import theming
    @mvc.controller_function(...)
    @theming.breadcrumbs # <- there it is
    def my_function(...):
        pass

Middleware
""""""""""

Again, like the theme, there's another way of adding breadcrumbs to your context, a handler option.

Simply set the ``breadcrumbs`` handler option to True and a subsequent middleware will take care of adding breadcrumbs to the context for you.

*Note: non-bool values for the ``breadcrumbs`` option are ignored*

.. code:: python
    from dyc.modules import theming
    @mvc.controller_function(
        ...
        breadcrumbs=True
    )
    def my_function(...):
        pass

Attach function
"""""""""""""""

Like theme breadcrumbs also provides a raw attach function called ``attach_breadcrumbs``, which takes an instance of ``dyc.util.structures.DynamicContent`` as an argument and performs the breadcrumb attachment **if** and **only if** there were no breadcrumbs attached previously.

This is mostly intended to be used by other decorators and/or middleware that depend on breadcrumbs.

Usage in template
^^^^^^^^^^^^^^^^^

Simply ``echo`` or ``print`` the ``breadcrumbs`` variable.

Output details
^^^^^^^^^^^^^^

The ``breadcrumbs`` variable in the template will contain a ``dyc.util.structures.InvisibleList``, which inherits from the builtin list with the only difference being, that when converted to a string (InvisibleList.__str__) will just return the concatenated string representations of its elements.

The elements of said list are instances of ``dyc.util.html.A`` and ``dyc.util.html.ContainerElement`` for the separator. This allows for easy access to the actual 'href' attributes so you can render you breadcrumbs in a different/custom way.
