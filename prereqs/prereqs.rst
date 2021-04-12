.. _prereqs:

--------------
Prerequisites
--------------

In order for an easy POC experience using this guide, it is important to ensure the environment you are testing has met all of the prerequisites. While the guide is applicable to both on-premises and Hosted POC (HPOC) environments, the prerequisites differ slightly.

Choosing Between HPOC and Physical
+++++++++++++++++++++++++++++++++++

The first determination to make is whether to use a Hosted POC cluster or an on-premises Nutanix cluster. The following considerations should be taken into account when making this decision:

- **Customer Impression and Effectiveness of Influencing Buying Decisions** - If a customer is less likely to be swayed by a HPOC or has reservations as to the real-world applicability of an HPOC to their environment an on-site POC may be considered. Phrases to listen for *“Of course it will work in your datacenter”, “How is this different from a demo?”, “Your environment is all pre-configured, correct?”*, etc.
- **Network and Data** - Does the customer need to integrate with any applications on their network or use their own data sets as part of the POC? If so, choose an on-premises POC.
- **Installation and Integration** - Is the installation process part of the POC test plan? If so, an on-premises POC is likely required (although there are ways to demonstrate Foundation within the HPOC).
- **Lead Time** - What is the timeline for beginning the POC? Typically you can secure a HPOC cluster 7 days out from the current date. Sourcing on-premises gear can vary significantly, depending on whether or not there is available gear already in your territory. Begin by asking in your regional Slack channel(s) about available POC blocks and their specs.
- **POC Length** - A HPOC cluster can be reserved for up to 14 days if it is associated with a POC record in Salesforce. On-premises POCs should last for no more than 45 days (although blocks can remain on-site longer if new use cases are identified and a new Salesforce POC record is created).
- **Your Availability** - Juggling many customers requests at once is difficult, and with physical POCs the level of involvement is higher. Outsourcing the success of a POC to the partner is usually not ideal.

General Prerequisites
+++++++++++++++++++++

Ensure the hardware you plan to use for your POC, either physical or HPOC, is not End-of-Life (EOL) or End-of-Maintenance (EOM). AOS upgrades are not supported on EOL or EOM hardware. Please refer to `KB Article #000009466 <https://portal.nutanix.com/page/documents/kbs/details?targetId=kA00e000000CyS2CAK>`_ for more information.

All EULAs **MUST** be accepted by the customer. Previously, customer's had to sign `this <https://docs.google.com/a/nutanix.com/viewer?a=v&pid=sites&srcid=bnV0YW5peC5jb218Y29ycHxneDoyMDIyMTVkOTBiMzA5NGI4>`_ product trial agreement. Now this language is included in our product EULAs.

Licensing
+++++++++

The vast majority of software will allow the operation of it, even without a license. Some are time-limited, after which point those features become disabled. In most cases, it will prevent a warning (i.e. "nag") bar across the top of Prism, and if you have SMTP configured, a daily e-mail will be sent to advise that the cluster is utilizing unlicensed features. That said, there are other features that have a hard requirement to be licensed, otherwise it cannot be enabled. Those features are listed below. You must ensure you license these features before enabling these features. Refer to the `SE Wiki / Proof of Concept (POCs) <https://confluence.eng.nutanix.com:8443/x/yA4sAw>`_ for further details. These instructions will aid you with licensing your cluster to avoid any restrictions or warnings.

   - Software Encryption

HPOC Prerequisites
+++++++++++++++++++

Prerequisites for a Nutanix Hosted POC environment are simple, as you will have a predictable network topology and use a pre-created Domain Controller image.

You'll need the following:

- **Salesforce POC Record** - You'll need an active POC record in Salesforce in order to create a HPOC reservation > 48 hours. See `SE Wiki - Salesforce POC Process <https://confluence.eng.nutanix.com:8443/pages/viewpage.action?pageId=53219016>`_ for complete instruction on how to create a POC record.

- **An HPOC reservation**

   .. note::

      See :ref:`hpoc` for further instruction

On-premises Prerequisites
++++++++++++++++++++++++++

Prerequisites for an on-premises POC are more involved, as we need to ensure the provided networks and Active Directory are suitable. Establishing who will provide components such as network cables early on is critical, as is reviewing the network installation details sheet prior to arriving on-site for the install.

You'll need to verify the following with the customer:

- **Rackspace**

   - 1-2 rack unit (RU) per block, depending on the form factor of the block(s) being utilized.

