.. _mssql_deploy:

------------------------
Microsoft SQL Deployment
------------------------

Traditional database VM deployment resembles the diagram below. The process generally starts with a IT ticket for a database (from Dev, Test, QA, Analytics, etc.). Next, one or more teams will need to deploy the storage resources, and VM(s) required. Once infrastructure is ready, a DBA needs to provision and configure database software. Once provisioned, any best practices and data protection/backup policies need to be applied. Finally the database can be handed over to the end user. That's a lot of handoffs, and the potential for a lot of friction.

.. figure:: images/0.png

For details on deploying Microsoft SQL Server on Nutanix best practices, please refer to `Microsoft SQL Server - Nutanix Best Practices <https://nutanixinc.sharepoint.com/sites/solutions/Solutions%20and%20GSO%20Document%20Library/BP-2015-Microsoft-SQL-Server.pdf>`_

You may wish to download the SQL Server Management Studio (SSMS) installer ahead of time - https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms

This workshop includes detailed instructions to:
   - Deploy a new VM from Windows 2016 or 2019 from clone (created in :ref:`vmmanage` or :ref:`windows_scratch`)
   - Deploy and configure SQL Server 2016

The goal is provide a completely transparent process to install SQL to meet the minumum requirements for Era, while avoiding use of any pre-built images, scripts, or anything that could be considered "black box". This is especially important for POCs being performed in highly-secure environments, where pre-built images or scripts may be forbidden. Everything used here is either publicly available (ex. Microsoft ISO images) and/or in plain text that can be easily reviewed (ex. SQL query file).

.. note::

   It is less time-consuming to use a pre-configured Windows + SQL image, so if that option is available to you, it's recommended to use that method.

`Era User Guide v2.0 <https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Era-User-Guide-v2_0:Nutanix-Era-User-Guide-v2_0>`_

**Prerequisites:** Completion of :ref:`windows_scratch` or :ref:`vmmanage`

**(Optional) Pre-requisite:** N/A

**Expected Module Duration:** 90 minutes

**Covered Test IDs:** N/A

SQL Server 2016 - Manual Deployment
+++++++++++++++++++++++++++++++++++

These instructions will walk you through:

- Cloning a previously-created Windows VM.

- Manually install SQL Server 2016. This VM will act as a master image to create a profile for deploying additional SQL VMs with Nutanix and Microsoft best practices automatically applied by Era.

.. note::

   It is less time-consuming to use a pre-configured Windows + SQL image, so if that option is available to you, it's recommended to use that method.

Upload SQL Server 2016 ISO
..........................

#. Log on to Prism Element, and choose **Settings** from the dropdown.

#. Click on **Image Configuration** from the left-hand side.

#. Click on :fa:`plus` **Upload Image**.

#. Enter the following with the listed fields:

   - **Name** - MSSQL2016

   - **Image Type** - ISO

#. Within *Image Source*, click **Upload a file > Choose File**. Browse to the ISO file for Windows 2016, select it, and click **Open**.

#. Click **Save**.

   .. figure:: images/1.png

Deploying Windows Server 2016
.............................

From Clone
**********

#. Choose **VM** from the dropdown menu.

#. Highlight your base Windows 2016 image (i.e. *Windows2016*), right click, and choose **Clone**.

#. Name the clone. Typically, this should be something that describes both its base operating system, and function (ex. Win16SQL16). The VM name should be fewer than 15 characters to conform to Windows computer name limitations.

#. Perform the following tasks once the clone has been created:

   - Right click on your *Win16SQL16* VM, and choose **Update**.

   - Click the :fa:`eject` icon if the CD-ROM is not already empty.

   - Click the pencil icon next to the CD-ROM.

   - Within the *Operation* dropdown, choose **Clone from image service**.

   - Within the *Image* dropdown, choose **MSSQL2016**. Click **Update**.

   - Select :fa:`plus` **Add New Disk**.

   - **Size** - 100 GiB.

   - Select **Add**.

   - Click **Save**.

.. note::

   We will utilize the additional disk will store the database and log files, as required by Era: "Database files must not exist in the Windows OS boot drive" (i.e. the "C:" drive). It will be presented to Windows as the "E:" drive, outlined in the proceeding steps. Refer to the Era User Guide for a full list of requirements.

From Disk Image
***************

