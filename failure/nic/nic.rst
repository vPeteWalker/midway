.. _nic:

NIC Failure
+++++++++++

In this section, we will be simulating a Network Interface Card (NIC), and observe the behavior of the cluster. This demonstration also shows what would happen in the event of a switch failure that was supplying a connection to the cluster. By default, Nutanix clusters' network connections are configured in an active/passive mode.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running on the host you are simulating the NIC failure on, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via SSH, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`.

.. note::

   If you choose to run X-Ray on the same cluster you are performing failures on, it is recommended to avoid simulating the NIC failure on the same host that is running X-Ray.

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

   ``manage_ovs show_uplinks``

   .. figure:: images/6.png

      Sample output of manage_ovs show_uplinks command

   ``manage_ovs show_interfaces``

   .. figure:: images/7.png

      Sample output of manage_ovs show_interfaces command

In our example, eth0 and eth1 report **False** under *link* as there is no physical connection to those ports. Ports eth2 and eth3 report **True** under link, as both are physically connected. We now need to identify the active port in this bridge.

#. SSH to the internal management address of the AHV host by entering ``ssh root@192.168.5.1``. This step does not require additional authentication.

#. Execute the command ``ovs-appctl bond/show``

   .. figure:: images/8.png

      Sample output of the ovs-appctl bond/show command

As we've previously seen, eth0 and eth1 are disabled, as they have no physical link. They both list *may_enable: false* as enabling these ports would be pointless without a physical connection.

What we're looking for is the port that states *active slave*. This is the active port for this bond.

Initiate failover within the CLI
--------------------------------

.. note::

   Ensure you are running the BASIC or RECOMMENDED workload tests on the selected host before proceeding.

#. Execute the following command, specifying the bond, and the interface that you are going to make active. In our example, the bond is *br0-up* and the interfaces is *eth2*

   ``ovs-appctl bond/set-active-slave <BOND-NAME> <INTERFACE-NAME>``

   .. figure:: images/9.png

      Sample output of the ovs-appctl bond/set-active-slave command

#. Now let's look at the output of the ``ovs-appctl bond/show`` command now that we've modified the active interface to be *eth2* in our example.

   .. figure:: images/10.png

      Sample output of the ovs-appctl bond/show command

#. You have now successfully forced a failover between interfaces. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.

Remove physical network cable (Physical POC only)
+++++++++++++++++++++++++++++++++++++++++++++++++

There is no single standard between all hardware vendors to consistently identify how physical network ports are represented within Nutanix. For example, on one vendor the numbering may start from the left as you observe the physical NIC, some from right, etc. This may also vary in between form factors from the same hardware vendor. Lastly, it could vary based on what manufacturer's NIC is being used.

With all this understood, it is therefore recommended that if you intend on performing this test during your POC, to plan ahead and remove one physical cable at a time, and document which port that corresponds to within Nutanix (e.g. eth0 is the left-most port, eth1 is the second from the left, etc.) during the initial setup phase. Then during the test with the customer, you can be confident that when you remove the cable, you'll know the result ahead of time.
