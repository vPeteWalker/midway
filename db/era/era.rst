--------------
Era Deployment
--------------

Network Configuration
+++++++++++++++++++++

Era requires the *Manage IP address pool* option enabled on a VLAN for certain features.

#. Within Prism, click on **Settings** from the dropdown, and then **Network Configuration** from the left-hand side.

#. Click :fa:`plus` **Create Network**.

#. Enter **Era** within the *Network Name* field, and the VLAN within the *VLAN ID* field.

   - If a secondary VLAN is available, use those values (i.e. if you are using the HPOC).
   - If only a single VLAN is available, you may adjust the end value for IP Pool on the *Primary* VLAN (ex. from 125 down to 100, and use 101 to 125 on VLAN 0 for this purpose).

#. Click **Save**.

Installation
++++++++++++

#. Download the **Era Install for AHV** image file from the `Era Downloads section - Nutanix Support Portal <https://portal.nutanix.com/page/downloads?product=era>`_ either click the **Download** button associated with the latest version of *Era Install for AHV*, or the :fa: `ellipsis-v` next to it, and choose **Copy Download Link**.

#. Log on to the *Prism Element* web console.

#. Select *Settings* from the main menu drop-down list, and click **Image Configuration**.

#. Under *Image Configuration*, click **Upload Image**.

#. In the *Create Image* dialog box, do the following in the indicated fields:

   - **Name**. Type a name of the image (ex. Era)

   - **Image Type**. Select **Disk** from the drop-down list.

   - **Storage Container**. Select the **default** storage container to install Era.

   - **Choose one of the following**:

      - Within *Image Source*, click **Upload a file > Choose File**. Browse to the Disk image for Era, and click **Open**.

         .. figure:: images/FIX IMAGE

      **OR**

      - Click :fa:`dot-circle` **From URL**, and paste the download link you previously copied from the Nutanix Portal.

         .. figure:: images/FIX IMAGE

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

   - Click **Add** to create a network interface for the VM and return to the *Create VM* dialog box.

#. (Optional) If you want to assign a static IP address to the Era VM, do the following:

   - Select the **Custom Script** check box.

   - In the *Type or paste script* text box, paste the following script, and replace the indicated references (ex. <NETMASK-IP> would be 255.255.255.128).

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

Configuration
+++++++++++++

#. Open `<ERA-VM-IP>` in a new browser tab.

#. Read the *Nutanix End User License Agreement (EULA) agreement*, click the **I have read and agree to terms and conditions option**, and then click **Continue**. In the *Nutanix Customer Experience Program* screen, click **OK**.

#. Within the logon screen, set a password for the administrator user (admin) in the *Enter new password* and *Re-enter new password* fields, and click **Set Password**.

#. In the *Era’s Cluster* screen, do the following in the indicated fields:

   - **Name** Type a name of the Nutanix cluster as you want the name to appear in Era.

   - (Optional) **Description** Type a description of the Nutanix cluster.

   - **Address** Type in the Prism Element VIP.

   - **Prism Element Administrator** Type the user name of the Prism Element user account with which you want Era to access the Nutanix cluster. (ex. admin)

      .. note::

         It is not best practice to use the default administrative account for Era operations. In a production environment, it is therefore recommended to use a separate Prism Element user account with Nutanix cluster administrative privileges as Era service account.

   - **Password** Type the password of the Prism Element user account.

   - Click **Next**.

      .. figure:: images/era1.png

#. (Optional) Configure the SMTP server. If you do not configure this, remove the e-mail address listed within the *Sender's EMail* box.

#. In the *Era Server's OS Time Zone* list, select a timezone, or leave the default UTC.

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

Windows Domain Configuration
............................

#. From the dropdown, choose **Profiles**.

#. Select **Windows Domain**.

#. Click **Create**.

#. In the *Create Windows Domain Profile* screen, do the following in the indicated fields:

   - **Name** NTNXLAB

   - **Domain to Join (FQDN)** ntnxlab.local

#. In the *Domain Account with Permission to Join Computer to the Domain* section, do the following in the indicated fields:

   - **Username** ntnxlab.local\administrators

   - **Password** nutanix/4u

#. In the *SQL Service Startup Account* section, deselect **Specify Startup Account in Profile**.

#. In the *Era Worker Service Account* section, do the following in the indicated fields:

   - **Username** ntnxlab.local\administrators

   - **Password** nutanix/4u

   .. figure:: images/era15.png

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

Configuring Era for Microsoft SQL
+++++++++++++++++++++++++++++++++

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
...........................

Before additional SQL Server VMs can be provisioned, a *Software Profile* must first be created from the SQL server VM registered in the previous step. A software profile is a template that includes the SQL Server database and operating system. This template exists as a hidden, cloned disk image on your Nutanix cluster.

#. From the dropdown, select **Profiles**, and then **Software** from the lefthand menu.

#. Click **+ Create**, and then **Microsoft SQL Server**. Fill out the following fields:

   - **Profile Name** - MSSQL_2016
   - **Database Server** - Select your registered MSSQL VM

   .. figure:: images/14.png

#. Click **Next > Create**.

#. Select **Operations** from the dropdown menu to monitor the registration. This process should take approximately 5 minutes.

#. Once the profile creation completes successfully, return to Prism. Right click your *Win16SQL16* VM, and choose **Power Off Actions > Guest Shutdown**.

Creating a New Microsoft SQL Database Server
............................................

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

#. Click **Provision** to begin creating your new database server VM and *Fiesta2* database.

#. Select **Operations** from the dropdown menu to monitor the *Provision* process. This process should take approximately 20 minutes.

   .. figure:: images/era19.png
