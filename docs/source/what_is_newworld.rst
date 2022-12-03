Was ist NewWorld?
==================

NewWorld ist ein leicht zu bedienendes und intuitives Tool um den Layer 2 eines Netzwerks zu überwachen. Es ist von drei Schülern der HTL Rennweg in Wien developed worden. Das Tool basiert auf Python und SQLite und kann bequem über die Kommandozeile bedient werden. 

Durch die Benutzung von NewWorld kann man den derzeitigen Status des Netzwerkes mit dem akzeptierten vergleichen. Außerdem ist das Tool in LazyConf_ integriert und aktualisiert sich automatisch ohne jeglichen Benutzer-Input. Durch diese Integration lässt sich eine noch bessere Überwachung erreichen, da nicht nur der Layer 2 sondern auch der Layer 3 überwacht wird.

Wie funktioniert NewWorld?
------------------------

NewWorld bekommt 'Cisco Show Commands' und 'JSON' Dateien als Input und wandelt diese in Python 'dictionaries' um. Nachdem dieser Prozess beendet wurde, werden diese 'dictionaries' in SQL Statements umgewandelt und auf einer SQLite Datenbank augeführt.

Das Ergebnis von NewWorld kommt auf die gewählte Methode an (siehe :ref:`compare` und :ref:`update`). 
Wenn man die Datenbank mit der derzeitgen Konfiguration befüllen möchte, muss man NewWorld mit der update (siehe :ref:`_update_usage` ) Methode starten. 
Wenn man die  derzeitige Konfiguration mit der Konfiguration in der Datenbank vergleichen möchte muss man die compare (siehe :ref:`_compare_usage` ) Methode benutzen.

The output of NewWorld is dependent on the method (see :ref:`compare` and :ref:`update`) chosen. When updating the Database, the success of the operation is returned. When comparing the current state of your network with the accepted one, the output is the differences found between these states.

.. _LazyConf: http://lazyconf.github.io

Compatibility
-------------

NewWorld workes platform-independently on any machine able to run Python 3.X.