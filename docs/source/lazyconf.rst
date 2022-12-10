LazyConf
=====

.. _lazyconf:

Zusammenarbeit
------------

`LazyConf <https://lazyconf.github.io/>`__ ist eine Diplomarbeit der HTL Rennweg, welche sich mithilfe von `Ansible <https://www.ansible.com/>`__ mit der automatisierten Konfiguration von Switches befasst. Sie speichern die ``Running Configuration`` zusammen mit ein paar ``show command outputs`` in zwei Ordnern. Einmal ``all_configs`` für veraltete Konfigurationen und einmal ``git`` für die aktuellste Konfiguration.

``New World`` wird somit in Lazy Conf "eingepflanzt" und benutzt die schon vorgestellte Ordnerstruktur:

.. code-block:: bash

    configs
    │
    ├── SW1
    │   ├── all_configs
    │   ├── git
    │       └── l2_interfaces.json
    │       └── vlan-data.json
    │       └── show_cdp_neighbors.txt
    │       └── show_interface_description.txt
    │       └── show_port-security_address.txt
    │
    └── SW2     
        ├── all_configs
        ├── git
            └── l2_interfaces.json
            └── vlan-data.json
            └── show_cdp_neighbors.txt
            └── show_interface_description.txt
            └── show_port-security_address.txt