#. Click **Create VM** and fill out the following fields:

   - **Name** - Win16SQL16
   - **vCPUs** - 2
   - **Number of Cores Per vCPU** - 1
   - **Memory** - 4 GiB
   - Click **+ Add New Disk**

      - **Type** - Disk
      - **Operation** - Clone from Image Service
      - **Bus Type** - SCSI
      - **Image** - Windows2016.qcow2
      - Click **Add**

      - **Type** - Disk
      - **Operation** - Allocate on Storage Container
      - **Bus Type** - SCSI
      - **Size (GiB)** - 100
      - Click **Add**

      - **Type** - CD-ROM
      - **Operation** - Clone from Image Service
      - **Bus Type** - IDE
      - **Image** - MSSQL2016
      - Click **Add**

   - Click **+ Add New NIC**

      - **Network Name** - Primary
      - Click **Add**

   - Click **Save**

Configuring Windows Server 2016
...............................

#. Right click the *Win16SQL16* VM, and select **Power On**.

#. Once powered on, right click the VM, and select **Launch Console**.

#. Click **Next > Accept**.

#. Use **nutanix/4u** for both the *Password* and *Reenter Password* fields. Click **OK**.

#. Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

#. Rename the computer.

   - Open *Server Manager* and select **Local Server**.

   - Click on the link to the right of *Computer Name* (ex. WIN-O74HDA2JLG0)

   - Click **Change**.

   - Enter the same name you chose for the VM within the *Computer Name* field (ex. Win16SQL16). Click **OK > OK > Close > Restart Now**.

#. Join the domain.

   - Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

   - Open *Server Manager* and select **Local Server**.

   - Click on the link to the right of *Computer Name* (ex. `Win16SQL16`)

   - Click **Change**.

   - Under *Member of* select **Domain:**. Enter the domain name within the **Domain:** field (ex. ntnxlab.local).

   - Enter your domain administrator credentials. For the *ntnxlab.local* domain, enter **Administrator** for the username, and **nutanix/4u** for the password.

   - Click **OK > OK > Close > Restart Now**.

#. Disable Windows Firewall for all networks.

   - Log in to the VM using the *DOMAIN* Administrator username (i.e. ntnxlab.local\\administrator or administrator@ntnxlab.local), and *nutanix/4u* password.

   - Open *Server Manager* and select **Local Server**.

   - Within the *Windows Firewall* entry, click on the **Domain: On** link.

   - In the left pane, click on **Turn Windows Firewall on or off**.

   - Under *Domain network settings*, *Private network settings* and *Public network settings*, click on the bullets for **Turn off Windows Firewall (not recommended)**.

   - Click **OK** and close the *Windows Firewall* window.

#. Enable Remote Desktop.

   - Open *Server Manager* and select **Local Server**.

   - Click on the **Disabled** link to the right of *Remote Desktop*.

      .. figure:: images/3.png

   - Within the *Remote Desktop* section, select **Allow remote connections to this computer**. Click **OK**. Click the box for **Allow connections only from computers running Remote Desktop with Network Level Authentication** to successfully connect to your VM via RDP. Click **OK**.

      .. figure:: images/3b.png

#. Close the console.

#. Using Prism, determine the IP address of your *Win16SQL16* VM.

#. Remote Desktop into your *Win16SQL16* VM using the *Domain* Administrator (i.e. ntnxlab.local\\administrator or administrator@ntnxlab.local), and *nutanix/4u* password.

#. Close *Server Manager*.

#. Open **Computer Management** and then select **Disk Management** perform the following disk operations:

   - Mark **Disk 1** online by right clicking on *Disk 1* and choosing **Online**.

   - Initialize the new disk by right clicking on *Disk 1* and choosing **Initialize**. Click **OK**.

   - Create a new simple volume (ex. **E:**) by right clicking on the unallocated space, and choose **New Simple Volume**. Click **Next > Next > Choose E from the dropdown > Next > Next > Finish**

   .. raw:: html

      <video controls src="_static/video/diskoperations3.mp4"></video>

   - Verify your new volume has a drive letter assigned (ex. E:), and is present within *File Explorer*. If it does not, within the *Disk Management* window, right click on the volume, and choose **Change Drive Letter and Paths...**. Click **Add**. Choose a drive letter (ex. E:). Click **OK > OK**, and then close the *Disk Management* window.

#. Within **File Explorer**, note the current disk configuration.

   .. note::

      Best practices for database VMs involve spreading the OS, SQL binaries, databases, TempDB, and logs into their own separate disks in order to maximize performance. In the interest of simplicity and brevity, we are not following all of these recommendations in this workshop, only the minimum necessary to meet Era's requirements to proceed.

      For complete details for running SQL Server on Nutanix (including guidance around NUMA, hyperthreading, SQL Server configuration settings, and more), see the `Nutanix Microsoft SQL Server Best Practices Guide <https://portal.nutanix.com/#/page/solutions/details?targetId=BP-2015-Microsoft-SQL-Server:BP-2015-Microsoft-SQL-Server>`_.

