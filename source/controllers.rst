Registering a new Controller
============================

TLDR
----

 - module ``framework.route``

 -  Put ``@controller_function(value, method, headers, query, **options)`` as the top-most decorator on your function (``value`` are the url_paths it applies to).

 -  If you're using a method use ``@controller_class`` on the class and ``@controller_method(value, method, headers, query, **options)`` on the method (otherwise works like ``@controller_function``)

 -  Let the function return the name of the view to use.

How it operates
---------------

Websites in dynamic_content are created using a html template, also called 'view' and a function called the controller function or controller method which supplies context/values for the template parser.

The frameworks backbone data structure called 'DynamicContent' glues these two together.

The decorator
-------------

Functions operating as a controller in dynamic_content are marked as such using a python decorator.

Name and location
^^^^^^^^^^^^^^^^^

The decorator is called ``@controller_function()`` or ``@controller_method()`` if your controller function is a method of a class and not a bare function.

**Important**: If you are using a method as a controller the class containing the method has to be annotated as ``@controller_class`` or your controller will not be registered.

The decorator is located in the package ``framework.route(.decorator)``.

This decorator then has to be supplied with configuration information. Which pages that this controller wished to handle and how it is to be handled.

**Important**: Usually this decorator is (has to be) the top-most decorator that is applied to the function since decorators resolve inside out, meaning any decorator applied above the ``@controller_function`` decorator has no effect on the in/output when the framework calls the controller function.

More that one of these decorators can be applied to the same function.

Parameters
^^^^^^^^^^

The full function signature is (the same signature applies to ``controller_method``):

.. code:: python

    def controller_function(
        value,
        *,
        method=dchttp.RequestMethods.GET,
        headers=None,
        query=False,
        **options
        ):
        pass

=========== =========================================== ===========
Name        Type                                        default
=========== =========================================== ===========
value       str, set[str]                               no default
method      str, set[str]                               'get'
headers     str, set[str]                               None
query       bool, list[str], tuple[str], dict{str: str} False
options     any                                         no options
=========== =========================================== ===========

Effects
"""""""

value
    Paths that this controller function handles

method
    Types of request methods this controller function handles

headers
    Special headers a request must have to be handled by this controller (None=no special headers) (does not work yet)

query
    Either marks whether this controller accepts a query **or** which query parameters this function wants and in the case of the dict, how they should be called

options
    Additional options that are attached to the resulting ControllerFunction (attribute 'options') and can be accessed by view middleware


Path parameters
^^^^^^^^^^^^^^^

When specifying a path for a controller function/method you can utilize a special minilanguage to handle dynamic paths.

Path parameters are enclosed in curly braces '{}' and can either be just types or types and an argument name separated by a space (``{str}``, ``{int}``, ``{int number}``, ``{str name}``).

When the controller function is called the current path gets matched against these parameters and the math values are given as arguments to the controller. Unnamed parameters are supplied as positional arguments in the order they appear in the path aka ``/hello/you/my/4`` when matched with ``/hello/{str}/my/{int}`` will yield ``'you', 4``. Named parameters are supplied as keyword arguments where the parameter name is the argument name aka ``/hello/you/my/4`` matched with ``/hello/{str name}/my/{int}`` yields ``4, name='you'``.

Currently supported types are ``str`` and ``int`` (support for ``float`` and ``hex`` is planned in the future)

Path parameters have to be an entire path section aka ``/some/{str}/hello``, there is no support for partial parameters aka ``some/either-{str}/hello`` yet. However you can use as many path parameters as you like and switch between named and unnamed path parameters.

Queries
^^^^^^^

Your controller function can express its desire to accept a query in three different ways. Depending on the type of the argument supplied to `query=` in the decorator.

bool (True)
    When True the controller will be called with the full query (type=dict[str,list[str]]) as a positional argument.

list[str], tuple[str], set[str], frozenset[str]
    controller will be called with keyword arguments whose keys are the strings from the iterable and values are the values from the query dict corresponding assigned to that key
    If a key is not present in query, None will be the value. (``query.get(key, None)``)

dict[str, str]
    Similar to the above as the query will be filtered for the keys from the dict. However the names of the keyword arguments the controller is called with will be the values from the dict you supplied.

str
    Special case of the list, set, etc. The controller will be called with a keyword argument where the name is the supplied string and the value is the value from the query assigned to this key.


Example
^^^^^^^

