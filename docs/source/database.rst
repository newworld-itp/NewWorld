Database
=====


.. _installation:

Tabellen
--------

- VLAN

  - name - Name des VLANs

  - pk_vlan_id - ID des VLANs

- Trunking

  - fk_interface_id - Interface auf welchem ein VLAN getrunked wird; Pointed auf Interface(pk_interface_id)

  - fk_vlan_id - VLAN welches auf einem Interface getrunked wird; Pointed auf VLAN(pk_vlan_id)

Interface
---------
fk_switch_id - ID von dem Switch dem das Inteface gehört; Pointed auf Switch(pk_switch_id)
fk_access_vlan - ID von dem VLAN welches als Access VLAN auf dem Interface konfiguriert ist; Pointed auf VLAN(pk_vlan_id)
fk_voice_vlan - ID von dem VLAN welches als Voice VLAN auf dem Interface konfiguriert ist; Pointed auf VLAN(pk_vlan_id)
fk_device_id - ID von dem angeschlossenem Gerät; Pointed auf Device(pk_device_id)
int_name - Interface Bezeichnung auf dem Switch
int_description - Description auf dem Interface
has_security - Boolean Feld für das Vorhandensein von Switchport Port-Security
allowed_mac - MAC welche von Switchport Security erlaubt wird
status - Status vom Interface (-1=admin_down, 0=down, 1=up)
protocol - Boolean für Protocol Status (Up/Down)
connected_switch - Der verbundene Switch auf dem Interface
connected_sw_interface - Gegenüberliegendes Interface

Switch_VLAN
-----------
fk_switch_id - ID von dem Switch auf dem das VLAN vorhanden ist; Pointed auf Switch(pk_switch_id)
fk_vlan_id - VLAN welches auf dem Switch vorhanden ist; Pointed auf VLAN(pk_vlan_id)

Switch
------
pk_switch_id - ID von dem Switch
hostname - Hostname auf dem Switch

End_Device
__________
pk_decive_id - ID vom End Gerät
hostname - Name/Bez vom End Gerät


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

