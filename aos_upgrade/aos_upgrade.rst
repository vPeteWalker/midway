.. _aos_upgrade:

-----------
AOS Upgrade
-----------

In this module you will upgrade AOS within Prism, and demonstrate no interruption to user VMs during this process.

**Prerequisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** 45-60 minutes

**Covered Test IDs:** `Core-011 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

`Nutanix POC Test Plan Tracker <https://docs.google.com/spreadsheets/d/15r8Q1kCIJY4ErwL1CaHHwv4Q7gmCbLOz5IaR51t9se0/edit#gid=398743295>`_

Each node in a cluster runs AOS. When commencing an upgrade, every node will be upgraded to that version. Nutanix provides a live upgrade mechanism that allows the cluster to run continuously while a rolling upgrade of the nodes is initiated in the background. Controller VMs in the cluster reboot one-at-a-time onto the new AOS version. Storage traffic from user VMs will be redirected to a neighboring CVM while the local one is upgrading. Examples of what's included with AOS upgrades are improvements and enhancements to our scalability, performance, resiliency, business continuity, security, and user interface (Prism).

.. note::

   - Before performing the procedure below, make sure you are running the latest version of the Nutanix Cluster Check (NCC) health checks and upgrade NCC if necessary.  Run all NCC checks, and check the Health Dashboard. If any health checks are failing, resolve them to ensure that the cluster is healthy before continuing.

   - If you use Prism Central to manage your Nutanix clusters, upgrade Prism Central first, then upgrade AOS on the clusters managed by Prism Central.

   - Depending on the version of AOS and LCM, upgrading AOS may also be performed in LCM, or exclusively availabile in LCM. Utilizing LCM is the recommended method to upgrade software and firmware on Nutanix, and will eventually become to sole mechanism for all software and firmware upgrades. Please see the :ref:`lcm` section to learn more about performing upgrades using LCM. Otherwise, proceed with the following steps labeled *Legacy AOS Upgrade*.

   - Visit the `Upgrade Paths <https://portal.nutanix.com/page/documents/upgrade-paths>`_ page, and choose **AOS** from the *Software Types* section. Input the *Current Release Version* and *Target Release Version* to confirm the upgrade you are attempting is supported.

   - If you encounter errors with the upgrade procedure as outlined below (ex. install hang) contact support to assist. A failure on the initial node will prevent the upgrade process proceeding on subsequent nodes to ensure multiple nodes aren’t taken offline from the same issue.

Legacy AOS upgrade
++++++++++++++++++

#. Log on to the web console for any node in the cluster.

#. Click the :fa:`gear` **> Settings > Upgrade Software > AOS**, or from the dropdown menu choose **Settings > Upgrade Software > AOS** to display the current status of your software versions (and start an upgrade if available and desired).

   .. figure:: images/1.png
      :align: right

   - *CURRENT VERSION* displays the version running currently in the cluster.

   - *AVAILABLE COMPATIBLE VERSIONS* displays any versions to which the cluster can be upgraded.

   - The upload the AOS base software binary link enables you to install from binary and metadata files, which might be helpful for updating isolated (dark-site) clusters not connected to the Internet.

#. *Optional* To run the pre-upgrade installation checks only on the Controller VM where you are logged on without upgrading, click **Upgrade > Pre-upgrade**. These checks will also run as part of the upgrade procedure, and is not required to run separately.

#. Before executing upgrade, we recommend performing one of the following to illustrate that workloads will continue to run without interruption while the AOS upgrade proceeds.

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`.

#. Do one of the following:

   - If you previously selected Enable Automatic Download and the desired software package has been downloaded, click **Upgrade > Upgrade Now**, then click **Yes** to confirm.

   - If Enable Automatic Download is cleared, click **Download** next to the desired software package. When the download task is completed, click **Upgrade > Upgrade Now**, then click **Yes** to confirm.

      .. figure:: images/2.png
         :align: left

      .. figure:: images/3.png
         :align: center

      .. figure:: images/4.png
         :align: right

   - Upgrading AOS by uploading binary and metadata files

      - Log on to the Nutanix Support Portal, and select the AOS release from the `Downloads <https://portal.nutanix.com/#/page/releases/nosDetails/>`_ page.

      - Download the AOS binary and metadata .JSON files on your local media. You can also copy these files to a USB stick, CD, or other media.

      - Click the *upload an AOS binary* link. Click **Choose File** for the AOS metadata and binary files, respectively, browse to the file locations, and click **Upload Now**.  Once the software package has been uploaded, click **Upgrade > Upgrade Now**, then click **Yes** to confirm.

      .. figure:: images/6.png
         :align: left

      .. figure:: images/7.png
         :align: center

      .. figure:: images/5.png
         :align: right

   The Upgrade Software dialog box shows the progress of your selection, including pre-installation and cluster health checks. After the upgrade process is completed on a Controller VM, the Controller VM restarts. This restart is not disruptive to node operations.

#. Return to :fa:`gear` **> Settings > Upgrade Software > AOS** once the upgrade has completed to verify the version of AOS has been upgraded as expected.

#. Demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.

Congratulations! You’ve upgraded AOS.  To upgrade additional components, including AHV, Calm, and others, see :ref:`lcm`
