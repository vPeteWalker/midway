.. _hpoc:

--------------
Using the HPOC
--------------

Creating A Reservation
++++++++++++++++++++++

Hosted POC cluster reservations are managed using the Reservation System: https://rx.corp.nutanix.com/. Both RX and the HPOC require users to be connected to the VPN in order to access.

#. Connect to the corporate network using GlobalProtect VPN and your Okta credentials.

   .. note::

      If you do not have the client installed, log in `here <https://gp.nutanix.com/>`_ and download/install the correct client for your OS. Use **gp.nutanix.com** as the **Portal Address**.

#. Open https://rx.corp.nutanix.com/ in your browser and click **Login**.

#. Provide your e-mail address and Okta password and click **Login**.



#. Set your correct time zone using the dropdown menu on the uppper right-hand side to ensure your reservation begins and ends at the correct time.

#. Click **Reserve a Cluster > Nutanix Hosted**.

   .. figure:: images/0.png

#. Select the desired datacenter. These are the most commonly used.

   - **Sales - PHX** (Phoenix, AZ, USA)
   - **Sales - NX-US-East** (Durham, NC, USA)
   - **Sales - NX-BLR** (Bangalore, India)

#. Specify the date range you are seeking for your reservation.

   .. note::

      Be aware that selecting the *Available Clusters Only* filter will only show clusters that are available for the entire selected date range. For POCs, it is recommended to use the **All Flash** filter.

   .. note::

      The **FROM:** time indicates when the reservation will start, the Foundation process will take ~1 hour of additional time before the cluster is ready to be used.

   .. note::

      It is recommended to start the reservation several hours before you need the cluster to be available to allow time for troubleshooting/re-imaging if you experience Foundation issues.

#. Green bars indicate the cluster is available during that time frame, click any green area to begin creating a reservation.

   .. figure:: images/1.png

#. Confirm the duration of your POC by clicking **Confirm**.

#. Fill out the fields in the *Purpose* section, ensuring you choose **Customer POC** from the *Reason* dropdown.

#. Click **Next**.

   .. note::

      See `SE Wiki - Salesforce POC Process <https://confluence.eng.nutanix.com:8443/pages/viewpage.action?pageId=53219016>`_ for complete instruction on how to create POC records in Salesforce.

#. Fill out all fields in the *Customer Information* section. It is recommended to choose **Do Not CC Customer**, as you can always choose to forward the relevant information to the customer at a later date.

#. Click **Next**.

#. Select **Use a Runbook**, and then click **Select** for *POC Workshop*.

#. Click **Next**.

#. After reviewing the details of your reservation to ensure they are correct, click **Confirm**. You will receive an e-mail shortly containing the details of your reservation, but you can always return to the reservation system to show all active reservations.

Troubleshooting HPOC Clusters
+++++++++++++++++++++++++++++

If you experience issues with RX failing to properly Foundation a cluster, network connectivity issues, etc., you can engage the Corp SE team responsible for the HPOC in `#rx-and-hpoc <slack://channel?team=T0252CLM8&id=C0JSE04TA>`_ on Slack.

Accessing HPOC Clusters
+++++++++++++++++++++++

When your reservation begins, Foundation will begin on your cluster. Once complete, you will receive an e-mail from **automation@nutanix.com** (be sure to check your Junk/Clutter folder!) with the following details:

::

   Your Reservation Information for PHX-POC093 (NX-1065-G5).

   Reservation ID: 108306
   Start Date: 2020-05-20 02:00
   Stop Date: 2020-07-31 02:00
   Timezone: America/New_York
   Reason: Personal Development & Testing
   Notes: Midway Dev

   Cluster IP: https://10.42.93.37:9440/console/#login

   Position: A CVM IP: 10.42.93.29 Hypervisor IP: 10.42.93.25 IPMI IP: 10.42.93.33
   Position: B CVM IP: 10.42.93.30 Hypervisor IP: 10.42.93.26 IPMI IP: 10.42.93.34
   Position: C CVM IP: 10.42.93.31 Hypervisor IP: 10.42.93.27 IPMI IP: 10.42.93.35
   Position: D CVM IP: 10.42.93.32 Hypervisor IP: 10.42.93.28 IPMI IP: 10.42.93.36


   LOGIN CREDENTIALS

   Prism UI Credentials: admin/XXXXXXXXX
   CVM Credentials: nutanix/XXXXXXXXX


   NETWORK INFORMATION

   Subnet Mask: 255.255.255.128
   Gateway: 10.42.93.1
   Nameserver IP: 10.42.194.10


   SECONDARY NETWORK INFORMATION

   Secondary VLAN: 931
   Secondary Subnet: 255.255.255.128
   Secondary Gateway: 10.42.93.129
   Secondary IP Range: 10.42.93.132-254

   HOSTED POC LAB - ACCESS INSTRUCTIONS
   Access to the Nutanix Hosted POC Lab environment is available via virtual desktops (Parallels/Frame) or via VPN.


   -------------------------
   Lab Access User Credentials
   -------------------------
   20 x VDI/VPN User Accounts: PHX-POC093-User01, PHX-POC093-User02 … PHX-POC093-User20 etc.
   VDI/VPN User Password: XXXXXXXXXX

   -------------------------
   Lab Access Methods
   -------------------------
   Parallels VDI
   1. Login to https://xld-uswest1.nutanix.com (for PHX) or https://xld-useast1.nutanix.com (for RTP) using your supplied credentials
   2. Select HTML5 (web browser) OR Install the Parallels Client
   3. Select a desktop or application of your choice.

   Frame VDI
   1. Login to https://frame.nutanix.com/x/labs using your supplied credentials
   2. Select the most applicable datacenter launchpad for the clusters you will be accessing or modify an existing selection using the breadcrumb menu at the top-center of the page
   3. Launch desktop
   For further guidance on features like clipboard sync, Frame file transfers, etc. SEs can reference: Frame Tips

   Pulse Secure VPN Client
   1. If client already installed skip to step 5
   2. To download the client, login to https://xlv-uswest1.nutanix.com or https://xlv-useast1.nutanix.com using the supplied user credentials
   3. Download and install client
   4. Logout of the Web UI
   5. Open client and ADD a connection with the following details:

   Type: Policy Secure (UAC) or Connection Server(VPN)
   Name: X-Labs - PHX
   Server URL: xlv-uswest1.nutanix.com

   OR

   Type: Policy Secure (UAC) or Connection Server(VPN)
   Name: X-Labs - RTP
   Server URL: xlv-useast1.nutanix.com

   6. Once setup, login with the supplied credentials

   HOSTED POC LAB - FILE TRANSFERS/UPLOADS
   Use a sftp client like Filezilla to access sftp externally from the internet at:
   xlf-uswest1.nutanix.com
   Username: nutanix
   Password: nutanix/4u

   Use a sftp client like Filezilla to access sftp internally at:
   xlf-uswest1.nutanix.com
   Username: nutanix
   Password: nutanix/4u

   IMPORTANT: The SFTP site is a shared environment. Anyone with the above credentials can view, modify, and delete data.
   Don’t upload sensitive information, and please deleted your data immediately after use.

At this time, ensure the customer is able to access the HPOC cluster using either **Frame**, **Pulse Secure VPN**, or **Parallels** using one of the 20 pre-created **Lab Access User Credential** accounts.
