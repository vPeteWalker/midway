.. _xray:

-----
X-RAY
-----

X-Ray is an automated testing framework and benchmarking application for enterprise-grade datacenters. The X-Ray application is a downloadable virtual machine (VM) with a user interface and complete documentation. Once installed, X-Ray can test and analyze several different systems and report comparable information for your use.

X-Ray provides test scenarios for hyperconverged platforms that demonstrate variations in areas such as performance, data integrity, and availability.

This guide will step you through installing and configuring X-Ray on AHV, using a single VLAN. Please refer to the `X-Ray Guide <https://portal.nutanix.com/page/documents/details/?targetId=X-Ray-Guide-v3_8%3AX-Ray-Guide-v3_8>`_ for detailed information on other configurations.

Prerequisites and Requirements
++++++++++++++++++++++++++++++

.. note::

   It is not recommended to install the X-Ray VM on a cluster that you are using as an X-Ray test target, or on a cluster that you are planning to use as an X-Ray test target.

To deploy the X-Ray, be sure to have the minimum following requirements available:
   vCPU  - 8
   RAM   - 8GB
   Disk  - 100GB
   Nutanix provides a QCOW2 X-Ray image for AHV.

Installation and configuration of X-RAY
+++++++++++++++++++++++++++++++++++++++

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

#. Set the *Operation* field to **Clone from Image Service**.

#. In the *Image* field, specify the X-Ray image you just created.

#. Click **Add** to add the disk, and return to the Create VM dialog box.

#. Scroll further down in the *Create VM* dialog box and click **Add New NIC** to open the *Create NIC* dialog box.

#. Specify the NIC properties and click **Add** to create the NIC and return to the *Create VM* dialog box.

#. Define a second interface on the X-Ray VM. The second interface controls zero-configuration access from the X-Ray VM to the test VMs. For zero-configuration purposes, configure the second interface to use the same *Primary* VLAN. Click **Save** to create the VM.

#. In Prism, click **Table** to display a tabular view of all VMs, and highlight the VM you just created.

#. The *Summary* displays the current state of your VM.

#. In the *Summary* toolbar, click **Power On** to start the VM.

#. In the *Summary* toolbar, click **Launch Console** to watch the VM start.

#. The console shows an IP address where you can load the X-Ray UI.
