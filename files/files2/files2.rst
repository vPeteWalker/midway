.. _files2:

--------------
SMB File Share
--------------

Creating an SMB File Share
..........................

This section details how to create new shares using the Nutanix file server.

A *distributed* (home) share is the repository for the user's personal files, and a *standard* share is the repository shared by a group. A home share is distributed at the top-level directories while standard shares are located on a single file server VM (FSVM). Users have the following permissions in distributed and standard shares.

   .. note::

      Distributed shares are only available on deployments of three or more FSVMs.

      Do not use Windows Explorer to create new top level directories (folders), as you will not be able to rename any folders created with the default New Folder name (see Troubleshooting). For optimal performance, the directory structure for distributed shares must be flat.

      **Distributed shares**

         Domain administrator: Full access

         Domain User: Read only

         Creator Owner: Full access (inherited only)

      **Standard shares**

         Domain administrator: Full access

         Domain User: Full access

         Creator Owner: Full access (inherited only)

#. Click **File Server** from the dropdown.

#. Click **+ Share/Export** in the top right corner.

#. Enter **smb01** as the name for the share, and click **Save** to create a standard file share.

   .. figure:: images/10.png

#. Click **Next**.

#. Select **Blocked File Types** and within the field, enter **.mov**

   .. figure:: images/10a.png

#. Click **Next > Create**.

#. To create a Distributed share, repeat the steps above, with two differences:

   - **NAME**: Enter the **smb02** as the name for the share.
   - On the *Settings* page, click the **Use "Distributed" share/export type instead of "Standard"** box.

      .. note::

         Best suited for home directories, user profiles and application folders. This option distributes top-level directories across FileServer VMs and allows for increased capacity and user connections. Note that only folders can be created at the root and these top-level folders must be managed using Nutanix Files MMC plugin and can be downloaded from `HERE <http://download.nutanix.com/misc/MMC/Latest/Files_MMC_TLD_setup.msi>`_. Once created, a distributed share/export cannot be downgraded to standard.

.. Testing with client desktop
.. ...........................
..
.. AutoAD is pre-populated with the following Users and Groups for your use:
..
..    .. list-table::
..       :widths: 25 35 40
..       :header-rows: 1
..
..       * - Group
..         - Username(s)
..         - Password
..       * - Administrators
..         - Administrator
..         - nutanix/4u
..       * - SSP Admins
..         - adminuser01-adminuser25
..         - nutanix/4u
..       * - SSP Developers
..         - devuser01-devuser25
..         - nutanix/4u
..       * - SSP Consumers
..         - consumer01-consumer25
..         - nutanix/4u
..       * - SSP Operators
..         - operator01-operator25
..         - nutanix/4u
..       * - SSP Custom
..         - custom01-custom25
..         - nutanix/4u
..       * - Bootcamp Users
..         - user01-user25
..         - nutanix/4u
..
..
.. #. Deploy new Windows 10 VM.
..
.. #. Configure static IP, and configure DNS to point to AutoAD.
..
.. #. Change the computer Name.
..
.. #. Join the *ntnxlab.local* domain.
..
.. #. Login to domain as chosen user from above list.

Testing "normal" SMB share
..........................

During this exercise, you will:

   - Configure a client computer to join the NTNXLAB.local domain.
   - Map a network drive to the smb01 share on the Files server.
   - Populate that network share with sample data (used for Files Analytics testing).
   - Test the file blocking feature of files.

#. We will be utilizing the **WinServer-2** VM previously created in the :ref:`vmmanage` section. Within Prism, make note of its IP address.

#. Launch the console for **WinServer-2**, and log in as the **NTNXLAB\\Administrator** account.

#. *Server Manager* will automatically open. Do not close this window, as we will be utilizing it to configure this server.