- **Power**

   - 1-2 power connections per block, 2 required for full redundancy.  This can either be a C-13 to C-14 cable, or a C-13 to NEMA 5-15P and is dependent on the type of Power Distribution Unit (PDU) the customer is intending to use.
   - Sufficient power capacity available.  Ex. NX-3060-G7 has a typical power usage of 1700 Watts.
   - 200-240V power is required to run a 2U4N block from a single power supply

- **1x VLAN**

   - A single network can be used for your CVM/AHV network, as well as user VMs. The customer may have a requirement to segregate UVMs and management interfaces, which would require additional VLANs or physical ports (and related wiring/tranceivers/switch ports).
   - Recommended to be /24 or larger subnet, with no DHCP configured.
   - This network should be routable to/from the customer network and to the Internet.

- **(Optional) 1x X-Ray VLAN**
   - If using X-Ray for load generation or additional testing :ref:`xray`, you will require an additional network.
   - If DHCP is enabled, you'll want a /20 or larger subnet to ensure you have an adequate number of IPs for testing.
   - Ideally, request a network with no DHCP, allowing X-Ray to leverage `Link-local <https://en.wikipedia.org/wiki/Link-local_address>`_ or "Zero Configuration" networking, where the VMs communicate via self-assigned IPv4 addresses.

- **Network**

   - Network switch ports availability per node - (1+) 10 Gb connections (SFP+ or BASE-T), 2 required for full redundancy.  (1) 100/1000 Mb for lights out management (IPMI, ILO, iDRAC).
   - Network cables available per node, ensuring the proper lengths to not only traverse the distance between the node(s) and the network switch(es), but to confirm you aren't exceeding the cable or transceiver specification you are using.  For customers with SFP+ network switches, you may either use TwinAx or fiber cables with SFP+ transceivers on each end. (1+) 10 Gb connections (SFP+ or BASE-T), 2 required for full redundancy.  (1) 100/1000 Mb for lights out management (IPMI, ILO, iDRAC).  Verify with the customer/partner who will be providing the network cables. Nutanix can supply generic TwinAx cables but these will not work will all switch brands (Cisco, HPE, etc.).
   - Network switch configuration - Ensure all network switch ports are properly configured, including VLAN tagging, and that both the ports to be used and VLANs are already created and identified.  Typical installs utilize a single VLAN for CVM, Hypervisor, and user VMs.  However, this should be discussed and agreed upon with the customer prior to install.
   - `Pre-Install Survey <https://docs.google.com/spreadsheets/d/15r8Q1kCIJY4ErwL1CaHHwv4Q7gmCbLOz5IaR51t9se0/edit#gid=8195649>`_ completed *and reviewed* prior to on-site arrival. This spreadsheet outlines required IPs and VLANs for a deployment.

- **Software**

   - Disk images can be downloaded from either the provided Amazon S3 links, or from the Nutanix Portal, directly to the cluster (vs. the two step process of downloading it, then uploading it to the cluster). However, if you know the customer environment has poor or no Internet connectivity, the images can also be downloaded to a local device (USB thumb drive, laptop, or similar) and uploaded to the cluster via Prism or CLI once on-site. It is always recommended to play it safe, and if you are unsure of the customer's capabilities, spend the extra time to download all the necessary files beforehand.

   - To capture the download link on the Nutanix Portal, click the :fa:`ellipsis-v`, and choose **Copy Download Link**. You can then paste that link into locations such as (but not limited to) the **From URL** entry within *Settings > Image Configuration > Create Image > Image Source section* or the **AOS Base Software Binary File** entry within *Settings > Upgrade Software > Update Software > Upload Upgrade Software Binary*. Be aware that these are time-limited links, in addition to the fact that not all links are short enough to be utilized within Prism on Windows (at the time of writing), so this method may be unavailable for certain downloads.

      .. figure:: images/3.png

   - You may also wish to use the CLI method to download images directly to the cluster. One benefit to this method is the ability to chain together multiple download requests in one command using the ampersand (&) to separate each command. This would allow you to execute a single command, and step away while it downloaded, allowing you to perform other tasks, chat with the customer, and so forth. Another benefit, is to save the command(s) for future use, and then only have to spend a few moments adjusting the specific file names within each command, as products receive updated versions. There are examples below for both disk (type=kDiskImage) and ISO (type=kIsoImage) images. Please see `Transferring Virtual Disks to an AHV Cluster <https://portal.nutanix.com/page/documents/kbs/details?targetId=kA032000000TTgPCAW>`_ for further information.

      .. code-block:: bash

         acli image.create Windows2016_05272020.qcow2 source_url=http://10.42.194.11/workshop_staging/Windows2016_05272020.qcow2 container=Era_Storage_pool image_type=kDiskImage &

         acli image.create SQLServer2014SP3.iso source_url=http://10.42.194.11/workshop_staging/SQLServer2014SP3.iso container=Era_Storage_pool image_type=kIsoImage

   - AOS and other products via `Nutanix Portal Downloads <https://portal.nutanix.com/page/downloads/list>`_. Recommend using a previous version of any products included in your POC. For example, AOS 5.18.0.5 vs. 5.18.0.6 (latest, in this example). This will allow you to demonstrate 1-Click upgrades as part of your POC.

   - To download an older or specific version you can navigate to `Nutanix Portal Downloads <https://portal.nutanix.com/page/downloads/list>`_ select AOS or AHV, then select the *Other Versions* tab and the corresponding version of software that you wish to use.

   - `(Optional) AutoAD <https://get-ahv-images.s3.amazonaws.com/AutoAD.qcow2>`_ (~10GB)
   - `CentOS <https://get-ahv-images.s3.amazonaws.com/CentOS7.qcow2>`_ (~1.5GB)
   - `Windows Server 2016 <https://get-ahv-images.s3.amazonaws.com/Windows2016.qcow2>`_ (~20GB)

   .. note::

   Future versions of this guide will provide instruction on creating your own CentOS and Windows Server 2016 images if the customer is uncomfortable using the existing disk images for security purposes.

