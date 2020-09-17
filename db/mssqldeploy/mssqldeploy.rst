.. _mssqldeploy:

-------------------------
Deploying MS SQL with Era
-------------------------

Traditional database VM deployment resembles the diagram below. The process generally starts with a IT ticket for a database (from Dev, Test, QA, Analytics, etc.). Next, one or more teams will need to deploy the storage resources, and VM(s) required. Once infrastructure is ready, a DBA needs to provision and configure database software. Once provisioned, any best practices and data protection/backup policies need to be applied. Finally the database can be handed over to the end user. That's a lot of handoffs, and the potential for a lot of friction.

.. figure:: images/0.png

Whereas with a Nutanix cluster and Era, provisioning and protecting a database should take you no longer than it took to read this intro.

In this workshop you will manually deploy a Microsoft SQL Server VM, using a script to apply best practices. This VM will act as a master image to create a profile for deploying additional SQL VMs using Era.

TEMP REQUIREMENTS PLACEHOLDER
+++++++++++++++++++++++++++++

Links to different datacenters for:
SQL 2016 image
Era image

https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Era-User-Guide-v1_3:Nutanix-Era-User-Guide-v1_3

SQL VM Deployment
+++++++++++++++++

#. From the dropdown within Prism, select **VM > + Create VM**.

#. Fill out the following fields:

   - **Name** - MSSQL
   - **Description** - (Optional) Description for your VM.
   - **vCPU(s)** - 2
   - **Memory** - 4 GiB

   - Select **+ Add New Disk**
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - MSSQL-2016-VM.qcow2
      - Select **Add**

   - Select **Add New NIC**
      - **VLAN Name** - *Primary*
      - Select **Add**

#. Click **Save** to create the VM.

#. Right click the VM, and select **Power On**.

#. Once powered on, right click the VM, and select **Launch Console**. Complete Windows Server setup as follows:

   - Click **Next**.
   - **Accept** the licensing agreement.
   - Enter **nutanix/4u** as the Administrator password and click **Finish**.

#. Log in to the VM using the *Administrator* username, and *nutanix/4u* password.

#. Disable Windows Firewall for all networks.

   - Open *Server Manager* and select **Local Server**.

   - Within the *Windows Firewall* entry, click on **Private: On**.

   - In the left pane, click on **Turn Windows Firewall on or off**.

   - Under both *Private network settings* and *Public network settings*, click on the bullets for **Turn off Windows Firewall (not recommended)**.

   - Click **OK** and close the *Windows Firewall* window.

   - Close the *Server Manager* window.

#. Join the *ntnxlab.local* domain.

   -

#. Launch **File Explorer** and note the current, single disk configuration.

   .. figure:: images/2.png

   .. note::

      Best practices for database VMs involve spreading the OS, SQL binaries, databases, TempDB, and logs across separate disks in order to maximize performance.

      For complete details for running Microsoft SQL Server on Nutanix (including guidance around NUMA, hyperthreading, SQL Server configuration settings, and more), see the `Nutanix Microsoft SQL Server Best Practices Guide <https://portal.nutanix.com/#/page/solutions/details?targetId=BP-2015-Microsoft-SQL-Server:BP-2015-Microsoft-SQL-Server>`_.

#. From the desktop, launch the **01 - Rename Server.ps1** PowerShell script shortcut and fill out the following fields:

   - **Enter the Nutanix cluster IP** - <CLUSTER-VIP>
   - **Enter the Nutanix user name for...** - admin
   - **Enter the Nutanix password for "admin"** - <CLUSTER-PASSWORD>

   The script will validate the VM name does not exceed 15 characters and then rename the server to match the VM name.

#. Once VM has rebooted, log in as previous, and launch the **02 - Complete Build.ps1** Powershell script shortcut. Fill out the following fields:

   - **Enter the Nutanix cluster IP** - <CLUSTER-VIP>
   - **Enter the Nutanix user name for...** - admin
   - **Enter the Nutanix password for "admin"** - <CLUSTER-PASSWORD>
   - **Enter the Nutanix container name** - <DEFAULT-CONTAINER-NAME>

   .. note::

      All fields in the above script are case sensitive.

   This script will setup and create disk drives according to best practices place SQL data files on those drives. The SQL Systems File is placed on the D:\ drive and data and logs files are placed on separate drives.

