.. _cvm:

CVM Failure
+++++++++++

In this section, we will be simulating a Controller Virtual Machine (CVM) failure by executing a command that will shut off the CVM unexpectedly (versus a gradual shutdown for maintenance). This test demonstrates the ability of Nutanix's AOS to immediately failover storage I/O operations to another CVM wjthin the cluster without interruption to any VMs running on that host.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running the CVM you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`

.. note::

   If you choose to run X-Ray on the same cluster you are performing failures on, it is recommended to avoid simulating the CVM failure on the same host that is running X-Ray.

#. SSH into any host within the cluster using the *root* username and password, and run the following commands to shut down the CVM running on that host.

   - ``virsh list`` - This command will *list all VMs* running on the host. Make note of the entry in the *Name* column for the CVM.

   - ``virsh shutdown <CVM-NAME>`` - This command will *shut down* the CVM.

   - ``virsh start <CVM-NAME>`` - This command will *start* the CVM.

   .. figure:: images/1.png

      Sample output of all commands

#. Login to Prism, and observe that the VMs on the same host are still running without interruption. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.

   .. figure:: images/2.png
