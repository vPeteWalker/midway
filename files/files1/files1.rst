.. _files1:

-------------------
Starting with Files
-------------------

Nutanix Files (Files) lets you to share files across user workstations in a centralized and protected location to eliminate the requirement for a third-party file server.

Files uses a scale-out architecture that provides file services to clients through the Server Message Block (SMB) or Network File System (NFS) protocol. Files consists of one or more file server VMs (FSVMs) combined into a logical file server instance sometimes referred to as a Files cluster. Files supports creating multiple file server instances within a single Nutanix cluster.

**Pre-requisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** XX minutes

**Covered Test IDs:** N/A

- Review `NUTANIX FILES GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=Files-v35:Files-v35>`_ and `FILE ANALYTICS GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=File-Analytics-v2_1%3AFile-Analytics-v2_1>`_ for all details including, but not limited to, prerequities, requirements, and recommendations before proceeding.

.. note::

   There are many options at various stages that are available to configure Files to suit the needs of our customers. This workshop will focus on the following configuration.

      - One File Server
         - Basic configuration - 3 File Server VMs (FSVM)
      - Two SMB file shares
         - *Initials*\ -smb01 (normal)
         - *Initials*\ -smb02 (distributed)
      - One NFS export
         - *Initials*\ -logs
      - One hypervisor
         - AHV
      - Authentication
         - Microsoft Active Directory - via AutoAD VM
         - Customer provided Active Directory
      - One VLAN
         - Unmanaged (IPAM not configured)
      - **Files Analytics**

Please be aware that any information such as server names, IP addresses, and similar information contained within any screen shots are strictly for demonstration purposes. Do not use these values when proceeding with any of the steps contained within this workshop.

This workshop was created with the following versions of Nutanix products. There may be differences - in both written steps and screen shots - between what is shown throughout this workshop, and what you experience if you are using later versions of the individual software packages listed below.

   - AOS             - 5.17.0.4
   - PC              - 5.17.0.3
   - Files           - 3.6.4
   - Files Analytics - 2.1.1.1

Finally, while you are welcome to vary your inputs compared to the instructions listed below, please be aware that by diverting from these instructions, you may negatively impact your ability to successfully complete this workshop.

Creating a File Server
......................

#. In the Prism web console, go to the *File Server Dashboard* page by clicking **File Server** from the dropdown.

#. Click **+ File Server**.

   .. figure:: images/1.png

#. In the *New File Server: Pre-Check* window, review the displayed information and address any unsatisfied prerequisites before clicking **Continue**.

   .. figure:: images/2.png

#. The *Create File Server* window appears and displays the *Basics* tab. Do the following in the indicated fields:

   .. figure:: images/3.png

   - **Name**: Enter **Files** as the name for the file server.

      The file server name is used by clients to access the file server. The fully qualified name (file server name + domain) must be unique.

   - **Domain**: ntnxlab.local

   - **File Server Storage**: Enter the file server total storage size (minimum 1 TiB).

   - Click the **Next** button.

      .. note::

         When utilizing the HPOC, it is recommended to use .8 to .14 for the last octet for the 7 IP addresses required by the File Server VMs (FSVM) in the proceeding steps.

#. In the *Client Network* tab:

   - **VLAN**: Select the target VLAN for the *client network* from the pull-down list.

   - **Subnet Mask**: Enter the subnet mask value.

   - **Gateway**: Enter the gateway IP address.

      .. figure:: images/4.png

   - **# IP addresses required**: Click **+IP Addresses**. Enter the starting IP address in the *From* field and the ending IP address in the *To* field (if it is not populated automatically), and then click **Save**. A single line assumes a consecutive set of IP addresses. To use a non-consecutive set, select the + IP Addresses link to open a new line. Add as many lines as necessary to complete the list of IP addresses.

   - **DNS Resolver IP**: Enter IP address for your AutoAD VM.

   - **NTP Servers**: Enter the server name(s) or IP address(es) for the NTP server(s). Use a comma separated list for multiple entries.

      .. figure:: images/5.png

   - When all the entries are correct, click the **Next** button.