#. Once VM has rebooted, log in to the VM, and verify the new disk configuration in both **Prism** and **File Explorer** within the VM itself.

   .. figure:: images/3.png

   .. figure:: images/4.png

#. Download `this <https://github.com/nutanixworkshops/EraWithMSSQL/raw/master/deploy_mssql_era/FiestaDB-MSSQL.sql>`_ file to the desktop.

#. Launch **SQL Server Management Studio 17** from the desktop.

#. Leave the default *Windows Authentication*, and click **Connect**.

#. Verify the database server is available, with only system databases provisioned.

   .. figure:: images/5.png

#. Right click on **Databases** and choose **New Database**. Enter **Fiesta** in the *Database name* field. Click **OK**.

#. Click on **File > Open > File**. Choose the *FiestaDB-MSSQL.sql* file you previously downloaded to the desktop, and click **Open**.

#. Click **Execute**. This will create data within the *Fiesta* database.

   .. figure:: images/era10.png

   Congratulations, you now have a functioning SQL Server VM. While this process could be further automated through `acli`, Calm, or REST API calls orchestrated by a third party tool, provisioning only solves a Day 1 problem for databases, and does little to address storage sprawl, cloning, or patch management.

Installing Era
++++++++++++++

#. Download the **Era Install for AHV** image file from the `Nutanix Support portal <https://portal.nutanix.com/page/downloads?product=era>`_.

#. Log on to the *Prism Element* web console.

#. Select *Settings* from the main menu drop-down list, and click **Image Configuration**.

#. Under *Image Configuration*, click **Upload Image**.

#. In the *Create Image* dialog box, do the following in the indicated fields:

   - **Name**. Type a name of the image (ex. Era)

   - **Annotation**. Type a description of the image.

   - **Image Type**. Select Disk from the drop-down list.

   - **Storage Container**. Select the **default** storage container to install Era.

   - Under *Image Source*, select **Upload a file**, and click **Choose File**.

   - Browse to the location on your local computer where you have saved the qcow2 image file of Era, and click **Open**.

   - Click **Save**.

   Wait until the **Create Image** task completes before proceeding.

#. From the main menu drop-down list, select **VM**, and then click **+ Create VM**.

   Do the following in the indicated fields.

   - **Name** Enter a name of the VM.

   - (Optional) **Description** Enter a description of the VM.

   - **Use this VM as an agent VM** Select this option to make this VM as an agent VM.

   - **vCPU(s)** Enter 4 as the number of virtual CPUs to allocate to this VM.

   - **Memory** Enter 16 GB as the amount of memory (in GB) to allocate to this VM.

#. Click **Add New Disk**.

   Do the following in the indicated fields.

   - **Operation** Select **Clone from Image Service** to copy the Era image that you have imported by using the image service feature onto the disk.

   - **Image** Select the Era image that you have imported by using the image service feature.

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

#. Determine the IP address assigned to the Era VM from the *IP Addresses* field.

   If you assigned a static IP address to the Era VM on a VLAN that has a DHCP server (ex. the *Primary* VLAN on the HPOC), Prism Element first assigns an IP address to the Era VM by using DHCP. Wait for one or two minutes and refresh the Prism Element page to verify if the static IP address you specified has been assigned to the VM.

Initial Era Configuration
+++++++++++++++++++++++++

#. Open `<ERA-VM-IP>` in a new browser tab.

#. Read the *Nutanix End User License Agreement (EULA) agreement*, click the **I have read and agree to terms and conditions option**, and then click **Continue**. In the *Nutanix Customer Experience Program* screen, click **OK**.

#. In the logon screen, set a password for the administrator user (admin) in the *Enter new password* and *Re-enter new password* fields, and click **Set Password**.

#. In the *Era’s Cluster* screen, do the following in the indicated fields:

   - **Name** Type a name of the Nutanix cluster as you want the name to appear in Era.

   - **Description** Type a description of the Nutanix cluster.

   - **IP Address of the Prism Element** Type the IP address of the Prism Element web console of the Nutanix cluster.

   - **Prism Element Administrator** Type the user name of the Prism Element user account with which you want Era to access the Nutanix cluster.

      .. note::

         It is not best practice to use the default administrative account for Era operations. In a production environment, it is therefore recommended to use a separate Prism Element user account with Nutanix cluster administrative privileges as Era service account.

   - **Password** Type the password of the Prism Element user account.

   - Click **Next**.

      .. figure:: images/era1.png

