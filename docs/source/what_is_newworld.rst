What is NewWorld?
==================

NewWorld provides an easy and intuitive way to monitor the Layer 2 of your network. It has been developed by three students of the HTL Rennweg in Vienna. It is based on Python and SQLite, but can be comfortabely executed on the command-line. By using NewWorld you can compare the current state of your network with the accepted one.

It is integrated in LazyConf_, and automatically updates without any user-input. This integration enables an even greater monitoring of your network, by monitoring not only the Layer 2 but also Layer 3.

How does NewWorld work?
------------------------

NewWorld takes Cisco Show Commands and JSON Files and input. These files are then parsed and converted into dictionaries. After the parsing is complete, these dictionaries are then turned into SQL Statements and excecuted on the Database.

The output of NewWorld is dependent on the method (see :ref:`compare` and :ref:`update`) chosen. When updating the Database, the success of the operation is returned. When comparing the current state of your network with the accepted one, the output is the differences found between these states.

.. _LazyConf: http://lazyconf.github.io

Compatibility
-------------

NewWorld workes platform-independently on any machine able to run Python 3.X.