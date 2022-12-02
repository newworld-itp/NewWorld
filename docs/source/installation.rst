Installation
=====

.. _installation:


Um NewWorld zu installieren muss man zuerst einmal das GitLab Repository clonen

.. code-block:: console

   gnsave@server $ git clone https://gitlab.com/HTL-Rennweg/itp2022/new-world.git
   
Danach muss man Python und die Python Libraries(z.B. Django) installieren

.. code-block:: console

   gnsave@server $ sudo apt-get install python3
   gnsave@server $ cd NewWorld
   gnsave@server $ pip3 install -r requirements.txt

Zu guter Letzt muss man newworld.py starten

.. code-block:: console

   gnsave@server $ python3 newworld.py -p <folder_path> <-c/-u>


