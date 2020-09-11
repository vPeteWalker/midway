.. _xray:

-------------------
Starting with X-Ray
-------------------

**Prerequisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** Varies depending on tests performed

**Covered Test IDs:** `Core-019, Core-020, VSAN-001, VSAN-002, VSAN-003, VSAN-004, HF-001, HF-002, HF-003, HF-004 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

Before you begin
++++++++++++++++

If you are competing against VSAN or VXRAIL, it is highly recommended that you view this series before beginning a POC where VSAN or VXRAIL is one of your competitors. `MicroLRN: Whiteboard Warriors - Using X-Ray Against VSAN <https://nutanix.mindtickle.com/#/courses/series/1232012167626956869?series=1232012167626956869>`_

If you are in a competitive POC, and performance is one of or perhaps *the* deciding factor, recommend against using workload simulators/perf tests on older, especially hybrid clusters. These type of POCs should be run on the latest generation available, using all-flash configurations only.

Deploying X-Ray
+++++++++++++++

X-Ray is an automated testing framework and benchmarking application for enterprise-grade datacenters. The X-Ray application is a downloadable virtual machine (VM) with a user interface and complete documentation. Once installed, X-Ray can test and analyze several different systems and report comparable information for your use.

X-Ray provides test scenarios for hyperconverged platforms that demonstrate variations in areas such as performance, data integrity, and availability.

This guide will step you through installing and configuring X-Ray on AHV, using a single VLAN. Please refer to the `X-Ray Guide <https://portal.nutanix.com/page/documents/details/?targetId=X-Ray-Guide-v3_8%3AX-Ray-Guide-v3_8>`_ for detailed information on other configurations.

.. note::

   It is not recommended to install the X-Ray VM on cluster that is also an X-Ray test target. This doesn't just apply to performance-related tests, but resiliency and scalability tests as well.

To deploy the X-Ray, be sure to have the minimum following requirements available:

   - vCPU  - 8

   - RAM   - 8GB

   - Disk  - 100GB

   - Nutanix provides a QCOW2 X-Ray image for AHV. This may be downloaded from the Nutanix portal to a local drive (ex. dark site), or directly from the portal via a URL link within Prism.

Installation and configuration of X-Ray
---------------------------------------

#. Create a new VM using the X-Ray disk image, specifying the following values:

   - **Name** - X-Ray

   - **vCPU(s)** - 8

   - **Memory** - 8

#. Define a second NIC on the X-Ray VM. The second interface controls zero-configuration access from the X-Ray VM to the test VMs. For zero-configuration purposes, configure the second interface to use the same *Primary* VLAN.

#. Power on the new X-Ray VM, and open its console.

   .. note::

      The following steps may be optional depending on your individual setup and network. When using an IPAM configuration, all VMs will receive a DHCP address by default.

#. Within the X-Ray VM, click **Application > System Tools > Settings**.

#. Click on **Network** and then the :fa:`gear` within the *eth0* section.

   .. figure:: images/9.png

#. Click on the **IPv4** heading.

   .. figure:: images/10.png

#. Under *IPv4 Method* choose **Manual**. Use the IP listed for this VM via Prism, along with the Name Server listed in your HPOC reservation to populate the **Addresses** and **DNS** sections, and only these sections. Switch the *Automatic* slider in the *DNS* section to **OFF**. Click **Apply**.

   .. figure:: images/10a.png

#. Slide the *Eth0* **Connected** slider to off, and then on again. This will apply the specified network settings to the VM.

#. You may now exit the console.

Connecting to the X-Ray Interface
---------------------------------

#. Open a browser window and enter the IP address for the X-Ray VM.

#. Enter and confirm a password for the local secret store, and click **Enter**. Recommend using **nutanix/4u** for simplicity.

#. Agree to the EULA that appears, and (optionally) to collection of Pulse metadata.

Creating an X-Ray Test Target
+++++++++++++++++++++++++++++

.. note::

   Be aware that you will receive an error if you are attempting to add a cluster as a target if it's not operating normally. For example, during an AHV upgrade you may see an error similar to the below.

   .. figure:: images/22.png

#. You will be presented with the *Tests* dashboard in X-Ray. Click the **View and Run Test** button on the test you wish to run.

#. Click **+ Create Target** in the upper right.

#. Complete the *General Config* fields.

   - **Name**: Type the name for the new test target.

   - **Manager Type**: Click the drop-down and select **Prism** if not already selected.

#. Complete the *Power Management Configuration* fields.

   - From the *Type* dropdown, choose **IPMI**

   - Enter **ADMIN** (all caps) for both *USERNAME* and *PASSWORD* fields.

#. Complete the *Prism Config* fields.

   .. figure:: images/11.png

#. Click **Next**.

#. Once the information on the *Cluster* tab is correct, click **Next**.

   .. figure:: images/12.png

#. Once the information on the *Node* tab is correct, click **Next**. If you are using an NX node, physical or HPOC, ensure the *IPMI TYPE* dropdown displays **SUPERMICRO**.

   .. figure:: images/13.png

#. Click **Run Validation**. This can take up to 10 minutes. Once complete, click **Done**.

   .. figure:: images/14.png
      :align: left

   .. figure:: images/15.png
      :align: right

You've now successfully added a target from which to run X-Ray tests. You may continue to the :ref:`xray1` where you may run a variety of tests, or straight to :ref:`xray3`. Once you've completed either section, you should visit the :ref:`xray2` section.
