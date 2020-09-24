.. _mssqldeploy:

-------------------
Era - Microsoft SQL
-------------------

Traditional database VM deployment resembles the diagram below. The process generally starts with a IT ticket for a database (from Dev, Test, QA, Analytics, etc.). Next, one or more teams will need to deploy the storage resources, and VM(s) required. Once infrastructure is ready, a DBA needs to provision and configure database software. Once provisioned, any best practices and data protection/backup policies need to be applied. Finally the database can be handed over to the end user. That's a lot of handoffs, and the potential for a lot of friction.

.. figure:: images/0.png

Whereas with Nutanix cluster and Era, provisioning and protecting a database should take you no longer than it took to read this intro. For details on deploying Microsoft SQL Server on Nutanix best practices, please refer to `Microsoft SQL Server - Nutanix Best Practices <https://nutanixinc.sharepoint.com/sites/solutions/Solutions%20and%20GSO%20Document%20Library/BP-2015-Microsoft-SQL-Server.pdf>`_

This workshop includes detailed instructions to:
   - Deploy a new VM from Windows 2016 or 2019 golden image
   - Deploy SQL Server 2016
   - Deploy Era 2.0
   -

The goal is provide a completely transparent process to install both SQL and Era, avoiding use of any pre-built images, scripts, or anything that could be considered "black box". This is especially important for POCs being performed in highly-secure environments, where pre-built images or scripts may be forbidden. Everything used here is either publicly available (ex. Microsoft ISO images) and/or in plain text that can be easily reviewed (ex. SQL query file).

TEMP PLACEHOLDER
++++++++++++++++

Links to different datacenters for:
SQL 2016 image
Era image
SQL Server Management Studio - https://aka.ms/ssmsfullsetup
Windows Scratch prereq

TEST BY REMOVING DNS TO ENSURE NOTHING IS BEING PULLING FROM ANYWHERE BEHIND THE SCENES


https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Era-User-Guide-v1_3:Nutanix-Era-User-Guide-v1_3

https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Era-User-Guide-v2_0:Nutanix-Era-User-Guide-v2_0

SQL Server 2016 - Manual Deployment
+++++++++++++++++++++++++++++++++++

These instructions will walk you through cloning a VM (originally created within the :ref:`_windows_scratch` section), and using it as a base image on which to install SQL Server 2016. You will then manually deploy a SQL Server 2016 VM. This VM will act as a master image to create a profile for deploying additional SQL VMs with Nutanix and Microsoft best practices automatically applied by Era.

Deploy and configure Windows Server 2016 from clone
...................................................

#. From the dropdown within Prism Element, select **VM**. Select the *Table* view, if not already selected.

#. Highlight your base Windows 2016 image, right click and choose **Clone**.

#. Name the clone. This should be something that describes both its base operating system, and function (ex. Win16SQL16). Recommend the VM name is fewer than 15 characters, since we'll be renaming the Windows server to the same name.

#. Perform the following tasks:

   - Right click on your *Win16SQL16* VM, and choose **Update**.

   - Click the :fa:`eject` icon if the CD-ROM is not already empty.

   - Click the :fa:`pencil-alt` icon next to the CD-ROM.

   - Within the *Operation* dropdown, choose **Clone from image service**.

   - Within the *Image* dropdown, choose **MSSQL2016**. Click **Update**.

   - Select **+ Add New Disk**.

   - **Size** - 100 GiB.

   - Select **Add**.

#. Right click the new VM, and select **Power On**.

#. Once powered on, right click the VM, and select **Launch Console**.

#. Click **Next > Accept**.

#. Use *nutanix/4u* for both the **Password** and **Reenter Password** fields. Click **OK**.

#. Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

#. Rename the computer.

   - Open *Server Manager* and select **Local Server**.

   - Click on the link to the right of *Computer Name* (ex. WIN-O74HDA2JLG0)

   - Click **Change**.

   - Enter the same name you chose for the VM within the *Computer Name* field. Click **OK > OK > Close > Restart Now**.

