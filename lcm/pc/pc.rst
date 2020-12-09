Performing Inventory with LCM
+++++++++++++++++++++++++++++

.. note::

   AOS, AHV, and component firmware operations are contained within Prism Element. To manage these entities, you must log on to **Prism Element**.

#. Select :fa:`bars` **> Administration > LCM**.

#. In the LCM sidebar, select **Inventory**.

#. To take an inventory, click **Perform Inventory**.

   If you do not have auto-update enabled, and a new version of the LCM framework is available, LCM shows the following warning message:

   .. figure:: images/3.png
      :align: center

#. To enable auto-inventory, click **Settings** and select the **Enable LCM Auto Inventory** checkbox in the dialog box that appears. You may optionally specify a time to run the LCM inventory. By default, it will run at 3:00am local cluster time.

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

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, and begin a continuous ping between these VMs prior to performing upgrades via LCM. Observe that there is no disruption to workloads occurs during this time. Creating VMs is outlined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is outlined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`. Observe that there is no disruption to workloads occurs during this time.

#. Select :fa:`bars` **> Administration > LCM**.

#. In the LCM sidebar, under *Updates*, select **Software**.

#. Select the updates you want to perform.

   - Select the checkbox for the node you want to update, or select All to update the entire cluster. For an AOS upgrade, all nodes must be upgraded.

   - If there are multiple versions of software available for that component (AOS/Foundation/BIOS etc.), you can click the hyperlink of the software version to select a specific version of software that you wish to install.

   - Select the components you want to update. When you select a node, LCM selects the checkboxes for all updateable components by default. Clear the checkbox of any component you do not want to update. This does not apply to AOS updates, as previously mentioned.

#. Click **Update**.

#. Review the selected updates and click **Apply Updates**. LCM will now proceed to update the selected components.

   .. figure:: images/5.png

   .. figure:: images/6.png

   .. figure:: images/7.png

   .. figure:: images/8.png

#. You may wish to run the **Performing Inventory with LCM** steps upon completion to identify any available updates to the cluster.
