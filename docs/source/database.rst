Database
=====


.. _installation:


.. image:: newworld.png


.. code-block:: sql

  CREATE TABLE Switch
  (
      pk_switch_id INTEGER PRIMARY KEY AUTOINCREMENT,
      hostname     varchar(20) UNIQUE
  );

  CREATE TABLE VLAN
  (
      pk_vlan_id INTEGER PRIMARY KEY,
      name       varchar(20)
  );

  CREATE TABLE End_Device
  (
      pk_device_id INTEGER PRIMARY KEY AUTOINCREMENT,
      hostname     varchar(20)
  );

  CREATE TABLE Interface
  (
      pk_interface_id        INTEGER PRIMARY KEY AUTOINCREMENT,
      fk_switch_id           INTEGER,
      fk_access_vlan_id      INTEGER,
      fk_voice_vlan_id       INTEGER,
      fk_device_id           INTEGER,
      int_name               varchar(10),
      int_description        varchar(20),
      has_security           boolean,
      allowed_mac            varchar(17),
      status                 INT, /* -1=admin_down, 0=down, 1=up */
      protocol               boolean,
      connected_switch       int,
      connected_sw_interface varchar(10),
      CONSTRAINT switch_interface FOREIGN KEY (fk_switch_id) REFERENCES Switch (pk_switch_id) ON DELETE CASCADE,
      CONSTRAINT vlan_interface FOREIGN KEY (fk_access_vlan_id) REFERENCES VLAN (pk_vlan_id) ON DELETE SET NULL,
      CONSTRAINT int_device FOREIGN KEY (fk_device_id) REFERENCES End_Device (pk_device_id) ON DELETE SET NULL,
      CONSTRAINT voice_vlan FOREIGN KEY (fk_voice_vlan_id) REFERENCES VLAN (pk_vlan_id) ON DELETE SET NULL,
      CONSTRAINT conntected_switch FOREIGN KEY (connected_switch) REFERENCES Switch (pk_switch_id) ON DELETE SET NULL
  );

  CREATE TABLE Switch_VLAN
  (
      fk_switch_id INTEGER,
      fk_vlan_id   INTEGER,
      CONSTRAINT vlan_switch FOREIGN KEY (fk_switch_id) REFERENCES Switch (pk_switch_id) ON DELETE CASCADE,
      CONSTRAINT switch_vlan FOREIGN KEY (fk_vlan_id) REFERENCES VLAN (pk_vlan_id) ON DELETE CASCADE
  );

  CREATE TABLE Trunking
  (
      fk_interface_id    INTEGER,
      fk_allowed_vlan_id INTEGER,
      CONSTRAINT trunk_interface FOREIGN KEY (fk_interface_id) REFERENCES Interface (pk_interface_id) ON DELETE CASCADE,
      CONSTRAINT trunk_vlan FOREIGN KEY (fk_allowed_vlan_id) REFERENCES VLAN (pk_vlan_id) ON DELETE CASCADE
  );

Tabellen
--------

VLAN
^^^^

- ``name`` - Name des VLANs

- ``pk_vlan_id`` - ID des VLANs

Trunking
^^^^^^^^

- ``fk_interface_id`` - Interface auf welchem ein VLAN getrunked wird; Pointed auf Interface(pk_interface_id)

- ``fk_allowed_vlan_id`` - VLAN welches auf einem Interface getrunked wird; Pointed auf VLAN(pk_vlan_id)

Interface
^^^^^^^^^

- ``fk_switch_id`` - ID von dem Switch dem das Inteface gehört; Pointed auf Switch(pk_switch_id)

- ``fk_access_vlan`` - ID von dem VLAN welches als Access VLAN auf dem Interface konfiguriert ist; Pointed auf VLAN(pk_vlan_id)

- ``fk_voice_vlan`` - ID von dem VLAN welches als Voice VLAN auf dem Interface konfiguriert ist; Pointed auf VLAN(pk_vlan_id)

- ``fk_device_id`` - ID von dem angeschlossenem Gerät; Pointed auf Device(pk_device_id)

- ``int_name`` - Interface Bezeichnung auf dem Switch

- ``int_description`` - Description auf dem Interface

- ``has_security`` - Boolean Feld für das Vorhandensein von Switchport Port-Security

- ``allowed_mac`` - MAC welche von Switchport Security erlaubt wird

- ``status`` - Status vom Interface (``-1``=admin_down, ``0``=down, ``1``=up)

- ``protocol`` - Boolean für Protocol Status (Up/Down)

- ``connected_switch`` - Der verbundene Switch auf dem Interface

- ``connected_sw_interface`` - Gegenüberliegendes Interface

Switch_VLAN
^^^^^^^^^^^

- ``fk_switch_id`` - ID von dem Switch auf dem das VLAN vorhanden ist; Pointed auf Switch(pk_switch_id)

- ``fk_vlan_id`` - VLAN welches auf dem Switch vorhanden ist; Pointed auf VLAN(pk_vlan_id)

Switch
^^^^^^

- ``pk_switch_id`` - ID von dem Switch

- ``hostname`` - Hostname auf dem Switch

End_Device
^^^^^^^^^^

- ``pk_decive_id`` - ID vom End Gerät

- ``hostname`` - Name/Bez vom End Gerät

Constraints
-----------

VLAN(pk_vlan_id)
^^^^^^^^^^^^^^^^

- ``AUTOINCREMENT``

- ``ON DELETE CASCADE`` → Trunking(fk_allowed_vlan_id)

- ``ON DELETE CASCADE`` → Switch_VLAN(fk_vlan_id)

- ``ON DELETE SET NULL`` → Interface(fk_access_vlan)

- ``ON DELETE SET NULL`` → Interface(fk_voice_vlan_id)

Switch(pk_switch_id)
^^^^^^^^^^^^^^^^^^^^

- ``AUTOINCREMENT``

- ``ON DELETE CASCADE`` → Switch_Vlan(fk_switch_id)

- ``ON DELETE CASCADE`` → Interface(fk_switch_id)

Deivce(pk_device_id)
^^^^^^^^^^^^^^^^^^^^

- ``AUTOINCREMENT``

- ``ON DELETE SET NULL`` → Interface(fk_device_id)

Interface(pk_interface_id)
^^^^^^^^^^^^^^^^^^^^^^^^^^
- ``AUTOINCREMENT``

- ``ON DELETE CASCADE`` → Trunking(fk_interface_id)



Installation
------------

To use Lumache, first install it using pip:

.. code-block:: console

   (.venv) $ pip install lumache

Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

