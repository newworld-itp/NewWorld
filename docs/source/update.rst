Update
=====

.. _update:

in db_calls.py


insert_switch
`````````````````````````````

.. code-block:: python

    def insert_switch(hostname: str, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Insert a switch into the database
        :param hostname: the hostname of the switch
        :param db_cursor: the cursor of the database
        :return:
        """
        sql_query = f"INSERT INTO Switch (hostname) " \
                    f"VALUES ('{hostname}')"
        db_cursor.execute(sql_query)

``sql_query`` ist das SQL-Statement welches ausgeführt wird. Hierbei wollen wir einen Switch mittels des ``hostname`` Parameters in die Datenbank schreiben. Ein Entry besteht somit aus der Switch-ID sowie dem jeweiligem Hostname. Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt.

insert_vlan
`````````````````````````````

.. code-block:: python

    def insert_vlan(vlans_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Insert the vlans into the database
        :param vlans_from_file: the parsed vlans from the file
        :param db_cursor: the cursor of the database
        :return:
        """
        for vlan_id in vlans_from_file:
            sql_query = f"INSERT OR REPLACE INTO Vlan (pk_vlan_id, name) " \
                        f"VALUES ('{vlan_id}', '{vlans_from_file[vlan_id]}')"
            db_cursor.execute(sql_query)

``sql_query`` ist das SQL-Statement welches ausgeführt wird. Hierbei wollen wir alle VLANs mittels dem zuvor geparsten ``vlans_from_file`` in die Datenbank schreiben. Ein Entry besteht somit aus der VLAN-ID sowie dem jeweiligem Namen. Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt.

insert_interfaces
`````````````````````````````

.. code-block:: python

    def insert_interfaces(switch_name: str, switch_id: int, l2_interfaces_from_file: dict, int_desc_from_file: dict,
                      port_security_from_file: dict, cdp_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Insert the interfaces into the database
        :param switch_id: the switch_id of the switch
        :param l2_interfaces_from_file: the parsed l2 interfaces from the file
        :param int_desc_from_file: the parsed interface descriptions from the file
        :param port_security_from_file: the parsed port security information from the file
        :param cdp_from_file: the parsed cdp information from the file
        :param db_cursor: the cursor of the database
        :return:
        """
        for interface_id in l2_interfaces_from_file:
            formatted_description = f"'{int_desc_from_file[interface_id][2]}'"
            end_device = get_end_device(switch_name, interface_id)
            formatted_end_device = f"'{end_device}'" if end_device else None
            sql_query = ""
            sql_query += f"INSERT INTO Interface (fk_switch_id, fk_access_vlan_id, fk_voice_vlan_id, end_device_name, " \
                         f"int_name, int_description, has_security, allowed_mac, status, protocol, connected_switch, " \
                         f"connected_sw_interface) " \
                         f"VALUES ('{switch_id}', " \
                         f"{'null' if l2_interfaces_from_file[interface_id][0] is None else l2_interfaces_from_file[interface_id][0]}, " \
                         f"{'null' if l2_interfaces_from_file[interface_id][1] is None else l2_interfaces_from_file[interface_id][1]}, " \
                         f"{'null' if formatted_end_device is None else formatted_end_device}, " \
                         f"'{interface_id}', " \
                         f"{'null' if int_desc_from_file[interface_id][2] is None else formatted_description}, "

            try:
                sql_query += f"'1', '{port_security_from_file[interface_id][1]}', "
            except KeyError:
                sql_query += f"'0', null, "

            sql_query += f"'{int_desc_from_file[interface_id][0]}', '{int_desc_from_file[interface_id][1]}', "

            try:
                sql_query += f"'{cdp_from_file[interface_id][0]}', '{cdp_from_file[interface_id][1]}');"
            except KeyError:
                sql_query += f"null, null);"
            db_cursor.execute(sql_query)

``sql_query`` ist das SQL-Statement welches ausgeführt wird. Hierbei wollen wir ein Interface mittels der ``switch_id`` sowie den zuvor geparsten ``l2_interfaces_from_file``, ``int_desc_from_file``, ``port_security_from_file`` & ``cdp_from_file`` in die Datenbank schreiben.

Aus dem ``l2_interfaces_from_file`` Dictionary wird das Access-VLAN, das Voice-VLAN sowie der Interface Identifier (z.B.: ``Gi0/0``) für die ``fk_access_vlan_id``, ``fk_voice_vlan_id`` & ``int_name`` Attribute ausgelesen.

Folgend wird aus dem ``int_desc_from_file`` Dictionary die Interface Description, der Port Link Status sowie der Port Protocol Status für die Attribute ``int_description``, ``status`` & ``protocol`` ausgelesen. Außerdem wird aus dem ``port_security_from_file`` Dictionary ausgelesen ob das Interface Port-Security eingeschaltet hat und falls, Ja, welche MAC-Adresse erlaubt worden ist.

Die Information wird für die Attribute ``has_security`` & ``allowed_mac`` ausgelesen.

Zuletzt wird aus dem ``cdp_from_file`` Dictionary, der an dem Interface verbundene Switch, sowie das angeschlossene Interface dieses Switches, für die Attribute ``connected_switch`` & ``connected_sw_interface`` ausgelesen.

Ein Entry besteht somit aus der Interface-ID, der Switch-ID, dem Access-VLAN, dem Voice-VLAN, einem verbundenen Edge-Device, dem Interface-Identifier, der Interface Description, dem Port-Security Zustand, der Allowed MAC-Adresse, dem Port Link Status, dem Port Protocol Status, dem an dem Interface verbundenen Switch sowie das Interface eben dieses Switches. Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt.

insert_trunk
`````````````````````````````

.. code-block:: python

    def insert_trunk(hostname: str, l2_interfaces_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Insert the trunk information into the database
        :param hostname: the hostname of the switch
        :param l2_interfaces_from_file: the parsed l2 interfaces from the file
        :param db_cursor: the cursor of the database
        :return:
        """
        trunk_interfaces = {interface_id: l2_interfaces_from_file[interface_id][2]
                            for interface_id in l2_interfaces_from_file
                            if l2_interfaces_from_file[interface_id][2] is not None}

        for trunk_interface_id in trunk_interfaces:
            for allowed_vlan_id in trunk_interfaces[trunk_interface_id]:
                sql_query = f"INSERT INTO Trunking (fk_interface_id, fk_allowed_vlan_id) " \
                            f"VALUES ('{find_interface_id(hostname, trunk_interface_id, db_cursor)}', '{allowed_vlan_id}')"
                db_cursor.execute(sql_query)

``sql_query`` ist das SQL-Statement welches ausgeführt wird. Hierbei wollen wir die Trunking Information der Interfaces als Zwischentabelle, mithilfe des ``hostname`` Parameters und dem zuvor geparsten ``l2_interfaces_from_file`` Dictionary in die Datenbank schreiben. Ein Entry besteht somit aus der Interface-ID sowie der VLAN-ID des VLANs, welches auf dem Interface erlaubt ist. Aus dem ``l2_interfaces_from_file`` Dictionary werden die Trunk Interfaces für die das Attribut ``fk_interface_id`` ausgelesen. Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt.

insert_switch_vlan
`````````````````````````````

.. code-block:: python

    def insert_switch_vlan(switch_id: int, vlans_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Insert the switch_id and the vlan_id into the junction table 'switch_vlan' from the database
        :param switch_id: the switch_id of the switch
        :param vlans_from_file: the parsed vlans from the file
        :param db_cursor: the cursor of the database
        :return:
        """
        for vlan_id in vlans_from_file:
            sql_query = f"INSERT INTO Switch_VLAN (fk_switch_id, fk_vlan_id) " \
                        f"VALUES ('{switch_id}', '{vlan_id}')"
            db_cursor.execute(sql_query)

``sql_query`` ist das SQL-Statement welches ausgeführt wird. Hierbei wollen wir die VLANs welche sich auf einem Switch befinden als Zwischentabelle, mithilfe des ``switch_id`` Parameters und dem zuvor geparsten ``vlans_from_file`` Dictionary in die Datenbank schreiben. Ein Entry besteht somit aus der Switch-ID sowie der VLAN-ID des VLANs, welches auf dem Switch konfiguriert ist. Aus dem ``vlans_from_file`` Dictionary werden die VLANs, welche sich auf einem Switch befinden, für das Attribut ``fk_vlan_id`` ausgelesen. Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt.

