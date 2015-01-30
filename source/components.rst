Components
==========

Overview - What are Components
------------------------------

Everyone knows global variables are bad, but sometimes they can be just so useful.

In order to avoid making extensive use of global variables, dynamic_content introduces a set of tools to make access to some objects available instance wide.

This system is not intended to be used with any kind of datastructure that is mutated by any system, which is why components themselves cannot be re-assigned. Components are meant to be used read-only.

How Components are stored
^^^^^^^^^^^^^^^^^^^^^^^^^

Components are stored in a non-case sensitive dictionary ``dycc._component.ComponentContainer``. Be aware that when assigning and looking up keys they will be reformatted with ``dycc._component.name_transform`` which removes all spaces and underscores and makes all characters lower case.

Components/Component keys can be types or strings.

Accessing Components
^^^^^^^^^^^^^^^^^^^^

The ``dycc`` module provides two functions for directly accessing Components: ``get_component`` and ``call_component`` which both do exactly the same thing. They are aliases for the ``ComponentContainer`` dictionary instance and invoke its ``__call__`` method which either returns the component (when supplied with only one argument) or call it with the remainder of the arguments.

The clean and test secure way of referencing/accessing Components is via injection.

Two injection decorators are provided by the ``_components`` module.

.. code:: python

    def inject(*components, **kwcomponents):
        """
        Injects components into the function.

        All *components will be prepended to the *args the
         function is being called with.

        All **kwcomponents will be added to (and overwrite on key collision)
         the **kwargs the function is being called with.

        :param component:
        :param argname:
        :return:
        """

        def inner(func):
            return functools.partial(
                func,
                *tuple(
                    get_component(a) for a in components
                ),
                **{
                    a:get_component(b) for a,b in kwcomponents.items()
                }
            )

        return inner

All \*components provided must be either strings or types. The wrapped function will be called with the Components corresponding to the string/type \*args in the order you specified them in prepended to the functions call \*args.

All \**kwcomponents values must as well be either strings or types. The \**kwargs the wrapped function was called with will be updated with the components corresponding to the keys.

*Implementation note: since Components are lazy, we can bind them at import time to a partial*

Components are lazy. The ComponentContainer always returns a ``ComponentWrapper`` object, which mimics the underlying wrapped Component. This means there will never be a failed component retrieval, even if the actual component does not exist. However trying to access atttributes or methods will result in a ``ComponentNotLoaded`` exception.

Storing Components
^^^^^^^^^^^^^^^^^^

You may define your own components and use them. The 'clean' way to do so is to use the ``@component`` or ``@Component`` decorator (they do not differ) on your 'class' definition. The decorator expects a 'name' argument which is name your Component will be accessible by.  You may provide further arguments to the decorator, it will provide those to the component instance when instantiating your class.

The new component will be stored under the name you provided as well as the class type.

You may also directly assign component, which you can theoretically do by assigning to ``get_component``, but the cleaner, and better lookign way is to use ``dycc.register(name_or_type)``.


Settings
--------

The 'settings' Component is a dictionary containing the fundamental program instance settings.

PathMap
-------

The URL resolver instance.
