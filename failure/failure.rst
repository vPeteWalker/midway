.. _failure:

-------
Failure
-------

Prerequisites and Requirements
++++++++++++++++++++++++++++++

It is recommended you complete the :ref:`clusterconfig` section before proceeding.

Before you begin
----------------

Please be aware that any information such as server names, IP addresses, and similar information contained within any screen shots are strictly for demonstration purposes. Do not use these values when proceeding with any of the steps contained within this workshop.

This workshop was created with the following versions of Nutanix products. There may be differences - in both written steps and screen shots - between what is shown throughout this workshop, and what you experience if you are using later versions of the individual software packages listed below.

   - AOS             - 5.17.0.4
   - PC              - 5.17.0.3

Finally, while you are welcome to vary your inputs compared to the instructions listed below, please be aware that by diverting from these instructions, you may negatively impact your ability to successfully complete this workshop.

CVM Failure
===========

In this section, we will be simulating a Controller Virtual Machine (CVM) failure by executing a command that will shut off the CVM unexpectedly (versus a gradual shutdown for maintenance). This test demonstrates the ability of Nutanix's AOS to immediately failover storage I/O operations to another CVM in the cluster without interruption to any VMs running on that host. This is all done while running a continuous ping and/or workload simulation via X-Ray or similar to illustrate a real world failure scenario.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

#. SSH into any host (not CVM) within the cluster using the *root* username and password, and run the following commands to shut down the CVM running on that host.

   - **virsh list** - This command will *list all VMs* running on the host.
   - **virsh shutdown `*CVM name*`** - This command will *shut down* the CVM.
   - **virsh start `*CVM name*`** - This command will *start* the CVM.

   .. figure:: images/1.png

      Sample output of all commands

#. Login to Prism, and observe that the VMs on the same host are still running without interruption. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.

   .. figure:: images/2.png

HDD Failure
===========

.. raw:: html

  <strong><font color="red">Proceed with caution. Recommend you only perform these steps if the POC absolutely requires this. These instructions will guide you with how to properly identify a disk with certainty, but be aware that you are using commands that if not entered correctly, could negatively impact your POC, and require involving support to rectify the issue.</font></strong>

In this section, we will be simulating a Hard Disk Drive (HDD) failure by executing a command that will instantly simulate degredation event for a hard disk that would normally happens over time under real world conditions. This is preferable to performing a "drive pull" test, as that is a very unlikely scenario. What is more likely, is a HDD to develop bad sectors or similar issues gradually over time, and as protecting customer data is vital, any infrastructure must handle this gracefully, and without interruption or loss.

This test demonstrates the ability of Nutanix's AOS to immediately begin rebuilding additional copies of data on the surviving drives, as a high priority event, utilizing all nodes in the cluster to aid in that rebuild. This results in a restoration to full redundancy in a very short amount of time. Internal tests show that using several generation old equipment, Nutanix can rebuild 5TB worth of data within 45 minutes. This differs between other platforms in two major ways. One, we don't have a default wait period before rebuilding data, and two, Nutanix doesn't operate in a paired fashion, so we can take advantage of the distributed nature and combined performance of an entire Nutanix cluster to rebuild missing data quickly. In a nutshell, the rebuild is both very fast and the workload per node is minimized to avoid bottlenecks and to reduce the impact to running workload. Data rebuild time decreases with each additional node added to the cluster, as a result of our MapReduce Framework which leverages the full power of the cluster to perform these types of activities concurrently.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

#. Identify the host you are planning to simulate the HDD drive failure on.

#. Within Prism, choose **Hardware** from the dropdown. Click on **Diagram**, and then on the selected host for testing.

#. Scroll down. On the left hand side, you will see a summary screen for the host itself, similar to the below. Use this to identify how many and what type (SSD or HDD) of drives the host contains.

   .. figure:: images/hdd1.png

#. Click on **Table** and note the IP address for the CVM that corresponds to the host you are testing on.

   .. figure:: images/hdd2.png

#. SSH to the CVM.

#. Run the command ``disk_operator list_usable_disks``. This will output similar to the below:

   .. figure:: images/hdd3.png