Deploy and configure Windows Server 2019 from clone
...................................................

This process is almost exactly the same as on Windows 2016, with one notable difference to be aware of:

- Disable Windows Firewall for all networks.

   - Log in to the VM using the *DOMAIN* Administrator username (i.e. ntnxlab.local\\administrator or administrator@ntnxlab.local), and *nutanix/4u* password.

   - Open *Server Manager* and select **Local Server**.

   - Within the *Windows Firewall* entry, click on the **Domain: On** link.

   - Click on **Domain Network**. Under *Windows Defender Firewall* move the selector to **Off**. Click :fa:`arrow-left`

   - Repeat the same process for *Private* and *Public* networks.

   - Close the *Windows Security* window.

SQL Server 2016 Installation (Windows 2016)
...........................................

#. Remote Desktop into your *Win16SQL16* VM using the *DOMAIN* Administrator (i.e. ntnxlab.local\\administrator or administrator@ntnxlab.local), and *nutanix/4u* password.

#. Download `this <https://github.com/nutanixworkshops/EraWithMSSQL/raw/master/deploy_mssql_era/FiestaDB-MSSQL.sql>`_ file to the desktop of your *Win16SQL16* VM. We will be using this file in proceeding steps to populate the database we create with data. Recommend using Chrome as the browser, as it allows you to **right click > Save As...**, whereas Internet Explorer does not. Choose **All Files** in the file type dropdown, otherwise you may inadvertantly save the file as *.txt* instead of *.sql*, preventing you from running it as a script from within *SQL Server Management Tools*.

#. Open **File Explorer** and double-click on the CD-ROM drive letter containing the SQL 2016 ISO, or open the CD-ROM drive, and double-click on **SETUP.EXE**. This will begin the SQL 2016 installation.

#. Click on **Installation > New SQL Server stand-alone installation or add features to an existing installation**.

#. Click **Next** on the *Product Key* page to use the *Evaluation* edition.

#. Click the **I accept the license terms.** check box on the *License Terms* page, and click **Next**.

#. Click **Next** on the *Microsoft Update* page.

#. Click the **Database Engine Services** box within the *Instance Features* section on the *Feature Selection* page, and click **Next**.

#. Click **Next** on the *Instance Configuration* page.

#. Click **Next** on the *Server Configuration* page.

#. Click **Add Current User** within the *Specify SQL Server administrators* of the *Database Engine Configuration* page, and click **Next**.

#. Click **Install** on the *Ready to Install* page.

The installation process should take approximately 5 minutes.

#. Click **Close** on the *Complete* page.

#. Install SQL Server Management Tools by clicking on **Install SQL Server Management Tools** within the *SQL Server Installation Center* window, if you haven't already downloaded it. This will redirect you to the Microsoft website to download the latest version.

#. Click **Install**. This process will take approximately 5-10 minutes. While you are waiting, you can proceed to the next step.

#. Open **File Explorer > This PC**. Click on your additional drive letter (ex. E:\), and create two folders: **databases** and **logs**.

#. Wait until the *SQL Server Management Tools* install is complete, then click **Restart**.

#. Remote Desktop into your *Win16SQL16* VM using the *DOMAIN* Administrator (i.e. ntnxlab.local\\administrator or administrator@ntnxlab.local), and *nutanix/4u* password.

#. Launch **SQL Server Management Studio**.

#. Click **Connect**.

#. Verify the database server is available, with only system databases provisioned.

   .. figure:: images/4.png

#. Create a new SQL database by performing the following:

   - Right click on **Databases** and choose **New Database**.

   - Enter **Fiesta** in the *Database name* field.

   - Scroll to the right, and select :fa:`ellipsis-h` within the *Path* section for the *Fiesta* entry. Browse to the *databases* directory within the secondary drive (ex. E:\). Click **OK**.

   - Scroll to the right, and select :fa:`ellipsis-h` within the *Path* section for the *FiestaDB_log* entry. Browse to the *logs* directory within the secondary drive (ex. E:\). Click **OK**.

   .. figure:: images/5.png

   - Click **OK**.

#. Close the Remote Desktop session.

*You have now successfully install Microsoft SQL Server. We will utilize this in proceeding modules with Era.*
