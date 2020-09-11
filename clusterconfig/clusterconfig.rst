.. _clusterconfig:

---------------------
Cluster Configuration
---------------------

In this module you will perform initial cluster configuration, including Pulse, network settings, alerting, deploying storage, and optionally deploying the AutoAD VM.

**Expected Module Duration:** 60 minutes

**Covered Test IDs:** `Core-003, Core-004, Core-005, Core-006, Core-008 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

This initial configuration needs to be completed for both a Hosted or on-prem POC. These steps could be performed without the customer present, but there is opportunity throughout to call out Nutanix differentiators and to highlight the simplicity of the deployment process.

Initial Login
+++++++++++++

#. In a browser, open your **Cluster Virtual IP** (or any CVM IP).

   .. note::

      Just entering the IP in the browser will redirect to the proper port: **https://<Cluster>:9440**. However, if you specify **https://** you must explicitly specify port **9440**.

   .. note::

      You will be prompted about making an unsecure connection as a signed SSL certificate is not installed on the POC cluster. Accept the warning and proceed to the address.

      This can be tricky using Chrome in macOS Catalina and above, as the browser will not show an **Advanced > Proceed to...** option. In order to `bypass this <https://stackoverflow.com/questions/35274659/does-using-badidea-or-thisisunsafe-to-bypass-a-chrome-certificate-hsts-error>`_ you must type **thisisunsafe** anywhere in the browser window. Annoying, I know. Alternately you can use a different browser.

#. Login as **admin**:

   - **HPOC** - You have been provided this password in your **automation@nutanix.com** e-mail.
   - **On-prem** - Use the default *Nutanix/4u* password and update when prompted.

#. Have the **customer** accept the EULA.

#. Unless otherwise directed by the customer, leave Pulse enabled and click **Continue**.

   *This is an opportunity to quickly highlight the benefits of Pulse to a customer, including faster time to resolution in the event of technical issues. For details on what information Pulse collects and how customer data is kept secure, see* `here <http://go.nutanix.com/rs/nutanix/images/pulse-datasheet.pdf>`_ and `here <https://portal.nutanix.com/page/documents/solutions/details?targetId=TN-2133-Nutanix-Pulse-Remote-Diagnostics:TN-2133-Nutanix-Pulse-Remote-Diagnostics>`_

#. Give a quick overview of the Prism Element dashboard: *The dashboard view provides key information an infrastructure or virtualization administrator would want at their fingertips, including inventory, performance and utilization metrics, cluster health, and alerting. We can dive deeper into each of these areas once we have completed setting up the cluster and have deployed some workloads.*

   .. figure:: images/0.png

#. It's not uncommon to have some alerts in a freshly imaged cluster. Select **Alerts** from the dropdown menu, select all alerts, and click **Resolve**.

   .. figure:: images/1.png

   .. note::

      These initial alerts are often a result of lack of network connectivity during the Foundation process on a flat switch with no access to the customer's network or Internet. If the alerts re-occur after resolving the first time, they should be investigated further to make sure there are no configuration issues present.

#. Select **Health** and under **Summary**, select **Failed** to view any failed checks.

   .. figure:: images/2.png

#. Select a **Failed** check to view information about the potential cause of the failure. You can manually run the NCC check again by clicking **Run Check**.

   This is an opportunity to provide a quick background on NCC: *Nutanix Cluster Check, or NCC, is a constantly growing set of tests used to evaluate the health of the cluster. These checks can be updated out of band of AOS or your hypervisor, and requires no CVM or host reboots. As it is very low impact, we recommend always keeping NCC up to date and running it prior to significant events like AOS upgrades or cluster expansions.*

   Once the check has completed running you can check the status under **Tasks** by clicking the **Succeeded** link, the NCC output can also be downloaded here for more verbose output.

   .. figure:: images/3.png

   If the check fails, see the associated Nutanix KB article listed under **References** or in the NCC output. Alternately, contact Nutanix support for additional assistance.

   .. figure:: images/4.png

Cluster Settings
++++++++++++++++