#. (Optional) Join the *ntnxlab.local* domain.

   - Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

   - Open *Server Manager* and select **Local Server**.

   - Click on the link to the right of *Computer Name* (ex. WIN-O74HDA2JLG0)

   - Click **Change**.

   - Under *Member of* select **Domain:**. Enter the domain name within the **Domain:** field (ex. ntnxlab.local).

   - You will be prompted for domain administrator credentials. For the *ntnxlab.local* domain, enter **Administrator** for the username, and **nutanix/4u** for the password.

   - Click **OK > OK > Close > Restart Now**.

#. Disable Windows Firewall for all networks.

   - Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

   - Open *Server Manager* and select **Local Server**.

   - Within the *Windows Firewall* entry, click on **Private: On**. If this is already set to **Private: Off** you may skip this section.

   - In the left pane, click on **Turn Windows Firewall on or off**.

   - Under both *Private network settings* and *Public network settings*, click on the bullets for **Turn off Windows Firewall (not recommended)**.

   - Click **OK** and close the *Windows Firewall* window.

#. Enable Remote Desktop.

   - Open *Server Manager* and select **Local Server**.

   - Click on the **Disabled** link to the right of *Remote Desktop*.

      .. figure:: images/3.png

   - Within the *Remote Desktop* section, select **Allow remote connections to this computer**. Click **OK**. Click the box for **Allow connections only from computers running Remote Desktop with Network Level Authentication** to successfully connect to your VM via RDP. Click **OK**.

      .. figure:: images/3b.png

#. Remote Desktop into your *Win16SQL16* VM using the *Administrator* username.

#. Open **Disk Management** and perform the following disk operations:

   - Mark **Disk 1** online by right clicking on *Disk 1* and choosing **Online**.

   - Initialize the new disk by right clicking on *Disk 1* and choosing **Initialize**.

   - Create a new simple volume (ex. **E:**) by right clicking on the unallocated space, and choose **New Simple Volume**. Click **Next > Next > Choose E from the dropdown > Next > Finish**

   .. raw:: html

      <video controls src="_static/video/diskoperations3.mp4"></video>

   - Verify your new volume has a drive letter assigned (ex. E:), and is present within *File Explorer*. If it does not, right click on the volume, and choose **Change Drive Letter and Paths...**. Click **Add**. Choose a drive letter (ex. E:). Click **OK > OK**, and then close the *Disk Management* window.

#. Within **File Explorer**, note the current disk configuration.

   .. note::

      Best practices for database VMs involve spreading the OS, SQL binaries, databases, TempDB, and logs across separate disks in order to maximize performance. We are specifically not following these recommendations in this workshop so that we may highlight one of the many benefits of Era later on.

      For complete details for running SQL Server on Nutanix (including guidance around NUMA, hyperthreading, SQL Server configuration settings, and more), see the `Nutanix Microsoft SQL Server Best Practices Guide <https://portal.nutanix.com/#/page/solutions/details?targetId=BP-2015-Microsoft-SQL-Server:BP-2015-Microsoft-SQL-Server>`_.

#. Close the console for *Win16SQL16*.

SQL Server 2016 Installation (Windows 2016)
...........................................

#. Within Prism Element, make note of the IP address for your *Win16SQL16* VM.

#. Connect to your *Win16SQL16* VM using Remote Desktop.

#. Download `this <https://github.com/nutanixworkshops/EraWithMSSQL/raw/master/deploy_mssql_era/FiestaDB-MSSQL.sql>`_ file to the desktop of your *Win16SQL16* VM.

#. Open **File Explorer** and double-click on the CD-ROM drive letter containing the SQL 2016 ISO. This will begin the SQL 2016 installation.

#. Click on **Installation > New SQL Server stand-alone installation or add features to an existing installation**.

   .. figure:: images/9.png

#. Click **Next** on the *Product Key* page to use the *Evaluation* edition.

#. Click **I accept the license terms.** on the *License Terms* page, and click **Next**.

#. Check the **Use Microsoft Update to check for updates (recommended)** and click **Next**.

