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
