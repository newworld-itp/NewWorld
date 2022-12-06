Usage
=====

.. _usage:


-p, --path
`````````````````````````````

NewWorld arbeitet mit mehreren Textdateien. Damit man nicht jede von diesen Dateien einzeln angeben muss, haben wir uns dafür entschieden die Dateien in einer Ordnerstruktur abzulegen.

.. code-block:: console

    python3 newworld.py -p fo<lder> <-u/-c>


In dem untenstehenden Beispiel würde man das Programm folgendermaßen ausführen:

.. code-block:: console

    python3 newworld.py -p configs <-u/-c>


Der angegebene Ordner ist das Überverzeichnis der Geräte.
Im untenliegenden Beispiel wäre das ``configs``.


Die unterliegenden Ordner sind die Geräte deren Konfiguration gespeichert werden.
Im untenliegenden Beispiel wäre das ``SW1`` oder ``SW2``.

Darunter liegt der ``git`` Ordner in welchem die Konfiguration gespeichert wurden.

Warum ist die Ordnerstruktur denn überhaupt so aufgebaut? Mehr erfahren Sie `hier`_.

.. _hier: https://newworld.readthedocs.io/en/latest/lazyconf.html

Die Dateien die in diesem Ordner liegen sind die Textdateien, in denen sich die Konfigurationen befinden.
Im untenliegenden Beispiel wäre das z.B. ``l2_interfaces.json`` oder ``show_cdp_neighbors.txt``.

.. code-block:: bash

    configs
    │
    ├── SW1
    │   ├── git
    │       └── l2_interfaces.json
    │       └── vlan-data.json
    │       └── show_cdp_neighbors.txt
    │       └── show_interface_description.txt
    │       └── show_port-security_address.txt
    │
    └── SW2      
        ├── git
            └── l2_interfaces.json
            └── vlan-data.json
            └── show_cdp_neighbors.txt
            └── show_interface_description.txt
            └── show_port-security_address.txt

.. _update_usage:

-u, --update
`````````````````````````````

Um die Datenbank upzudaten, muss man die -u Option verwenden. Damit wird die Datenbank mit den Daten die derzeit im ``folder`` stehen überschrieben.

.. code-block:: bash

    python3 newworld.py -p <folder> -u
   
   
.. _compare_usage:

-c, --compare
`````````````````````````````

Um die derzeit aktive Konfiguration mit der in der Datenbank zu vergleichen, muss man die -c Option verwenden. Als Ergebnis bekommt man die Abweichungen ausgegeben.

.. code-block:: bash

    python3 newworld.py -p <folder> -c


