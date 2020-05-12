.. _foundation:

-------------------
Physical Foundation
-------------------

Introduction... anything!

**What are we going to do in this section?**

Pre-Foundation Discovery
++++++++++++++++++++++++

Cover discovery questions for POC install
  - checklist sheet for IPs
  - Power (cables/outlets)
  - Network (cables/ports/etc.)

 Install media - DO NOT use latest AOS/AHV

Setting Up Your Foundation Environment
++++++++++++++++++++++++++++++++++++++

App vs VM?

Setting Up Foundation VM
........................

Reference to existing field install guide on how to set up your Foundation VM (including relevant tips) - https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-cluster-environment-foundation-t.html

Setting Up Foundation App
........................

Reference to existing field install guide on how to set up your Foundation App (including relevant tips) - https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-portable-foundation-app-c.html

Cabling Your Hardware
+++++++++++++++++++++

.. note::

  The following lab will be performed with a cluster in the Nutanix Hosted POC environment. The information on cabling below is for reference when performing a physical, baremetal Nutanix installation.

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

Note on pre-configuring Foundation using https://install.nutanix.com/

Imaging Your Cluster
++++++++++++++++++++

Reference existing Foundation documentation for CURRENT install procedure - https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-5:v45-foundation-configure-nodes-with-foundation-t.html

Oh hey if you want to practice in the HPOC you can use this lab: :ref:`diyfoundation_lab`.