#. Click the **Database Engine Services** box within the *Instance Features* section on the *Feature Selection* page, and click **Next**.

#. Click **Next** on the *Instance Configuration* page.

#. Click **Next** on the *Server Configuration* page.

#. Click **Add Current User** within the *Specify SQL Server administrators* of the *Database Engine Configuration* page. Click **Next**.

#. Click **Install** on the *Ready to Install* page.

The installation process should take approximately 5 minutes. Click **Close** once complete. Close the *SQL Server Installation Center*.

#. Install SQL Server Management Tools by executing **SSMS-Setup-ENU.exe**.

#. Click **Install**. This process will take approximately 5-10 minutes. Click **Restart** once complete.

#. Connect to your *Win16SQL16* VM using Remote Desktop.

#. Open **File Explorer > This PC**. Click on your additional drive letter (ex. E:\), and create two folders: **Databases** and **Logs**.

#. Launch **SQL Server Management Studio 18**.

#. Leave the default *Windows Authentication*, and click **Connect**.

#. Verify the database server is available, with only system databases provisioned.

   .. figure:: images/5.png

#. Add and modify a SQL database by performing the following:

   - Right click on **Databases** and choose **New Database**.

   - Enter **Fiesta** in the *Database name* field.

   - Scroll to the right, and select :fa:`elipsis-h` within the *Path* section for the *Fiesta* entry. Browse to the *databases* directory within the secondary drive (ex. E:\). Click **OK**.

   - Scroll to the right, and select :fa:`elipsis-h` within the *Path* section for the *Fiesta_log* entry. Browse to the *logs* directory within the secondary drive (ex. E:\). Click **OK**.

   .. figure:: images/20.png

   - Click **OK**.

#. Click on **File > Open > File**. Choose the *FiestaDB-MSSQL.sql* file you previously downloaded to the desktop, and click **Open**.

#. Click **Execute**. This will create data within the *Fiesta* database.

   .. figure:: images/era10.png

Installing Era
++++++++++++++

#. Download the **Era Install for AHV** image file from the `Era Downloads section - Nutanix Support Portal <https://portal.nutanix.com/page/downloads?product=era>`_ either click the **Download** button associated with the latest version of *Era Install for AHV*, or the :fa:`elipsis`next to it, and choose **Copy Download Link**.

#. Log on to the *Prism Element* web console.

#. Select *Settings* from the main menu drop-down list, and click **Image Configuration**.

#. Under *Image Configuration*, click **Upload Image**.

#. In the *Create Image* dialog box, do the following in the indicated fields:

   - **Name**. Type a name of the image (ex. Era)

   - **Image Type**. Select **Disk** from the drop-down list.

   - **Storage Container**. Select the **default** storage container to install Era.

   - **Choose one of the following**:

      - Within *Image Source*, click **Upload a file > Choose File**. Browse to the Disk image for Era, and click **Open**.

         .. figure:: images/6.png

      **OR**

      - Click :fa:`dot-circle` **From URL**, and paste the download link you previously copied from the Nutanix Portal.

         .. figure:: images/5.png

         .. note::

            Verify that the *Image Type* is **Disk**.

   - Click **Save**.

   - Wait until the **Create Image** task completes before proceeding.

#. From the main menu drop-down list, select **VM**, and then click **+ Create VM**.

   Do the following in the indicated fields.

   - **Name** Enter a name of the VM.

   - **vCPU(s)** 4

   - **Memory** 16 GiB

#. Click **Add New Disk**.

   Do the following in the indicated fields.

   - **Operation** Select **Clone from Image Service**.

   - **Image** Select the Era image (ex. Era)

   - Click **Add** to attach the disk to the VM and return to the *Create VM* dialog box.

#. To create a network interface for the VM, click **Add New NIC**.

   -  **VLAN Name** Verify **Primary** is selected.

   - (Optional) **IP Address**. Enter an IP address for the VLAN.

   - Click **Add** to create a network interface for the VM and return to the *Create VM* dialog box.

