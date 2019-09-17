====================
elastic7-doc-manager
====================

.. image:: https://travis-ci.org/tmpaul06/elastic7-doc-manager.svg
   :alt: View build status
   :target: https://travis-ci.org/tmpaul06/elastic7-doc-manager

Forked from original `elastic2-doc-manager <https://github.com/yougov/elastic2-doc-manager>`_ to support ES 7.x.

To see an example, check: https://github.com/tmpaul06/es7-mongo-example

   >  Note: This project is not available via pypi. If you want to use the project, please point your requirements file or setup.py to this github repo.
   
   
Changelog
~~~~~~~~~
The following are the changes/differences between ``elastic7-doc-manager`` and ``elastic2-doc-manager``.

1. MongoDB database + MongoDB collection is mapped to a single Elasticsearch index with the convention: ``{db}_{collection}`` where ``db`` and ``collection`` are the lowercase names of the MongoDB database and MongoDB collection.
2. No more ``doc_type`` for ES indices.
3. Removed ``mapper-attachment`` plugin which was responsible for parsing file attachments, and replaced it with newer ``ingest-attachment`` plugin.

Refer to the es7-mongo-example link given in the first section for more details.

Rationale
=========
ES 7.x is deprecating ``doc_type`` going forward, hence this library takes a slightly different route compared to the previous ``elastic2-doc-manager``.

Previously, MongoDB databases were mapped to ES indices, and MongoDB collections were mapped to ES doc types. But this causes problems with the underlying Lucene backend. An index in ES is not a database, but it is more like a table, and therefore it makes sense to store a collection of documents of similar type in an ES index.

For more info, https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html

Since ES has no notion of a "database", we namespace the ES index according to MongoDB db & collection.

For e.g ``foobar`` database in MongoDB and ``test_me`` collection in MongoDb will map to ``foobar_test_me`` ES index. The naming convention is fixed for now, may become flexible in the future to support other use cases.

Since mongo-connector uses a namespace mapping to create a many to many relationship betweeen MongoDB and ES, please go through the test cases to confirm that it works for you.

Original Docs
==============

The mongo-connector project originated as a MongoDB mongo-labs
project and is now community-maintained under the custody of YouGov, Plc.

Getting Started
===============

This package is a document manager for
`mongo-connector <https://github.com/yougov/mongo-connector>`_ that
targets Elasticsearch versions 2.x and 5.x -- don't let the name fool you!
For information on running mongo-connector with Elasticsearch, please see the
`MongoConnector Usage with Elasticsearch
<https://github.com/yougov/mongo-connector/wiki/Usage%20with%20ElasticSearch>`_
wiki page.

Installation
============

The installation of the elastic2-doc-manager depends on which version of
Elasticsearch you are targeting.

Elasticsearch 1.x
-----------------

This is the document manager for Elasticsearch 2.x and 5.x. If you
want to target Elasticsearch 1.x, please install the
`elastic-doc-manager <https://github.com/yougov/elastic-doc-manager>`_.

Elasticsearch 2.x
-----------------

For use with an Elasticsearch 2.x server, install with
`pip <https://pypi.python.org/pypi/pip>`__::

  pip install 'elastic2-doc-manager[elastic2]'

Elasticsearch 5.x
-----------------

For use with an Elasticsearch 5.x server, install with::

  pip install 'elastic2-doc-manager[elastic5]'

.. note:: Version 0.3.0 added support for Elasticsearch 5.x.

Elasticsearch 7.x
-----------------
For use with Elasticsearch 7.x, install this repo with::

   pip install git+https://github.com/tmpaul06/elastic7-doc-manager.git


Amazon Elasticsearch Service
----------------------------

To use with Amazon Elasticsearch Service, you must install the required AWS
dependencies along with the version of Elasticsearch::

  pip install 'elastic2-doc-manager[elastic2,aws]'


Development
-----------

You can also install the development version of elastic2-doc-manager
manually::

  git clone https://github.com/yougov/elastic2-doc-manager.git
  pip install -e './elastic2-doc-manager[elastic2]'

You may have to run ``pip`` with ``sudo``, depending on where you're
installing and what privileges you have.

.. note:: Please note that before mongo-connector version 2.2.2, the elastic
doc manager was packaged with mongo-connector and only supported
Elasticsearch 1.x.

Running the tests
-----------------
Requirements
~~~~~~~~~~~~

1. Copy of the Elastic 2.x Document Manager Github repository

  The tests are not included in the package from PyPI and can only be acquired
  by cloning this repository on Github::

      git clone https://github.com/yougov/elastic2-doc-manager

2. Tox

  Install `tox <https://pypi.org/project/tox>`_.

2. Environment variables

  There are a few influential environment variables that affect the tests. These are
  defined in the tox.ini.

All the tests live in the `tests` directory.

Running tests on the command-line
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

While the tests take care of setting up and tearing down MongoDB clusters on
their own, make sure to start Elasticsearch before doing a full test run!

You can run all the tests with one command (this works in all supported Python versions)::

  tox

Error messages
~~~~~~~~~~~~~~

Some of the tests are meant to generate lots of ``ERROR``-level log messages,
especially the rollback tests. mongo-connector logs exceptions it encounters
while iterating the cursor in the oplog, so we see these in the console output
while MongoDB clusters are being torn apart in the tests. As long as all the
tests pass with an `OK` message, all is well.
