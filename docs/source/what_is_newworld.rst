Was ist NewWorld?
==================

NewWorld ist ein leicht zu bedienendes und intuitives Tool, um den ``Layer 2`` eines Netzwerks zu überwachen. Es ist von drei Schülern der HTL Rennweg in Wien developed worden. Das Tool basiert auf ``Python`` und ``SQLite`` und kann bequem über die Kommandozeile bedient werden. 

Durch die Benutzung von NewWorld kann man den derzeitigen Status des Netzwerkes mit dem Akzeptierten vergleichen. Außerdem ist das Tool in LazyConf_ integriert und aktualisiert sich automatisch, ohne jeglichen Benutzer-Input. Durch diese Integration lässt sich eine noch bessere Überwachung erreichen, da nicht nur der ``Layer 2``, sondern auch der ``Layer 3`` überwacht wird. Mehr über unsere Zusammenarbeit mit LazyConf finden Sie hier_.

Wie funktioniert NewWorld?
------------------------

NewWorld bekommt ``Cisco Show Commands`` und ``JSON`` Dateien als Input und wandelt diese in Python ``dictionaries`` um. Nachdem dieser Prozess beendet wurde, werden diese ``dictionaries`` in ``SQL-Statements`` umgewandelt und auf einer ``SQLite`` Datenbank augeführt.

Das Ergebnis von NewWorld kommt auf die gewählte Methode an (siehe :ref:`usage` ).
Wenn man die Datenbank mit der derzeitgen Konfiguration befüllen möchte, muss man NewWorld mit der ``update`` (siehe :ref:`update_usage` ) Methode starten. 
Wenn man die derzeitige Konfiguration mit der Konfiguration in der Datenbank vergleichen möchte, muss man die ``compare`` (siehe :ref:`compare_usage` ) Methode benutzen.

.. _LazyConf: http://lazyconf.github.io

.. _hier: https://newworld.readthedocs.io/en/latest/lazyconf.html

Kompatibilität
-------------

NewWorld läuft plattformunabhängig auf jedem Gerät, auf dem Python 3.X läuft.
