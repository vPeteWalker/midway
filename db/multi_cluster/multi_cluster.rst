.. _multi_cluster:

Please complete :ref:`era_mssql` before proceeding.

-------------------------------
Era Multi-Cluster Configuration
-------------------------------

This section requires availability of an additional cluster. This can be physical, HPOC, or a Nutanix Clusters instance. It is recommended to choose a cluster that matches the initial cluster for simplicity. For example, if you are using an HPOC cluster, use an additional cluster from the HPOC pool.

.. note::

   Ensure you configure all network settings, including creating a *Primary* VLAN, Name Servers, and NTP Servers on the additional cluster before proceeding, as this will be used during this section.

Enabling Era Multi-Cluster
++++++++++++++++++++++++++

Era allows multi-cluster database management. You can manage databases on multiple Nutanix clusters using a single Era server instance. The centrally located Era server enables database administrators to perform all database management activities (database registration, provisioning, cloning, and so on) on different databases spanning across multiple Nutanix clusters (both cloud and on-prem).

As part of enabling Era multi-cluster, the Era server creates an Era agent that runs on a dedicated VM. All the remote Nutanix clusters added to Era have an instance of the Era agent created to manage, schedule, perform, and execute database operations of that cluster.

#. Within Era, select **Administration** from the drop-down menu.

#. Select **Nutanix Clusters** from the left-hand side, and click **Enable Multi-Cluster**.

#. Choose **Era** from the *vLAN* drop-down, and then click **Enable Multi-Cluster**.

   .. figure:: images/1.png

This process should take approximately 10 minutes.

A message indicating that an operation to enable Era Multi-Cluster has started is displayed. You can click on it to monitor the progress of the operation. Alternatively, you may select Operations in the drop-down list of the main menu.

You can see the Era agent details in the Era Service page (left-hand side).

Registering a Nutanix Cluster with Era
++++++++++++++++++++++++++++++++++++++

#. Within Era, select **Administration** from the drop-down menu.

#. Select **Nutanix Clusters** from the left-hand side, and then click **Register Nutanix Cluster**. The *Register Nutanix Cluster* window appears.

#. Enter the **EraCluster02** within the *Cluster Name* field. Fill out the remaining fields, and click **Verify**. Ensure there is a green check mark before clicking **Next**.

   .. figure:: images/2.png

#. Within the *Agent VM* section, enter the following with the listed fields, and click **Next**.

   - **Name Prefix** - EraAgent

   - **VLAN** - Primary

   - **DNS Servers** - AutoAD IP of Primary Cluster (ex. `10.42.69.40`)

   - **IP Address** - Enter an unused static IP address outside of the IPAM pool. (ex. `10.42.61.48`)

#. Within the *Storage Container* section, choose the storage container (ex. `default-container-98754`), and click **Register**.

This process should take approximately 10 minutes.

The new cluster appears in the list of Nutanix clusters and a message appears at the top indicating that the operation to register a cluster has started. You can click on it to monitor the progress of the operation. Alternatively, you may select Operations in the drop-down list of the main menu.

Select the name of the cluster to view the cluster status and database information.
