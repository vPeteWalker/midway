.. _files2:

Creating an SMB File Share
++++++++++++++++++++++++++

This task details how to create new shares using the Nutanix file server.

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

#. Complete the fields and click **Save** to create a standard file share.

   - **NAME**: Enter the **Initials*\ -smb01** as the name for the share.
   - **FILE SERVER**: From the drop-down list, select the file server to place the share.

   .. figure:: images/10.png

#. Click **Next**.

#. Select **Blocked File Types** and enter **.mov**

   .. figure:: images/10a.png

#. Click **Next > Create**.

#. To create a Distributed share, repeat the steps above, with two differences:

   - **NAME**: Enter the ***Initials*\ -smb02** as the name for the share.
   - On the *Settings* page, click the **Use "Distributed" share/export type instead of "Standard"** box.

Testing with client desktop
+++++++++++++++++++++++++++

AutoAD is pre-populated with the following Users and Groups for your use:

   .. list-table::
      :widths: 25 35 40
      :header-rows: 1

      * - Group
        - Username(s)
        - Password
      * - Administrators
        - Administrator
        - nutanix/4u
      * - SSP Admins
        - adminuser01-adminuser25
        - nutanix/4u
      * - SSP Developers
        - devuser01-devuser25
        - nutanix/4u
      * - SSP Consumers
        - consumer01-consumer25
        - nutanix/4u
      * - SSP Operators
        - operator01-operator25
        - nutanix/4u
      * - SSP Custom
        - custom01-custom25
        - nutanix/4u
      * - Bootcamp Users
        - user01-user25
        - nutanix/4u
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
..
.. #. Map the newly created share(s) in your directory. In the Windows client, you can map to the network and create folders at the top level of the file share.
..
..    - In the Windows client VM, open *File Explorer*. Right click on **This PC** and select **Map Network Drives**.
..
..    - Select the drive letter to use for the share. Enter the path to the share in the `\\`*FileServerFQDN*`\`*share* format. Click the **Reconnect at sign-in** box, and then click **Finish**.
..
..    .. figure:: images/12.png
..
..    A new window will open displaying the contents of the share. You may close this window.
..
.. #. Repeat the process for any additional shares.

Testing "normal" SMB share
++++++++++++++++++++++++++

#. Deploy a new VM from the WinTools image named *Initials*\ **-WinTools**.

#. Connect to your *Initials*\ **-WinTools** VM via VM console as a **non-Administrator NTNXLAB** domain account:

   .. note::

      You will not be able to connect using these accounts via RDP.

   - user01 - user25
   - devuser01 - devuser25
   - operator01 - operator25
   - **Password** nutanix/4u

   .. note::

     The *Initials*\ **-WinTools** VM has already been joined to the **ntnxlab.local** domain. You could use any domain joined VM to complete the following steps.

#. Open ``\\files.ntnxlab.local\`` in **File Explorer**.

#. Open a browser within your *Initials*\ **-WinTools** desktop and download sample data to populate in your share: (HOW DO WE HANDLE THIS IF PHYSICAL POC? STORE IT OUTSIDE OF GITHUB, REDUCE FILE SIZE, BREAK IT INTO MULTIPLE ZIPS, OR...?)

   - **If using a PHX cluster** - http://10.42.194.11/workshop_staging/peer/SampleData_Small.zip
   - **If using a RTP cluster** - http://10.55.251.38/workshop_staging/peer/SampleData_Small.zip

#. Extract the contents of the zip file into your file share.

   - The **NTNXLAB\\Administrator** user was specified as a Files Administrator during deployment of the Files Server, giving it read/write access to all shares by default.
   - Managing access for other users is no different than any other SMB share.

..   #. From ``\\BootcampFS.ntnxlab.local\``, right-click *Initials*\ **-FiestaShare > Properties**.

   - Select the **Security** tab and click **Advanced**.

   - Click **Add**.

   - Click **Select a principal** and specify **Everyone** in the **Object Name** field. Click **OK**.

   #. Fill out the following fields and click **OK**:

      - **Type** - Allow
      - **Applies to** - This folder only
      - Select **Read & execute**
      - Select **List folder contents**
      - Select **Read**
      - Select **Write**

   #. Click **OK > OK > OK** to save the permission changes.

   All users will now be able to create folders and files within the share.

#. Open **PowerShell** and create a file with a blocked file type by executing the following command:

   .. code-block:: PowerShell

      New-Item \\files\\*Initials*\ -smb01\testfile.mov``

   Observe that creation of the new file is denied.

#. Return to **Prism Element > File Server > Share/Export**, select your share. Review the **Share Details**, **Usage** and **Performance** tabs to understand the high level information available on a per share basis, including the number of files & connections, storage utilization over time, latency, throughput, and IOPS.