#. Modify the Window Server's DNS entry by completing the following steps:

   .. note::

      You may skip this section if you are using the HPOC, as we've already specified the AutoAD IP address as the Primary DNS server within the IPAM settings.

   - Within *Server Manager*, click on **Local Server**.

   - To the right of *Ethernet*, click on the **IPV4 address assigned by DHCP, IPV6 enabled** link. The *Network Connections* window will open.

            .. figure:: images/ethernet1.png

   - Within the *Network Connections* window, right click on *Ethernet* and choose **Properties**. The *Ethernet Properties* window will open.

            .. figure:: images/ethernet2.png

   - Within the *Ethernet Properties* window, select the *Internet Protocol Version 4 (TCP/IPv4)* entry, and click **Properties**. The *Internet Protocol Version 4 (TCP/IPv4)* window will open.

            .. figure:: images/ethernet3.png

   - Within the *Internet Protocol Version 4 (TCP/IPv4)* window, click the radio button for **Use the following DNS server addresses:**, and enter the IP for your domain controller (AutoAD or customer-provided) within the **Preferred DNS Server** field.

            .. figure:: images/ethernet4.png

   - Click **OK > Close**. You may now close the *Network Connections* window.

#. Join the server to the domain by completing the following steps:

   - Within *Server Manager*, click on **Local Server**.

   - Click on the link for the server name. The *System Properties* window will open.

      .. figure:: images/domain1.png

   - In the *System Properties* window, click on the **Change** button at the lower right. The *Computer Name/Domain Changes* window appears.

      .. figure:: images/domain2.png

   - In the *Computer Name* field, enter **WinServer-2**.

   - Click **OK**. A *Computer Name/Domain Changes* dialog box appears, prompting you to reboot the computer. Click **OK > Close > Restart Now**. The computer will reboot.

   - Log in as the **NTNXLAB\\Administrator** account, and revisit the *Computer Name/Domain Changes* window.

   - Within the *Member Of:* section, enter **ntnxlab.local** or the customer-provided domain name in the *Domain:* field.

   - Click **OK**. The *Windows Security* dialog box will open.

      .. figure:: images/domain3.png

   - Enter the domain administrator username and password, and click **OK**.

   - You will be presented with a welcome message to the domain. Click **OK**.

   - You will be prompted to reboot the computer. Click **OK > Close > Restart Now**. The computer will reboot.

#. Remote Desktop into **WinServer-2** and log in as the **NTNXLAB\\Administrator** account.

#. (Recommended) Open a command prompt and ping both your domain controller (e.g. **dc** and **Files**) by name, to confirm DNS resolution is working correctly before proceeding.

