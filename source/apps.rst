A new Application
=================

Configuring modules
-------------------

1.  Download the modules you like and

    either

    -   place them in ``dycm`` and add them to the settings file (will be ``settings.yml`` if you're using a pre-packaged distribution)

    -   import them in your own start script

Configuring Settings
--------------------

The way you configure your settings strongly depends on whether you use dynamic_content as a pre-packaged cms or want to write your own application.

If you are using a pre-packaged disttribution
    Simply open settings.yml in the directory of the applcation and change the values you'd like (if required at all).

If you're writing you own application
    You may add custom settings by two means, either

     - create a settings.yml or settings.json file containing a dictionary/mapping/object with keys and values representing the settings and provide the filepath to main.main() when starting the application. (Command line arguments still work)

     - directly provide a python dictionary of settings to main.main()

1.  Define a database

    by setting the 'database' variable in your settings

    -   Sqlite: ``{type: 'sqlite', name: 'your/database/path.db'}``

    -   MySQL: ``{type: 'mysql', name: 'username', port: 5318008, host: 'your host', password: '????'}``

Starting the app
----------------

Write your own main script
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    from dycc.application import main
    # import your own packages
    # and modules

    # do your preparation work

    # call this function from anywhere
    main.main('path/to/settings.yml')

    # BAM, there you go, up and running

and run it with either ``pypy3`` or ``python3``

*make sure the dycc package is in the PYTHONPATH*

Use the predefined main script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you chose to use a pre-packaged distribution you can start the server by invoking the python/pypy interpreter on the main script, which is probably `main.py` in your app directory.

Use the predefined bash script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This method is the easiest and probably most convenient way of starting dynamic_content and mainly designed for topse who chose the cms distribution and dont want any code-level customisation.

invoke ``start.sh`` in the dynamic_content root directory

*please note: command line arguments are not yet propagated from the bash script to the application*

Command line arguments
^^^^^^^^^^^^^^^^^^^^^^

Command line arguments let you you specify certain settings when starting the app. These overwrite the settings defined in your settings file for this instance of the app.

+-----------------+----------------------------+
| Option          |  Choices                   |
+=================+============================+
| --http_enabled  | true, false                |
+-----------------+----------------------------+
| --https_enabled | true, false                |
+-----------------+----------------------------+
| --logfile       | path/to/logfile            |
+-----------------+----------------------------+
| --pathmap       | multi_table, tree          |
+-----------------+----------------------------+
| --port          | integer                    |
+-----------------+----------------------------+
| --ssl_port      | integer                    |
+-----------------+----------------------------+
| --host          | string or ip               |
+-----------------+----------------------------+
| --ssl_certfile  | path/to/certfile           |
+-----------------+----------------------------+
| --ssl_keyfile   | path/to/keyfile            |
+-----------------+----------------------------+
| --server        | plain, wsgi                |
+-----------------+----------------------------+
| --loglevel      | log_warnings, log_errors,  |
|                 | throw_errors, throw_all    |
+-----------------+----------------------------+

Effects of selected Options:
""""""""""""""""""""""""""""

http_enabled/https_enabled
    Enable/disable types of server

pathmap
    Which implementation of pathmap to use (can have an impact on performance)

port/ssl_port
    Ports to use for http/https. Cannot be the same and has not effect if corresponding server is disabled.

loglevel
    Which messages to log