#. In the *Services* screen, do the following in the indicated fields:

#. Ensure that the AutoAD IP address is the only entry in the *DNS Servers* field.

#. (Optional) Configure the SMTP server. If you choose not to configure SMTP, remove the e-mail address in the *Sender's Email* field.

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

Configure Windows Domain
........................

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

#. Set the *Timeout* setting to **Never**. This will help avoid being logged out unexpectedly.

Modifying Era VM Network Settings Post-Launch
.............................................

.. note::

   These instructions are taken from the *Assigning A Static IP Address To The Era VM By Using The Console* section of the Era Guide. However, you may utilize any or all of the parameters for the `era-server set` command to accomplish your goal. For example, if you only need to modify the name server that the Era VM is using, you would type `era_server set nameserver=<NAMESERVER-IP>`.

#. Within Prism, right click the Era VM, and click **Launch Console**
.
#. Use the following credentials to log on to Era:

   - User name: era
   - Password: Nutanix.1

#. Launch the Era server prompt by typing `era-server`.

#. The command is `era_server set ip=<IP-address> gateway=<GATEWAY-ADDRESS> netmask=<NETMASK-IP> nameserver=<NAMESERVER>

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

#. From the dropdown, select **Databases**.

#. Click **+ Register > Microsoft SQL Server > Database**.

   .. figure:: images/era8.png

#. The *Register a SQL Server Database* window appears. In the *Database Server VM* step, select **Not registered**.

#. In the *Era’s Cluster* screen, do the following in the indicated fields:

   - **IP Address or Name of VM** Select the VM you created in the *SQL VM Deployment* section. Verify that the associated IP address matches the IP address that is listed in Prism, as there may be multiple IP addresses listed for the same VM.

   - **Windows Administrator Name** Type the user name of the administrator account (ex. Administrator).

   - **Windows Administrator Password** Type the password of the administrator account.

   - **Instance** Era automatically discovers all the instances a database server VM. In our case, there is only one instance named **MSSQLSERVER**.

   - The *Connect to SQL Server Login* and *User Name* fields allow a choice of authentication between Windows Admin, and SQL Server user. Leave the default at **Windows Admin User**, and click **Next**.

   - Select the **Fiesta** database within the *Unregistered Databases* section. Click **Next**.

   .. figure:: images/era11.png

   - Click **Register**.

#. The registration process will take approximately 5 minutes. In the meantime, proceed with these steps:

   - From the dropdown menu, select **SLAs**. Era has five built-in SLAs (Gold, Silver, Bronze, Zero, and Brass). SLAs control however the database server is backed up. This can with a combination of Continuous Protection, Daily, Weekly Monthly and Quarterly protection intervals.

   - From the dropdown menu, select **Profiles**.

   Profiles pre-define resources and configurations, making it simple to consistently provision environments and reduce configuration sprawl. For example, Compute Profiles specifiy the size of the database server, including details such as vCPUs, cores per vCPU, and memory.

Creating A Software Profile
+++++++++++++++++++++++++++

Before additional SQL Server VMs can be provisioned, a Software Profile must first be created from the database server VM registered in the previous step. A software profile is a template that includes the SQL Server database and operating system. This template exists as a hidden, cloned disk image on your Nutanix storage.

#. Select **Profiles** from the dropdown menu and then **Software** from the lefthand menu.

   .. figure:: images/14.png

#. Click **+ Create** and fill out the following fields:

   - **Engine** - Microsoft SQL Server
   - **Name** - MSSQL_2016
   - **Description** - (Optional)
   - **Database Server** - Select your registered MSSQL VM

#. Click **Create**.

   .. figure:: images/15.png

#. Select **Operations** from the dropdown menu to monitor the registration. This process should take approximately 5 minutes.

   .. figure:: images/16.png

#. Once the profile creation completes successfully, return to Prism. Right click your MSSQL VM, and choose **Power Off Actions > Guest Shutdown**.

