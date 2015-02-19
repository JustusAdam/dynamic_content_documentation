Components
==========

Overview - What are Components
------------------------------

Everyone knows global variables are bad, but sometimes they can be just so useful.

In order to avoid making extensive use of global variables, dynamic_content introduces a set of tools to make access to some objects available instance wide.

This system is not intended to be used with any kind of datastructure that is mutated by any system, which is why components themselves cannot be re-assigned by default. Components are meant to be used read-only.

Component related functionality is found in the ``framework.machinery.component`` module. All component related functions and classes mentioned here can be found in that module.

How Components are stored
^^^^^^^^^^^^^^^^^^^^^^^^^

Components are stored in a non-case sensitive dictionary ``ComponentContainer``. Be aware that when assigning and looking up keys they will be reformatted with ``name_transform`` which removes all spaces and underscores and makes all characters lower case.

Components/Component keys can be types or strings.



Accessing Components
^^^^^^^^^^^^^^^^^^^^

The clean and test secure way of referencing/accessing Components is via injection.

Two injection decorators are provided by the module:

``inject(*components, **kwcomponents)``
    for decorating functions

``inject_method(*components, **kwcomponents)``
    for decorating methods, including ``__init__``

All \*components provided must be either strings or types. The wrapped function will be called with the Components corresponding to the string/type \*args, in the order you provided them in, prepended to the functions call \*args. ``inject_method(

All \**kwcomponents values must as well be either strings or types. The \**kwargs the wrapped function was called with will be updated with the components corresponding to the keys.

There is another way of accessing components using ``get_component`` (or ``call_component``).

Components accessed via injection will be the actual registered object. Whereas components accessed via ``get_component`` or ``call_component`` return a ``ComponentWrapper`` object even if a Component with the corresponding name or type has not been registered yet. Thus the ``get_component`` method will never fail. This is due to Components being lazy and because ``get_component`` might get called at import time and the component might not be loaded yet. The actual component can be obtained using the ``get()`` method on the ``ComponentWrapper`` object but be aware that this may raise a ``ComponentNotLoaded`` exception if the component has not been registered yet.


Storing Components
^^^^^^^^^^^^^^^^^^

You may define your own components and use them. The 'clean' way to do so is to use the ``@component`` or ``@Component`` decorator (they do not differ) on your 'class' definition. The decorator expects a 'name' argument which is name your Component will be accessible by.  You may provide further arguments to the decorator, it will provide those to the component instance when instantiating your class.

The new component will be stored under the name you provided as well as the class type.

You may also directly assign component, which you can theoretically do by assigning to ``get_component``, but the cleaner, and better looking way is to use ``register(name_or_type)``.


Settings
--------

The 'settings' Component is a dictionary containing the fundamental program instance settings.

PathMap
-------

The URL resolver instance.

HookManger
----------

Singleton managing different hooks. Registering, removing and calling them.