Testing "distributed" SMB share
+++++++++++++++++++++++++++++++

TO BE COMPLETED

Testing with File Analytics
+++++++++++++++++++++++++++

In this exercise you will explore the new, integrated File Analytics capabilities available in Nutanix Files, including scanning existing shares, creating anomaly alerts, and reviewing audit details. File Analytics is deployed in minutes as a standalone VM through an automated, One Click operation in Prism Element. This VM has already been deployed and enabled in your environment.

#. In **Prism Element > File Server > File Server**, select *File Server* and click **File Analytics**.

#. To scan your newly created share, click :fa:`gear` **> Scan File System**. Select your share and click **Scan**.

   .. figure:: images/14.png

#. Close the **Scan File System** window and refresh your browser.

#. You should see the **Data Age**, **File Distribution by Size** and **File Distribution by Type** dashboard panels update.

   .. figure:: images/15.png

#. From your *Initials*\ **-WinTools** VM, create some audit trail activity by opening several of the files under **Sample Data**.

   .. note::

      You may need to complete a short wizard for OpenOffice if using that application to open a file.

#. Refresh the **Dashboard** page in your browser to see the **Top 5 Active Users**, **Top 5 Accessed Files** and **File Operations** panels update.

   .. figure:: images/17.png

#. To access the audit trail for your user account, click on your user under **Top 5 Active Users**.

   .. figure:: images/17b.png

#. Alternatively, you can select **Audit Trails** from the toolbar and search for your user or a given file.

   .. figure:: images/18.png

   .. note::

      You can use wildcards for your search, for example **.doc**

#. Next, we will create rules to detect anomalous behavior on the File Server. From the toolbar, click :fa:`gear` **> Define Anomaly Rules**.

      .. figure:: images/19.png

#. Click **Define Anomaly Rules** and create a rule with the following settings:

      - **Events:** Delete
      - **Minimum Operation %:** 1
      - **Minimum Operation Count:** 10
      - **User:** All Users
      - **Type:** Hourly
      - **Interval:** 1

#. Under **Actions**, click **Save**.

#. Choose **+ Configure new anomaly** and create an additional rule with the following settings:

   - **Events**: Create
   - **Minimum Operation %**: 1
   - **Minimum Operation Count**: 10
   - **User**: All Users
   - **Type**: Hourly
   - **Interval**: 1

#. Under **Actions**, click **Save**.

   .. figure:: images/20.png

#. Click **Save** to exit the **Define Anomaly Rules** window.

#. To test the anomaly alerts, return to your *Initials*\ **-WinTools** VM and make a second copy of the sample data (via copy/paste) within your share.

#. Delete the original sample data folders.

   .. figure:: images/21.png

   While waiting for the Anomaly Alerts to populate, next we’ll create a permission denial.

   .. note:: The Anomaly engine runs every 30 minutes.  While this setting is configurable from the File Analytics VM, modifying this variable is outside the scope of this workshop.

#. Create a new directory called *Initials*\ **-MyFolder** in the share.

#. Create a text file in the *Initials*\ **-MyFolder** directory and enter some sample text to populate the file. Save the file as *Initials*\ **-file.txt**.

   .. figure:: images/22.png

#. Right-click *Initials*\ **-MyFolder > Properties**. Select the **Security** tab and click **Advanced**. Observe that **Users (BootcampFS\\Users)** lack the **Full Control** permission, meaning that they would be unable to delete files owned by other users.

   .. figure:: images/23.png

#. Open a PowerShell window as another non-Administrator user account by holding **Shift** and right-clicking the **PowerShell** icon in the taskbar and selecting **Run as different user**.

   .. figure:: images/24.png

#. Change Directories to *Initials*\ **-MyFolder** in the *Initials*\ **-FiestaShare** share.

     .. code-block:: bash

        cd \\files.ntnxlab.local\\*Initials*\ -smb01\*initials*\ -MyFolder

#. Execute the following commands:

     .. code-block:: bash

        cat .\\ *initials*\ -file.txt
        rm .\\ *initials*\ -file.txt

   .. figure:: images/25.png

#. Return to **Analytics > Dashboard** and note the **Permission Denials** and **Anomaly Alerts** widgets have updated.

   .. figure:: images/26.png

#. Under **Permission Denials**, select your user account to view the full **Audit Trail** and observe that the specific file you tried to removed is recorded, along with IP address and timestamp.

   .. figure:: images/27.png

#. Select **Anomalies** from the toolbar for an overview of detected anomalies.

   .. figure:: images/28.png

File Analytics puts simple, yet powerful information in the hands of storage administrators, allowing them to understand and audit both utilization and access within a Nutanix Files environment.
