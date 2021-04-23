.. _diyfoundation_lab:

-------------------------
(Practice) Foundation Lab
-------------------------

This module is provided for the sole purposes of allowing you to PRACTICE using Foundation using an HPOC cluster if you are unable to readily obtain a physical cluster with which to practice.

**Expected Module Duration:** 120 minutes

This lab requires a Nutanix Hosted POC (HPOC) reservation. "Nested" or "Single Node" clusters are not supported. Hypervisor and AOS version for the reservation do not matter.

It is highly recommended to use a virtual desktop provided as part of your HPOC reservation to perform this lab. Using the virtual desktop will localize the transfer of large files (such as AOS and hypervisor binaries) to your Foundation VM.

Unless otherwise directed by support, always use the latest version of Foundation.

Foundation is used to automate the installation of the hypervisor and Controller VM on one or more nodes. In this exercise you will practice imaging a physical cluster with Foundation. In order to keep the lab self-contained, you will create a single node "cluster" on which you will deploy your Foundation VM. That Foundation instance will be used to image and create a cluster from the remaining 3 nodes in the Block.

Refer to the Nutanix `Field Installation Guide <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-0:Field-Installation-Guide-v4-0>`_ for additional instruction on configuring your local environment (Oracle VirtualBox, VMware Fusion, etc.) to run your Foundation VM.

.. note::

   Additional, helpful resources related to Foundation:

   - `Field Installation Guide <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-v4-3:Field-Installation-Guide-v4-3>`_ - *Comprehensive Foundation documentation, including steps for local deployment of Foundation VM.*
   - `Foundation Release Notes <https://portal.nutanix.com/#/page/docs/details?targetId=Field-Installation-Guide-Rls-Notes-v4-3:Field-Installation-Guide-Rls-Notes-v4-3>`_ - *Fixes, known issues, enhancements, and upgrade information.*
   - `NX Hardware System Specifications <https://portal.nutanix.com/#/page/docs/list?type=hardware>`_ - *Helpful for determining LAN/IPMI/Shared ports for different hardware platforms.*
   - `Foundation binaries and related files <https://portal.nutanix.com/page/downloads?product=foundation>`_ - *Downloads for baremetal Foundation, CVM Foundation, and ISO whitelist.*
   - `KB2430 <https://portal.nutanix.com/#/page/kbs/details?targetId=kA032000000TT1HCAW>`_ - *Internal Only KB detailing how to download old versions of AOS/AHV that are no longer available on Nutanix Portal.*
   - `vSphere Administration Guide for Acropolis <https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v5_17:vSphere-Admin6-AOS-v5_17>`_ - *Includes post-install steps for configuring HA/DRS on vSphere.*

Staging Your Environment
++++++++++++++++++++++++

A Hosted POC reservation provides a fully imaged cluster consisting of 4 nodes. To keep the lab self-contained within a single, physical block, you will:

- Destroy the existing cluster
- Create a single node "cluster" using Node A
- Install the Foundation VM on Node A
- Use Foundation to image Nodes B, C, and D and create a 3 node cluster

Using an SSH client, connect to the **Node A CVM IP** (e.g. 10.21.\ *XXX*\ .29) in your assigned block using the following credentials:

- **Username** - nutanix
- **Password** - *<HPOC Cluster Password>*

Execute the following commands to power off any running VMs on the cluster, stop cluster services, and destroy the existing cluster:

.. code-block:: bash

  acli -y vm.off \*
  cluster stop        # Enter 'Y' when prompted to proceed
  cluster destroy     # Enter 'Y' when prompted to proceed

Replacing the **Node A CVM IP**, **Name Server IP**, **NTP server** and execute the following to manually create the cluster:

.. code-block:: bash

  cluster -s <NODE-A-CVM-IP> redundancy_factor=1 --cluster_name=NHTLab --dns_servers=<NAME-SERVER-IP> --ntp_servers=<NTP-SERVER> create

.. note::

  The above command will create a "cluster" from a single node using RF1, offering no redundancy to recover from hardware failure. This configuration is being used for non-production, instructional purposes and should **NEVER** be used for a customer deployment.

  After the "cluster" is created, Prism will reflect Critical Health status due to lack of redundancy.

  .. figure:: images/0.png

Open `https://<NODE-A-CVM-IP>` in your browser and log in with the following credentials:

- **Username** - admin
- **Password** - Nutanix/4u (or in the case of HPOC, your HPOC reservation password)

Provide a new **admin** password that conforms to the displayed password policy. Log in as **admin** using your new password.

Accept the EULA and Pulse prompts.

Troubleshooting
...............

If you run into an issue where the cluster and/or node is in a state where you can't destroy or create a cluster, refer to `Forcibly Destroying the Cluster Using the .node_unconfigure File <https://portal.nutanix.com/page/documents/kbs/details?targetId=kA032000000CietCAC>`_

