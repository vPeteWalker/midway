.. _files:

-----
Files
-----

Prerequisites and Requirements
++++++++++++++++++++++++++++++

Review `NUTANIX FILES GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=Files-v35:Files-v35>`_ and `FILE ANALYTICS GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=File-Analytics-v2_1%3AFile-Analytics-v2_1>`_ for all details including, but not limited to, prerequities, requirements, and recommendations before proceeding.

.. note::

   There are many options at various stages that are available to configure Files to suit the needs of our customers. This workshop will focus on the following configuration. Refer to the *NUTANIX FILES GUIDE* linked above for additional configuration options.

      - One File Server    - basic configuration, 3 File Server VMs (FSVM)
      - One file share     - SMB
      - One hypervisor     - AHV
      - AD authentication  - Microsoft Active Directory - via AutoAD VM
      - One VLAN           - Unmanaged (i.e. IPAM is not configured)
      - Files Analytics

Creating a File Server
++++++++++++++++++++++

#. In the Prism web console, go to the *File Server Dashboard* page by clicking **File Server** from the dropdown.

#. Click **+ File Server**.

   .. figure:: images/1.png

#. In the *New File Server: Pre-Check* window, review the displayed information and address any unsatisfied prerequisites before clicking **Continue**.

   .. figure:: images/2.png

#. The *Create File Server* window appears and displays the *Basics* tab. Do the following in the indicated fields:

   .. figure:: images/3.png

   - **Name**: Enter a name for the file server.

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



#. In the *Directory Services* tab, check the **Use SMB Protocol** box.

   - **Username**: Enter the name of an Active Directory user with administrator privileges. Use the following format: domain\username. (ex. ntnxlab\administrator)

   - **Password**: Enter the user's password.

   - **Make this user a File Server admin**: Check this box.

      .. figure:: images/7.png

#. Select the SMB protocol, and fill out the following in the indicated fields:

   - **Active Directory Realm Name**: Displays the Active Directory realm name (read-only).

   - **Username**: Enter the name of an Active Directory user with administrator privileges. Use the following format: *domain\username*.

   - **Password**: Enter the user's password.

   - **Make this user a File Server admin**: Check this box.

   - Check the box for **Show Advanced Options** and check the box for **Add File Server DNS Entries Using The Same Username And Password**. This will save you the extra steps of registering the File Server DNS entry separately.



#. In the **Summary** tab, review the displayed information. When all the information is correct, click **Create**.

   .. figure:: images/8.png

Creating the file server begins. You can monitor progress through the **Tasks** page.

.. warning::

   If you accidentally did not configure Files to use the AutoAD as the DNS server, after deploying the File Server you will get the following errors.

      - DNS 'NS' records not found for *domain*

      - Failed to lookup IP address of *domain*. Please verify the domain name, DNS configuration and network connectivity.

   This can easily be corrected after deployment, without having to delete and redeploy the Files Server.

      - Within the **File Server** dropdown, select the file server you deployed, and click **Update > Network Configuration**. Modify the entry for *DNS Resolver IP*, and click **Next > Save**.

      - Click **DNS**. Update this page with the AutoAD FQDN - **dc.ntnxlab.local**, Username and Password of an Active Directory user with administrator privileges, and click **Submit**.

         .. figure:: images/9.png

Creating a File Share
+++++++++++++++++++++

This task details how to create new shares using the Nutanix file server.

A *distributed* (home) share is the repository for the user's personal files, and a *standard* share is the repository shared by a group. A home share is distributed at the top-level directories while standard shares are located on a single file server VM (FSVM). Users have the following permissions in distributed and standard shares.

.. note::

   Distributed shares are only available on deployments of three or more FSVMs.

   Do not use Windows Explorer to create new top level directories (folders), as you will not be able to rename any folders created with the default New Folder name (see Troubleshooting). For optimal performance, the directory structure for distributed shares must be flat.

Distributed shares

   Domain administrator: Full access

   Domain User: Read only

   Creator Owner: Full access (inherited only)

Standard shares

   Domain administrator: Full access

   Domain User: Full access

   Creator Owner: Full access (inherited only)

#. Click **File Server** from the dropdown.

#. Click **+ Share/Export** in the top right corner.

#. Complete the fields and click **Save** to create a standard file share.

   - **NAME**: Enter the name for the share.
   - **FILE SERVER**: From the drop-down list, select the file server to place the share.

|

#. Click **Next > Next > Create**.

   .. figure:: images/10.png

#. Repeat the steps above, except this time on the *Settings* page, click the **Use "Distributed" share/export type instead of "Standard"** box. This will create a Distributed share.

Files Analytics
+++++++++++++++

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

   - **Name**: Enter a name for the File Analytics VM (AVM).
   - **Storage Container**: Select a storage container from the dropdown. The dropdown only displays file server storage containers.
   - **Network List**: Select VLAN.

      .. figure:: images/11.png

#. Click **Deploy**.

   Verify that the deployment process has completed before proceeding.

#. In the *File Server* view, select the target file server, and click **File Analytics** in the tabs bar. This will open a new browser tab.

#. In the *Enable File Analytics* dialog-box, enter the AD username and password for the file server administrator, and click **Enable**.

Enabling Files Analytics
++++++++++++++++++++++++

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


Testing with client PC
++++++++++++++++++++++

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


#. Deploy new Windows 10 VM.

#. Configure static IP, and configure DNS to point to AutoAD.

#. Change the computer Name.

#. Join the *ntnxlab.local* domain.

#. Login to domain as chosen user from above list.

#. Map the newly created share(s) in your directory. In the Windows client, you can map to the network and create folders at the top level of the file share.

   - In the Windows client VM, open *File Explorer*. Right click on **This PC** and select **Map Network Drives**.

   - Select the drive letter to use for the share. Enter the path to the share in the `\\`*FileServerFQDN*`\`*share* format. Click the **Reconnect at sign-in** box, and then click **Finish**.

   .. figure:: images/12.png

   A new window will open displaying the contents of the share.

#. Repeat the process for any additional shares.

#. Create files and folders as you see fit, and run tests utilizing your predefined criteria.
