Compare
=====

.. _compare:

in db_calls.py

find_switch_id()
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

``sql_query`` ist das SQL-Statement welches ausgeführt wird.
Hierbei wollen wir die ID von dem Switch mit einem bestimmten Hostname.

Das Statement wird in ``db_cursor.execute(sql_query)`` ausgeführt und mit
``db_curser.fetchall()`` wird die Ergebnis-Tabelle returned.

Zuletzt wird dann die Switch-ID, welche sich in ``rows`` befindet
mit einem ``return`` weitergegeben.

        
compare_vlans()
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
                    f"WHERE fk_switch_id = {switch_id};"

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

Diese Methode liefert die Unterschiede zwischen dem Status von einem Gerät in der DB
und dem des neuen Files. ``sql_query`` ist hierbei das SQL-Statement welches ausgeführt wird.
Hier wollen wir die VLANs auf einem Switch zusammen mit dem VLAN-Name herausfinden.
Das Ergebnis wird in ``rows`` gespeichert. 

Diese VLANs werden dann mit denen aus der File ``vlans_from_file`` verglichen.

Die Unterschiede werden dann ``errors`` gespeichert und zurückgeben.
        
compare_interfaces()
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

Diese Methode liefert die Unterschiede zwischen dem Status von einem Gerät in der DB
und dem des neuen Files. ``sql_query`` ist hierbei das SQL-Statement welches ausgeführt wird.
Hier wollen wir die VLANs die auf einem Interface konfiguriert wurden ermitteln.
Das Ergebnis wird in ``rows`` gespeichert. 

Diese Interfaces(mit VLANs) werden dann mit denen aus der File ``interfaces_from_file`` verglichen.

Die Unterschiede werden dann ``errors`` gespeichert und zurückgeben.
    
compare_port_security()
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
    
Diese Methode liefert die Unterschiede zwischen dem Status von einem Gerät in der DB
und dem des neuen Files. ``sql_query`` ist hierbei das SQL-Statement welches ausgeführt wird.
Hier wollen wir die Mac-Addresse die an einem Interface erlaubt ist herausfinden.
Das Ergebnis wird in ``rows`` gespeichert. 

Diese Mac-Addressen werden dann mit denen aus der File ``compare_port_security`` verglichen.

Die Unterschiede werden dann ``errors`` gespeichert und zurückgeben.
    
compare_interface_descriptions()
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

Diese Methode liefert die Unterschiede zwischen dem Status von einem Gerät in der DB
und dem des neuen Files. ``sql_query`` ist hierbei das SQL-Statement welches ausgeführt wird.
Hier wollen wir die Interface Stati und die Interface Descriptions von einem Switch herausfinden.
Das Ergebnis wird in ``rows`` gespeichert. 

Diese Interface Stati und die Interface Descriptions werden dann mit denen aus der File ``int_desc_from_file`` verglichen.

Die Unterschiede werden dann ``errors`` gespeichert und zurückgeben.
    
    
compare_cdp()
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
        sql_query = 'SELECT int_name, connected_switch, connected_sw_interface FROM Interface ' + \
                    f"WHERE fk_switch_id = {switch_id};"

        db_cursor.execute(sql_query)
        rows = db_cursor.fetchall()

        errors = []

        for information in rows:
            neighbor = information[1]
            local_interface = information[0]
            remote_interface = information[2]

            if local_interface not in cdp_from_file.keys() and neighbor is not None:
                errors.append(
                    f"Der Neighbor {neighbor} ist am Interface {local_interface} in der DB aber nicht in der File")
            elif neighbor is not None:
                if neighbor != cdp_from_file[local_interface][0]:
                    errors.append(f"Der Neighbor am lokalen Interface {local_interface} ist falsch. DB-Wert: {neighbor}, "
                                  f"File-Wert: {cdp_from_file[local_interface][0]}")
                elif remote_interface != cdp_from_file[local_interface][1]:
                    errors.append(f"Das remote Interface des Neighbors {neighbor} am lokalen Interface {local_interface} "
                                  f"ist falsch. DB-Wert: {remote_interface}, "
                                  f"File-Wert: {cdp_from_file[local_interface][1]}")

                del cdp_from_file[local_interface]

        return errors + [f"Der Neighbor {values[0]} ist am Interface {local_interface} in der File aber nicht in der DB" for local_interface, values in cdp_from_file.items()]



Diese Methode liefert die Unterschiede zwischen dem Status von einem Gerät in der DB
und dem des neuen Files. ``sql_query`` ist hierbei das SQL-Statement welches ausgeführt wird.
Hier wollen wir die Informationen des gegenüberliegenden Switch herausfinden.
Das Ergebnis wird in ``rows`` gespeichert. 

Diese Informationen werden dann mit denen aus der File ``cdp_from_file`` verglichen.

Die Unterschiede werden dann ``errors`` gespeichert und zurückgeben.