#. Run the command ``sudo parted /dev/sdX p`` replacing X with the letter that corresponds to the disks contained within your host. This command will only display information about the drive, and no changes are made at this stage. We are using this command to confirm which drives are SSDs and which are HDDs. In our example, we ran ``sudo parted /dev/sda p`` which identified an SSD - the only one in this host, since this is a single SSD configuration. Recommend you repeat this command, substituting the letter corresponding to each drive in your host.

   .. figure:: images/hdd4.png
      :align: left
      :scale: 50%

      This is an example of an SSD

   .. figure:: images/hdd5.png
      :align: right
      :scale: 50%

      This is an example of a HDD

#. Now that we have identified our HDD that we wish to use in our failure test, we can run ``disk_operator mark_disks_unusable /dev/sdX`` where X is the identified disk. In our example, we ran ``disk_operator mark_disks_unusable /dev/sdb``, and the output is below. **You will run this command four times.**

   .. figure:: images/hdd6.png

#. Return to Prism and observe that the disk is in the process of being removed, and shows a failure state. Make note of the disk serial number at this time. Do not proceed until the disk removal task has completed.

   .. figure:: images/hdd7.png

#. Enable hidden commands in ncli by running ``ncli -h=true``.

#. Run the command ``disk list-tombstone-entries`` to show the tombstone list.

#. Observe that the disk you marked unusable is now in the tombstone list.

   .. figure:: images/hdd8.png

      Sample output of all commands

#. Run the command ``edit-hades``. This will open the *vi* text editor, enabling you to remove the necessary entries to bring the disk back online. Recommend to take a screen shot to document the existing settings.

#. Hit **Insert** to begin editing. Remove anything entitled **is_bad** or **disk_diagnostics**, including anything within those sections, as shown below. Once complete, hit **ESC** to stop editing, followed by **:wq** and **Enter** to exit the file editor.

   .. figure:: images/hdd9.png
      :align: left
      :scale: 50%

      Before

   .. figure:: images/hdd10.png
      :align: right
      :scale: 50%

      After

#. Run the command ``genesis restart``. You may now exist your SSH session.  This will refresh Prism, and you will now see that the disk is available to add and repartition.

#. Return to Prism, and select **+ Repartition and Add > Yes**.

   .. figure:: images/hdd11.png

#. The previously removed disk will now be reincorporated into the cluster, and perform as normal.

NIC Failure
===========

In this section, we will be simulating a Network Interface Card (NIC), and observe the behavior of the cluster. This demonstration also shows what would happen in the event of a switch failure that was supplying a connection to the cluster. By default, Nutanix clusters' network connections are configured in an active/passive mode.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

Viewing AHV Host Network Configuration in Prism
-----------------------------------------------

This will display a visual representation of the network layout of the selected host.

#. Click **Network** from the dropdown.

   .. figure:: images/3.png

#. Click on the action link for the host you intend to use for this test. We've chosen the host *PHX-POC212-1* in this example.

   .. figure:: images/4.png

#. You will now be presented with a diagram of the links between the CVM and the physical NIC ports. Select a network connection to view the details. We've chosen the *eth3* port. Ensure the port you choose is active. At the right, the details of that port are now displayed. This includes *MTU (bytes)*, *Link Capacity*, *MAC Address*, *Port Name*, and *Link Status*.

   .. figure:: images/5.png

For more details, please view `Network Visualization <https://portal.nutanix.com/page/documents/details/?targetId=Web-Console-Guide-Prism-v5_16%3Awc-network-visualization-intro-c.html/>`_ portion of the `Prism Web Console Guide <https://portal.nutanix.com/page/documents/details/?targetId=Web-Console-Guide-Prism-v5_17%3AWeb-Console-Guide-Prism-v5_17>`_

View AHV Host Network Configuration in the CLI
----------------------------------------------

.. note::

   For better security and a single point of management, avoid connecting directly to the AHV hosts. All AHV host operations can be performed from the CVM by connecting to 192.168.5.1, the internal management address of the AHV host.

#. SSH to the CVM on the host using the *nutanix* username and password.

#. To verify the names, speed, and connectivity status of all AHV host interfaces, use the `manage_ovs show_uplinks` command, followed by the `manage_ovs show_interfaces` command. Since in previous steps we've identified that there is only a single bridge, with a single bond. If we had multiple bridges, use the command `manage_ovs --bridge_name <bridge name> show_uplinks`.

   .. code-block:: bash

      manage_ovs show_uplinks

   .. figure:: images/6.png

      Sample output of ``manage_ovs show_uplinks`` command

   .. code-block:: bash

      manage_ovs show_interfaces

   .. figure:: images/7.png

      Sample output of ``manage_ovs show_interfaces`` command

