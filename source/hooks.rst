Hooks
=====

This document describes a are ways of implementing points in your code where dynamically changing handlers can be called.

Hooks are essentially classes of a specific type that handle certain events.

The Hook Manager
----------------

In order to implement hooks for certain processes dynamic_content provides you with some funtions for regitering and retreiving and executing hooks.

The basis for all hooks is formed by a Component called ``HookManager`` of type ``dycc.hooks.HookManager``. The Manager object internally keep a dictionary of hooks identified with a non-empty string name.

The manager object can be obtained by either calling ``dycc.get_component('HookManager')`` or, the clean way, by calling the static ``manager()`` method on the ``HookManager`` or ``Hook`` (sub)class.

Registering Hooks
-----------------

Before a hook can be registered the hook should be initialized by calling ``init_hook(self, hook, expected_class=Hook)`` on the ``HookManager.manager()`` instance. This adds a new, empty ``HookList`` object to the dictionary. Newly registered hooks will be required to be an instance of the expected_class. This is intended to ensure that hooks of the same key implement the same methods.

The modules also provides a ``dycc.hooks.Hook`` class which provides the following methods:

(classmethod) init_hook(cls)
    Inits a new hook with ``hook_name = cls.hook_name`` and ``expected_type == cls``

(classmethod) register_class(cls, priority=0)
    Registeres a new instance of this class '``cls()``' as a hook with ``cls.hook_name``

    This obviously will not work if your class requires arguments in ``__init__`` (other than 'self')

register_instance(self, priority=0)
    Registers this instance as a new hook with ``self.hook_name``

In order to register a new Hook you can also directly call ``register(self, hook, handler, priority=0)`` on the ``HookManager`` instance, which will add the provided handler to the specified hook. This is intended for hooks that do not inherit from ``dycc.hooks.Hook``. The priority indicated where to place it in the list (the higher the closer to the front). Raises a Type error if the handler is not an instance of ``expected_class``.

If you did not initialize your hook, the type of the first hook that gets registered with that name is assumed to be the required type for hooks registered under that key, which might lead to type errors if you're registering a subclass as first handler. *So. Remember kids , always register your hooks! (or just use the methods on Hook)*

Using Hooks
-----------

The hook manager provides some convenience methods for using hooks.

get_hooks(self, hook)
    Returns a list of hooks registered with key 'hook'

blank_call_hooks(self, hook, *args, **kwargs)
    Calls all hooks registered with key 'hook' once with args and kwargs

blank_call_hooks_with(self, hook, executable, *args, **kwargs)
    Calls executable with all hooks in key 'hook' once with args and kwargs

yield_call_hooks(self, hook, *args, **kwargs)
    Calls all hooks from key 'hook' once yielding results. (Evaluates lazily)

yield_call_hooks_with(self, hook, executable, *args, **kwargs)
    Calls executable with all hooks in key 'hook' once with args and kwargs yielding results. (Evaluates lazily)

return_call_hooks(self, hook, *args, **kwargs)
    Calls all hooks from key 'hook' once returning a result once one of the hooks returns something other than None.

return_call_hooks_with(self, hook, executable, *args, **kwargs)
    Calls executable on all hooks from key 'hook' once returning a result once one of the hooks returns something other than None.
