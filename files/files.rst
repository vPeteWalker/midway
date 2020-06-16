.. _files:

-----
Files
-----

Prerequisites and Requirements
++++++++++++++++++++++++++++++

Review `NUTANIX FILES GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=Acropolis-File-Services-Guide-v22:Acropolis-File-Services-Guide-v22/>`_ and `FILE ANALYTICS GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=File-Analytics-v2_1%3AFile-Analytics-v2_1>`_ for all details including, but not limited to, prerequities and requirements before proceeding.

Creating a File Server
++++++++++++++++++++++

#. In the Prism web console, go to the *File Server* page by clicking **Home > File Server** in the left corner.

#. Click **+ File Server**.

   .. figure:: images/1.png

#. In the *New File Server* window, complete the **Name** and **File Server Storage** fields in *Basics*.

   .. figure:: images/2.png

#. For *Performance Configuration*, Files automatically recommends the number of file server VMs, vCPUs per VM, and memory per VM. You can also manually enter the file server configuration or select the configuration based on workload performance.
   - If you are satisfied with the recommendation, click Next.
   - To change the recommendation manually, click **Custom Configuration > Configure Manually** make your modifications, and click **Save**.

      .. figure:: images/3.png

   - To change the configuration based on performance requirements, enter the number of *Concurrent Connections* and the throughout in Mbps. Click **Save**.

      .. figure:: images/4.png

.. note::

   For the purposes of streamlining the POC, using 1 VLAN is recommended for both storage and client networks.

#. Complete the fields in the *Client Network* tab and click **Next**.

#. Complete the fields in the *Storage Network* tab and click **Next**.

#. Join the file server to an Active Directory domain within *Join AD Domain* tab and click **Next** to begin the installation process.

#. The **Summary** tab displays the size and capacity information for the new file server. Files automatically creates a protection domain name for the file server.  To change the protection domain name, replace the text in *Protection Domain Name* with the name of your protection domain. Click **Save** when finished.

Creating a File Share
+++++++++++++++++++++

This task details how to create new shares using the Nutanix file server.

A home share is the repository for the user's personal files and a general purpose share is the repository shared by a group. By default, a share is created for home directories for each file server. This share is distributed at the top-level directories. Shares created after the default share are distributed across the FSVMs at the share-level. For example, share 1 contains top level directories such as User Directory 1, User Directory 2, and User Directory 3. User Directory 1 might be placed on FSVM 1, User Directory 2 might be placed on FSVM 2, and User Directory 3 might be placed on FSVM 3. These shares are not recommended for use with home directories.

For Files, Shares have certain permission details. The permissions for each user are the following.

- HOME SHARES

   - Domain administrator: Full access
   - Domain User:          Read only
   - Creator Owner:        Full access (inherited only)

- GENERAL PURPOSE SHARES

   - Domain administrator: Full access
   - Domain User:          Full access
   - Creator Owner:        Full access (inherited only)

#. Click **Home > File Server** in the main menu.

#. Click **+ Share** in the right corner.

#. Complete the fields to create the file share. Click **Save**.

   - **NAME**: Enter the name for the share.
   - **FILE SERVER**: From the drop-down list, select the file server to place the shares.
   - **MAX SHARE**: (Optional) Type the maximum size for the share (in GiB).
   - **DESCRIPTION**: Type a description for the share for your information.
   - **Enable Access-Based Enumeration (ABE)**: Access-based enumeration (ABE) is a windows (SMB protocol) feature that allows the users to view only the files and folders to which they have read access when browsing content on the file server.
   - **Self Service Restore**: Allow the share users to restore files from snapshots.


#. Click **Save**.

What to do next
+++++++++++++++

Map the newly created share in your directory. In the Windows client, you can map to the network and create folders at the top level of the file share.

#. In the Windows client, go to your PC explorer and select **Map Network Drives**.

#. Select the drive letter to use for the network.

#. Click **Browse**.

---------------
Files Analytics
---------------

#. Go to **Support Portal > Downloads > Files** and download the File Analytics QCOW2 and JSON files.

#. In Prism, go to the *File Server* view and click the **Deploy File Analytics** action link.

.. figure:: images/5.png

#. In the *Deploy File Analytics* window, click **Deploy**.

#. Upload installation files.
   - In the *Upload installation binary* section, click on the **upload the File Analytics binary** link to upload the File Analytics JSON and QCOW files.
   - Under *File Analytics Metadata File (.Json)*, click **Choose File** to choose the downloaded JSON file.
   - Under *File Analytics Instalation Binary (.Qcow2)*, click **Choose File** to choose the downloaded QCOW file.
   - Click **Upload Now** after choosing the files.

#. Do the following in the indicated fields:

   - **Name**: Enter a name for the File Analytics VM (AVM).
   - **Storage Container**: select a storage container from the dropdown. The dropdown only displays file server storage containers.
   - **Network List**: Select a VLAN.

#. (optional) Check the **Show Advanced Settings** box and do the following in the indicated fields.

   - Memory (GiB): Enter the memory capacity for the Analytics VM.
   - VCPUs: Enter the number of vCPUs.
   - Cores: Enter the number of cores.
   - DNS Resolver IP: Enter a coma-separated list of DNS resolver IPs.
   - NTP Servers: Enter a coma-separated list of NTP servers.

#. Click **Deploy**.

Enabling Files Analytics
++++++++++++++++++++++++

#. In the *File Server* view in Prism, select the target file server.

#. Click **Manage roles** to add a file server admin user.

#. In the *File Server* view, select the target file server and click **File Analytics** in the tabs bar.

#. In the *Data Retention* field, select a data retention period.

#. In the *Enable File Analytics* dialog-box, enter the credentials as indicated:

   - (For SMB users only) In the SMB Authentication section, do the following in the indicated fields to provide SMB authentication details:
      - Username: Enter the AD username for the file server administrator, see File Analytics Prerequisites.
      - Password: Enter the AD user password for the file server administrator.
   - (For NFS users only) In the NFS Authentication section, do the following in the indicated fields to provide NFS authentication details:
      - LDAP Server URI: Enter the URI of the LDAP server.
      - Base DN: Enter the base DN for the LDAP server.
      - Password: Enter the LDAP user password for the file server administrator.
