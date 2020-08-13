.. _node_addremove:

--------------------
Add or Remove a node
--------------------

In this module we will demonstrate how to remove a node from a cluster, and add a node to a cluster.

A cluster is a collection of nodes. You can add new nodes to a cluster at any time after physically installing and connecting them to the network on the same subnet as the cluster. The cluster expansion process compares the AOS version on the existing and new nodes and performs any upgrades necessary for all nodes to have the same AOS version.

Why does this matter to our customers?

A customer may choose to add a node to account for a new workloads such as VDI, or increase performance, capacity, or resiliency. Additionally, a customer may elect to remove a node if that node is past their usable life, and may not be meeting the customer's performance needs, or the node is no longer supported.

**Pre-requisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** 45-60 minutes

**Covered Test IDs:** `Core-009, Core-010 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

**FEEDBACK** :ref:`feedback`

.. note::

   Before performing the procedure below, make sure you are running the latest version of the Nutanix Cluster Check (NCC) health checks and upgrade NCC if necessary.  Run all NCC checks, and check the Health Dashboard. If any health checks are failing, resolve them to ensure that the cluster is healthy before continuing.

   These instuctions assume:
      All nodes have the same hardware configuration, AOS/AHV versions, and passwords.
      Data-at-rest encryption is not in use.
      Rack awareness is not in use.

Remove node
+++++++++++

#. From the dropdown, choose the **Home** dashboard. Observe the **Hosts** count within the *Hardware Sumnmary*, and the total **Storage** **CPU** and **Memory** values before and after proceeding with the instructions below.

   .. figure:: images/7.png
      :align: center

#. From the dropdown, choose the **Hardware** dashboard, then click the **Diagram** or **Table** tab.

#. Select the target host, and click the **Remove Host** link on the right of the *Summary* line. A dialog box appears to verify the action. Click the **OK** button.

   .. figure:: images/6.png
      :align: center

   Removing a host takes some time because data on that host must be migrated to other hosts before it can be removed from the cluster. You can monitor progress through the dashboard messages. Removing a host automatically removes all the disks in that host. Only one host can be removed at a time. If you want to remove multiple hosts, you must wait until the first host is removed completely before attempting to remove the next host.

   After a node is removed, it goes into an unconfigured state. You can add such a node back into the cluster through the Add Node(s) workflow.

Add node
++++++++

#. Either click :fa:`gear` **> Settings > Expand Cluster**, or from the dropdown menu choose **Hardware > +Expand Cluster**.

   The network is searched for Nutanix nodes and then the **Expand Cluster** dialog box appears (on the *Select Host* screen) with a graphical list of the discovered blocks and nodes. Discovered blocks are blocks with one or more unassigned factory-prepared nodes (hypervisor and Controller VM installed) residing on the same subnet as the cluster. Discovery requires that IPv6 multicast packets are allowed through the physical switch. A lack of IPv6 multicast support might prevent node discovery and successful cluster expansion.

#. Select the check box for each block to be added to the cluster. All nodes within a checked block are also checked automatically; uncheck any nodes you do not want added to the cluster.

   .. figure:: images/1.png
      :align: center

   When a block is checked, more fields appear below the block diagram. A separate line for each node (host) in the block appears under each field name.

#. Do the following in the indicated fields for each checked block:

   - **Host Name**
      Enter just the host name, not the fully qualified domain name.

   - Review the **Controller VM IP**, **Hypervisor IP**, and **IPMI IP** assigned to each host and do one of the following:

      If the address is correct, do nothing in this field.
      If the address is not correct, either change the incorrect address or enter a starting address on the top line (for multiple hosts). The entered address is assigned to the Controller VM of the first host, and consecutive IP addresses (sequentially from the entered address) are assigned automatically to the remaining hosts.

         .. figure:: images/2.png
            :align: center

#. When all the node values are correct, click the **Next** button.

   The network addresses are validated before continuing. If an issue is discovered, the problem addresses are highlighted in red. If there are no issues, the process moves to the *Assign Rack* screen with a message at the top when the hypervisor, AOS, or other relevant feature is incompatible with the cluster version.

      .. figure:: images/3.png
         :align: center

#. When all the fields are correct, click the **Expand Cluster** button.

   The **Expand Cluster** dialog box closes and the add node process begins. As the nodes are added, messages appear on the dashboard. A blue bar indicates that the task is progressing normally. Nodes are processed (upgraded or reimaged as needed) and added in parallel. Adding nodes can take some time. Imaging a node typically takes a half hour or more depending on the hypervisor.

      .. figure:: images/4.png
         :align: left

      .. figure:: images/5.png
         :align: right
