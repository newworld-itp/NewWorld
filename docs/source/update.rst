Update
=====

.. _update:


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


insert_switch
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


insert_switch
`````````````````````````````

.. code-block:: python

    def insert_interfaces(switch_id: int, l2_interfaces_from_file: dict, int_desc_from_file: dict,
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
            sql_query = ""
            sql_query += f"INSERT INTO Interface (fk_switch_id, fk_access_vlan_id, fk_voice_vlan_id, fk_device_id, " \
                         f"int_name, int_description, has_security, allowed_mac, status, protocol, connected_switch, " \
                         f"connected_sw_interface) " \
                         f"VALUES ('{switch_id}', " \
                         f"{'null' if l2_interfaces_from_file[interface_id][0] is None else l2_interfaces_from_file[interface_id][0]}, " \
                         f"{'null' if l2_interfaces_from_file[interface_id][1] is None else l2_interfaces_from_file[interface_id][1]}, " \
                         f"null, " \
                         f"'{interface_id}', " \
                         f"{'null' if int_desc_from_file[interface_id][2] is None else int_desc_from_file[interface_id][2]}, "

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