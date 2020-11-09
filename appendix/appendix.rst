.. _appendix:

--------
Appendix
--------

IP Address Allocation
+++++++++++++++++++++

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - IP Address
     - Description
   * - .37
     - Cluster Virtual IP
   * - .38
     - Cluster Data Services IP
   * - .39
     - Prism Central
   * - .40
     - AutoAD, NTNXLAB.local Domain Controller
   * - .41
     - Foundation
   * - .42 - .43
     - Objects - Internal Access (2)
   * - .44 - .47
     - Objects - Client Access (4)
   * - .50 - .125
     - IPAM range
   * - .126
     - IPAM DHCP Server
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -
   * -
     -


Resources
+++++++++++

Planning & Deployment
+++++++++++++++++++++

Initial Configuration
+++++++++++++++++++++

Infrastructure Lifecycle
++++++++++++++++++++++++

Flow
++++

Calm
++++

Files
+++++

X-Ray
+++++

Failure Scenarios
+++++++++++++++++

Put video of HDD failure here?


Windows from scratch
++++++++++++++++++++

Era
+++


Selecting Physical PoCs vs HPoCs
++++++++++++++++++++++++++++++++

PoC hardware may not always be in proper working condition

   It is suggested that if at all possible you have an opportunity to vet out the HW, and/or confirm that the PoC team has done so before sending it to the customer. We have been burned with bad HW in PoCs before.

HPoC environments may not support certain versions of sw for integration with Nutanix portfolio products, and an on site PoC may be the best way to show the customer how integration will work for certain specialty products

HPoCs may not be desirable if the customer wants real workload testing

To properly test replication the customer may not feel comfortable replicating from their on-premise network to the Nutanix corp network or Xi Leap regardless of secure VPN connectivity

   It is recommended that 2 Physical PoC units be used to accomplished this task

If possible it is suggested that the PoC be done in an isolated environment as to not adversely affect production systems accidentally

NVMe drives are not suggested for use in PoCs where customers wish to pull drives

If benchmarking is a requirement for PoC success make sure to engage the performance team to ensure CVMs and the cluster are rightsized for IOMeter, VDIBench, XRAY(Suggested) etc. prior to deployment.

Make sure that the specific hardware you are using in a Physical PoC can support PCIe devices

   Ex. The 1065S - Are single proc and cannot support PCIe devices and only have 1GBaseT built in NICs for example

   .. figure:: images/1.png

Foundation
++++++++++

PoC gear not see as discoverable by Foundation

   Refer to the `Prepare Bare Metal Nodes For Foundation section of the Field Installation Guide <https://portal.nutanix.com/page/documents/details?targetId=Field-Installation-Guide-v4-4:v44-cluster-image-foundation-t.html%23task_lmh_msc_zm>`_

      Install and configure the Foundation VM on your laptop using VirtualBox.

      Run through this Foundation guide prior to the Foundation scheduled at the customer location to validate you have the latest ISOs and diagnostics.

It is suggested that you bring your own 1GBaseT flat switch to customer premise to do foundation process. Set your laptop ethernet port IP address to the same subnet/network as the IPMI network. Also make sure (1) built in, 1GbaseT data port is plugged in for Foundation discovery.

When you first run Foundation, *do not* select VLAN tagging, as if you are using the recommended flat switch, it will not support VLAN tagging, and therefore the nodes will not be able to communicate with one another. You can change VLAN settings in AHV/CVM at a later time if required (see *Networking* section).

Networking
++++++++++

Always refer to the latest version of the `AHV Administration Guide <https://portal.nutanix.com/page/documents/details?targetId=AHV-Admin-Guide-v5_18:AHV-Admin-Guide-v5_18>`_ and `AHV Network Best Practices <https://portal.nutanix.com/page/documents/solutions/details?targetId=BP-2071-AHV-Networking:BP-2071-AHV-Networking>`_ for networking best practices and guidance.

Assigning The Controller VM To A VLAN
.....................................

Refer to the Assigning The Controller VM To A VLAN section of the `AHV Administration Guide <https://portal.nutanix.com/page/documents/details?targetId=AHV-Admin-Guide-v5_18:AHV-Admin-Guide-v5_18>`_

.. note::

   To avoid losing connectivity to the Controller VM, do not change the VLAN ID when you are logged on to the Controller VM through its public interface. To change the VLAN ID, log on to the internal interface that has IP address 192.168.5.254.

#. Log on to the AHV host with SSH.

#. Log on to the Controller VM.

   `root@host# ssh nutanix@192.168.5.254`

   Accept the host authenticity warning if prompted, and enter the Controller VM nutanix password.

#. Assign the public interface of the Controller VM to a VLAN.

      `nutanix@cvm$ change_cvm_vlan vlan_id`

   Replace vlan_id with the ID of the VLAN to which you want to assign the Controller VM.

   For example, add the Controller VM to VLAN 10.

      `nutanix@cvm$ change_cvm_vlan 10`

#. Restart the network service.

      `nutanix@cvm$ sudo service network restart`

Assigning An Acropolis Host To A VLAN
.....................................

