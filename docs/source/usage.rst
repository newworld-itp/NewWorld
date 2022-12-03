Usage
=====

.. _usage:

-p, --path
`````````````````````````````

NewWorld arbeitet mit mehreren Textdateien. Damit man nicht jede von diesen Dateien einzeln angeben muss, haben wir uns dafür entschieden die Dateien in einer Ordnerstruktur abzulegen. 

In dem untenstehenden Beispiel würde man das Programm folgendermaßen ausführen:

.. code-block:: console

    python3 newworld.py -p configs <-u/-c>


Der angegebene Ordner ist das Überverzeichnis der Geräte.
Im untenliegenden Beispiel wäre das ``configs``.


Die unterliegenden Ordner sind die Geräte deren Konfiguration gespeichert werden.
Im untenliegenden Beispiel wäre das ``SW1`` oder ``SW2``.

Die darunterliegenden Ordner sind die Timestamps wann diese Konfiguration gespeichert wurde.
Im untenliegenden Beispiel wäre das ``2022-11-22_08-02-07`` oder ``2022-11-23_09-17-13``.

.. note::

   Es wird immer der NEUSTE Ordner verwendet! (= der Ordner mit dem "höchsten" Namen)

Die Dateien die in diesem Ordner liegen sind die Textdateien, in denen sich die Konfigurationen befinden.
Im untenliegenden Beispiel wäre das z.B. ``l2_interfaces.json`` oder ``show_cdp_neighbors.txt``.

.. code-block:: bash

    configs
    │
    ├── SW1
    │   ├── 2022-11-22_08-02-07
    │   │   └── l2_interfaces.json
    │   │   └── vlan-data.json
    │   │   └── show_cdp_neighbors.txt
    │   │   └── show_interface_description.txt
    │   │   └── show_port-security_address.txt
    │   │
    │   └── 2022-11-23_09-17-13
    │       └── l2_interfaces.json
    │       └── vlan-data.json
    │       └── show_cdp_neighbors.txt
    │       └── show_interface_description.txt
    │       └── show_port-security_address.txt
    │
    └── SW2      
        ├── 2022-11-22_08-02-07
        │   └── l2_interfaces.json
        │   └── vlan-data.json
        │   └── show_cdp_neighbors.txt
        │   └── show_interface_description.txt
        │   └── show_port-security_address.txt
        │
        └── 2022-11-23_09-17-13
            └── l2_interfaces.json
            └── vlan-data.json
            └── show_cdp_neighbors.txt
            └── show_interface_description.txt
            └── show_port-security_address.txt

.. _update_usage:

-u, --update
`````````````````````````````

Danach muss man Python und die Python Libraries(z.B. Django) installieren

.. code-block:: console

   gnsave@server $ sudo apt-get install python3
   gnsave@server $ cd NewWorld
   gnsave@server $ pip3 install -r requirements.txt
   
   
.. _compare_usage:

-c, --compare
`````````````````````````````

Zu guter Letzt muss man newworld.py starten

.. code-block:: console

   gnsave@server $ python3 newworld.py -p <folder_path> <-c/-u>


