.. _prereqs:

--------------
Prerequisites
--------------

In order for an easy POC experience using this guide, it is important to ensure the environment your testing is has met all of the pre-requisites. While the guide is applicable to both on-premises and Hosted POC (HPOC) environments, the pre-requisites differ slightly.

Choosing Between HPOC and Physical
+++++++++++++++++++++++++++++++++++

**FEEDBACK** - Any additional points for HPOC v. on-prem?

The first determination to make is whether to use a Hosted POC cluster or an on-premises Nutanix cluster. The following considerations should be taken into account when making this decision:

- **Network and Data** - Does the customer need to integrate with any applications on their network or use their own data sets as part of the POC? If so, choose an on-premises POC.
- **Installation and Integration** - Is the installation process part of the POC test plan? If so, an on-premises POC is likely required (although there are ways to demonstrate Foundation within the HPOC).
- **Lead Time** - What is the timeline for beginning the POC? Typically you can secure a HPOC cluster 7 days out from the current date. Sourcing on-premises gear can vary significantly, depending on whether or not there is available gear already in your territory. Begin by asking in your regional Slack channel(s) about available POC blocks and their specs.
- **POC Length** - A HPOC cluster can be reserved for up to 14 days if it is associated with a POC record in Salesforce. On-premises POCs should last for no more than 45 days (although blocks can remain on-site longer if new use cases are identified and a new Salesforce POC record is created).

HPOC Prerequisites
+++++++++++++++++++

Pre-requisites for a Nutanix Hosted POC environment are simple, as you will have a predictable network topology and use a pre-created Domain Controller image.

You'll need the following:

- **An HPOC reservation**

   .. note::

      See :ref:`hpoc` for further instruction

- **Disk images**

   - `AutoAD <http://10.42.194.11/workshop_staging/AutoAD.qcow2>`_ (~10GB)
   - `CentOS <http://10.42.194.11/workshop_staging/CentOS7.qcow2>`_ (~1.5GB)
   - `Windows Server 2016 <http://10.42.194.11/workshop_staging/Windows2016.qcow2>`_ (~20GB)

   .. note:: The disk images do not need to be downloaded in advance of the POC, as they can be uploaded directly to the cluster using the links above.

On-premises Prerequisites
++++++++++++++++++++++++++

Pre-requisites for an on-premises POC are more involved, as we need to ensure the provided networks and Active Directory are suitable.

You'll need the following:

- **4+ Nutanix nodes** - See `SE Wiki - Salesforce POC Process <https://confluence.eng.nutanix.com:8443/pages/viewpage.action?pageId=53219016>`_ for complete instruction on how to obtain physical POC hardware.

   .. note::

      See :ref:`foundation` for complete considerations on network, power, and cabling considerations.

- **1x VLAN** - A single network can be used for your CVM/AHV network, as well as user VMs. Recommended to be /24 or larger subnet, with no DHCP configured. This network should be routable to the Internet.

- **(Optional) 1x XRay VLAN** - If using X-Ray for load generation or additional :ref:`xray` testing, you will require an additional network. If DHCP is enabled, you'll want a /20 or larger subnet to ensure you have an adequate number of IPs for testing. Ideally, request a network with no DHCP, allowing X-Ray to leverage `Link-local <https://en.wikipedia.org/wiki/Link-local_address>`_ or "Zero Configuration" networking, where the VMs communicate via self-assigned IPv4 addresses.

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

- **Disk Images**

   - `(Optional) AutoAD <https://get-ahv-images.s3.amazonaws.com/AutoAD.qcow2>`_ (~10GB)
   - `CentOS <https://get-ahv-images.s3.amazonaws.com/CentOS7.qcow2>`_ (~1.5GB)
   - `Windows Server 2016 <https://get-ahv-images.s3.amazonaws.com/Windows2016.qcow2>`_ (~20GB)

   .. note::

   The disk images can be downloaded directly onto the cluster using the provided Amazon S3 links during the POC. However, if you know the customer environment has poor bandwidth or no Internet connectivity, the images can also be downloaded separately and uploaded to the cluster locally via Prism.

   .. note::

   Future versions of this guide will provide instruction on creating your own CentOS and Windows Server 2016 images if the customer is uncomfortable using the existing disk images for security purposes.

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