#. In the *Storage Network* tab, do the following in the indicated fields:

   - **VLAN** - Select the target VLAN for the *client network* from the pull-down list.

   - **Subnet Mask**: Enter the subnet mask value.

   - **Gateway**: Enter the gateway IP address.

   - **# IP addresses required**: Click **+ IP Addresses**. Enter the starting IP address in the *From* field and the ending IP address in the *To* field (if it is not populated automatically), and then click **Save**. A single line assumes a consecutive set of IP addresses. To use a non-consecutive set, select the + IP Addresses link to open a new line. Add as many lines as necessary to complete the list of IP addresses.

      .. figure:: images/6.png

   - When all the entries are correct, click the **Next** button.

#. In the *Directory Services* tab:

   - Check the **Use SMB Protocol** box.

   - **Username**: Enter the name of an Active Directory user with administrator privileges.

   - **Password**: Enter the user's password.

   - **Make this user a File Server admin**: Check this box.

      .. figure:: images/7.png

   - Check the box for **Show Advanced Options**, and then the box for **Add File Server DNS Entries Using The Same Username And Password**. This will save you the extra steps of registering the File Server DNS entry separately.

   - Check the **Use NFS Protocol** box.

   - From within the **User Management And Authentication** dropdown, choose **Unmanaged**.

   - When all the entries are correct, click the **Next** button.

#. In the **Summary** tab, review the displayed information. When all the information is correct, click **Create**.

   .. figure:: images/8.png

Creating the file server begins. You can monitor progress through the **Tasks** page.

   .. note::

      If you accidentally did not configure Files to use the AutoAD as the DNS server, after deploying the File Server you will get the following errors.

         - DNS 'NS' records not found for *domain*

         - Failed to lookup IP address of *domain*. Please verify the domain name, DNS configuration and network connectivity.

      This can easily be corrected after deployment, without having to delete and redeploy the Files Server.

         - Within the **File Server** dropdown, select the file server you deployed, and click **Update > Network Configuration**. Modify the entry for *DNS Resolver IP*, and click **Next > Save**.

         - Click **DNS**. Update this page with the AutoAD FQDN - **dc.ntnxlab.local**, Username and Password of an Active Directory user with administrator privileges, and click **Submit**.

            .. figure:: images/9.png


Creating an NFS export
......................

#. In the Prism web console, go to the *File Server Dashboard* page by clicking **File Server** from the dropdown.

#. Click **+ Share/Export** action link.

#. Fill out the following fields:

   - **Name** - *Initials*\ -logs
   - **Description (Optional)** - File share for system logs
   - **File Server** - **Files**
   - **Share Path (Optional)** - Leave blank
   - **Max Size (Optional)** - Leave blank
   - **Select Protocol** - **NFS**

   .. figure:: images/24b.png

#. Click **Next**.

#. Fill out the following fields:

   - Select **Enable Self Service Restore**.
      These snapshots appear as a .snapshot directory for NFS clients.
   - **Authentication** - System
   - **Default Access (For All Clients)** - No Access
   - Select **+ Add exceptions**.
   - **Clients with Read-Write Access** - *The first 3 octets of your cluster network*\ .* (e.g. 10.38.1.\*)

   .. figure:: images/25b.png

   By default an NFS export will allow read/write access to any host that mounts the export, but this can be restricted to specific IPs or IP ranges.

#. Click **Next**.

#. Review the **Summary** and click **Create**.

Deploying Files Analytics
.........................

#. Go to **Support Portal > Downloads > Files** and download the File Analytics QCOW2 and JSON files.

#. In Prism, go to the *File Server* view and click the **Deploy File Analytics** action link.

#. In the *Deploy File Analytics* window, click **Deploy**.

#. Upload installation files.
   - In the *Upload installation binary* section, click on the **upload the File Analytics binary** link to upload the File Analytics JSON and QCOW files.
   - Under *File Analytics Metadata File (.Json)*, click **Choose File** to choose the downloaded JSON file.
   - Under *File Analytics Instalation Binary (.Qcow2)*, click **Choose File** to choose the downloaded QCOW file.
   - Click **Upload Now** after choosing the files.

#. Click **Install** once the upload has completed.

#. Do the following in the indicated fields:

   - **Name**: Enter **AVM** for the File Analytics VM (AVM).
   - **Storage Container**: Select a storage container from the dropdown. The dropdown only displays file server storage containers.
   - **Network List**: Select VLAN.
   - Enter network details in the **Subnet Mask**, **Default Gateway IP**, and **IP Address** fields as indicated.

      .. note::

         When utilizing the HPOC, it is recommended to use .15 for the last octet for the IP address.

      .. figure:: images/11.png

   - Scroll down, and click the **Show Advanced Settings** box. Within the **DNS Resolver IP (Comma Separated)** field, enter the IP address of your AutoAD VM.

      .. figure:: images/11a.png