- **4+ Nutanix nodes**

   - See `SE Wiki - Salesforce POC Process <https://confluence.eng.nutanix.com:8443/pages/viewpage.action?pageId=53219016>`_ for complete instruction on how to obtain physical POC hardware.

- **SE Installation Hardware** - Performing an on-premises Foundation requires, at a minimum, network connectivity between your Foundation app/VM and the block. The following are recommended parts of every SE's install "kit":

   - **16+ Port Switch** - Flat/unmanaged switches avoid any potential configuration issues (disabled IPv6, etc.) that could negatively impact Foundation. This hardware can be requested directly from Nutanix IT.
   - **Ethernet Cables** - 2x cables per node being imaged, PLUS a single, long cable for connecting your laptop to the switch. This hardware can be requested directly from Nutanix IT.
   - **Compact Power Strip** - To plug in your laptop and your flat switch. *No one wants their laptop going to sleep mid-Foundation!*
   - **PDU Power Plug Adapter** - `Allowing you to connect your compact power strip to the rack PDU, which likely will not have standard outlets. <https://www.sfcable.com/nema-5-15r-to-c14-power-plug-adapter.html>`_ *Country specific*.
   - **(Optional) SFP to 1000Base-T Adapter** - `These <https://www.amazon.com/Cable-Matters-1000BASE-T-Transceiver-Supermicro/dp/B07TXRYJGF/ref=sr_1_3?dchild=1&keywords=sfp+ethernet+adapter&qid=1594907341&sr=8-3>`_ are only required when using Foundation on a node type without built-in Base-T/RJ45 NICs.

- **Active Directory** - You'll need to provide AD using one of these approaches:

   - **(Recommended)** Use the pre-created **AutoAD** disk image
   - If a customer requires integration with their own AD, you'll need:

      - Verify the minimum AD Forest Level is Windows Server 2008 R2 or newer
      - A Domain Admin account for Prism Element/Prism Central integration
      - 4x Security Groups, each with 1 or more users, that can map to the following roles:
         - Admin
         - Developer
         - Operator
         - Consumer



.. _ntnxlab:

NTNXLAB.local Details
+++++++++++++++++++++

The NTNXLAB.local domain provided by the **AutoAD** VM is pre-populated with the following Security Groups and User Accounts:

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - Security Group
     - Username(s)
     - Password
   * - Administrators
     - Administrator
     - nutanix/4u
   * - SSP Admins
     - adminuser01-adminuser25
     - nutanix/4u
   * - SSP Developers
     - devuser01-devuser25
     - nutanix/4u
   * - SSP Consumers
     - consumer01-consumer25
     - nutanix/4u
   * - SSP Operators
     - operator01-operator25
     - nutanix/4u
   * - SSP Custom
     - custom01-custom25
     - nutanix/4u
   * - Bootcamp Users
     - user01-user25
     - nutanix/4u
