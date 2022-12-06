Lazy Conf
=====

.. _lazyconf:

Zusammenarbeit
------------

Lazy Conf ist eine Diplomarbeit der HTL Rennweg, welche sich mithilfe von Ansible_ mit der automatisierten Konfiguration von Switches befasst.

.. _Ansible: https://www.ansible.com/

``New World`` wird somit in Lazy Conf "eingepflanzt" und benutzt die schon vorgestellte Ordnerstruktur:

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