.. code:: python

    from framework import route
    from framework import http

    # defining a controller function
    @route.controller_function(
        {'greeting/hello', 'greeting/hola'}, # set of paths to handle
        method=http.RequestMethods.GET, # method to handle
        query=False # we dont want a query
    )
    def my_controller(dc_obj):
        dc_obj.context['title'] = "Greeting"
        dc_obj.context['greeting'] = "Whoever you are"
        return "greeting" # returning the view name

    # defining a controller method
    @route.controller_class
    class MyController(object):
        @route.controller_method(
            'hello/{str}', # we can specify a path with a string instead of a set
            method=http.RequestMethods.POST, # lets handle some post requests
            query=['city', 'street'],
            anti_csrf=False, # one of the **options, this one turns csrf checking off
            require_ssl=True # another **option, this one will force ssl, if available
        )
        def my_method(self, dc_obj, path_arg, city, street):
            # do stuff
            return ':redirect:/somewhere'


Known Options
^^^^^^^^^^^^^

=============== =============== =========================================== ==========
Name            Expected type   Used by                                     Default
=============== =============== =========================================== ==========
anti_csrf       bool            dycc.middleware.csrf.AntiCSRFMiddleware     True
require_ssl     bool            dycc.middleware.ssl.ConditionalSSLRedirect  False
no_context      bool            dycc.application.app.Application            False
json_output     bool            dycc.middleware.rest.JSONTransform          False
theme           bool, str       dycm.theming.Middleware                     False
breadcrumbs     bool            dycm.theming.Middleware                     False
=============== =============== =========================================== ==========


With the following effects:

anti_csrf
    en/disable csrf checking for requests to this path

require_ssl
    forces ssl encryption on requests to this path, if ssl is enabled in settings

no_context
    if True the context (DynamicContent) object argument is omitted when calling the controller

    Please note that some decorators, such as ``@dycm.users.decorator.authorize(permission)`` still required the DynamicContent object

json_output
    if True the middleware will compile what you returned as view into a json object and wrap it in a valid dycc.http.response.Response object

theme
    - True: themes the view (with default theme from settings.DEFAULT_THEME)

    - False: does not theme the view

    - str: themes the view (with the theme name provided as default theme)

breadcrumbs
    if True attaches rendered breadcrumbs to context

Implementation details
^^^^^^^^^^^^^^^^^^^^^^

 1. The actual signature of the decorator is obscured, since it is only a partially applied function. The real decorator is called ``_controller_function``/``_controller_method`` and additionally takes a type as a first argument.

    An instance of that type is registered with the pathmapper when the decorator registers the controller.

    .. code:: python

        def _controller_function(
            class_,
            value,
            *,
            method=http.RequestMethods.GET,
            headers=None,
            query=False,
            **options
            ):
            pass

 #. ``@controller_method`` does not return the original function but rather a callable instance of dycc.route.decorator.ControllerFunction.

Structure
---------

Any function that handles a view requires a specific signature that depends on the options chosen in the decorator.

Common Signature
^^^^^^^^^^^^^^^^

Any normal controller function has the following base signature:

.. code:: python

    @controller_function(**options)
    def controller_f(dc_obj):
        dc_obj # instance of dycc.util.structures.DynamicContent
        return "" # view name


Common signature features
"""""""""""""""""""""""""

 -  unless ``no_context=True`` is set in the controller options every controller function is being called with an instance of dycc.util.structures.DynamicContent matching the request as the first argument.
 -  unless a decorator is used to change the return outside of the controller itself, the return should be the name of the view/template that will be used.

    The '.html' can be omitted in the view name, it'll automatically get added by the formatter.

    Decorators changing the return are for example:

     -  ``dycc.route.decorator.json_return``,

     -  ``dycm.node.make_node``

Additional features
"""""""""""""""""""


Argument ordering rules
"""""""""""""""""""""""

 1. positional arguments first
 #. instance of DynamicContent always first
 #. path arguments next, in the order they appear
 #. the query dict (if query=True)
 #. keyword arguments next
 #. the named path arguments
 #. the named query arguments

.. code:: python

    # '/' before the path is optional
    @dycc.route.controller_function(
        'handle/{str}/{int}/{str name}/hello/{int number}',
        method=dycc.http.RequestMethods.GET,
        query=['some', 'argument']
    )
    def my_function(
        instance_of_DynamicContent,
        path_argument_1,
        path_argument_2,
        name,
        argument,
        number,
        some
        ):
        # assuming a path 'handle/jeremy/2/clarkeson/hello/300'
        # and a query with {'argument': [12]}
        #
        # the variables would be as follows:

        path_argument_1 == 'jeremy'
        path_argument_2 == 2
        name == 'clarkeson'
        number == 300
        some == None
        argument == [12]

        return 'page'
