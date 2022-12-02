Parse
=====

.. _parse:


parse_vlan
`````````````````````````````

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


parse_interface_descriptions
`````````````````````````````

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


parse_interfaces
`````````````````````````````

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


parse_port_security
`````````````````````````````

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
        

parse_cdp
`````````````````````````````

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