#. (Optional) If you want to assign a static IP address to the Era VM, do the following:

   - Select the **Custom Script** check box.

   - In the *Type or paste script* text box, paste the following script.

   .. code-block:: bash

      #cloud-config
      runcmd:
       - configure_static_ip ip=<STATIC-IP-ADDRESS> gateway=<GATEWAY-ADDRESS> netmask=<NETMASK-IP> nameserver=<NAMESERVER>

   All parameters except the nameserver parameter are mandatory.

#. Click the **Save** button to create the VM.

#. In the *Table* view of the VM dashboard, right click the Era VM, and select **Power On** to start the VM.

#. If you did not set a static IP, determine the IP address assigned to the Era VM from the *IP Addresses* field.

   .. note::

      If you assigned a static IP address to the Era VM on a VLAN that has a DHCP server (ex. the *Primary* VLAN on the HPOC), Prism Element first assigns an IP address to the Era VM by using DHCP. Wait for one or two minutes and refresh the Prism Element page to verify if the static IP address you specified has been assigned to the VM.

Era Configuration
+++++++++++++++++

#. Open `<ERA-VM-IP>` in a new browser tab.

#. Read the *Nutanix End User License Agreement (EULA) agreement*, click the **I have read and agree to terms and conditions option**, and then click **Continue**. In the *Nutanix Customer Experience Program* screen, click **OK**.

#. In the logon screen, set a password for the administrator user (admin) in the *Enter new password* and *Re-enter new password* fields, and click **Set Password**.

#. In the *Era’s Cluster* screen, do the following in the indicated fields:

   - **Name** Type a name of the Nutanix cluster as you want the name to appear in Era.

   - (Optional) **Description** Type a description of the Nutanix cluster.

   - **IP Address of the Prism Element** Type the IP address of the Prism Element VIP.

   - **Prism Element Administrator** Type the user name of the Prism Element user account with which you want Era to access the Nutanix cluster. (ex. admin)

      .. note::

         It is not best practice to use the default administrative account for Era operations. In a production environment, it is therefore recommended to use a separate Prism Element user account with Nutanix cluster administrative privileges as Era service account.

   - **Password** Type the password of the Prism Element user account.

   - Click **Next**.

      .. figure:: images/era1.png

#. In the *Services* screen, do the following in the indicated fields:

#. In the *DNS Servers Addresses* list, ensure that the AutoAD IP address is the only entry in the *DNS Servers* field.

#. In the *NTP Servers Addresses* list, use the same NTP servers you used when configuring Prism.

#. (Optional) Configure the SMTP server.

#. In the *Era Server's OS Time Zone* list, select a timezone.

   .. figure:: images/era2.png

#. Click **Next**. This will validate your settings.

   .. figure:: images/era3.png

#. In the *Storage Container* screen, select the storage container that you want Era to use to provision new databases and database servers. Click **Next**.

   .. figure:: images/era4.png

#. In the *Network Profile* screen, within the *VLAN* section, select the **Primary** VLAN from the drop-down list. Click **Next**.

   .. figure:: images/era5.png

#. In the *Setup* screen, click **Get Started**. The *Getting Started* page describes how to register and provision databases in Era. You can also open the main menu and start using the product.

   .. figure:: images/era6.png

#. In the *Getting Started* screen, select the **Yes** button.

   .. figure:: images/era7.png

(Optional) Configure Windows Domain
...................................

Only proceed with the following if you joined your *Win16SQL16* VM to a domain.

#. From the dropdown, choose **Profiles**.

#. Select **Windows Domain**.

#. Click **Create**.

#. In the *Create Windows Domain Profile* screen, do the following in the indicated fields:

   - **Name** NTNXLAB

   - **Domain to Join (FQDN)** ntnxlab.local

   - **Username** ntnxlab.local\administrators

   - **Password** nutanix/4u

   .. figure:: images/era14.png

#. Click **Create**.

Configure UI Timeout
....................

#. Click on the **admin** dropdown at the top right, and choose **Profile**.

#. Set the *Timeout* setting to **Never**. This will help avoid being logged out unexpectedly during your POC.

