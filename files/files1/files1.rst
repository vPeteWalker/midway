.. _files1:

-------------------
Starting with Files
-------------------

Nutanix Files (Files) lets you to share files across user workstations in a centralized and protected location to eliminate the requirement for a third-party file server.

Files uses a scale-out architecture that provides file services to clients through the Server Message Block (SMB) or Network File System (NFS) protocol. Files consists of one or more file server VMs (FSVMs) combined into a logical file server instance sometimes referred to as a Files cluster. Files supports creating multiple file server instances within a single Nutanix cluster.

In this module you will deploy Files and Files analytics, providing the foundation for creating, configuring, and testing SMB shares and NFS exports.

**Prerequisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** 60 minutes

**Covered Test IDs:** N/A

.. note::

   Review `NUTANIX FILES GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=Files-v35:Files-v35>`_ and `FILE ANALYTICS GUIDE <https://portal.nutanix.com/page/documents/details/?targetId=File-Analytics-v2_1%3AFile-Analytics-v2_1>`_ for all details including, but not limited to, prerequities, requirements, and recommendations before proceeding.

Environment
+++++++++++

There are many options at various stages that are available to configure Files to suit the needs of our customers. This workshop will focus on the following configuration.

   - One File Server
      - Basic configuration - 3 File Server VMs (FSVM)
   - Two SMB file shares
      - smb01 (normal)
      - smb02 (distributed)
   - One NFS export
      - logs
   - One hypervisor
      - AHV
   - Authentication
      - Microsoft Active Directory - via AutoAD VM
      - Customer-provided Active Directory
   - One VLAN
      - Managed (IPAM configured)
   - **Files Analytics**

.. note::

   Please be aware that any information such as server names, IP addresses, and similar information contained within any screen shots are strictly for demonstration purposes. Do not use these values when proceeding with any of the steps contained within this workshop.

   You are welcome to vary your inputs compared to the instructions listed below, please be aware that by diverting from these instructions, you may negatively impact your ability to successfully complete this workshop.

   Refer to :ref:`ntnxlab` for details on AD Security Groups, user accounts, and passwords when using the AutoAD VM.

Creating a File Server
++++++++++++++++++++++

#. In the Prism web console, go to the *File Server Dashboard* page by clicking **File Server** from the dropdown.

#. Click **+ File Server**.

   .. figure:: images/1.png

#. In the *New File Server: Pre-Check* window, review the displayed information and address any unsatisfied prerequisites before clicking **Continue**.

   .. figure:: images/2.png

#. The *Create File Server* window appears and displays the *Basics* tab. Do the following in the indicated fields:

   - **Name**: Enter **Files** as the name for the file server.

      The file server name is used by clients to access the file server. The fully qualified name (file server name + domain) must be unique.

   - **Domain**: **ntnxlab.local** if using AutoAD, otherwise customer-provided Active Directory domain.

   - **File Server Storage**: Enter the file server total storage size (minimum 1 TiB).

   .. figure:: images/3.png

   - Click the **Next** button.

#. In the *Client Network* tab:

   - **VLAN**: Select the **Primary** VLAN for the *client network* from the pull-down list.

   - **DNS Resolver IP**: Enter IP address for your AutoAD VM, or customer-provided domain controller.

      .. figure:: images/4m.png

   - When all the entries are correct, click the **Next** button.

#. In the *Storage Network* tab, do the following in the indicated fields:

   - **VLAN** - Select the **Primary** VLAN for the *client network* from the pull-down list.

      .. figure:: images/6m.png

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

      .. figure:: images/nfs-unmanaged.png

   - When all the entries are correct, click the **Next** button.

#. In the **Summary** tab, review the displayed information. When all the information is correct, click **Create**.

   .. figure:: images/8.png

Creating the file server begins. You can monitor progress through the **Tasks** page.

   .. note::

      If you accidentally did not configure Files to use the Active Director domain controller (AutoAD or customer-provided) as the DNS server, after deploying the File Server you will get the following errors.

         - DNS 'NS' records not found for *domain*

         - Failed to lookup IP address of *domain*. Please verify the domain name, DNS configuration and network connectivity.

      This can easily be corrected after deployment, without having to delete and redeploy the Files Server.

         - Within the **File Server** dropdown, select the file server you deployed, and click **Update > Network Configuration**. Modify the entry for *DNS Resolver IP*, and click **Next > Save**.

         - Click **DNS**. Update this page with the AutoAD FQDN **dc.ntnxlab.local** (or customer-provided), Username and Password of an Active Directory user with administrator privileges, and click **Submit**.

            .. figure:: images/9.png

Deploying Files Analytics
+++++++++++++++++++++++++

.. note::

   As of Files Analytics version 2.2.0, only SMB file shares are supported. If you are only testing using NFS exports, you may skip this section.

#. In **Prism > File Server**, click **Deploy File Analytics**.

   .. figure:: images/11d.png

#. Select the latest available version to download directly to the cluster and click **Next**.

   .. figure:: images/11b.png

   .. note::

      If delivering an on-prem POC and the customer has a slow Internet connection, you can pre-download the Files Analytics image from https://portal.nutanix.com and upload through Prism at this stage.

#. Fill out the following fields:

   - **Name** - POC
   - **Server Size** - Small
   - **Storage Container** - Select your **Default** container
   - **Network List** - Select your **Primary** VLAN

   .. figure:: images/11c.png

#. Click the **Show Advanced Settings** box. Set the **DNS Resolver IP (Comma Separated)** to the IP address of your AutoAD VM, or customer-provided domain controller.

      .. figure:: images/11a.png

#. Click **Deploy**.

   Verify that the deployment process has completed before proceeding.

#. In the *File Server* view, select the target file server, and click **File Analytics** in the tabs bar. This will open a new browser tab.

#. In the *Enable File Analytics* dialog-box, enter the AD username and password for the file server administrator, and click **Enable**.

Enabling Files Analytics
++++++++++++++++++++++++

#. In the *File Server* view, select the target file server and click **File Analytics** in the tabs bar.

#. In the *Enable File Analytics* dialog-box, in the *SMB Authentication* section, enter the AD username and password for the file server administrator (e.g. ntnxlab\\Administrator).

#. Click **Enable**.

   .. note::

      To update DNS server settings on File Analytics VM after deployment:

       - Login into File Analytics VM CLI using

         - User: nutanix

         - Password: nutanix/4u

       - Execute the following command. Click the icon in the upper right corner of the window below to copy the command to your clipboard, and then paste within your SSH session.

         ::

            sudo bash /opt/nutanix/update_dns.sh

         .. code-block:: bash

            (test) sudo bash /opt/nutanix/update_dns.sh

WHAT TO DO NEXT
+++++++++++++++

You may wish to continue to the next section :ref:`files2`, which outlines creating and testing an SMB share, and includes File Analytics. Otherwise, you can proceed to the :ref:`files3` section, which outlines creating and testing an NFS export.
