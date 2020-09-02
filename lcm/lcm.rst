.. _lcm:

-----------------------
Lifecycle Manager (LCM)
-----------------------

**Prerequisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** Varies depending on number of updates performed

**Covered Test IDs:** N/A

In this module we will demonstrate how to perform an inventory, followed by performing updates with LCM.

What is LCM?
++++++++++++

- LCM aims to enable administrators to take inventory of the firmware and software in their Nutanix environment and perform updates in a simple, cluster-aware, fashion.

- LCM is an industry-unique solution, providing the same 1-click operational upgrade simplicity for 7 (and growing) different hardware vendor appliances running Nutanix software.

- LCM just offers a management layer that will allow users to deploy firmware upgrades simply without having to worry about upgrade paths or conflicts between versions. Customers should not require a manual to upgrade their environment. LCM aims to solve this at scale.

- Once a customer performs an inventory of their cluster, all the firmware is downloaded and staged. From there, users can deploy the updates with as much or as little granularity as they desire. You can either select individual entities for upgrade or do the entire cluster at once (subject to what the actual component being upgraded allows).

- A Dark Site LCM version is available.

.. note::

   For detailed information about LCM, please refer to the `Life Cycle Manager Guide v2.3 <https://portal.nutanix.com/page/documents/details?targetId=Life-Cycle-Manager-Guide-v2_3:Life-Cycle-Manager-Guide-v2_3>`_

Performing Inventory with LCM
+++++++++++++++++++++++++++++

.. note::

   Some entities are local to Prism Central, such as Calm, Epsilon, and Karbon. To manage these entities, you must log on to **Prism Central**.

   Other entities, such as AOS, AHV, and component firmware, are local to Prism Element. To manage these entities, you must log on to **Prism Element**.

#. From the drop-down menu, select **LCM**.

#. In the LCM sidebar, select **Inventory**.

#. To take an inventory, click **Perform Inventory**.

   If you do not have auto-update enabled, and a new version of the LCM framework is available, LCM shows the following warning message:

   .. figure:: images/3.png
      :align: center

#. To enable auto-inventory, click **Settings** and select the **Enable LCM Auto Inventory** checkbox in the dialog box that appears.

#. Click **OK**.


   .. figure:: images/4.png
      :align: center

      Use the Focus button to switch between a general display and a component-by-component display.

.. note::

   A bit of context on what LCM is actually doing during the inventory process, as it could take up to 20 minutes to perform on a 4-node cluster, and up to 40 minutes on a 32-node cluster.

   - Use our master manifest to determine which modules are applicable on each host based on the hardware family.

   - Download these modules (note only the control logic, no images needed) onto the catalog service.

   - Execute these modules on their respective environment, host or CVM.

   - We pull these modules from the catalog service into the respective environment, run their detect logic, and determine current versions.

   - These processes are distributed to the LCM service running on all nodes. Each node picks up the work items, and runs the 2 scripts (one for CVM and another for the host).

Performing Updates with LCM
+++++++++++++++++++++++++++

#. From the drop-down menu, select **LCM**.

#. In the LCM sidebar, under *Updates*, select **Software** or **Firmware**.

#. Select the updates you want to perform.

   - Select the checkbox for the node you want to update, or select All to update the entire cluster.

   - Select the components you want to update. When you select a node, LCM selects the checkboxes for all updateable components by default. Clear the checkbox of any component you do not want to update.

#. Click **NCC Check**.  In the dialog box that appears, specify which prechecks you want LCM to run before updating.

#. Click **Run**.

#. When the prechecks are complete, click **Update**.

#. Review the selected updates and click **Apply Updates**. LCM updates the selected components.

   .. figure:: images/5.png

   .. figure:: images/6.png

   .. figure:: images/7.png

   .. figure:: images/8.png

#. You may wish to run the **Performing Inventory with LCM** steps upon completion to identify any available updates to the cluster.
