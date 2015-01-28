The Backbone Datastructure
==========================

Structure
---------

The 'DynamicContent' data structure is the frameworks backbone, and the major way different components use to communicate.

Whenever a Request is received and some preprocessing has been done, a new instance of 'DynamicContent' is created.

DynamicContent is an immutable `namedtuple`_ which is essentially just a container for three fields.

.. _namedtuple: https://docs.python.org/3/library/collections.html#collections.namedtuple

When a new instance of 'DynamicContent' is being created by the framework, it contains the following fields:

=========== =========================== ==========
Field name  Type                        Function
=========== =========================== ==========
config      dict                        contains configuration information that can be used and changed by middleware and other view processing functions
context     dict                        all keys in this field will be available to code in the template during the template parsing step
request     dyc.dchttp.request.Request  contains information about the current Request (for exact description see type documentation)
=========== =========================== ==========