Creating a New MSSQL Database Server
++++++++++++++++++++++++++++++++++++

You've completed all the one time operations required to be able to provision any number of SQL Server VMs. Follow the steps below to provision a database of a fresh database server, with best practices automatically applied by Era.

#. In **Era**, select **Databases** from the dropdown menu and **Sources** from the lefthand menu.

#. Click **+ Provision > Microsoft SQL Server > Database**.

   .. figure:: images/era12.png

#. In the **Provision a Database** wizard, fill out the following fields to configure the Database Server:

   - **Database Server VM** - Create New Server
   - **Database Server VM Name** - MSSQL2
   - **Description** - (Optional)
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

      A **Instance Name** is the name of the database server, this is not the hostname. The default is **MSSQLSERVER**. You can install multiple separate instances of MSSQL on the same server as long as they have different instance names. This was more common on a physical server, however, you do not need additional MSSQL licenses to run multiple instances of SQL on the same server.

      **Server Collation** is a configuration setting that determines how the database engine should treat character data at the server, database, or column level. SQL Server includes a large set of collations for handling the language and regional differences that come with supporting users and applications in different parts of the world. A collation can also control case sensitivity on database. You can have different collations for each database on a single instance. The default collation is **SQL_Latin1_General_CP1_CI_AS** which breaks out like below:

         - **Latin1** makes the server treat strings using charset latin 1, basically **ASCII**
         - **CP1** stands for Code Page 1252. CP1252 is  single-byte character encoding of the Latin alphabet, used by default in the legacy components of Microsoft Windows for English and some other Western languages
         - **CI** indicates case insensitive comparisons, meaning **ABC** would equal **abc**
         - **AS** indicates accent sensitive, meaning **ü** does not equal **u**

      **Database Parameter Profiles** define the minimum server memory SQL Server should start with, as well as the maximum amount of memory SQL server will use. By default, it is set high enough that SQL Server can use all available server memory. You can also enable contained databases feature which will isolate the database from others on the instance for authentication.

#. Click **Next**, and fill out the following fields to configure the Database:

   - **Database Name** - Fiesta2
   - **Description** - (Optional)
   - **Database Parameter Profile** - DEFAULT_SQLSERVER_DATABASE_PARAMS

   .. figure:: images/era17.png

   .. note::

      Common applications for pre/post-installation scripts include:

      - Data masking scripts
      - Register the database with DB monitoring solution
      - Scripts to update DNS/IPAM
      - Scripts to automate application setup, such as app-level cloning for Oracle PeopleSoft

#. Click **Next** and fill out the following fields to configure the Time Machine for your database:

   .. note::

      The default BRASS SLA does NOT include Continuous Protection snapshots.

   - **Description** - (Optional)
   - **SLA** - DEFAULT_OOB_GOLD_SLA

   .. figure:: images/era18.png

#. Click **Provision** to begin creating your new database server VM and **Fiesta** database.

#. Select **Operations** from the dropdown menu to monitor the provisioning. This process should take approximately 20 minutes. You may continue with the first two steps in the next section while this operation is running.

   .. figure:: images/era19.png

If you encounter errors unrelated to the information you entered, you can select the name of the process within *Operations* and click **Resubmit**.

   .. note::

      Observe the step for applying best practices in **Operations**.

      Some of the best practices automatically configured by Era include:

      - Distribute databases and log files across multiple vDisks.
      - Do not use Windows dynamic disks or other in-guest volume management.
      - For each database, use multiple data files; one file per vCPU.
      - Configure initial log file size to 4 GB or 8 GB and iterate by the initial amount to reach the desired size.
      - Use multiple TempDB data files, all the same size.

Exploring the Provisioned DB Server
++++++++++++++++++++++++++++++++++++

#. In **Prism Element > Storage > Volume Groups**, locate the **ERA_MSSQL2_...** VG and observe the layout on the **Virtual Disk** tab.

   .. figure:: images/23.png

#. View the disk layout of your newly provisioned VM in Prism, and compare to the previous VM. This VM follows best practices when it comes to disk layout, where as the original VM does not.

   .. figure:: images/24.png

