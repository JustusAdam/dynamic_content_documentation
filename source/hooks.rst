Hooks
=====

This document describes a are ways of implementing points in your code where dynamically changing handlers can be called.

Hooks are essentially classes of a specific type that handle certain events.

Hooks via subclassing
---------------------

The ``hooks.ClassHook`` is the base class for hooks using inheritance to implement hooks. As such you may create your own custom hook classes that inherit from ClassHook and definging the ``ClassHook.hook_name`` class attribute.

Classhooks offer a number of methods for interacting with the hooks as classmethods.

``@classmethod init_hook``
    Initilize the hook with the manager.

``@classmethod get_hooks``
    Obtain the list of registered hooks from the manager.

``@classmethod register_class``
    Registers a new instance of this hook class with the manager (only usable if ``__init__`` takes no arguments).

``register_instance``
    This is an instance method. It will register the object with the manager.

As well as call methods, such as ``yield_call_hooks``, ``yield_call_hooks_with``, ``return_call_hooks``, ``return_call_hooks_with``, ``blank_call_hooks`` and ``blank_call_hooks_with``. Which work very much the same way as those defined in manager, but basically have the ``hook`` argument already filled in with their own ``hook_name``.

Hooks via Instancing
--------------------

The ``hooks.InstanceHook`` is the base class for hooks using instancing.

The idea is, that you will create a subclass of ``InstanceHook`` and implement the methods you'll want to call on it later and then register individual instances for various hooks. InstanceHooks can be used for mutliple hooks per class in contrast to the ClassHook, which can only be used for one hook per class.

The defined methods are the same as with ``ClassHook`` but are not class but rather instance methods, with one exception, there is no ``register_class`` method.

Functions as hooks
^^^^^^^^^^^^^^^^^^

The ``hooks.FunctionHook`` class is a special subclass of ``InstanceHook`` and is designed to allow you to easily turn any function into a hook.

You can do so by instantiating a new ``FunctionHook`` with your function, the name of the hook and a priority and then do all the initializing and registering yourself or you can simply decorate you function with the ``@FunctionHook.make(hook_name, priority, expected_class=None)`` decorator and it'll take care of initializing the hook and registering your function.

*Implementation detail: @FunctionHook.make is a classmethod, as such you may subclass FunctionHook and still have it work properly*

The Hook Manager
----------------

In order to implement hooks for certain processes dynamic_content provides you with some funtions for regitering and retreiving and executing hooks.

The basis for all hooks is formed by a Component called ``HookManager`` of type ``dycc.hooks.HookManager``. The Manager object internally keep a dictionary of hooks identified with a non-empty string name.

The manager object can be obtained by either calling ``dycc.get_component('HookManager')`` or, the clean way, by calling the static ``manager()`` method on the ``HookManager`` or ``Hook`` (sub)class.

Calling/Using Hooks
-----------

The hook manager provides some convenience methods for using hooks.

``get_hooks(self, hook)``
    Returns a list of hooks registered with key 'hook'

``blank_call_hooks(self, hook, *args, **kwargs)``
    Calls all hooks registered with key 'hook' once with args and kwargs

``blank_call_hooks_with(self, hook, executable, *args, \**kwargs)``
    Calls executable with all hooks in key 'hook' once with args and kwargs

``yield_call_hooks(self, hook, *args, **kwargs)``
    Calls all hooks from key 'hook' once yielding results. (Evaluates lazily)

``yield_call_hooks_with(self, hook, executable, *args, **kwargs)``
    Calls executable with all hooks in key 'hook' once with args and kwargs yielding results. (Evaluates lazily)

``return_call_hooks(self, hook, *args, **kwargs)``
    Calls all hooks from key 'hook' once returning a result once one of the hooks returns something other than None.

``return_call_hooks_with(self, hook, executable, *args, **kwargs)``
    Calls executable on all hooks from key 'hook' once returning a result once one of the hooks returns something other than None.
