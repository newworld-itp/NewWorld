Usage
=====

.. _usage:

-p, --path
`````````````````````````````

NewWorld arbeitet mit mehreren Textdateien. Damit man nicht jede von diesen Dateien einzeln angeben muss, haben wir uns da

.. code-block:: console

   gnsave@server $ git clone https://gitlab.com/HTL-Rennweg/itp2022/new-world.git
   

.. _update_usage:

-u, --update
`````````````````````````````

Danach muss man Python und die Python Libraries(z.B. Django) installieren

.. code-block:: console

   gnsave@server $ sudo apt-get install python3
   gnsave@server $ cd NewWorld
   gnsave@server $ pip3 install -r requirements.txt
   
   
.. _compare_usage:

-c, --compare
`````````````````````````````

Zu guter Letzt muss man newworld.py starten

.. code-block:: console

   gnsave@server $ python3 newworld.py -p <folder_path> <-c/-u>


