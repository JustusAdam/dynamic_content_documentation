A new Application
=================

Configuring modules
-------------------

1.  Download the modules you like and

    either

    - place them in ``dyc/modules`` and add them to ``settings.MODULES``

    - import them in your own start script

Configuring Settings
--------------------

1.  Define a database

    by setting the DATABASE variable in ``dyc.includes.settings``

    -   Sqlite: ``dyc.structures.SQLite(path_to_database)``

    -   MySQL: ``dyc.structures.MySQL(user, autocommit, password, name, host)``

Starting the app
----------------

Write your own main script
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    from dyc.application import main
    # import your own packages
    # and modules

    # do your preparation work

    # call this function from anywhere
    main.main()

    # BAM, there you go, up and running

and run it with either ``pypy3`` or ``python3``

Use the predefined main script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

invoke either

-   ``pypy3 path_to_framework/dyc/application/main.py --some command_line --arguments true``

-   ``python3 path_to_framework/dyc/application/main.py --some command_line --arguments true``

Use the predefined bash script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This method is the easiest and probably most convenient way of starting dynamic_content and mainly designed for topse who chose the cms distribution and dont want any code-level customisation.

invoke ``start.sh`` in the dynamic_content root directory

*please note: command line arguments are not yet propagated from the bash script to the application*