#. In Prism, note the IP address of your *MSSQL2* VM and connect to it via RDP using the following credentials:

   - **User Name** - Administrator
   - **Password** - nutanix/4u

#. Open **Start > Run > diskmgmt.msc** to view the in-guest disk layout. Right-click an unlabeled volume and select **Change Drive Letter and Paths** to view the path to which Era has mounted the volume. Note there are dedicated drives corresponding to SQL data and log locations, similar to the original SQL Server to which you manually applied best practices.

   .. figure:: images/25.png

Migrating Fiesta App Data
+++++++++++++++++++++++++

In this exercise you will import data directly into your database from a backup exported from another database. While this is a suitable method for migrating data, it potentially involved downtime for an application, or our database potentially not having the very latest data.

Another approach could involve adding your new Era database to an existing database cluster (AlwaysOn Availability Group) and having it replicate to your Era provisioned database. Application level synchronous or asynchronous replication (such as SQL Server AAG or Oracle RAC) can be used to provide Era benefits like cloning and Time Machine to databases whose production instances run on bare metal or non-Nutanix infrastructure.

#. From your *Initials*\ **-MSSQL2** RDP session, launch **Microsoft SQL Server Management Studio** from the desktop and click **Connect** to authenticate as the currently logged in user.

   .. figure:: images/26.png

#. Expand the *Initials*\ **-fiesta** database and note that it contains no tables. With the database selected, click **New Query** from the menu to import your production application data.

   .. figure:: images/27.png

#. Copy and paste the following script into the query editor and click **Execute**:

   .. literalinclude:: FiestaDB-MSSQL
     :caption: FiestaDB Data Import Script
     :language: sql

   .. figure:: images/28.png

#. Note the status bar should read **Query executed successfully**.

#. You can view the contents of the database by clicking **New Query** and executing the following:

   .. code-block:: sql

      SELECT * FROM dbo.products
      SELECT * FROM dbo.stores
      SELECT * FROM dbo.InventoryRecords

   .. figure:: images/29.png

#. In **Era > Time Machines**, select your *initials*\ **-fiesta_TM** Time Machine. Select **Actions > Log Catch Up > Yes** to ensure the imported data has been flushed to disk prior to the cloning operation in the next lab.

Provision Fiesta Web Tier
+++++++++++++++++++++++++

Manipulating data using **SQL Server Management Studio** is boring, especially when THE *Sharon Santana* went through all of the trouble of building a neat front end for your business critical app. In this section you'll deploy the web tier of the application and connect it to your production database.

#. `Download the Fiesta Blueprint by right-clicking here <https://raw.githubusercontent.com/nutanixworkshops/ts2020/master/db/mssqldeploy/FiestaNoDB.json>`_. This single-VM Blueprint is used to provision only the web tier portion of the application.

#. From **Prism Central > Calm**, select **Blueprints** from the lefthand menu and click **Upload Blueprint**.

   .. figure:: images/30.png

#. Select **FiestaNoDB.json**.

#. Update the **Blueprint Name** to include your initials. Even across different projects, Calm Blueprint names must be unique.

#. Select your Calm project and click **Upload**.

   .. figure:: images/31.png

#. In order to launch the Blueprint you must first assign a network to the VM. Select the **NodeReact** Service, and in the **VM** Configuration menu on the right, select *Your Assigned User VLAN* as the **NIC 1** network.

   .. figure:: images/32.png

#. Click **Credentials** to define a private key used to authenticate to the CentOS VM that will be provisioned by the Blueprint.

