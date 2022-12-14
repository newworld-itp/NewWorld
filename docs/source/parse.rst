Parse
=====

.. _parse:



Die Methoden in ``config_parser.db`` werden von NewWorld importiert. Sie lesen die angegeben Dateien ein und wandeln diese in ``dictionaries`` um.

parse_vlan()
`````````````````````````````

``parse_vlan()`` nimmt eine JSON File als input und wandelt diesen in ein dictionary im Format ``{VLAN_ID: 'VLAN_NAME', ...}`` um.

filename: ``vlan-data.json``

Input file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    [
        {
            "name": "default",
            "vlan_id": 1,
            "state": "active",
            "shutdown": "disabled",
            "mtu": 1500
        },
        {
            "name": "VLAN20",
            "vlan_id": 20,
            "state": "active",
            "shutdown": "disabled",
            "mtu": 1500
        },
        {
            "name": "VLAN30",
            "vlan_id": 30,
            "state": "active",
            "shutdown": "disabled",
            "mtu": 1500
        },
        {
            "name": "VLAN40",
            "vlan_id": 40,
            "state": "active",
            "shutdown": "disabled",
            "mtu": 1500
        },
        {
            "name": "Management",
            "vlan_id": 99,
            "state": "active",
            "shutdown": "disabled",
            "mtu": 1500
        },
        {
            "name": "fddi-default",
            "vlan_id": 1002,
            "state": "active",
            "shutdown": "enabled",
            "mtu": 1500
        },
        {
            "name": "token-ring-default",
            "vlan_id": 1003,
            "state": "active",
            "shutdown": "enabled",
            "mtu": 1500
        },
        {
            "name": "fddinet-default",
            "vlan_id": 1004,
            "state": "active",
            "shutdown": "enabled",
            "mtu": 1500
        },
        {
            "name": "trnet-default",
            "vlan_id": 1005,
            "state": "active",
            "shutdown": "enabled",
            "mtu": 1500
        }
    ]

Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

path_to_vlan_json_file: the path to the vlan-data.json file

.. code-block:: python

    def parse_vlan(path_to_vlan_json_file: str):
        """
        Parses the vlan-data.json file and returns a dictionary with the vlan id as key and the vlan name as value
        :param path_to_vlan_json_file: the path to the vlan-data.json file
        :return: a dictionary with the vlan id as key and the vlan name as value
        """
        erg = {}
        data = json.load(open(path_to_vlan_json_file, "r"))
        for line in data:
            erg[line["vlan_id"]] = line["name"]
        return erg


parse_interface_descriptions()
`````````````````````````````

``parse_interface_descriptions()`` nimmt den ``Cisco "show interface descriptions"`` Output als input und wandelt diesen in ein dictionary im Format ``{INT_NAME: ('STATUS', 'PROTOCOL', 'DESCRIPTION'), ...}`` um.

filename: ``interface-descriptions.txt``

Input file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: text

    Interface                      Status         Protocol Description
    Gi0/0                          up             up       to_Catalyst6880X_078
    Gi0/1                          up             up       to_Nexus7000_078
    Gi0/2                          down           down
    Gi0/3                          down           down
    Gi1/0                          down           down
    Gi1/1                          down           down
    Gi1/2                          down           down
    Gi1/3                          down           down
    Gi2/0                          down           down
    Gi2/1                          down           down
    Gi2/2                          down           down
    Gi2/3                          down           down
    Vl1                            up             up


Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    def parse_interface_descriptions(path_to_interface_descriptions_file: str):
        """
        Parses the interface-descriptions.txt file and returns a dictionary with the interface name as key and a list
        consisting of status, protocol and description as value
        :param path_to_interface_descriptions_file: the path to the interface-descriptions.txt file
        :return: a dictionary with the interface name as key and a list consisting of status, protocol and description as
        value
        """
        erg = {}
        with open(path_to_interface_descriptions_file, "r") as file:
            for line in file.readlines()[1:]:
                values = line.split()
                interface = values[0]
                status = -1 if values[1].startswith("admin") else 0 if values[1] == "down" else 1
                protocol = 1 if values[3 if status == -1 else 2] == "up" else 0
                description = values[4 if status == -1 else 3] if len(values) == (5 if status == -1 else 4) else None
                erg[interface] = [status, protocol, description]
        return erg