Modifying Era VM Network Settings Post-Launch
.............................................

.. note::

   These instructions are taken from the *Assigning A Static IP Address To The Era VM By Using The Console* section of the Era Guide. However, you may utilize any or all of the parameters for the `era-server set` command to accomplish your goal. For example, if you only need to modify the name server that the Era VM is using, you would type `era_server set nameserver=<NAMESERVER-IP>`.

#. Within Prism, right click the Era VM, and click **Launch Console**
.
#. Use the following credentials to log on to Era:

   - **User name**: era
   - **Password**: Nutanix.1

#. Launch the Era server prompt by typing `era-server`.

#. The full command is `era_server set ip=<IP-address> gateway=<GATEWAY-ADDRESS> netmask=<NETMASK-IP> nameserver=<NAMESERVER>`

Registering MSSQL VM
++++++++++++++++++++

Registering a database server with Era allows you to deploy databases to that resource, or to use that resource as the basis for a Software Profile.

A SQL Server database server must meet the following requirements before you are able to register it with Era. Your SQL VM meets all of these criteria.

   - A local user account or a domain user account with administrator privileges on the database server must be provided.
   - Windows account or the SQL login account provided must be a member of sysadmin role.
   - SQL Server instance must be running.
   - Database files must not exist in C:\ Drive.
   - Database must be in an online state.
   - Windows remote management (WinRM) must be enabled.

#. From the dropdown, select **Databases**, then **Sources** from the lefthand menu.

#. Click **+ Register > Microsoft SQL Server > Database**.

   .. figure:: images/era8.png

#. The *Register a SQL Server Database* window appears. In the *Database Server VM* screen, do the following in the indicated fields:

   - Select **Not registered** within *Database is on a Server VM that is:*.

   - **IP Address or Name of VM** Select the VM you created in the *SQL VM Deployment* section.

   - **Windows Administrator Name** Type the user name of the administrator account (ex. Administrator).

   - **Windows Administrator Password** Type the password of the administrator account.

   - **Instance** Era automatically discovers all the instances within a SQL server VM. In our case, there is only one instance named **MSSQLSERVER**.

   - The *Connect to SQL Server Login* and *User Name* fields allow a choice of authentication between Windows Admin, and SQL Server user. Leave the default at **Windows Admin User**, and click **Next**.

#. In the *Database Server VM* screen, select the **Fiesta** database within the *Unregistered Databases* section. Click **Next**.

   .. figure:: images/era11.png

#. In the *Time Machine* screen, choose **DEFAULT_OOB_GOLD_SLA** within the *SLA* field.

   .. figure:: images/era11a.png

#. Click **Register**.

#. In the *Status* column, click **Registering** to monitor the status.

#. The registration process will take approximately 5 minutes. In the meantime, proceed with these steps:

   - From the dropdown menu, select **SLAs**. Era has five built-in SLAs (Gold, Silver, Bronze, Zero, and Brass). SLAs control however the database server is backed up. This can with a combination of Continuous Protection, Daily, Weekly Monthly and Quarterly protection intervals.

   - From the dropdown menu, select **Profiles**.

   Profiles pre-define resources and configurations, making it simple to consistently provision environments and reduce configuration sprawl. For example, Compute Profiles specifiy the size of the database server, including details such as vCPUs, cores per vCPU, and memory.

Creating A Software Profile
+++++++++++++++++++++++++++

Before additional SQL Server VMs can be provisioned, a *Software Profile* must first be created from the SQL server VM registered in the previous step. A software profile is a template that includes the SQL Server database and operating system. This template exists as a hidden, cloned disk image on your Nutanix cluster.

#. From the dropdown, select **Profiles**, and then **Software** from the lefthand menu.

#. Click **+ Create**, and then **Microsoft SQL Server**. Fill out the following fields:

   - **Profile Name** - MSSQL_2016
   - **Database Server** - Select your registered MSSQL VM

   .. figure:: images/14.png

#. Click **Next > Create**.