#. Next we'll validate network details set during the Foundation process. Start by selecting **Settings** from the dropdown menu.

   Under **Network**, select **Name Servers** and verify your DNS entries are correct. Select **NTP Servers** and verify your entries are correct.

   .. figure:: images/5.png

   Under **General**, select **Cluster Details** and verify **Cluster Virtual IP Address** is correct.

   If on-prem, specify a static IP for **iSCSI Data Services IP** (in your CVM/hypervisor VLAN) and click **Save**. This IP is used to present block storage volumes directly to VMs or baremetal hosts and is required for Calm, Files, Karbon, Era, and others to function.

   .. figure:: images/6.png

   The **iSCSI Data Services IP** should already be set if using the HPOC.

#. (Optional) Set up e-mail alerting from the cluster to a customer e-mail address. In **Settings**, under **Email and Alerts**, select **SMTP Server**.

   This will already be configured for HPOC clusters, otherwise specify **SMTP Hostname**, **Port**, **Security Mode** and **From Email Address** using customer provided details.

   .. note::

      The customer may need to whitelist all CVM IPs and Cluster Virtual IP with their SMTP server in order to successfully send alert e-mails.

   Under **Email and Alerts**, select **Alert Email Configuration** and add a comma separated list of all recipients who should receive alerts. Include your own e-mail, so you are alerted to any potential issues throughout the duration of the POC. Click **Save**.

   .. figure:: images/7.png

   *Alert Policies in Prism Central provides a powerful rules engine to configure specific alerts, or types of alert, to be sent to different groups, such as a wider mailing list of users being alerted to a critical memory utilization alert.*

#. (Optional) To configure SNMP based alerting for the cluster, see complete instructions in the `Prism Web Console Guide <https://portal.nutanix.com/page/documents/details/?targetId=Web-Console-Guide-Prism-v5_17:wc-system-snmp-profiles-wc-t.html>`_.

#. In **Settings**, under **Data Resiliency**, select **Manage VM High Availability**. Select **Enable HA Reservation** and click **Save**.

   *In AHV, High Availability (HA) and real-time VM load balancing, what we call Acropolis Dynamic Scheduler (ADS), are enabled out of the box. Enabling HA Reservation ensures you have N+1 amount of memory available so all running VMs are able to restart on other nodes in the event of a host failure. 1-Click HA!*

Lifecycle Manager
+++++++++++++++++

*Lifecycle Manager, or LCM, is the new home for enterprise-grade 1-Click upgrades for your Nutanix environment. We'll dig into LCM later in the POC to perform upgrades to AOS, our hypervisor, and other services - for now we'll start the inventory process to determine current software and firmware versions.*

#. Select **LCM** from the dropdown menu.

#. To start the inventory process to populate software and firmware versions for your cluster, select **Options > Perform Inventory**.

   .. figure:: images/8.png

   *The LCM Framework can be updated independent of other cluster services, meaning you can be sure you're taking advantage of the latest update checks and fixes without disruption to your cluster.*

#. Select **Enable LCM Framework Auto Update** and click **OK**. Continue while the LCM inventory process runs in the background.

Storage Configuration
+++++++++++++++++++++

*Next we'll deploy storage for our virtual machines to use. One of the key benefits of Nutanix is the lack of tuning required to provision storage ready to run your VMs.*

#. Select **Storage** from the dropdown menu.

*Similar to the Dashboard view, the Storage Overview provides key metrics relevant to storage, including capacity, data efficiency, performance, and alerting.

#. Select the **Table** view.

*The two main storage concepts in Nutanix are a Storage Pool and a Storage Container. The Storage Pool is simply the aggregation of all physical disks within the cluster. There is only one Storage Pool, as the Nutanix distributed storage fabric is intelligently spreading data across all physical disks to provide optimal performance and capacity utilization - no multiple LUNs or volumes to manage separately. Storage Containers are logical policies that apply to the Storage Pool (in vSphere each Storage Container would be presented as a Datastore to the hypervisor). Container policies allow you to do things like turn on and off different data efficiency settings like compression or erasure coding.*

*While the cluster already has a default container, we'll create an additional container to show you how simple the process is. Typically you would only have multiple containers when there are different data efficiency requirements, for example, not wanting compression enabled on a datastore primarily storing pre-compressed data such as video files. Alternatively you may create different storage containers to map to different projects or business units for reporting or quota purposes.*

#. Click **+ Storage Container**.

   .. figure:: images/9.png

#. Specify a **Name** (e.g. POC-Compression), and click **Advanced Settings**.

