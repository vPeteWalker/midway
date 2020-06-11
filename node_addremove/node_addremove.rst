.. _node_addremove:

--------------------
Add or Remove a node
--------------------

A cluster is a collection of nodes. You can add new nodes to a cluster at any time after physically installing and connecting them to the network on the same subnet as the cluster. The cluster expansion process compares the AOS version on the existing and new nodes and performs any upgrades necessary for all nodes to have the same AOS version.

.. note::

   - Run all NCC checks, and check the Health Dashboard. If any health checks are failing, resolve them to ensure that the cluster is healthy before adding any nodes.
   - These instuctions assume:
      - All nodes have the same hardware configuration, AOS/AHV versions, and passwords.
      - Data-at-rest encryption is not in use.
      - Rack awareness is not in use.

Add node(s)
+++++++++++

#. Either click the gear icon in the main menu and then select **Expand Cluster** in the **Settings** page, or go to the **Hardware** dashboard and click the **Expand Cluster** button.

- The network is searched for Nutanix nodes and then the **Expand Cluster** dialog box appears (on the *Select Host* screen) with a graphical list of the discovered blocks and nodes. Discovered blocks are blocks with one or more unassigned factory-prepared nodes (hypervisor and Controller VM installed) residing on the same subnet as the cluster. Discovery requires that IPv6 multicast packets are allowed through the physical switch. A lack of IPv6 multicast support might prevent node discovery and successful cluster expansion.

#. Select the check box for each block to be added to the cluster. All nodes within a checked block are also checked automatically; uncheck any nodes you do not want added to the cluster.

- When a block is checked, more fields appear below the block diagram. A separate line for each node (host) in the block appears under each field name.

#. Do the following in the indicated fields for each checked block:

- **Host Name**: Enter the name of the host.
   - Enter just the host name, not the fully qualified domain name.

- **Controller VM IP**: Review the Controller VM IP address assigned to each host and do one of the following:

   - If the address is correct, do nothing in this field.
   - If the address is not correct, either change the incorrect address or enter a starting address on the top line (for multiple hosts). The entered address is assigned to the Controller VM of the first host, and consecutive IP addresses (sequentially from the entered address) are assigned automatically to the remaining hosts.

- **Hypervisor IP**: Repeat the previous step for this field.

   - This field sets the hypervisor IP addresses for all the hosts to be added.

- **IPMI IP**: Repeat the previous step for this field.

   - This field sets the IPMI port IP addresses for all the hosts to be added. An IPMI port is used for the hypervisor host console.

- When all the node values are correct, click the **Next** button.

   - The network addresses are validated before continuing. If an issue is discovered, the problem addresses are highlighted in red. If there are no issues, the process moves to the *Assign Rack* screen with a message at the top when the hypervisor, AOS, or other relevant feature is incompatible with the cluster version.

- When all the fields are correct, click the **Expand Cluster** button.

- The **Expand Cluster** dialog box closes and the add node process begins. As the nodes are added, messages appear on the dashboard. A blue bar indicates that the task is progressing normally. Nodes are processed (upgraded or reimaged as needed) and added in parallel. Adding nodes can take some time. Imaging a node typically takes a half hour or more depending on the hypervisor.

Remove node
+++++++++++

#. In the **Hardware** dashboard, click the **Diagram** or **Table** tab.

#. Select the target host, and click the **Remove Host** link on the right of the *Summary* line. A dialog box appears to verify the action. Click the **OK** button.

- The Prism web console displays a warning message that you need to reclaim the license after you have removed the node. See the "Reclaiming Licenses" or "Reclaiming Licenses (Portal Connection)" section in the Nutanix Licensing Guide.

- Removing a host takes some time because data on that host must be migrated to other hosts before it can be removed from the cluster. You can monitor progress through the dashboard messages. Removing a host automatically removes all the disks in that host. Only one host can be removed at a time. If you want to remove multiple hosts, you must wait until the first host is removed completely before attempting to remove the next host.

#. After a node is removed, it goes into an unconfigured state. You can add such a node back into the cluster through the Add Node(s) workflow.
