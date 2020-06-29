.. _foundation:

-------------------
Physical Foundation
-------------------

If doing an on-premises POC, you will need to perform a fresh Foundation on your hardware. Many SEs opt to test Foundation on the cluster at home to identify any potential issues (bad SATADOM, loose memory, etc.) before bringing the block to a customer site. The SE would then typically run Foundation again on-site as the installation process is often a part of POC test plans, allowing us to demonstrate how fast and simple it is to deploy Nutanix.

**Expected Module Duration:** 150 minutes (Including racking, cabling, Foundation, and transition to customer network)
**Covered Test IDs:** Core-001, Core-002

Pre-Foundation Discovery
++++++++++++++++++++++++

To ensure a physical POC deployment is successful, please ensure that these typical requirements are considered before the day of install, including which components will be supplied by Nutanix or the customer:

- **Rackspace**

   - 1-2 rack unit (RU) per block, depending on the form factor of the block(s) being utilized.

- **Power**

   - 1-2 power connections per node, 2 required for full redundancy.  This can either be a C-13 to C-14 cable, or a C-13 to NEMA 5-15P and is dependent on the type of Power Distribution Unit (PDU) the customer is intending to use.
   - Sufficient power capacity available.  Ex. NX-3060-G7 has a typical power usage of 1700 Watts.
   - 200-240V power is required to run a 2U4N block from a single power supply

- **Network**

   - Network switch ports availability per node - (1+) 10 g/bit connections (SFP+ or BASE-T), 2 required for full redundancy.  (1) 100/1000 m/bit for lights out management (IPMI, ILO, iDRAC).
   - Network cables available per node, ensuring the proper lengths to not only traverse the distance between the node(s) and the network switch(es), but to confirm you aren't exceeded the cable or transceiver specification you are using.  For customers with SFP+ network switches, you may either use TwinAx or fiber cables with SFP+ transceivers on each end. (1+) 10 g/bit connections (SFP+ or BASE-T), 2 required for full redundancy.  (1) 100/1000 m/bit for lights out management (IPMI, ILO, iDRAC).  Verify with the customer/partner who will be providing the network cables. Nutanix can supply generic TwinAx cables but these will not work will all switch brands (Cisco, HPE, etc.).
   - Network switch configuration - Ensure all network switch ports are properly configured, including VLAN tagging, and that both the ports to be used and VLANs are already created and identified.  Typical installs utilize a single VLAN for CVM, Hypervisor, and user VMs.  However, this should be discussed and agreed upon with the customer prior to install.
   - `Pre-Install Survey <https://docs.google.com/spreadsheets/d/15r8Q1kCIJY4ErwL1CaHHwv4Q7gmCbLOz5IaR51t9se0/edit#gid=8195649>`_ completed *and reviewed* prior to on-site arrival. This spreadsheet outlines required IPs and VLANs for a deployment.


- **Software**

   - A downloaded version of AOS from https://portal.nutanix.com - Do **NOT** use the very latest version of AOS/AHV as you will be unable to show 1-Click upgrades as part of your POC.
   - `Nutanix KB2340 <https://portal.nutanix.com/#/page/kbs/details?targetId=kA032000000TT1HCAW>`_ provides instruction on how to download previous versions of Nutanix software that are no longer available through http://portal.nutanix.com
Refer to https://portal.nutanix.com/#/page/compatibilitymatrix/software to ensure you are utilizing the correct version of AOS, hypervisor, and guest OS.
Operating system templates (optional), ISOs for operating systems and applications.

Setting Up Your Foundation Environment
++++++++++++++++++++++++++++++++++++++

Currently, there are two options for performing baremetal Foundation of a Nutanix block, Portable Foundation and the Foundation VM. The full **Foundation Use Case Matrix** can be found `here <https://portal.nutanix.com/page/documents/details/?targetId=Field-Installation-Guide-v4-5%3Av45-features-compatibility-matrix-r.html>`_.

(Recommended) Portable Foundation
.................................

- Portable Foundation is a native application that runs on Windows 10+ or macOS 10.13.1+
- Only supports Nutanix G4 and above, Dell, HPE, and Lenovo Cascade Lake and above

Complete instructions for setting up Portable Foundation can be found `here <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-cluster-environment-foundation-t.html>`_

Foundation VM
.............

- The Foundation VM needs to be deployed as a VM on VirtualBox, VMware Fusion, Workstation, etc.
- Supports all NX, OEM, and software only HCL models

Complete instructions for setting up the standalone Foundation VM can be found `here <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-portable-foundation-app-c.html>`_

Cabling Your Hardware
+++++++++++++++++++++

Foundation requires connectivity to **both** the standard network interface of a node and the Baseboard Management Controller (BMC) network interface. The BMC, called **IPMI** on Nutanix NX nodes, is a dedicated system present in every enterprise server platform used for out of band management. Other supported platforms use different names for IPMI, such as iDRAC on Dell, IMM on Lenovo, and iLO on HPE.

Referring to the example diagram below, there are two options for cabling Nutanix nodes prior to imaging with Foundation:

- Using two cables per node, one connected to either onboard LAN port and one connected to the dedicated IPMI port.
- Using one cable per node, connected to the **Shared IPMI** port. With only the **Shared** port connected, it is capable of intelligently forwarding traffic to either the IPMI or LAN interface, allowing Foundation to communicate with both interfaces simultaneously.

.. figure:: images/back-panel.png

.. note::

  During node power off/on the Shared port on certain platforms may switch between 100Mb and 1Gb speeds, which can cause issues if your switch cannot auto-negotiate to the proper speed.

  Additionally, for nodes such as the NX-3175 which only have 10Gb SFP+ onboard NICs, the 1Gb transceiver used to connect to your flat switch requires electrical power. That power is only available when the node is powered on, making it critical to use two cables per node in this situation.

  Overall, if there are sufficient cables and ports available, using two cables per node is preferred.

Both the nodes and the host used to run the Foundation VM should be connected to the same flat switch. If imaging on a customer switch, ensure that any ports used are configured as **Access** or **Untagged**, or that a **Native** VLAN has been configured.

Refer to the appropriate `manufacturer's hardware documentation <https://portal.nutanix.com/#/page/docs/list?type=hardware>`_ to determine the locations of the **IPMI** and **Shared** ports.

Creating Install Configuration File
+++++++++++++++++++++++++++++++++++

To save time entering IP/MAC Address information when on-site with the customer, you can pre-populate and export the configuration as a JSON file using `this tool <https://install.nutanix.com>`_.

Imaging Your Cluster
++++++++++++++++++++

Complete instructions for using Foundation to perform a baremetal installation can be found `here <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-foundation-configure-nodes-with-foundation-t.html>`_.

If you do not have access to a physical block, and wish to practice using Foundation, and can do so with a HPOC reservation and the :ref:`diyfoundation_lab` lab.