parse_interfaces()
`````````````````````````````
``parse_interfaces()`` nimmt eine JSON File als input und wandelt diesen in ein dictionary im Format ``{INT_NAME: ('ACCESS_VLAN', 'VOICE_VLAN', [ALLOWED_TRUNK_VLAN_1,ALLOWED_TRUNK_VLAN_2,...,ALLOWED_TRUNK_VLAN_N]), ...}`` um.

filename: ``l2-interfacess.txt``

Input file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    [
        {
            "name": "GigabitEthernet0/0"
        },
        {
            "name": "GigabitEthernet0/1"
        },
        {
            "name": "GigabitEthernet0/2",
            "mode": "access",
            "access": {
                "vlan": 20
            },
            "voice": {
                "vlan": 30
            }
        },
        {
            "name": "GigabitEthernet0/3",
            "mode": "trunk",
            "trunk": {
                "encapsulation": "dot1q",
                "allowed_vlans": [
                    "10",
                    "20",
                    "30"
                ]
            }
        },
        {
            "name": "GigabitEthernet1/0"
        },
        {
            "name": "GigabitEthernet1/1"
        },
        {
            "name": "GigabitEthernet1/2"
        },
        {
            "name": "GigabitEthernet1/3"
        },
        {
            "name": "GigabitEthernet2/0"
        },
        {
            "name": "GigabitEthernet2/1"
        },
        {
            "name": "GigabitEthernet2/2"
        },
        {
            "name": "GigabitEthernet2/3"
        },
        {
            "name": "GigabitEthernet3/0"
        },
        {
            "name": "GigabitEthernet3/1"
        },
        {
            "name": "GigabitEthernet3/2"
        },
        {
            "name": "GigabitEthernet3/3"
        }
    ]

Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    def parse_interfaces(path_to_l2_interface_file: str):
        """
        Parses the l2-interface.txt file and returns a dictionary with the interface name as key and a list consisting of
        access vlan, voice vlan and allowed trunk vlans
        :param path_to_l2_interface_file: the path to the l2-interface.txt file
        :return: a dictionary with the interface name as key and a list consisting of access vlan, voice vlan and allowed
        trunk vlans
        """
        erg = {}
        data = json.load(open(path_to_l2_interface_file, "r"))
        for line in data:
            name = line["name"][:2] + line["name"][-3:]
            access = line["access"]["vlan"] if "access" in line else None
            voice = line["voice"]["vlan"] if "voice" in line else None
            trunk = line["trunk"]["allowed_vlans"] if "trunk" in line else []
            trunk.sort()
            erg[name] = [access, voice, trunk]
        return erg


parse_port_security()
`````````````````````````````

``parse_port_security()`` nimmt den ``Cisco "show port-security address"`` Output als input und wandelt diesen in ein dictionary im Format ``{INT_NAME: [VLAN, MAC-ADDRESS], ...}`` um.

filename: ``show_port-security_address.txt``

Input file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: text

                   Secure Mac Address Table
    -----------------------------------------------------------------------------
    Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                       (mins)
    ----    -----------       ----                          -----   -------------
      10    cafe.cafe.cafe    SecureConfigured              Gi0/2        -
      20    1234.5678.9abc    SecureConfigured              Gi0/3        -
    -----------------------------------------------------------------------------
    Total Addresses in System (excluding one mac per port)     : 0
    Max Addresses limit in System (excluding one mac per port) : 4096

Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    def parse_port_security(path_to_port_security_file: str):
        """
        Parses the port-security.txt file and returns a dictionary with the interface name as key and a list consisting of
        access vlan and the allowed mac address
        :param path_to_port_security_file: the path to the port-security.txt file
        :return: a dictionary with the interface name as key and a list consisting of access vlan and the allowed mac
        """
        erg = {}
        with open(path_to_port_security_file, "r") as file:
            for line in file.readlines()[5:-3]:
                values = line.split()
                vlan = values[0]
                mac_address = values[1]
                ports = values[3]
                erg[ports] = [vlan, mac_address]
        return erg
        

parse_cdp()
`````````````````````````````

``parse_cdp()`` nimmt den ``Cisco "show cdp neighbors"`` Output als input und wandelt diesen in ein dictionary im Format ``{INT_NAME: [VLAN, MAC-ADDRESS], ...}`` um.

filename: ``show_cdp_neighbors.txt``

Input file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: text

    Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                      S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                      D - Remote, C - CVTA, M - Two-port Mac Relay

    Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
    SW1.test.com     Gig 0/1           170             R S I            Gig 0/1

    Total cdp entries displayed : 1


Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    def parse_cdp(path_to_cdp_file: str):
        """
        Parses the cdp.txt file and returns a dictionary with the local interface name as key and a tuple consisting of
        the cdp neighbor and the remote interface name
        :param path_to_cdp_file: the path to the cdp.txt file
        :return: a dictionary with the local interface name as key and a list consisting of the cdp neighbor and the
        remote interface name
        """
        erg = {}
        with open(path_to_cdp_file, "r") as file:
            for line in file.readlines()[5:-2]:
                arr = line.split("  ")
                neighbor = arr[0]
                local_interface = arr[7][:2] + arr[7][-3:]
                remote_interface = arr[-1].strip()[:2] + arr[-1].strip()[-3:]
                erg[local_interface] = [neighbor, remote_interface]
        return erg
