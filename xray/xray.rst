.. _xray:

-----
X-RAY
-----

Deploying X-Ray
+++++++++++++++

X-Ray is an automated testing framework and benchmarking application for enterprise-grade datacenters. The X-Ray application is a downloadable virtual machine (VM) with a user interface and complete documentation. Once installed, X-Ray can test and analyze several different systems and report comparable information for your use.

X-Ray provides test scenarios for hyperconverged platforms that demonstrate variations in areas such as performance, data integrity, and availability.

This guide will step you through installing and configuring X-Ray on AHV, using a single VLAN. Please refer to the `X-Ray Guide <https://portal.nutanix.com/page/documents/details/?targetId=X-Ray-Guide-v3_8%3AX-Ray-Guide-v3_8>`_ for detailed information on other configurations.

Prerequisites and Requirements
==============================

POINT TO INITIAL CLUSTER CONFIG SECTION

.. note::

   It is not recommended to install the X-Ray VM on a cluster that you are using as an X-Ray test target, or on a cluster that you are planning to use as an X-Ray test target.

To deploy the X-Ray, be sure to have the minimum following requirements available:
   vCPU  - 8
   RAM   - 8GB
   Disk  - 100GB
   Nutanix provides a QCOW2 X-Ray image for AHV.

Installation and configuration of X-RAY
=======================================

#. Log into Prism. Click :fa:`gear`. In the sidebar that appears, select **Image Configuration > Upload Image**.

   .. figure:: images/1.png
       :align: left

   .. figure:: images/2.png
       :align: right

#. Give the image a name in the *Name* field, set the image type to **DISK**, and specify the upload location.
   - If you are retrieving the image directly from the link provided by Nutanix, enter the URL in the *From URL* field.
   - If you have saved the image on your own system and want to upload it from there, select the **Upload a file** option and click **Choose File** to specify the image location.

   .. figure:: images/3a.png
       :align: left

   .. figure:: images/3b.png
       :align: right

#. Click **Save** to upload the image.

#. From the Prism dropdown, select **VM**.

#. Click Create VM. In the *Create VM* dialog box, specify a VM name, the number of virtual CPUs, and the memory size.

   .. figure:: images/4.png

#. Scroll further down in the *Create VM* dialog box, and click **Add New Disk** to open the *Add Disk* dialog box.

   .. figure:: images/5.png

#. Set the *Operation* field to **Clone from Image Service**.

#. In the *Image* field, specify the X-Ray image you just created.

#. Click **Add** to add the disk, and return to the Create VM dialog box.

   .. figure:: images/6.png

#. Scroll further down in the *Create VM* dialog box and click **Add New NIC** to open the *Create NIC* dialog box.

   .. figure:: images/7.png

#. Specify the NIC properties and click **Add** to create the NIC and return to the *Create VM* dialog box.

   .. figure:: images/8.png

#. Repeat the preceeding two steps to define a second interface on the X-Ray VM. The second interface controls zero-configuration access from the X-Ray VM to the test VMs. For zero-configuration purposes, configure the second interface to use the same *Primary* VLAN. Click **Save** to create the VM.

#. In Prism, click **Table** to display a tabular view of all VMs, and highlight the VM you just created.

#. Click **Power On** to start the VM.

   .. figure:: images/9.png

#. Click **Launch Console** to watch the VM start.

.. note::

   If you are using the Primary VLAN within the HPOC environment, the following steps are optional, as all VMs will receive a DHCP address by default.

#. Within the X-Ray VM, click **Application > System Tools > Settings**.

#. Click the :fa:`gear` within the *eth0* section.

   .. figure:: images/10.png

#. Click on the **IPv4** heading.

   .. figure:: images/11.png

#. Under *IPv4 Method* choose **Manual**. Enter the appropriate network information within the **Addresses** and **DNS** sections only. Click **Apply**.

#. You may now exit the console.

Managing X-Ray
+++++++++++++++

Use the X-Ray dashboards to create targets, tests, and examine test results.

Connecting to the X-Ray Interface
=================================

#. Open a browser window and enter the IP address for the X-Ray VM.

#. Agree to the EULA that appears, and (optionally) to collection of Pulse metadata.

#. Log on with your Nutanix credentials.

Creating an X-Ray Test Target
=============================

#. Go to the *Tests* dashboard in X-Ray and click the **View and Run** button on the test you wish to run. Click **+ Create Target** in the upper right.

#. Complete the *General Config* fields.

   - Name: Type the name for the new test target.
   - Manager Type: Click the drop-down and select **Prism**.

#. Complete the *Power Management Configuration* fields.

   - From the *Type* dropdown, choose **IPMI**
   - Enter the password for both *USERNAME* and *PASSWORD* fields.

#. Complete the *Prism Config* fields.

   .. figure:: images/12.png

#. Click **Next**.

#. Once the information on the *Cluster* tab is correct, click **Next**.

   .. figure:: images/13.png

#. Once the information on the *Node* tab is correct, click **Next**. If you are using an NX node, physical or HPOC, ensure the *IPMI TYPE* dropdown displays **SUPERMICRO**.

   .. figure:: images/14.png

#. Click **Run Validation**. This can take up to 10 minutes. Once complete, click **Done**.

   .. figure:: images/15.png
       :align: left

   .. figure:: images/16.png
       :align: right

Executing an X-Ray Test
=======================

The X-Ray test scenarios offer predefined test cases that consist of multiple events and predefined parameters. X-Ray executes scenarios against test targets to produce results for analysis. X-Ray scenarios simulate real-world workloads on test targets. Effective virtualized data center solutions delegate resources so that workloads do not monopolize resources from other workloads. Running different workloads in this manner helps evaluate how multiple workloads interact with one another.

X-Ray uses the open-source Flexible I/O (FIO) benchmark tool to generate an I/O workload. FIO files define the characteristics of the FIO workload. Each FIO file contains defined parameters and job descriptions involved in the file.

The test scenarios simulate Online Transaction Processing (OLTP), Virtual Desktop Infrastructure (VDI), and Decision Support System (DSS) workloads.

To view detailed information about each test scenario, click **View & Run Test** within the *Tests* dashboard to display the details of the selected test.

#. In the *Choose test target* dropdown, choose your cluster.

   .. figure:: images/17.png

#. Review the test requirements in the left pane before proceeding. Modify the entries within *Choose the test variant*. Once finished, click **Run Test**.

#. You will be presented with the following message. Click **View** within it, if you wish to view the test in progress.

   .. figure:: images/18.png

#. Otherwise, click **Results** and then click anywhere within the test entry itself to open the *Results* page for your test.

   .. figure:: images/20.png
       :align: left

   .. figure:: images/19.png
       :align: right

#. For other options, select the check box next to the test and click one of the option buttons.

   - For the raw data, click **Export Raw Results**.
   - To have X-Ray return a report with a description, summary tests results, and high level information about each target in the test, click **Generate Report**.

   .. figure:: images/21.png


Creating Comparisons
====================

Compare the results of multiple tests.

#. In the *Results* dashboard, select two or more sets of results for comparison. The results you select must be from the same test scenario and variant.

   .. figure:: images/22.png

#. Click **Create Comparison**. X-Ray compares the results of the selected tests.

#. Select the **My Comparisons** heading to see a list of all comparisons you have created.

#. To generate a comparison report, click **Generate Report**.

#. To delete the comparison, click **Delete**.