Installing Foundation
+++++++++++++++++++++

In **Prism**, click :fa:`cog` **> Image Configuration > + Upload Image**.

Fill out the following fields and click **Save**:

- **Name** - Foundation
- **Image Type** - Disk
- **Storage Container** - default-container
- Select **From File**
- **Image Source** - Download the latest version from the `Nutanix Portal - Foundation Downloads <https://portal.nutanix.com/page/downloads?product=foundation>`_.

.. note::

  At the time of writing, Foundation 4.5.4.2 is the latest available version. The URL for the latest Foundation VM QCOW2 image can be downloaded from the `Nutanix Portal - Foundation Downloads <https://portal.nutanix.com/page/downloads?product=foundation>`_.

  **Unless otherwise directed by support, always use the latest version of Foundation.**

After the image creation process completes, browse to **Prism > VM > Table** and click **Network Config**.

Before creating the VM, we must first create a virtual network to assign to the Foundation VM. The network will use the Native VLAN assigned to the physical uplinks for all 4 nodes in the block.

Click **Virtual Networks > Create Network**.

Fill out the following fields and click **Save**:

- **Name** - Primary
- **VLAN ID** - 0

In **Prism > VM > Table** and click **+ Create VM**.

Fill out the following fields and click **Save**:

- **Name** - Foundation
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Foundation
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - Select **Add**

Select your **Foundation** VM and click **Power on**.

Once the VM has started, click **Launch Console**.

Once the VM has finished booting, click **nutanix**. Enter the default password and click **Log In**.

.. figure:: images/1.png

Double-click **set_foundation_ip_address > Run in Terminal**.

Select **Device configuration** and press **Return**.

.. figure:: images/2.png

Select **eth0** and press **Return**.

.. figure:: images/3.png

.. note:: Use the arrow keys to navigate between menu items.

Replacing the octet(s) that correspond to your HPOC network, fill out the following fields, select **OK** and press **Return**:

- **Use DHCP** - Press **Space** to de-select
- **Static IP** - 10.42.\ *XXX*\ .41
- **Netmask** - 255.255.255.128
- **Gateway** - 10.42.\ *XXX*\ .1

.. figure:: images/4.png

.. note::

  The Foundation VM IP address should be in the same subnet as the target IP range for the CVM/hypervisor of the nodes being imaged. As Foundation is typically performed on a flat switch and not on a production network, the Foundation IP can generally be any IP in the target subnet that doesn't conflict with the CVM/hypervisor/IPMI IP of a targeted node.

Select **Save** and press **Return**.

.. figure:: images/5.png

Select **Save & Quit** and press **Return**.

.. figure:: images/6.png

Running Foundation
++++++++++++++++++

From within the Foundation VM console, launch **Nutanix Foundation** from the desktop.

.. note::

  Foundation can be accessed via any browser at `http://<Foundation-VM-IP>:8000/gui/index.html`

On the **Start** page, fill out the following fields, replacing the octet(s) that correspond to your HPOC network:

- **Select which network to use for this installer** - eth0
- **Select your hardware platform** - Nutanix
- **Will your production switch do link aggregation?** - No
- **Will your production switch have VLANs** - No
- **Netmask of Every Host and CVM** - 255.255.255.128
- **Gateway of Every Host and CVM** - 10.42.\ *XXX*\ .1
- **Netmask of Every IPMI** - 255.255.255.128
- **Gateway of Every IPMI** - 10.42.\ *XXX*\ .1

.. note::

  Foundation node/cluster settings can optionally be pre-configured using `https://install.nutanix.com` and imported from the **Start** page. This will not be done as part of the lab.

.. note::

  When imaging a cluster with Foundation, the CVMs and hypervisor management IP addresses must be in the same subnet. IPMI IP addresses can be in the same, or different, subnet. If IPMI will not be in the same subnet as CVM/hypervisor, Foundation can use different IP addresses for IPMI and CVM/hypervisor while on a flat, L2 network by clicking **Assign two IP addresses to this installer**.

Click **Next**.

As the remaining nodes do not currently belong to a cluster and are on the same L2 IPv6 broadcast domain as Node A, Nodes B, C, and D will be automatically discovered.

Replacing the octet(s) that correspond to your HPOC network, fill out the following fields and select **Next**:

- **IPMI IP** - 10.42.\ *XXX*\ .34
- **Hypervisor IP** - 10.42.\ *XXX*\ .26
- **CVM IP** - 10.42.\ *XXX*\ .30
- **Node B Hypervisor Hostname** - POC\ *XXX*\ -2
- **Node C Hypervisor Hostname** - POC\ *XXX*\ -3
- **Node D Hypervisor Hostname** - POC\ *XXX*\ -4

.. figure:: images/10.png

.. note::

  Use **Tools > Range Autofill** to quickly specify Node IPs. Specify the first IP in the field at the top of the table to provide enumerated values for the entire column.

