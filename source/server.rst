Servers
=======


The builtin Servers
-------------------

dynamic_content comes with two ready-to-go server implementations in python with the standard python server interface (wsgi) and a plain http server + request handler.

WSGI
^^^^

`WSGI`_ is the standard way python web frameworks interface with webservers. You can find the exact specifications `here`_.

.. _WSGI: https://wsgi.readthedocs.org
.. _here: https://wsgi.readthedocs.org

The python WSGI server implementation is the default and best supported/tested server version. This server version is out of the ptyhon standard library and not tested for security yet. If you're particularily knowledgeable about server security please consider sharing.

Plain HTTP
^^^^^^^^^^

Python plain HTTP socketserver and request handler.

Apache
------

Support for Apache (mod_wsgi) is not implemented yet, but planned for the future.

Nginx
-----

Support for nginx servers is not implemented yet, but planned for the future.
