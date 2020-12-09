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

- LCM is an industry-unique solution, providing the same 1-click operational upgrade simplicity for hardware vendor appliances running Nutanix software.

- LCM just offers a management layer that will allow users to deploy firmware upgrades simply without having to worry about upgrade paths, conflicts between versions, or dependencies that traditionally are documented in multiple support articles or confusing product matrices. Customers should not require a lengthy manual to upgrade their environment. LCM aims to solve this at scale.

- Once a customer performs an inventory of their cluster, all the firmware is downloaded and staged. From there, users can deploy the updates with as much or as little granularity as they desire. You can either select individual entities for upgrade or do the entire cluster at once (subject to what the actual component being upgraded allows).

- A Dark Site LCM version is available.

.. note::

   For detailed information about LCM, please refer to the `Life Cycle Manager Guide v2.3 <https://portal.nutanix.com/page/documents/details?targetId=Life-Cycle-Manager-Guide-v2_3:Life-Cycle-Manager-Guide-v2_3>`_

Below you will see two sections, one for LCM within Prism Central (PC), and one for LCM within Prism Element (PE). It is not required to complete both - only the operations you intend to demonstrate within your particular POC.

While the long-term goal is to migrate the operations that are currently in PE to PC, there are several that exist in PE today, and therefore require use of both PE and PC for full functionality.

Below is a flowchart that illustrates the LCM process.

   .. figure:: images/1.png

   .. figure:: images/2.png

.. note::

   - Before performing any LCM operation, ensure you are running the latest version of the Nutanix Cluster Check (NCC) health check. Run all NCC checks, and check the Health Dashboard. If any health checks are failing, resolve them to ensure that the cluster is healthy before continuing.

   - If you use Prism Central to manage your Nutanix clusters, upgrade Prism Central first, then upgrade AOS on the clusters managed by Prism Central.

   - Depending on the version of AOS and LCM, upgrading AOS may also be performed Settings > Upgrade Software. Utilizing LCM is the recommended method to upgrade software and firmware on Nutanix, and will eventually become to sole mechanism for all software and firmware upgrades.

   - Visit the `Upgrade Paths <https://portal.nutanix.com/page/documents/upgrade-paths>`_ page, and choose **AOS** from the *Software Types* section. Input the *Current Release Version* and *Target Release Version* to confirm the upgrade you are attempting is supported.

   - If you encounter errors with the upgrade procedure as outlined below (ex. install hang) contact support to assist. A failure on the initial node will prevent the upgrade process proceeding on subsequent nodes to ensure multiple nodes aren’t taken offline from the same issue.
