Applications
============
Applications for dynamic_content are programs run with the dynamic_content runtime/framework.

Each application instance corresponds to a *project* that is being run.

A *project* is a directory with some content.

Project contents
----------------

Every project (directory) is required to contain a settings file. If no settings file is found the runtime will throw an error.
The Settings file has to either be called *settings.yml*.

Furthermore the project may contain a template directory with some templates to use and your own application code in the form of python modules/packages.

Configuring Settings
--------------------

1.  Define a database

    by setting the 'database' variable in your settings

    -   Sqlite: ``{type: 'sqlite', name: 'your/database/path.db'}``

    -   MySQL: ``{type: 'mysql', name: 'username', port: 5318008, host: 'your host', password: '????'}``

Starting the app
----------------
run ``python dynamic_content [path/to/project]``

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