#. Click **Deploy**.

   Verify that the deployment process has completed before proceeding.

#. In the *File Server* view, select the target file server, and click **File Analytics** in the tabs bar. This will open a new browser tab.

#. In the *Enable File Analytics* dialog-box, enter the AD username and password for the file server administrator, and click **Enable**.

Enabling Files Analytics
........................

#. In the *File Server* view, select the target file server and click **File Analytics** in the tabs bar.

#. In the *Enable File Analytics* dialog-box, enter the credentials as indicated:

#. In the *SMB Authentication* section, enter the AD username and password for the file server administrator.

#. Check the **Show Advanced Settings** box

#. With the **DNS Resolver IP:** field, enter the AutoAD IP address.

#. Click **Enable**.

   .. note::

      To update DNS server settings on File Analytics VM after deployment:
       - Login into File Analytics VM CLI using
         - User: nutanix
         - Password: nutanix/4u
       - Execute the following command. Click the icon in the upper right corner of the window below to copy the command to your clipboard, and then paste within your SSH session.

         ::

            sudo bash /opt/nutanix/update_dns.sh


Testing with client desktop
...........................

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
..........................

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
...............................

TO BE COMPLETED

Testing the NFS export
......................

The following steps utilize the LinuxTools VM as a client for your Files NFS export.

#. Note the IP address of the VM in Prism, and connect via SSH using the following credentials:

   - **Username** - root
   - **Password** - nutanix/4u

#. Execute the following:  NEED A BETTER WAY TO DO THE DNS, SO WE DON'T OVERWRITE, YET IT RESOLVES VIA AUTOAD

     .. code-block:: bash
      sh -c "echo nameserver *IP address of AutoAD VM* > /etc/resolv.conf" #Overwrites the contents of the existing resolv.conf with the IP of your AutoAD VM to handle DNS queries. Example: sudo sh -c "echo nameserver 10.38.212.50 > /etc/resolv.conf"
      yum install -y nfs-utils #This installs the NFSv4 client
      mkdir /filesmnt #Creates directory named /filesmnt
      mount.nfs4 files.ntnxlab.local:/ /filesmnt/ #Mounts the NFS export to the /filesmnt directory
      df -kh #show disk utilization for a Linux file system.

   .. note::

      You will see output similar to the below.

      Filesystem                      Size  Used Avail Use% Mounted on
      /dev/mapper/centos_centos-root  8.5G  1.7G  6.8G  20% /
      devtmpfs                        1.9G     0  1.9G   0% /dev
      tmpfs                           1.9G     0  1.9G   0% /dev/shm
      tmpfs                           1.9G   17M  1.9G   1% /run
      tmpfs                           1.9G     0  1.9G   0% /sys/fs/cgroup
      /dev/sda1                       494M  141M  353M  29% /boot
      tmpfs                           377M     0  377M   0% /run/user/0
      **Files.ntnxlab.local:/             1.0T  7.0M  1.0T   1% /afsmnt**
      [root@CentOS ~]# ls -l /filesmnt/
      total 1
      drwxrwxrwx. 2 root root 2 Mar  9 18:53 *Initials*\ -logs

#. Observe that the **logs** directory is mounted in ``/filesmnt//*Initials*\ /-logs``.

#. Reboot the VM and observe the export is no longer mounted. To persist the mount, add it to ``/etc/fstab`` by executing the following:

     .. code-block:: bash

       echo 'files.ntnxlab.local:/ /filesmnt nfs4' >> /etc/fstab

#. The following command will add 100 2MB files filled with random data to ``/filesmnt/logs``:

     .. code-block:: bash

       mkdir /filesmnt/*Initials*\ -logs/host1
       for i in {1..100}; do dd if=/dev/urandom bs=8k count=256 of=/filesmnt/*Initials*\ -logs/host1/file$i; done

#. Return to **Prism > File Server > Share > *Initials*\ -logs** to monitor performance and usage.

   Note that the utilization data is updated every 10 minutes.

Testing with File Analytics
...........................

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