Fill out the following fields and click **Next**:

- **Cluster Name** - Test-Cluster

  *Cluster Name is a "friendly" name that can be easily changed post-installation. It is common to create a DNS A record of the Cluster Name that points to the Cluster Virtual IP.*
- **Timezone of Every CVM** - America/Los_Angeles
- **Cluster Redundancy Factor** - RF2

  *Redundancy Factor 2 requires a minimum of 3 nodes, Redundancy Factor 3 requires a minimum of 5 nodes. Cluster creation during Foundation will fail if the appropriate minimum is not met.*
- **Cluster Virtual IP** - 10.42.\ *XXX*\ .37

  *Cluster Virtual IP needs to be within the same subnet as the CVM/hypervisor.*
- **DNS Servers of Every CVM and Host** - 10.42.196.10
- **NTP Servers of Every CVM** - 10.42.196.10

  *DNS and NTP servers should be captured as part of install planning with the customer.*
- **vRAM Allocation for Every CVM, in Gigabytes** - 32

  *Refer to AOS Release Notes > Controller VM Memory Configurations for guidance on CVM Memory Allocation.*

.. figure:: images/11.png

Download your desired AOS package from the `Nutanix Portal <https://portal.nutanix.com/#/page/releases/nosDetails>`_.

By default, Foundation does not have any AOS or hypervisor images. To upload AOS or hypervisor files, click **Manage AOS Files**.

.. figure:: images/14.png

.. note::

  If downloading the AOS package within the Foundation VM, the .tar.gz package can also be moved to ~/foundation/nos rather than uploaded to Foundation through the web UI. After moving the package into the proper directory, click **Manage AOS Files > Refresh**.

Click **+ Add > Choose File**. Select your downloaded *nutanix_installer_package-release-\*.tar.gz* file and click **Upload**.

.. figure:: images/15.png

After the upload completes, click **Close**. Click **Next**.

.. figure:: images/16.png

Select a target hypervisor:

- :ref:`diyfoundation_lab_ahv`
- :ref:`diyfoundation_lab_vsphere`
- :ref:`diyfoundation_lab_hyperv`

--------------------------------------------------------------

.. _diyfoundation_lab_ahv:

Using AHV
.........

Fill out the following fields and click **Next**:

- **Select a hypervisor installer** - AHV, AHV installer bundled inside the AOS installer

.. figure:: images/17.png

.. note::

  Every AOS release contains a version of AHV bundled with that release.

.. note::

  When selecting an alternate hypervisor (ESXi, Hyper-V, XenServer) you can use this page to upload installation ISO files and, if necessary, modified whitelists.

Continue to :ref:`diyfoundation_lab_posthypervisor`.

.. _diyfoundation_lab_vsphere:

Using vSphere
.............

*Coming soon*

.. _diyfoundation_lab_hyperv:

Using Hyper-V
.............

*Coming soon*

--------------------------------------------------------------

.. _diyfoundation_lab_posthypervisor:

Post-Hypervisor Configuration
.............................

Select **Fill with Nutanix defaults** from the **Tools** dropdown menu to populate the credentials used to access IPMI on each node.

.. figure:: images/18.png

.. note:: When performing a baremetal Foundation in the field, ensure your laptop will not go to sleep due to inactivity.

Click **Start > Proceed** and continue to monitor Foundation progress through the Foundation web console. Click the **Log** link to view the realtime log output from your node.

.. figure:: images/19.png

Foundation will leverage IPMI (or the Out of Band Management standard for the given hardware platform, e.g. iDRAC, iLO, CIMC, etc.) to boot each node to a virtual CD image called Phoenix. The Phoenix image contains what are called "Layout Modules." Layout Modules provide critical hardware information to the installer, allowing Nutanix to support a wide range of hardware configurations (NX, Dell, Lenovo, IBM, Cisco, HPE, Klas, Crystal, etc.).

Phoenix will download the AOS and hypervisor binaries from the Foundation VM. Once Phoenix is booted on each node, Phoenix communicates with Foundation via the node's LAN connection. IPMI is only used for mounting the virtual CD image.

Phoenix will then perform an automated installation of the hypervisor (including any packaged drivers) to the appropriate boot media (SATADOM, SD Card, M.2 SSD) and writes the CVM filesystem to a dedicated partition on the first SSD in the system (NOT on the hypervisor boot media).

After these tasks are completed, the node reboots to the newly installed hypervisor. The hypervisor iterates through the SSDs to find out which SSD has the CVM, and then boots the CVM. Firstboot scripts are run to prepare the hypervisor and CVM on the node, including setting IP information.

When all CVMs are ready, Foundation initiates the cluster creation process.

.. figure:: images/20.png

Open `https://<Cluster-Virtual-IP>:9440` in your browser and log in with the following credentials:

- **Username** - admin
- **Password** - Nutanix/4u

.. figure:: images/21.png
