.. _mssql_restore:

-----------------------
Restoring Microsoft SQL
-----------------------

#. Within Era, select **Databases** from the drop-down menu.

#. On the left-hand side, select **Sources**, and then click on the database you wish to restore (i.e. `FiestaDB_Prod`).

#. Click **Restore** in the database summary page. The *Restore Source* window appears.

#. Uncheck **Tail Logs**, and click **Next**.

   .. figure:: images/1.png

#. Select **Point in Time**, then enter the time to which you want to restore your database. Alternatively, you can click anywhere on the green line.

   .. figure:: images/2.png

#. Click **Restore**.

#. Within the *Confirm Database Restore* window, type in the database name (ex. `FiestaDB_Prod`), and click **Restore** to proceed with the restore process.

   .. figure:: images/3.png
