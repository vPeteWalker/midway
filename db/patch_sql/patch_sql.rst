.. _patch_sql:

Please complete :ref:`db/mssqldeploy` before proceeding.

----------------------
Patching Microsoft SQL
----------------------

Links

   - `Microsoft SQL Server 2016 - Cumulative Update <https://www.microsoft.com/en-us/download/details.aspx?id=56975>`_
   -

Patching a SQL Server Database Server VM
++++++++++++++++++++++++++++++++++++++++

Perform the following procedure to apply updates from the available software profile versions to a provisioned/registered database server VM.

#. Within Era, select **Database Server VMs** from the drop-down list.

#. Click on **List** from the left-hand side, then click the database server VM you wish to update the software profile version. The *Database Server VM Summary* page appears.

#. Go to the *Software Profile Version* widget, and click **Update**. The Update Database Server VM window appears.

The *Software Profile Version* widget displays the current version, recommended version, and the status of the software profile version.

.. Note::

   The Update option only appears when a new software profile version is available.

#. For registered database server VMs which have not been patched in Era, do the following in the indicated fields:
   - Software Profile. Select a software profile from the drop-down list.
   - Version. Select a software profile version from the drop-down list.

#. Under *Start Update*, select one of the following:
   - **Now.** Select this option if you want to start updating the software version now.
   - **Later.** Select this option and then select the day and time if you want to create a schedule for patching the software profile version.

#. Click Pre-Post Commands and do the following in the indicated fields:
   - **Pre-Create Command.** Type a complete OS command that you want to run before the single-instance database is created.
   - **Post-Create Command.** Type a complete OS command that you want to run after the single-instance database is created.
   - **Figure.** SQL Server Patching for Newly Registered Database Server VM

#. For registered database server VMs and provisioned database server VMs which have been patched at least once in Era, do the following in the indicated fields:
   - **Update to Software Profile Version.** Select a software profile version to update from the drop-down list.

#. Under *Start Update*, select one of the following:
   - **Now.** Select this option if you want to start updating the software version now.
   - **Later.** Select this option and then select the day and time if you want to create a schedule for patching the software profile version.

#. Click **Pre-Post Commands** and do the following in the indicated fields:
   - **Pre-Create Command.** Type a complete OS command that you want to run before the single-instance database is created.
   - **Post-Create Command.** Type a complete OS command that you want to run after the single-instance database is created.
   - Provide the database server VM name as confirmation, and click **Update**.

A message appears at the top indicating that the operation to update a database has started. Click the message to monitor the progress of the operation. Alternatively, select **Operations** in the drop-down list of the main menu to monitor the progress of the operation.

Patching a SQL Server Database Server Cluster
+++++++++++++++++++++++++++++++++++++++++++++

Perform the following procedure to apply updates from the available software profile versions to a provisioned/registered database server cluster (Windows cluster). Patches are applied in a rolling upgrade.

#. In the drop-down list of the main menu, select **Database Server VMs**.

#. Go to **List** from the left-hand side, and click the database server cluster for which you want to update the software profile version. The *Server Cluster Summary* page appears.

#. Go to the *Software Profile Version* widget and click **Update**. The Update Windows Cluster window appears.

#. The *Software Profile Version* widget displays the current version, recommended version, and the status of the software profile version.

.. Note::

   The Update option only appears when a new software profile version is available.

#. For registered database server clusters which have not been patched in Era, do the following in the indicated fields:
   - **Software Profile.** Select a software profile from the drop-down list.
   - **Version.** Select a software profile version from the drop-down list.

#. Under *Start Update*, select one of the following:
   - **Now.** Select this option if you want to start updating the software version now.
   - **Later.** Select this option and then select the day and time if you want to create a schedule for patching the software profile version.

#. Click **Pre-Post Commands** and do the following in the indicated fields:
   - **Pre-Create Command.** Type a complete OS command that you want to run before the single-instance database is created.
   - **Post-Create Command.** Type a complete OS command that you want to run after the single-instance database is created.

#. For registered database server clusters and provisioned database server clusters which have been patched at least once in Era, do the following in the indicated fields:
   - **Update to Software Profile Version.** Select a software profile version to update from the drop-down list.
   - Under *Start Update*, select one of the following:

Now. Select this option if you want to start updating the software version now.
Later. Select this option and then select the day and time if you want to create a schedule for patching the software profile version.
Click Pre-Post Commands and do the following in the indicated fields:
Pre-Create Command. Type a complete OS command that you want to run before the single-instance database is created.
Post-Create Command. Type a complete OS command that you want to run after the single-instance database is created.
Figure. SQL Server AG Patching for Existing Database Server VM
Click to enlarge



Provide the Windows cluster name as confirmation and click Update.
A message appears at the top indicating that the operation to update a database has started. Click the message to monitor the progress of the operation. Alternatively, select Operations in the drop-down list of the main menu to monitor the progress of the operation.