#. Select **Compression** and specify a **60 Minute Delay**. Do **NOT** enable Deduplication.

   .. figure:: images/10.png

   *Compression is a great option for nearly all workloads, except for pre-compressed datasets. Erasure Coding is another option that can be used to minimize the storage footprint of your RF2 and RF3 replicas for write-cold data. Deduplication is appropriate for full byte-copy clones of VMs. In traditional storage arrays deduplication can also be helpful for eliminating zeros, but as Nutanix doesn't write zeros to begin with, we save that capacity without incurring any of the overhead of deduplication.*

   *New production, all flash deployments will default to 0 min (Inline) Compression. Post Process Compression is used in the POC for the purposes of maximizing performance, especially when comparing performance figures to other platforms (e.g. VSAN will likely not test performance with storage efficiencies enabled).*

#. Click **Save**.

#. Select the newly created container.

   *Through Prism you can see your storage utilization, performance, and alerting on a per container basis. Within the Storage Container Details you'll find information on the effective free storage, which takes into account savings from compression, erasure coding, and deduplication. Overall efficiency ratio accounts for compression, erasure coding, deduplication, AND savings from data avoidance such as snapshots and cloning.*

   .. figure:: images/10b.png

#. (Optional) Create an additional Storage Container with a **Reserved Capacity** equal to available capacity of 1 node. This will ensure that in the event of a node failure, you are guaranteed to have enough capacity to re-protect all data on the cluster.

Network Configuration
+++++++++++++++++++++

*Before we deploy any VMs, we first need to review physical network connectivity and configure virtual networks, both of which are done in Prism for AHV clusters.*

#. Select **Network** from the dropdown menu.

#. Verify you have an active 10Gb+ connection from each node to your switch.

   .. figure:: images/12.png

   .. note::

      By default, AHV clusters include all physical network interfaces in br0, in an Active/Backup configuration. This is the recommended configuration for POCs.

      Alternatively, you can change to software based Active/Active (MAC pinning) or LAG based Active/Active (requiring LACP configuration on switch ports) under **Network > + Uplink Configuration**.

      Full instruction for updating uplink modes can be found `here <https://portal.nutanix.com/page/documents/details/?targetId=Web-Console-Guide-Prism-v5_17%3Awc-uplink-configuration-c.html>`_.

#. Select **VM** from the dropdown menu and click **Network Config**.

   .. figure:: images/11.png

#. If using a HPOC cluster, click the **X** icon beside **Rx-Automation-Network** to remove the default network.

#. Click **Create Network**.

   *This is the primary network we will use for VMs in the POC. For simplicity, it is the same VLAN used by the CVMs and hypervisor. In addition to adding the virtual network, we'll also configure AHV's integrated IP Address Management to provide IP assignment to VMs on this network. This can potentially eliminate the need for separately managed DHCP services in an environment. Rather than depending on lease times, AHV IPAM will assign addresses for the life of a VM, and also makes static assignments simple at the time of VM creation. The prospect may require use of their own DHCP solution. If that is the case, do not enable IPAM in the below step.*

#. Provide a name for the network. This guide will consistently refer to this as your **Primary** network throughout.

#. Provide the VLAN ID for your CVM/hypervisor network. For HPOC clusters, this will be **0**.

   .. figure:: images/13.png

#. Select **Enable IP address management** and use customer provided values, or the following if using a HPOC cluster:

   - **Network IP Address/Prefix Length** - Use the first three octets of **YOUR** HPOC cluster IP, followed by a 0. The prefix length for a 255.255.255.128 network is /25. (e.g. 10.42.93.0/25)
   - **Gateway IP Address** - Found in your **automation@nutanix.com** Reservation e-mail. (e.g. 10.42.93.1)
   - **Domain Name Servers** - Found in your **automation@nutanix.com** Reservation e-mail, varies based on HPOC datacenter.
   - **Domain Search** - ntnxlab.local
   - **Domain Name** - NTNXLAB

   .. note::

      For on-prem POCs, the `IP Subnet Calculator <https://www.calculator.net/ip-subnet-calculator.html>`_ is helpful for determining **Prefix Length** based on subnet mask, and also usable IP ranges for the **IP Address Pool**.