#. Expand the **CENTOS** credential and use your preferred SSH key, or paste in the following value as the **SSH Private Key**:

   ::

     -----BEGIN RSA PRIVATE KEY-----
     MIIEowIBAAKCAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNG
     ZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK
     6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9
     HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7Gy
     hCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNR
     uz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5OrwIBJQKCAQB++q2WCkCmbtByyrAp
     6ktiukjTL6MGGGhjX/PgYA5IvINX1SvtU0NZnb7FAntiSz7GFrODQyFPQ0jL3bq0
     MrwzRDA6x+cPzMb/7RvBEIGdadfFjbAVaMqfAsul5SpBokKFLxU6lDb2CMdhS67c
     1K2Hv0qKLpHL0vAdEZQ2nFAMWETvVMzl0o1dQmyGzA0GTY8VYdCRsUbwNgvFMvBj
     8T/svzjpASDifa7IXlGaLrXfCH584zt7y+qjJ05O1G0NFslQ9n2wi7F93N8rHxgl
     JDE4OhfyaDyLL1UdBlBpjYPSUbX7D5NExLggWEVFEwx4JRaK6+aDdFDKbSBIidHf
     h45NAoGBANjANRKLBtcxmW4foK5ILTuFkOaowqj+2AIgT1ezCVpErHDFg0bkuvDk
     QVdsAJRX5//luSO30dI0OWWGjgmIUXD7iej0sjAPJjRAv8ai+MYyaLfkdqv1Oj5c
     oDC3KjmSdXTuWSYNvarsW+Uf2v7zlZlWesTnpV6gkZH3tX86iuiZAoGBAKM0mKX0
     EjFkJH65Ym7gIED2CUyuFqq4WsCUD2RakpYZyIBKZGr8MRni3I4z6Hqm+rxVW6Dj
     uFGQe5GhgPvO23UG1Y6nm0VkYgZq81TraZc/oMzignSC95w7OsLaLn6qp32Fje1M
     Ez2Yn0T3dDcu1twY8OoDuvWx5LFMJ3NoRJaHAoGBAJ4rZP+xj17DVElxBo0EPK7k
     7TKygDYhwDjnJSRSN0HfFg0agmQqXucjGuzEbyAkeN1Um9vLU+xrTHqEyIN/Jqxk
     hztKxzfTtBhK7M84p7M5iq+0jfMau8ykdOVHZAB/odHeXLrnbrr/gVQsAKw1NdDC
     kPCNXP/c9JrzB+c4juEVAoGBAJGPxmp/vTL4c5OebIxnCAKWP6VBUnyWliFhdYME
     rECvNkjoZ2ZWjKhijVw8Il+OAjlFNgwJXzP9Z0qJIAMuHa2QeUfhmFKlo4ku9LOF
     2rdUbNJpKD5m+IRsLX1az4W6zLwPVRHp56WjzFJEfGiRjzMBfOxkMSBSjbLjDm3Z
     iUf7AoGBALjvtjapDwlEa5/CFvzOVGFq4L/OJTBEBGx/SA4HUc3TFTtlY2hvTDPZ
     dQr/JBzLBUjCOBVuUuH3uW7hGhW+DnlzrfbfJATaRR8Ht6VU651T+Gbrr8EqNpCP
     gmznERCNf9Kaxl/hlyV5dZBe/2LIK+/jLGNu9EJLoraaCBFshJKF
     -----END RSA PRIVATE KEY-----

   .. figure:: images/33.png

#. Click **Save** and click **Back** once the Blueprint has completed saving.

#. Click **Launch** and fill out the following fields:

   - **Name of the Application** - *Initials*\ -Fiesta
   - **db_dialect** - mssql
   - **db_domain_name** - ntnxlab.local
   - **db_host_address** - The IP of your *Initials*\ **-MSSQL2** VM
   - **db_name** - *Initials*\ -fiesta (as configured when you deployed through Era)
   - **db_password** - nutanix/4u
   - **db_username** - Administrator

   .. figure:: images/34.png

#. Click **Create**.

#. Select the **Audit** tab to monitor the deployment. This process should take < 5 minutes.

   .. figure:: images/35.png

#. Once the application status changes to **Running**, select the **Services** tab and select the **NodeReact** service to obtain the **IP Address** of your web server.

   .. figure:: images/36.png

#. Open \http://*NODEREACT-IP-ADDRESS:5001*/ in a new browser tab to access the **Fiesta** application.

   .. figure:: images/37.png

   Congratulations! You've completed the deployment of your production application.

Takeaways
+++++++++

What are the key things we learned in this lab?

- Existing databases can be easily onboarded into Era, and turned into templates
- Existing brownfield databases can also be registered with Era
- Profiles allow administrators to provision resources based on published standards
- Customizable recovery SLAs allow you to tune continuous, daily, and monthly RPO based on your app's requirements
- Era provides One-click provisioning of multiple database engines, including automatic application of database best practices
