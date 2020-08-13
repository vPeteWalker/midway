.. _psu:

Power Supply Failure (Physical POC only)
++++++++++++++++++++++++++++++++++++++++

.. PW - Asked Scott Ellis for other hardware guide similar to the NX PSU redundancy guide.
      - Rewrite below to have tables in the doc vs. pictures?

In this section, we will be simulating a power failure by removing power from one of the power supplies on the cluster, and observe the behavior of the cluster.

.. note::

   Nutanix recommends that you carefully plan your AC power source needs, especially in cases where the cluster consists of mixed models. Nutanix recommends that you use 180 V ~ 240 V AC power source to secure PSU redundancy. However, according to the below tables, and depending on the number of nodes in the chassis, some NX platforms can work with redundant 100 V ~ 210 V AC power supply units. If using non-NX hardware, refer to their respective manufacturer's platform guides.

.. figure:: images/psug4.png
   :align: left

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G4 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G4-Multinode:har-psu-redundancy-table-r.html>`_

.. figure:: images/psug5.png
   :align: right

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G5 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G5-Single-Node:har-psu-redundancy-table-r.html>`_

.. figure:: images/psug6.png
   :align: left

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G6 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G6-Multinode:har-psu-redundancy-table-g6-r.html>`_

.. figure:: images/psug7.png
   :align: right

   `Power Supply Unit (PSU) Redundancy and Node Configuration for G7 platforms <https://portal.nutanix.com/page/documents/details?targetId=System-Specs-G7-Multinode:har-psu-redundancy-table-r.html>`_

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings or X-Ray VM interruption post-CVM shutdown.

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload.

   - BASIC: Create as many VMs as there are hosts. Begin a continuous ping between each VM to another VM, prior to issuing the shutdown command via SSH, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`.

#. Identify the physical power supply cord on the node you wish to remove as a part of this test. This can also be performed remotely if the customer has the ability to control individual sockets on their Power Distribution Unit (PDU), and you've confirmed the associated sockets connected to the node being tested.

#. Disconnect power cord or otherwise remove power from the identified power supply.

#. Observe that no cluster interruption has occurred, and an alert was generated in Prism. If SMTP was configured on this cluster, a support ticket may be generated for a power supply failure.

#. Reconnect the previously removed power supply cable to the cluster. Acknowledge and resolve the associated alert. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.