#. Click **+ Create Pool**:

   - **Start Address** - Use the first three octets of **YOUR** HPOC cluster IP, followed by 50. (e.g. 10.42.93.50)
   - **End Address** - Use the first three octets of **YOUR** HPOC cluster IP, followed by 125. (e.g. 10.42.93.125)

#. Save the network configuration.

#. (Optional) If planning to use X-Ray as part of your POC, click **+ Create Network** to create the additional virtual network that will be used by X-Ray worker VMs.

   - **Network Name** - XRay
   - **VLAN ID** - Use the **Secondary VLAN** found in your **automation@nutanix.com** Reservation e-mail for HPOC clusters. Otherwise use the customer provided VLAN ID.

   .. note::

      IPAM is not configured for the XRay virtual network as X-Ray can use self-assigned link local IP addresses to discovery and communicate with worker VMs.

   .. figure:: images/14.png

#. (Optional) Finally, to identify any bandwidth issues between CVMs, you can run a quick iPerf diagnostic from the CVM console. SSH into the **Cluster Virtual IP Address**:

   - **Username** - nutanix
   - **Password** - Your HPOC password or the default **nutanix/4u** password for on-prem POCs.

   Run the following command:

   ::

      ncc --ncc_enable_intrusive_plugins=true health_checks network_checks inter_cvm_bandwidth_check

   This test is not run as part of normal NCC checks as it stresses the network to determine maximum available bandwidth between CVMs. The test will FAIL if performing < 800MB/s on a 10Gb network, at which point further investigation to determine the source of the network issue is warranted. See `KB1634 <https://portal.nutanix.com/page/documents/kbs/details/?targetId=kA0600000008ec5CAA>`_ for more info.

(Optional) AutoAD Image Deployment
++++++++++++++++++++++++++++++++++

To streamline the POC deployment, we have provided a pre-packaged Windows Server Domain Controller to provide Active Directory services. Skip this section if the customer will be using their own Active Directory.

#. Select **Settings** from the dropdown menu. Under **General**, click **Image Configuration**.

#. Click **+ Upload Image** and fill out the following:

   - **Name** - AutoAD
   - **Annotation** - NTNXLAB.local Domain Controller
   - **Image Type** - Disk
   - **Storage Container** - Your previously created Storage Container with Post-Process Compression enabled
   - **Image Source**

      - If you have the AutoAD.qcow2 file downloaded, you can select **Upload a file**.

      .. note::

         **Do not close the browser window while uploading!** You can still perform other Prism tasks in another tab.

      - If on-prem with cluster Internet connectivity, select **From URL** - https://get-ahv-images.s3.amazonaws.com/AutoAD.qcow2
      - If PHX HPOC, select **From URL** - http://10.42.194.11/workshop_staging/AutoAD.qcow2
      - If RTP HPOC, select **From URL** - http://10.55.251.38/workshop_staging/AutoAD.qcow2
      - If BLR HPOC, select **From URL** - http://10.136.239.13/workshop_staging/AutoAD.qcow2
   .. figure:: images/15.png

#. Click **Save** to begin uploading/downloading the disk image. Status can be monitored in **Tasks**. While the download completes, proceed to `Prism Central Deployment`_ and return after the disk image task has completed.

#. Select **VM** from the dropdown menu.

#. Click **+ Create VM**.

   .. figure:: images/19.png

#. Fill out the following fields:

   - **Name** - AutoAD
   - **vCPU(s)** - 4
   - **Number of Cores Per vCPU** - 1
   - **Memory** - 4 GiB

#. Click **+ Add New Disk** and fill out the following:

   - **Type** - Disk
   - **Operation** - Clone from Image Service
   - **Bus Type** - SCSI
   - **Image** - AutoAD

#. Click **Add**.

#. Under **Network Adapters (NIC)**, click **+ Add New NIC**:

   - **Network Name** - Primary
   - **IP Address** - A static IP in your Primary network. For HPOC, XX.XX.XX.40 is recommended. This will be referenced as the **AutoAD IP** throughout the guide.

#. Click **Save**.

#. From the VM table, select **AutoAD** and click the **Power on** action.

   .. figure:: images/20.png

   Once booted, this VM will automatically begin installing Active Directory services. This process will take ~10 minutes.

#. After 10 minutes, select the VM and click **Launch Console**. If necessary, log in using the following credentials:

   - **Username** - NTNXLAB\\Administrator
   - **Password** - nutanix/4u

