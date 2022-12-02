Compare
=====

.. _compare:

in db_calls.py

find_switch_id
`````````````````````````````

.. code-block:: python

    def find_switch_id(hostname: str, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Find the switch_id of a switch with the given hostname
        :param hostname: the hostname of the switch
        :param db_cursor: the cursor of the database
        :return: the switch_id of the switch
        """
        sql_query = f"SELECT pk_switch_id FROM Switch WHERE hostname = '{hostname}'"
        db_cursor.execute(sql_query)
        rows = db_cursor.fetchall()
        row = rows[0]
        switch_id = row[0]
        return switch_id
        
        
compare_vlans
`````````````````````````````

.. code-block:: python

    def compare_vlans(switch_id: int, vlans_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Compare the vlan information of a switch with the vlan information in the file
        :param switch_id: the switch_id of the switch
        :param vlans_from_file: the vlan information from the file
        :param db_cursor: the cursor of the database
        :return: the errors
        """
        sql_query = "SELECT fk_vlan_id, name FROM Switch_VLAN JOIN VLAN ON pk_vlan_id = fk_vlan_id " + \
                    f"WHERE fk_switch_id = {switch_id} UNION SELECT fK_voice_vlan_id, name FROM Interface " + \
                    "JOIN Switch_VLAN SV on Interface.fk_switch_id = SV.fk_switch_id " + \
                    "JOIN VLAN V on V.pk_vlan_id = fK_voice_vlan_id " + \
                    f"WHERE SV.fk_switch_id = {switch_id};"

        db_cursor.execute(sql_query)
        rows = db_cursor.fetchall()

        vlans_from_db = dict(rows)

        only_in_db = vlans_from_db.keys() - vlans_from_file.keys()
        only_in_file = vlans_from_file.keys() - vlans_from_db.keys()
        in_both = vlans_from_file.keys() & vlans_from_db.keys()

        errors = [f"VLAN {vlan} ist in der DB aber nicht in der File" for vlan in only_in_db]
        errors += [f"VLAN {vlan} ist in der File aber nicht in der DB" for vlan in only_in_file]

        errors += [
            f"VLAN {vlan} ist in File und DB, aber der Name ist falsch. DB-Wert: {vlans_from_db[vlan]}, " +
            f"File-Wert: {vlans_from_file[vlan]}"
            for vlan in in_both if vlans_from_db[vlan] != vlans_from_file[vlan]]
        return errors
        
compare_interfaces
`````````````````````````````

.. code-block:: python
    
    def compare_interfaces(switch_id: int, interfaces_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
    """
    Compare the interface information of a switch with the interfaces in the file
    :param switch_id: the switch_id of the switch
    :param interfaces_from_file: the interfaces from the file
    :param db_cursor: the cursor of the database
    :return: the errors
    """
    sql_query = "SELECT int_name, pk_interface_id, fk_access_vlan_id, fk_voice_vlan_id FROM Interface " \
                f"WHERE fk_switch_id = {switch_id}"

    db_cursor.execute(sql_query)
    rows = db_cursor.fetchall()

    errors = []

    for row in rows:
        row = list(row)

        interface = row[0]
        interface_id = row[1]
        values = row[2:]

        sql_query = "SELECT fk_allowed_vlan_id FROM Trunking " + \
                    f"WHERE fk_interface_id = {interface_id}"

        db_cursor.execute(sql_query)
        trunked_vlans = [str(entry[0]) for entry in db_cursor.fetchall()]
        trunked_vlans.sort()

        if row[0] in interfaces_from_file:
            err = f"Interface: {interface} ist in der DB und File, aber"
            error_occurred = False

            if values[0] != interfaces_from_file[interface][0]:
                err += f" das Access_Vlan ist falsch. DB-Wert: {values[0]}, " \
                       f"File-Wert: {interfaces_from_file[interface][0]}"
                error_occurred = True
            if values[1] != interfaces_from_file[interface][1]:
                err += f" das Voice_Vlan ist falsch. DB-Wert: {values[1]}, " \
                       f"File-Wert: {interfaces_from_file[interface][1]}"
                error_occurred = True
            if trunked_vlans != interfaces_from_file[interface][2]:
                err += f" die Allowed_Trunk_Vlans sind falsch. DB-Wert: {trunked_vlans}, " \
                       f"File-Wert: {interfaces_from_file[interface][2]}"
                error_occurred = True
            if error_occurred:
                errors.append(err)
            del interfaces_from_file[interface]
        else:
            errors.append(f"Interface: {interface} ist in der Datenbank aber nicht in der File")

    errors += [f"Interface {interface} ist in der File aber nicht in der DB" for interface in
               interfaces_from_file.keys()]
    return errors
    
    
compare_port_security
`````````````````````````````

.. code-block:: python

def compare_port_security(switch_id: int, port_security_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
    """
    Compare the port security information of a switch with the port security in the file
    :param switch_id: the switch_id of the switch
    :param port_security_from_file: the port security from the file
    :param db_cursor: the cursor of the database
    :return: the errors
    """
    sql_query = "SELECT int_name, fk_access_vlan_id, allowed_mac FROM Interface " \
                f"WHERE fk_switch_id = {switch_id} AND has_security = TRUE"

    db_cursor.execute(sql_query)
    rows = db_cursor.fetchall()
    list_from_db = [(row[0], (str(row[1]), row[2])) for row in rows]  # 0=int_name, 1=vlan_id as int, 2=allowed_mac
    list_from_db.sort()

    port_security_from_db = dict(list_from_db)

    only_in_db = port_security_from_db.keys() - port_security_from_file.keys()
    only_in_file = port_security_from_file.keys() - port_security_from_db.keys()
    in_both = port_security_from_file.keys() & port_security_from_db.keys()

    errors = [f"Port_Security am {interface} ist in der DB aktiviert, aber nicht in der File" for interface in
              only_in_db]
    errors += [f"Port_Security am {interface} ist in der File aktiviert, aber nicht in der DB" for interface in
               only_in_file]

    for interface in in_both:
        err = f"Port_Security am {interface} ist in der File und der DB aktiviert, aber"
        error_occurred = False
        if port_security_from_file[interface][0] != port_security_from_db[interface][0]:
            err += f" das VLAN ist falsch. DB-Wert: {port_security_from_db[interface][0]}, " \
                   f"File-Wert: {port_security_from_file[interface][0]}"
            error_occurred = True
        if port_security_from_file[interface][1] != port_security_from_db[interface][1]:
            err += f" die MAC-Adresse ist falsch. DB-Wert: {port_security_from_db[interface][1]}, " \
                   f"File-Wert: {port_security_from_file[interface][1]}"
            error_occurred = True
        if error_occurred:
            errors.append(err)

    return errors
    
    
compare_interface_descriptions
`````````````````````````````

.. code-block:: python
    
    def compare_interface_descriptions(switch_id: int, int_desc_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
    """
    Compare the interface description of a switch with the interface descriptions in the file
    :param switch_id: the switch_id of the switch
    :param int_desc_from_file: the interface descriptions from the file
    :param db_cursor: the cursor of the database
    :return: the errors
    """
    sql_query = "SELECT int_name, status, protocol, int_description FROM Interface " \
                f"WHERE fk_switch_id = {switch_id}"

    db_cursor.execute(sql_query)
    rows = db_cursor.fetchall()
    list_from_db = [(row[0], row[1:]) for row in rows]  # 0=int_name, 1=vlan_id as int, 2=allowed_mac
    list_from_db.sort()

    int_desc_from_db = dict(list_from_db)

    in_both = int_desc_from_file.keys() & int_desc_from_db.keys()

    states = ['down', 'up', 'administratively down']
    protocols = ['down', 'up']

    errors = []
    for interface in in_both:
        if int_desc_from_db[interface][0] != int_desc_from_file[interface][0]:
            errors.append(
                f"Am Interface {interface} ist der Status falsch. "
                f"DB-Wert: {states[int_desc_from_db[interface][0]]}, "
                f"File-Wert: {states[int_desc_from_file[interface][0]]}")
        if int_desc_from_db[interface][1] != int_desc_from_file[interface][1]:
            errors.append(
                f"Am Interface {interface} ist das Protocol falsch. "
                f"DB-Wert: {protocols[int_desc_from_db[interface][1]]}, "
                f"File-Wert: {protocols[int_desc_from_file[interface][1]]}")
        if int_desc_from_db[interface][2] != int_desc_from_file[interface][2]:
            errors.append(
                f"Am Interface {interface} ist die Description falsch. "
                f"DB-Wert: {int_desc_from_db[interface][2]}, "
                f"File-Wert: {int_desc_from_file[interface][2]}")
    return errors


    
compare_cdp
`````````````````````````````

.. code-block:: python

    def compare_cdp(switch_id: int, cdp_from_file: dict, db_cursor: sqlite3.dbapi2.Cursor):
        """
        Compare the cdp information of a switch with the cdp in the file
        :param switch_id: the switch_id of the switch
        :param cdp_from_file: the cdp information from the file
        :param db_cursor: the cursor of the database
        :return: the errors
        """
        sql_query = 'SELECT hostname, int_name, connected_sw_interface ' + \
                    'FROM Interface ' + \
                    'JOIN Switch S on connected_switch = pk_switch_id ' + \
                    f'WHERE fk_switch_id = "{switch_id}";'

        db_cursor.execute(sql_query)
        rows = db_cursor.fetchall()
        errors = []
        for information in rows:
            neighbor = information[0]
            local_interface = information[1]
            remote_interface = information[2]
            err = f"CDP findet den Nachbarn '{neighbor}' in der File und der DB, aber"
            error_occurred = False

            if neighbor in cdp_from_file.keys():
                if local_interface != cdp_from_file[neighbor][0]:
                    err += f", das lokale Interface ist falsch. DB-Wert: {local_interface}, " \
                           f"File-Wert: {cdp_from_file[neighbor][0]}"
                    error_occurred = True
                if remote_interface != cdp_from_file[neighbor][1]:
                    err += f", das remote Interface ist falsch. DB-Wert: {remote_interface}, " \
                           f"File-Wert: {cdp_from_file[neighbor][1]}"
                    error_occurred = True

                if error_occurred:
                    errors.append(err)
                rows.remove(information)
        return errors