#. Map the newly created share(s) in your directory. In the Windows client, you can map to the network and create folders at the top level of the file share.

   - In the Windows client VM, open *File Explorer*. Right click on **This PC** and select **Map Network Drives**.

   - Select the drive letter to use for the share. Enter the path to the share in the \\ /*server* \ /*share* format (e.g. ``\\files.ntnxlab.local\smb01``). Click the **Reconnect at sign-in** box, and then click **Finish**.

      .. figure:: images/12.png

   A new window will open displaying the contents of the share.

#. Repeat the process for any additional shares.

#. Open a browser within your **WinServer-2** desktop and download sample data to populate in your share: (MATT WILL HOST EXTERNALLY)

   - **If using a PHX cluster** - http://10.42.194.11/workshop_staging/peer/SampleData_Small.zip
   - **If using a RTP cluster** - http://10.55.251.38/workshop_staging/peer/SampleData_Small.zip

#. Extract the contents of the zip file into your file share. This should take approximately 3-5 minutes.

   - The **NTNXLAB\\Administrator** user was specified as a Files Administrator during deployment of the Files Server, giving it read/write access to all shares by default.
   - Managing access for other users is no different than any other SMB share.

#. Using *File Explorer* navigate to **\\ / files.ntnxlab.local \\ /**, right-click **smb01 > Properties**.

   - Select the **Security** tab and click **Advanced**.

   - Click **Add**.

   - Click **Select a principal** and specify **Everyone** in the **Object Name** field. Click **OK**.

   - Fill out the following fields and click **OK**:

      - **Type** - Allow
      - **Applies to** - This folder only
      - Select **Read & execute**
      - Select **List folder contents**
      - Select **Read**
      - Select **Write**

   - Click **OK > OK > OK** to save the permission changes.

   All users will now be able to create folders and files within the share, should you wish to further test with other domain users.

#. Open **PowerShell** and create a file with a blocked file type by executing the following command:

   .. code-block:: PowerShell

      New-Item \\files.ntnxlab.local\smb01\testfile.mov

   Observe that creation of the new file is denied.

#. Create a file that isn't on the blocked list.

   .. code-block:: PowerShell

      New-Item \\files.ntnxlab.local\smb01\testfile.txt

   Observe that creation of the new file suceeded.

   .. figure:: images/13.png

#. Return to **Prism Element > File Server > Share/Export**, select your share. Review the **Share Details**, **Usage** and **Performance** tabs to understand the high level information available on a per share basis, including the number of files & connections, storage utilization over time, latency, throughput, and IOPS.

Testing "distributed" SMB share
...............................

During this exercise, you will:

   - Install and configure the Nutanix Files MMC plugin.
   - Create a user *Home* folder.
   - Configure the client PC to redirect their Documents folder to a network share located within the smb02 share on the Files server.

#. Download and install the Nutanix Files MMC plugin from `HERE <http://download.nutanix.com/misc/MMC/Latest/Files_MMC_TLD_setup.msi>`_

#. Click the Windows **Start** button (lower left corner), type **MMC**, and hit **Enter**.

#. Click on **File > Add/Remove Snap-in...**.

#. Select the **Files TLD Namespace Management** entry from the left column, and click **Add**. The *Shared Folders* dialog box appears.

#. Enter **files.ntnxlab.local** within the *Shared Folders* dialog box, and click **Finish > OK**.

#. Expand the **Files TLD Namespace Management**.

#. Right click on **smb02**, and choose **New Folder**. A *Create new folder* dialog box appears.

#. Within the *Create new folder* dialog box, type **Home**, and click **OK**.

#. Open *File Explorer*.

#. Click **Quick Access**, and then the **Documents** folder.

#. Click the **Home** tab on the *Ribbon*, and then **Properties**.

   .. figure:: images/distributed1.png
      :align: left
      :scale: 50%

   .. figure:: images/distributed2.png
      :align: right
      :scale: 50%

#. In the *Folder Properties* window, click the **Location** tab.

#. Click **Move**.

   .. figure:: images/distributed3.png

#. Browse to the *smb02* network drive you mapped previously.

#. Double click on the **Home** folder.

#. Click on **New Folder**, type **User01** as the folder name, and hit **Enter**.

#. Select the **User01** folder, and click **Select Folder**.

   .. figure:: images/distributed4.png

#. Click **OK**.

   .. figure:: images/distributed5.png

#. A *Move Folder* dialog box will appear. Confirm you wish to move the user's *Documents* location by clicking **Yes**.

   .. figure:: images/distributed6.png

#. Within *File Explorer*, click on **Documents**.

#. Right click on any empty space within this window, and choose **New > Text Document**. Name the document, and open it. Type in some characters, close and save the file.

#. Navigate to the **smb02** mapped drive. Proceed to the **Home > User01** folder.

#. Observe that the file you created is on the mapped drive, and you have successfully migrated the user's local documents directory to a mapped *Home* directory stored within Files.

Testing with File Analytics
...........................

In this exercise you will explore the new, integrated File Analytics capabilities available in Nutanix Files, including scanning existing shares, creating anomaly alerts, and reviewing audit details. File Analytics is deployed in minutes as a standalone VM through an automated, One Click operation in Prism Element. This VM has already been deployed and enabled in your environment.

#. In **Prism Element > File Server > File Server**, select *File Server* and click **File Analytics**.

#. To scan your newly created share, click :fa:`gear` **> Scan File System**. Select your share and click **Scan**.

   .. figure:: images/14.png

#. Close the **Scan File System** window and refresh your browser.

#. You should see the **Data Age**, **File Distribution by Size** and **File Distribution by Type** dashboard panels update.

   .. figure:: images/15.png

#. From your **WinServer-2** VM, create some audit trail activity by opening several of the files under **Sample Data** (e.g. Graphics, Pictures, Documents).

#. Refresh the **Dashboard** page in your browser to see the **Top 5 Active Users**, **Top 5 Accessed Files** and **File Operations** panels update.

   .. figure:: images/17.png

#. To access the audit trail for your user account, click on your user under **Top 5 Active Users**.

   .. figure:: images/17b.png

#. Alternatively, you can select **Audit Trails** from the toolbar and search for your user or a given file.

   .. figure:: images/18.png

   .. note::

      You can use wildcards for your search, for example **.docx**

#. Next, we will create rules to detect anomalous behavior on the File Server. From the toolbar, click :fa:`gear` **> Define Anomaly Rules**.

      .. figure:: images/19.png

#. Click **Define Anomaly Rules** and create a rule with the following settings:

      - **Events:** Delete
      - **Minimum Operation %:** 1
      - **Minimum Operation Count:** 10

#. Under **Actions**, click **Save**.

#. Choose **+ Configure new anomaly** and create an additional rule with the following settings:

   - **Events**: Create
   - **Minimum Operation %**: 1
   - **Minimum Operation Count**: 10

#. Under **Actions**, click **Save**.

   .. figure:: images/20.png

#. Click **Save** to exit the **Define Anomaly Rules** window.

#. To test the anomaly alerts, return to your **WinServer-2** VM and make a second copy of the sample data (via copy/paste) within your share.

#. Delete the original sample data folders.

   .. figure:: images/21.png

   While waiting for the Anomaly Alerts to populate, next we’ll create a permission denial.

   .. note:: The Anomaly engine runs every 30 minutes.  While this setting is configurable from the File Analytics VM, modifying this variable is outside the scope of this workshop.

#. Create a new directory called **MyFolder** in the share.

#. Create a text file in the **MyFolder** directory and enter some sample text to populate the file. Save the file as **file.txt**.

   .. figure:: images/22.png

#. Right-click *MyFolder* and choose **Properties**. Select the **Security** tab and click the **Advanced** button.

#. Choose **Disable inheritance > Convert inherited permissions into explicit permissions on this object.**

#. Highlight the **Users** principal, and choose **Remove**.

#. Click **Add > Select a principal**. Enter **Everyone** in the *Enter the object name to select* field, and click **OK > OK > OK**.

#. Click **Add** the Everyone permissions with the following:

#. Open a PowerShell window as another non-Administrator user account by holding **Shift** and right-clicking the **PowerShell** icon in the start menu and selecting **More > Run as a different user**.

   .. figure:: images/24.png

#. Change Directories to **MyFolder** in the **smb01** share.

     .. code-block:: bash

        cd \\ /files.ntnxlab.local \\ /smb01\ /MyFolder

#. Execute the following commands:

     .. code-block:: bash

        cat file.txt
        rm file.txt

   .. figure:: images/25.png

#. Return to **Analytics > Dashboard** and note the **Permission Denials** and **Anomaly Alerts** widgets have updated.

   .. figure:: images/26.png

#. Under **Permission Denials**, select your user account to view the full **Audit Trail** and observe that the specific file you tried to removed is recorded, along with IP address and timestamp.

   .. figure:: images/27.png

#. Close the Audit details screen, and select **Anomalies** from the toolbar for an overview of detected anomalies.

File Analytics puts simple, yet powerful information in the hands of storage administrators, allowing them to understand and audit both utilization and access within a Nutanix Files environment.