#. Verify that the **AD DS** and **DNS** roles appear green.

   .. figure:: images/21.png

   Before moving on, we'll want to update the IPAM settings of the virtual network to use the **AutoAD** VM as the primary DNS server, allowing VMs to join the domain for later exercises.

#. Select **VM** from the dropdown menu and click **Network Config**.

#. Beside the **Primary** network, click :fa:`pencil` to edit the configuration.

#. Under **Domain Name Servers**, replace the existing value with the IP of your **AutoAD** VM.

#. Click **Save** and then the **X** in the upper-right hand.

Prism Central Deployment
++++++++++++++++++++++++

*While you can operate a single Nutanix cluster without Prism Central, PC provides the ability to easily manage a large number of clusters, across datacenters, and provides advanced functionality such as Prism Ops for infrastructure analytics and automation, Calm for workload deployment and management automation, Leap for DR, and more.*

*Unlike traditional solutions requiring dedicated databases, licensing servers, and other components - Prism Central deploys as a virtual appliance, either as a single VM or a scale out cluster to provide redundancy and scale.*

#. Select **Home** from the dropdown menu. Under **Prism Central**, click **Register or create new**.

   .. figure:: images/16.png

#. Click **Deploy**.

   If you wish to show a Prism Central upgrade as part of the POC, select the second most recent **Available version** (ensuring **Show compatible versions** is selected), and click **Download**. Otherwise, download the most recent, compatible version.

   .. figure:: images/17.png

   .. note::

      If the cluster has slow or no Internet connectivity, you can also directly upload a previously downloaded Prism Central binary.

#. Select **Deploy 1-VM PC** and fill out the following fields:

   - **VM Name** - PrismCentral
   - **Select A Container** - You can leave the default
   - **VM Sizing** - Large (should be suitable for most every POC)
   - **AHV Network** - Primary
   - **IP Address** - A static IP address in your **Primary** network. For HPOC, XX.XX.XX.39 is recommended. This will be referenced as the **Prism Central IP** throughout the guide.

   .. figure:: images/18.png

#. Click **Deploy**.

   This process takes ~25 minutes, during which time you can return to `(Optional) AutoAD Image Deployment`_, if applicable, to complete the VM deployment.

   .. note::

      If you experience issues deploying Prism Central using the wizard, refer to the `PC Troubleshooting Guide <https://portal.nutanix.com/page/documents/kbs/details?targetId=kA032000000TT1MCAW>`_.

#. Once deployment has completed successfully (as seen in **Tasks**), browse to the **Prism Central IP** in a separate tab. Log in using the default credentials:

   - **Username** - admin
   - **Password** - Nutanix/4u

#. When prompted, change the password.

   .. note::

   For POC simplicity, we recommend setting the same password as Prism Element.

#. Log in using the new password, accept the EULA, and (recommended) enable Pulse.

#. In **Prism Element**, return to **Home** to register the cluster with Prism Central. Under **Prism Central**, click **Register or create new > Connect > Next**.

   .. figure:: images/16.png

#. Provide your **Prism Central IP** and **admin** credentials, then click **Connect**. Prism Central registration should only take a few seconds to complete.

   .. figure:: images/22.png

#. (Optional) Configure NTP server settings. Refer to the `Prism Web Console Guide - Recommendations for Time Synchronization section <https://portal.nutanix.com/page/documents/details?targetId=Web-Console-Guide-Prism-v5_18:wc-ntp-server-time-sync-recommendations-c.html>`_ for guidance on selecting the correct NTP servers available to you before you begin.

   - Click on :fa:`bars` **> Prism Central Settings > NTP Servers**.

   - Enter the IP address or (preferred for external servers) the FQDN of each NTP server, and click **Add** after each one.

#. Configure Name Server settings.

   - Click on :fa:`bars` **> Prism Central Settings > Name Server.

   - Delete and replace the existing value with the IP of your **AutoAD** VM.

   *From this point, Prism Central will be used for the majority of day to day monitoring and operations - providing you a user interface that can manage multiple clusters simultaneously. This includes clusters running different hypervisors and hardware platforms. Prism Central enables the ability to perform limited VM management, potentially eliminating the need to use a separate interface for some tasks.*
