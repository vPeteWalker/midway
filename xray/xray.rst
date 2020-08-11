.. _xray:

-----
X-RAY
-----

**Pre-requisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** Varies depending on updates performed

**Covered Test IDs:** `Core-019, Core-020, VSAN-001, VSAN-002, VSAN-003, VSAN-004, HF-001, HF-002, HF-003, HF-004 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

Deploying X-Ray
---------------

X-Ray is an automated testing framework and benchmarking application for enterprise-grade datacenters. The X-Ray application is a downloadable virtual machine (VM) with a user interface and complete documentation. Once installed, X-Ray can test and analyze several different systems and report comparable information for your use.

X-Ray provides test scenarios for hyperconverged platforms that demonstrate variations in areas such as performance, data integrity, and availability.

This guide will step you through installing and configuring X-Ray on AHV, using a single VLAN. Please refer to the `X-Ray Guide <https://portal.nutanix.com/page/documents/details/?targetId=X-Ray-Guide-v3_8%3AX-Ray-Guide-v3_8>`_ for detailed information on other configurations.

.. note::

   It is not recommended to install the X-Ray VM on cluster that is also an X-Ray test target. This applies not only to performance-related tests, but resiliency and scalability tests.

To deploy the X-Ray, be sure to have the minimum following requirements available:

   - vCPU  - 8

   - RAM   - 8GB

   - Disk  - 100GB

   - Nutanix provides a QCOW2 X-Ray image for AHV. This may be downloaded from the Nutanix portal to a local drive (ex. dark site), or directly from the portal via a URL link within Prism.

Installation and configuration of X-Ray
---------------------------------------

#. Log into Prism. Click :fa:`gear`. In the sidebar that appears, select **Image Configuration > Upload Image**.

   .. figure:: images/1.png
      :align: left

   .. figure:: images/2.png
      :align: right

#. Give the image a name in the *Name* field, set the image type to **DISK**, and specify the upload location.

   - If you are retrieving the image directly from the link provided by Nutanix, enter the URL in the *From URL* field.

   - If you have saved the image on your own system and want to upload it from there, select the **Upload a file** option and click **Choose File** to specify the image location.

   .. figure:: images/3.png
      :align: left

#. Click **Save** to upload the image.

#. From the Prism dropdown, select **VM** from the dropdown.

#. Create a new VM using the X-Ray disk image, specifying the following values:

   - **Name** - X-Ray

   - **vCPU(s)** - 8

   - **Memory** - 8

   .. figure:: images/4.png

#. Define a second network interface on the X-Ray VM. The second interface controls zero-configuration access from the X-Ray VM to the test VMs. For zero-configuration purposes, configure the second interface to use the same *Primary* VLAN.

#. Power on the new X-Ray VM, and open its console.

.. note::

   The following steps are optional, as all VMs will receive a DHCP address by default when using an IPAM configuration.

#. Within the X-Ray VM, click **Application > System Tools > Settings**.

#. Click the :fa:`gear` within the *eth0* section.

   .. figure:: images/10.png

#. Click on the **IPv4** heading.

   .. figure:: images/11.png

#. Under *IPv4 Method* choose **Manual**. Enter the appropriate network information within the **Addresses** and **DNS** sections only. Click **Apply**.

#. You may now exit the console.

Connecting to the X-Ray Interface
---------------------------------

#. Open a browser window and enter the IP address for the X-Ray VM.

#. Enter and confirm a password for the local secret store, and click **Enter**. Recommend using **nutanix/4u** for simplicity.

#. Agree to the EULA that appears, and (optionally) to collection of Pulse metadata.

Creating an X-Ray Test Target
-----------------------------

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

Executing an X-Ray Test
-----------------------

The X-Ray test scenarios offer predefined test cases that consist of multiple events and predefined parameters. X-Ray executes scenarios against test targets to produce results for analysis. X-Ray scenarios simulate real-world workloads on test targets. Effective virtualized data center solutions delegate resources so that workloads do not monopolize resources from other workloads. Running different workloads in this manner helps evaluate how multiple workloads interact with one another.

X-Ray uses the open-source Flexible I/O (FIO) benchmark tool to generate an I/O workload. FIO files define the characteristics of the FIO workload. Each FIO file contains defined parameters and job descriptions involved in the file.

The test scenarios simulate Online Transaction Processing (OLTP), Virtual Desktop Infrastructure (VDI), and Decision Support System (DSS) workloads.

To view detailed information about each test scenario, click **View & Run Test** within the *Tests* dashboard to display the details of the selected test.

#. In the *Choose test target* dropdown, choose your cluster.

   .. figure:: images/16.png

#. Review the test requirements in the left pane before proceeding. Modify the entries within *Choose the test variant*. Once finished, click **Run Test**.

#. You will be presented with the following message. Click **View** within it, if you wish to view the test in progress.

   .. figure:: images/17.png

#. Otherwise, click **Results** and then click anywhere within the test entry itself to open the *Results* page for your test.

   .. figure:: images/19.png
      :align: left

   .. figure:: images/18.png
      :align: right

#. For other options, select the check box next to the test and click one of the option buttons.

   - For the raw data, click **Export Raw Results**.

   - To have X-Ray return a report with a description, summary tests results, and high level information about each target in the test, click **Generate Report**.

   .. figure:: images/20.png

Creating Comparisons
--------------------

Compare the results of multiple tests.

#. In the *Results* dashboard, select two or more sets of results for comparison. The results you select must be from the same test scenario and variant.

   .. figure:: images/21.png

#. Click **Create Comparison**. X-Ray compares the results of the selected tests.

#. Select the **My Comparisons** heading to see a list of all comparisons you have created.

#. To generate a comparison report, click **Generate Report**.

#. To delete the comparison, click **Delete**.