#. Select **Operations** from the dropdown menu to monitor the registration. This process should take approximately 5 minutes.

#. Once the profile creation completes successfully, return to Prism. Right click your *Win16SQL16* VM, and choose **Power Off Actions > Guest Shutdown**.

Creating a New MSSQL Database Server
++++++++++++++++++++++++++++++++++++

You've completed all the one-time operations required to be able to provision any number of SQL Server VMs. Follow the steps below to provision a new database server, with best practices automatically applied by Era.

#. In **Era**, select **Databases** from the dropdown menu, and then **Sources** from the lefthand menu.

#. Click **+ Provision > Microsoft SQL Server > Database**.

   .. figure:: images/era12.png

#. In the **Provision a Database** wizard, fill out the following fields with the *Database Server VM* screen to configure the Database Server:

   - **Database Server VM** - Create New Server
   - **Database Server VM Name** - MSSQL2
   - **Software Profile** - MSSQL_2016
   - **Compute Profile** - DEFAULT_OOB_COMPUTE
   - **Network Profile** - DEFAULT_OOB_SQLSERVER_NETWORK
   - **Database Time Zone** - Eastern Standard Time
   - Select **Join Domain**
   - **Windows Domain Profile** - NTNXLAB
   - **Windows License Key** - (Leave Blank)
   - **Administrator Password** - nutanix/4u
   - **Instance Name** - MSSQLSERVER
   - **Database Parameter Profile** - DEFAULT_SQLSERVER_INSTANCE_PARAMS
   - **SQL Service Startup Account** - ntnxlab.local\\Administrator
   - **SQL Service Startup Account Password** - nutanix/4u

   .. figure:: images/era16.png

   .. note::

      A *Instance Name* is the name of the database server, not the hostname. The default is **MSSQLSERVER**. You can install multiple separate instances of MSSQL on the same server as long as they have different instance names. This was more common on a physical server. However, you do not need additional MSSQL licenses to run multiple instances of SQL on the same server.

      *Server Collation* is a configuration setting that determines how the database engine should treat character data at the server, database, or column level. SQL Server includes a large set of collations for handling the language and regional differences that come with supporting users and applications in different parts of the world. A collation can also control case sensitivity on database. You can have different collations for each database on a single instance. The default collation is *SQL_Latin1_General_CP1_CI_AS* which breaks down to:

         - *Latin1* makes the server treat strings using charset latin 1, basically *ASCII*
         - *CP1* stands for Code Page 1252. CP1252 is  single-byte character encoding of the Latin alphabet, used by default in the legacy components of Microsoft Windows for English and some other Western languages
         - *CI* indicates case insensitive comparisons, meaning *ABC* would equal *abc*
         - *AS* indicates accent sensitive, meaning *ü* does not equal *u*

      *Database Parameter Profiles* define the minimum server memory SQL Server should start with, as well as the maximum amount of memory SQL server will use. By default, it is set high enough that SQL Server can use all available server memory. You can also enable contained databases feature which will isolate the database from others on the instance for authentication.

#. Click **Next**, and fill out the following fields within the *Database* screen:

   - **Database Name** - Fiesta2
   - **Database Parameter Profile** - DEFAULT_SQLSERVER_DATABASE_PARAMS

   .. figure:: images/era17.png

   .. note::

      Common applications for pre/post-installation scripts include:

      - Data masking scripts.
      - Register the database with DB monitoring solution.
      - Scripts to update DNS/IPAM.
      - Scripts to automate application setup, such as app-level cloning for Oracle PeopleSoft.

#. Click **Next**, and fill out the following fields within the *Time Machine* screen:

      .. note::

         The default *BRASS* SLA does not include Continuous Protection snapshots.

   - **SLA** - DEFAULT_OOB_GOLD_SLA

   .. figure:: images/era18.png

#. Click **Provision** to begin creating your new database server VM and **Fiesta2** database.

#. Select **Operations** from the dropdown menu to monitor the *Provision* process. This process should take approximately 20 minutes.

   .. figure:: images/era19.png
