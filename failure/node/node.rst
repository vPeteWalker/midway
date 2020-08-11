.. _node:

Node Failure
============

In this section, we will be simulating a node failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off the node unexpectedly, and observe the behavior of the cluster. In a 2+ node configuration, Nutanix can tolarate the unavailability of a single node - whether due to a failure, or scheduled maintenance.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running on the host you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via IPMI, and observe that pings are lost once the host is powered off, and the VM resumes operation after the HA event. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`

   - MORE RECOMMENDED: Use X-Ray to run Extended Node Failure test (estimated time of completion: 11 hours)

.. note::

   If you choose to run X-Ray on the same cluster you are performing failures on, you must avoid simulating the node failure on the same host that is running X-Ray.

#. Login to the IPMI interface of the selected node to participate in the simulated node failure test.

#. Click the **Power Down** button.

   .. figure:: images/11.png

#. Observe that the test VM(s) on the selected host are now powered off, and a High Availability (HA) event has occurred. The cluster will automatically attempt to restart the VM(s) on the remaining hosts.

#. Log into Prism.

#. From the dropdown, select **Hardware**. Click on the selected host. Observe that the host is offline.

#. From the dropdown, select **VM**. Monitor the VM(s) that were previously running on the test host, now will boot up on the remaining hosts. This process should take approximately 3-5 minutes between the time you power off the host, and the VM(s) being up and running once again.

#. Return to the IPMI interface for the selected node, and click **Power On**. This will take approximately 10-15 minutes to boot up, and be available within the cluster.

#. Perform a short demo, ensure all VMs are now back up and running, and for a few VMs or services, show the customer that all is up and running without problems. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.

Complete Power Failure
======================

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Follow the instructions below.

   - RECOMMENDED: Use X-Ray to run Total Power Loss test (estimated time of completion: 2 hours). Setup of X-Ray is oulined in :ref:`xray`

.. note::

   You must run X-Ray on a different cluster than the cluster you are performing the power failure on.

In this section, we will be simulating a cluster failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off all nodes simultaneously, and observe the behavior of the cluster once the simulated power is restored.

#. Open a separate browser tab for each, and within each tab, login to the IPMI interface of each node. This will allow you to quickly and easily power off all nodes.

#. Click the **Power Down** button for each node.

   .. figure:: images/11.png

#. Demonstrate that all test VM(s) on the selected host are now powered off, as all hosts themselves are powered off.

#. After a few minutes, click the **Power On** button within the IPMI console for each node. Wait approximately 15-20 minutes.

#. SSH into any CVM, and run the ``cluster status`` command.

   .. figure:: images/12.png

      Sample output of the ``cluster status`` command

#. Wait for all services on all nodes in the cluster to be up before you attempt to log in to Prism.

#. Perform a short demo, ensure all VMs are now back up and running, and for a few VMs or services, show the customer that all is up and running without problems.

Power Supply Failure (Physical POC only)
========================================

.. PW - Asked Scott Ellis for other hardware guide similar to the NX PSU redundancy guide.
      - Rewrite below to have tables in the doc vs. pictures?

In this section, we will be simulating a power failure by removing power from one of the power supplies on the cluster, and observe the behavior of the cluster.

.. note::

   Nutanix recommends that you carefully plan your AC power source needs, especially in cases where the cluster consists of mixed models. Nutanix recommends that you use 180 V ~ 240 V AC power source to secure PSU redundancy. However, according to the below tables, and depending on the number of nodes in the chassis, some NX platforms can work with redundant 100 V ~ 210 V AC power supply units. If using non-NX hardware, refer to their respective manufacturer's platform guides.

.. figure:: images/psug4.png
   :align: left
   :scale: 50%

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G4 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G4-Multinode:har-psu-redundancy-table-r.html>`_

.. figure:: images/psug5.png
   :align: right
   :scale: 50%

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G5 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G5-Single-Node:har-psu-redundancy-table-r.html>`_

.. figure:: images/psug6.png
   :align: left
   :scale: 50%

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G6 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G6-Multinode:har-psu-redundancy-table-g6-r.html>`_

.. figure:: images/psug7.png
   :align: right
   :scale: 50%

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G7 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G7-Multinode:har-psu-redundancy-table-r.html>`_

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

   - BASIC: Create as many VMs as there are hosts. Begin a continuous ping between each VM to another VM, prior to issuing the shutdown command via SSH, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`

#. Identify the physical power supply cord on the node you wish to remove as a part of this test. This can also be performed remotely if the customer has the ability to control individual sockets on their Power Distribution Unit (PDU), and you've confirmed the associated sockets connected to the node being tested.

#. Disconnect power cord or otherwise remove power from one power supply.

#. Observe that no cluster interruption has occurred, and an error was generated in Prism. If SMTP was configured on this cluster, a support ticket may be generated for a power supply failure.

#. Reconnect the previously removed power supply cable to the cluster. Acknowledge and resolve the associated alert. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.