Refer to the Assigning An Acropolis Host To A VLAN section of the `AHV Administration Guide <https://portal.nutanix.com/page/documents/details?targetId=AHV-Admin-Guide-v5_18:AHV-Admin-Guide-v5_18>`_

#. Log on to the AHV host with SSH.

#. Assign port br0 (the internal port on the default OVS bridge, br0) to the VLAN that you want the host be on.

   `root@ahv# ovs-vsctl set port br0 tag=host_vlan_tag`

   Replace host_vlan_tag with the VLAN tag for hosts.

#. Confirm VLAN tagging on port br0.

   `root@ahv# ovs-vsctl list port br0`

#. Check the value of the tag parameter that is shown.

#. Verify connectivity to the IP address of the AHV host by performing a ping test.

Load Balancing Within Bond Interfaces
.....................................

Refer to the `Load Balancing Within Bond Interfaces section of the <https://portal.nutanix.com/page/documents/solutions/details?targetId=BP-2071-AHV-Networking:BP-2071-AHV-Networking>`_

#. Active-backup mode is enabled by default, but you can also configure it with the following ovs-vsctl command on the CVM:

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-vsctl set port br0-up bond_mode=active-backup"`

#. View the bond mode with the following CVM command:

   `nutanix@CVM$ manage_ovs show_uplinks`

#. In the active-backup configuration, this command returns a variation of the following output, where eth2 and eth3 are marked as interfaces used in the bond br0-up.

   .. code::
      Bridge: br0
        Bond: br0-up
          bond_mode: active-backup
          interfaces: eth3 eth2
          lacp: off
          lacp-fallback: false
          lacp_speed: slow

#. For more detailed bond information such as the currently active adapter, use the following ovs-appctl command on the CVM:

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-appctl bond/show"`

Link aggregation is required to take full advantage of the bandwidth provided by multiple links. In OVS it is accomplished though dynamic link aggregation with LACP and load balancing using balance-tcp.

Nutanix and OVS require dynamic link aggregation with LACP instead of static link aggregation on the physical switch. Do not use static link aggregation such as etherchannel with AHV.

.. note::

   Nutanix recommends enabling LACP on the AHV host with fallback to active-backup. Then configure the connected upstream switches. Different switch vendors may refer to link aggregation as port channel or LAG. Using multiple upstream switches may require additional configuration such as a multichassis link aggregation group (MLAG) or virtual PortChannel (vPC). Configure switches to fall back to active-backup mode in case LACP negotiation fails (sometimes called fallback or no suspend-individual). This switch setting assists with node imaging and initial configuration where LACP may not yet be available on the host.

#. If upstream LACP negotiation fails, the default AHV host configuration disables the bond, thus blocking all traffic. The following command allows fallback to active-backup bond mode in the AHV host in the event of LACP negotiation failure:

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-vsctl set port br0-up other_config:lacp-fallback-ab=true"`

#. In the AHV host and on most switches, the default OVS LACP timer configuration is slow, or 30 seconds. This value—which is independent of the switch timer setting—determines how frequently the AHV host requests LACPDUs from the connected physical switch. The fast setting (1 second) requests LACPDUs from the connected physical switch every second, thereby helping to detect interface failures more quickly. Failure to receive three LACPDUs—in other words, after 3 seconds with the fast setting—shuts down the link within the bond. Nutanix recommends setting lacp-time to fast on the AHV host and physical switch to decrease link failure detection time from 90 seconds to 3 seconds.

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-vsctl set port br0-up other_config:lacp-time=fast"`

#. Next, enable LACP negotiation and set the hash algorithm to balance-tcp.

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-vsctl set port br0-up lacp=active"`

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-vsctl set port br0-up bond_mode=balance-tcp"`

#. Enable LACP on the upstream physical switches for this AHV host with matching timer and load balancing settings. Confirm LACP negotiation using ovs-appctl commands, looking for the word "negotiated" in the status lines.

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-appctl bond/show br0-up"`

   `nutanix@CVM$ ssh root@192.168.5.1 "ovs-appctl lacp/show br0-up"`

#. Exit maintenance mode and repeat the preceding steps for each node and every connected switch port one node at a time, until you have configured the entire cluster and all connected switch ports.

General Networking
..................

#. From the CVM, Validate current state of br0 interfaces:

   `manage_ovs show_interfaces`

#. From the CVM, validate current state of br0 uplinks:

   `manage_ovs --bridge_name br0 show_uplinks`

#. Command to add ALL 10GiB NIC interfaces to CVM br0, and remove 1GiB interfaces:

   `manage_ovs --bridge_name br0 --bond_name br0-up --interfaces 10g update_uplinks`

#. Create a separate br1 for the 1GiB NIC interfaces

   `manage_ovs --bridge_name br1 --bond_name br1-up --interfaces 1g --require_link=false update_uplinks`

#. Add specific NIC interfaces to CVM br0:

   `manage_ovs --bridge_name br0 --bond_name br0-up --interfaces eth2,eth3`

#. Command to check the current bond configuration:

   `ovs-appctl bond/list`

Miscellaneous Helpful Commands
..............................

To SSH into the local CVM on an AHV host:

   `ssh nutanix@192.168.5.254`

To shutdown an AHV host:

   `shutdown -h now`

To start a VM in AHV

   `virsh start VM_name`