In our example, eth0 and eth1 report **False** under *link* as there is no physical connection to those ports. Ports eth2 and eth3 report **True** under link, as both are physically connected. We now need to identify the active port in this bridge.

#. SSH to the internal management address of the AHV host. This step does not require additional authentication.

   .. code-block:: bash

      ssh root@192.168.5.1

#. Execute the command:

   .. code-block:: bash

      ovs-appctl bond/show

   .. figure:: images/8.png

      Sample output of the ``ovs-appctl bond/show`` command

As we've previously seen, eth0 and eth1 are disabled, as they have no physical link. They both list *may_enable: false* as enabling these ports would be pointless without a physical connection.

What we're looking for is the port that states *active slave*. This is the active port for this bond.

Initiate failover within the CLI
--------------------------------

.. note::

   Ensure you are running the BASIC or RECOMMENDED workload tests on the selected host before proceeding.

#. Execute the following command, specifying the bond, and the interface that you are going to make active. In our example, the bond is *br0-up* and the interfaces is *eth2*

   .. code-block:: bash

      ovs-appctl bond/set-active-slave <bond name> <interface name>

   .. figure:: images/9.png

      Sample output of the ``ovs-appctl bond/set-active-slave`` command

#. Now let's look at the output of the `ovs-appctl bond/show` command now that we've modified the active interface to be *eth2* in our example.

   .. figure:: images/10.png

      Sample output of the ``ovs-appctl bond/show`` command

#. You have now successfully forced a failover between interfaces.

Node Failure
============

In this section, we will be simulating a node failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off the node unexpectedly, and observe the behavior of the cluster. In a 2+ node configuration, Nutanix can tolarate the unavailability of a single node - whether due to a failure, or scheduled maintenance.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

#. Login to the IPMI interface of the selected node to participate in the simulated node failure test.

#. Click the **Power Down** button.

   .. figure:: images/11.png

#. Observe that the test VM(s) on the selected host are now powered off, and a High Availability (HA) event has occurred. The cluster will automatically attempt to restart the VM(s) on the remaining hosts.

#. Login to Prism.

#. From the dropdown, select **Hardware**. Click on the selected host. Observe that the host is offline.

#. From the dropdown, select **VM**. Monitor the VM(s) that were previously running on the test host, now will boot up on the remaining hosts. This process should take approximately 3-5 minutes from power off to the VM(s) being up and running once again.

Complete Power Failure
======================

In this section, we will be simulating a cluster failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off all nodes simultaneously, and observe the behavior of the cluster once the simulated power is restored.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

#. Open a separate browser tab for each, and within each tab, login to the IPMI interface of each node. This will allow you to quickly and easily power off all nodes.

#. Click the **Power Down** button for each node.

   .. figure:: images/11.png

#. Demonstrate that both the test VM(s) on the selected host are now powered off, but all hosts themselves are powered off.

#. After a few minutes, click the **Power On** button within the IPMI console for each node. Wait approximately 15-20 minutes.

#. SSH into any CVM, and run the following command:

   .. code-block:: bash

      cluster status

   .. figure:: images/12.png

      Sample output of the ``cluster status`` command for a one node

#. Wait for all services on all nodes in the cluster to be up before you attempt to log in to Prism.

#. Perform a short demo, ensure all VMs are now back up and running, and for a few VMs or services, show the customer that all is up and running without problems.

Power Supply Failure
====================

In this section, we will be simulating a power failure by removing power from one of the power supplies on the cluster, and observe the behavior of the cluster.

.. note::

   This applies to a physical POC only.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

#. Identify the physical power supply on the node you wish to remove as a part of this test. This can also be performed remotely if the customer has network capacity for their Power Distribution Unit (PDU).

#. Disconnect or otherwise shut power to one power supply.

#. Observe that no interruption has occurred. If SMTP was configured on this cluster, a support ticket may be generated for a power supply failure.

#. Reconnect the previously removed power supply cable to the cluster.
