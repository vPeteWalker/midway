.. _pcconfig:

---------------------------
Prism Central Configuration
---------------------------

Similar to Prism Element, you'll want to configure and verify a few settings before diving in with all of the various operations that can be performed in Prism Central. In this module you'll validate network settings, configure Active Directory integration, and upload the additional disk images used throughout the POC guide.

**Expected Module Duration:** 15 minutes
**Covered Test IDs:** N/A

You will have considerable amounts of time to walk the customer through Prism Central menus and features, but can still begin with a high level overview of the **Main Dashboard**.

*Similar to Prism Element, the Prism Central dashboard puts key status and performance information at the administrator's fingertips. The key difference being that Prism Central is now aggregating data from all registered clusters, and you have the ability to customize (or add additional) dashboards to highlight the information most important to your teams.*

**FEEDBACK** ?

Network Settings
++++++++++++++++

#. Select :fa:`bars` **> Prism Central Settings**.

   .. figure:: images/0.png

#. Under **Network > Name Servers**, verify the DNS entries are correct.

#. Under **Network > NTP Servers**, specify your NTP Server IP/Hostname and click **+ Add**. Repeat as necessary to add multiple NTP servers.

   .. note::

      This configuration does **NOT** carry over from the Prism Element configuration.

#. Under **Alerts and Notifications > SMTP Server**, specify your **SMTP Server Settings** to match the values used in Prism Element.

#. (Optional) To configure SNMP based alerting for the cluster, see complete instructions in the `Prism Central Guide <https://portal.nutanix.com/page/documents/details/?targetId=Prism-Central-Guide-Prism-v5_17:mul-snmp-configure-pc-t.html#ntask_jqd_fd4_kbb>`_.

#. (Optional) If the customer has an available Syslog server, you can enable Audit, API Audit, and Flow logging under **Alerts and Notifications > Syslog Server**. See complete instructions in the `Prism Central Guide <https://portal.nutanix.com/#/page/docs/details?targetId=Prism-Central-Guide-Prism-v5_17:mul-syslog-server-configure-pc-t.html>`_.

Disk Images
+++++++++++

*Similar to other public clouds, AHV provides a centrally managed image store, making it easy to provision new VMs from Disk or .ISO images. AHV can ingest multiple disk formats, including raw, QCOW2, and VMDK.*

#. Select :fa:`bars` **> Virtual Infrastructure > Images**.

#. Click **Add Image**.

#. If uploading directly from the browser:

   - Select **Image File** and click **+ Add File** to browse for your local **CentOS7.qcow2** disk image.
   - Click **+ Add File** again to browse for your local **Windows2016.qcow2** disk image.
   - Click **Next**.
   - Verify **All clusters** is selected, and click **Save** to begin the file upload.

   .. figure:: images/1.png

   .. note::

      **Do not close the browser window while uploading!** You can still perform other Prism tasks in another tab.

#. If downloading images from the HPOC network or Internet:

   - Select **URL** and provide the appropriate URL for your **CentOS7.qcow2** disk image.

      - **HPOC (PHX)** - http://10.42.194.11/workshop_staging/CentOS7.qcow2
      - **Internet** - https://get-ahv-images.s3.amazonaws.com/CentOS7.qcow2

   - Click **Upload file**.
   - Provide the appropriate URL for your **Windows2016.qcow2** disk image.

      - **HPOC (PHX)** - http://10.42.194.11/workshop_staging/Windows2016.qcow2
      - **Internet** - https://get-ahv-images.s3.amazonaws.com/Windows2016.qcow2

   - Click **Upload file**.

   .. figure:: images/2.png

   - Click **Next**.
   - Verify **All clusters** is selected, and click **Save** to begin the file upload.

   *For complex environments, where Prism Central manages multiple clusters, you can leverage Categories to tag images and define policies that control which image categories and provisioned on which clusters. This provides fine grained control from a centralized location, ensuring that only required images are sent over the WAN to remote sites.

Active Directory
++++++++++++++++

While using the local Prism admin account keeps a POC simple, most organizations will want to understand how they can provide access to multiple users, with varying levels of privileges. Using either the pre-packaged AD environment, or the customer's AD, integration into Prism Central is straight forward. While not explicitly covered in this guide, Prism Element can also be similarly integrated with Active Directory to provide better audit logging within Prism Element functions. However, Prism Element lacks the same RBAC controls as Prism Central, and is limited to either Admin or Read-Only access.

.. note::

   In addition to Active Directory/LDAP, Prism Central can also use SAML-based identity providers for authentication. For complete instructions, see the `Nutanix Security Guide <https://portal.nutanix.com/page/documents/details/?targetId=Nutanix-Security-Guide-v5_17%3Amul-security-authentication-pc-t.html>`_.

#. Select :fa:`bars` **> Prism Central Settings**.

#. Under **Users and Roles > Authentication**, click **+ New Directory**.

#. If using the pre-packaged **AutoAD**, use the following values, otherwise use the customer-provided data.

   .. note::

      For customer-provided AD, see the `Nutanix Security Guide <https://portal.nutanix.com/page/documents/details/?targetId=Nutanix-Security-Guide-v5_17%3Amul-security-authentication-pc-t.html>`_ for complete details on **Directory URL** ports and which **Search Type** to use.

      Also recommended that the Service Account have Domain Admin privileges because I can't find any documentation on minimum required privileges anywhere. **FEEDBACK** - Have you?!

   - **Name** - NTNXLAB
   - **Domain** - ntnxlab.local
   - **Directory URL** - ldap://*AutoAD-IP-Address*:389
   - **Search Type** - Non-Recursive
   - **Service Account Username** - Administrator@ntnxlab.local
   - **Service Account Password** - nutanix/4u

#. Click **Save**.

#. To complete Active Directory configuration, you must map AD users to Prism Central roles. Under **Users and Roles**, click **+ New Mapping**.

#. If using **AutoAD**, specify either the **SSP Admins** group or an individual **adminuser** account. Choose the **Cluster Admin** role and click **Save**.

   .. figure:: images/3.png

   .. note::

      Refer to :ref:`ntnxlab` for details on AD Security Groups, user accounts, and passwords.

#. If using customer-provided AD, specify a Security Group or individual user account to extend the **Cluster Admin** role.

#. Sign out of Prism Central.

   .. figure:: images/4.png

#. Log in as an AD user mapped in the previous step.

   .. note::

      You need to use the username@FQDN format when authenticating.

   .. figure:: images/5.png

   *And now the fun starts!*
