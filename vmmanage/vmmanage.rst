.. _vmmanage:

-------------------
Basic VM Management
-------------------

In this module you will use Prism Central to perform basic AHV VM management tasks including creation, power operations, cloning, live migration, monitoring, Nutanix Guest Tools installation, and category assignment.

**Expected Module Duration:** 60 minutes

**Covered Test IDs:** Core-007, Core-012, Core-013

Creating VMs
++++++++++++

#. Select :fa:`bars` **> Virtual Infrastructure > VMs**.

   Optionally you can click :fa:`star` to make any nested Prism Central page a favorite, providing easy access directly below the Dashboard in the menu.

   .. figure:: images/0.png

   *By default, this view will show us all VMs across your environment. After we created additional workloads we can look at how to leverage the search and filtering capabilities.*

#. Click **Create VM** and fill out the following fields:

   - **Name** - WinServer
   - **vCPUs** - 4
   - **Number of Cores Per vCPU** - 1
   - **Memory** - 4 GiB
   - Click **+ Add New Disk**

      - **Type** - Disk
      - **Operation** - Clone from Image Service
      - **Bus Type** - SCSI
      - **Image** - Windows2016.qcow2
      - Click **Add**

   - Click **+ Add New NIC**

      - **Network Name** - Primary
      - Click **Add**

   - Click **Save**

   *Using the Windows2016 disk image we previously uploaded to the cluster, we'll create a VM using a space-efficient, redirect-on-write clone, and connect it to our Primary VM network. Optionally, we could specify a subset of hosts for the VM to run on, this can be helpful when satisfying licensing requirements or pinning VMs that benefit from higher clock speeds to nodes with specific processors. Using this dialog you can also pass in Sysprep unattend scripts to Windows guests, or cloud-init scripts to Linux guests.*

   *In this environment we're using a pre-built OS image as you would for the majority of your VM deployments on the cluster. However, if we were creating a Windows VM from installation media, we would also need to mount and install the VirtIO storage driver in order for Windows to find the virtual disk drive. Modern Linux distributions will include a version of this driver, so additional steps aren't required during installation.*

   .. note::

      A future version of this guide will include an appendix exercise for creating the OS images from .iso install media.

#. Select the checkbox beside the newly created VM and click **Actions > Power On**.

   .. figure:: images/1.png

#. Once powered on, click **Actions > Launch console**.

   *Like Prism itself, the VM console is fully HTML5 and requires no additional plugins to use. This is helpful for basic VM console access.*

#. Complete the Windows Server setup process and set the local Administrator password. **RECOMMENDED** to use **nutanix/4u** for simplicity.

#. Log in and verify that **Remote Desktop** and **Remote Management** are enabled.

   .. figure:: images/3.png

   .. note::

      You will also likely need to disable **Allow connections only from computers running Remote Desktop with Network Level Authentication** to successfully connect to your VM via RDP.

      .. figure:: images/3b.png

#. Return to Prism Central and note the **IP Address** of your **WinServer** VM.

   .. figure:: images/2.png

#. Connect to the VM using an RDP client (this will allow you to copy and paste text into the session) and the built-in Administrator account.

#. Open **PowerShell** and run the following commands to enable WinRM with SSL (required for Nutanix Guest Tools):

   .. code-block:: powershell
      :linenos:

      $c = New-SelfSignedCertificate -DnsName $env:computername -CertStoreLocation cert:\LocalMachine\My ;
       winrm create winrm/config/Listener?Address=*+Transport=HTTPS "@{Hostname=`"$env:computername`";CertificateThumbprint=`"$($c.ThumbPrint)`"}"
      cmd /c 'winrm set winrm/config/service/auth @{Basic="true"}';
      netsh advfirewall firewall add rule name=\"WinRM-HTTPS\" dir=in localport=5986 protocol=TCP action=allow

#. Return to Prism Central, select your VM and click **Actions > Soft Shutdown** to power off the VM.

#. Repeat **Step 2** and create a VM named **CentOS** using the **CentOS.qcow2** disk image.

Cloning VMs
+++++++++++

*You can rapidly create and automatically enumerate multiple clones through PC, with the ability to make configuration changes from your base VM. Because cloning on Nutanix is a simple metadata operation, cloning is instantaneous.*

*In a production environment using VMs on a domain, you would typically sysprep the VM we created in the previous exercise, potentially with a built-in unattend.xml script, and clone that version, ensuring each clone has a unique SID and domain identifiers.*

#. With the **WinServer** VM selected, click **Actions > Clone**.

#. Make the following changes and click **Save**:

   - **Number of Clones** - 5
   - **Prefix Name** - WinServer-

#. Select all 6 **WinServer\*** VMs and click **Actions  > Power On**.

   *PC also enables bulk operations for entities, in this case allowing you to batch power on a group of VMs.*

Nutanix Guest Tools
+++++++++++++++++++

.. note::

   Looks like bulk install isn't working as expected right now (https://jira.nutanix.com/browse/ENG-248471) and is fixed in 5.18.

*Nutanix Guest Tools is a package that can be installed in Windows and Linux guests to provide advanced capabilities including enabling self-service file level restore using VM snapshots, application-consistent snapshots, cross-hypervisor VM mobility between AHV and ESXi, and in-guest runbook scripting for Leap.*

.. note::

   Full requirements and limitations for Nutanix Guest Tools can be found in the `Prism Web Console Guide <https://portal.nutanix.com/page/documents/details/?targetId=Web-Console-Guide-Prism-v5_17%3Aman-nutanix-guest-tool-c.html>`_.

#. Select the **WinServer** VM and click **Actions  > Install NGT**.

#. Select **Enable Self Service Restore** and **Enable Volume Snapshot Service**. Select **Restart as soon as the install is completed**.

#. Click **Confirm & Enter Password**.

#. Provide the **WinServer** built-in Administrator credentials (e.g. Administrator, nutanix/4u).

#. Click **Done**.

*This will mount a customized NGT .iso image to each WinServer VM and begin the automated tools installation process.*

Updating VMs
++++++++++++

*AHV provides the ability to hot-add vCPUs (sockets) and memory to supported guest operating systems through Prism or ACLI. Additionally you can easily expand existing disks or add new virtual disks. No need to take app downtime just to increase resources when needed. You could also leverage X-Play to automate the process of adding resources based on VM utilization.*

.. note::

   See `AHV Administration Guide <https://portal.nutanix.com/page/documents/details/?targetId=AHV-Admin-Guide-v5_17%3Aahv-vm-memory-and-cpu-configuration-c.html>`_ for full AHV Hot-Plug documentation and Guest OS Compatibility.

#. Select **WinServer-1** and click **Actions > Update**.

#. Increase the **Memory** to 6 GiB.

#. Under **Disks**, click :fa:`pencil` next to your existing **DISK** and increase the size by 10 GiB. Click **Update**.

#. Click **+ Add New Disk** and allocate a new 100 GiB disk on the default container. Click **Add**.

   .. figure:: images/4.png

#. Click **Save**.

#. Launch the console for **WinServer-1** and login.

#. Open **Disk Management** and click **Actions > Rescan Disks** to see the updated disk configuration.

#. Extend the **C:** partition.

   .. figure:: images/5.png

#. Mark **Disk 1** online, initialize, and create a new simple volume (e.g. **E:**).

#. Open **Task Manager** and verify the guest sees the additional memory.

   .. figure:: images/6.png

Live Migration
++++++++++++++

*Similar to other hypervisors, AHV allows live migration between nodes in the cluster. Live migration between different clusters is currently on the roadmap.*

#. Open a console to one of your **WinServer** VMs and run ```ping -t <CLUSTER IP>``` to create a repeating activity within the guest.

   .. figure:: images/7.png

#. In Prism Central, note the current **Host** running the VM.

   .. figure:: images/8.png

#. Select **Actions > Migrate** and select a different host in the cluster. While watching the VM console, click **Submit**.

#. Verify the VM is now running on the selected host (may require refreshing Prism if you're impatient) and that there was no interruption to the guest.

#. Select **Actions > Update** and click **+ Set Affinity**.

#. De-select the current host, and select two or more of the remaining hosts. Click **Save > Save**.

   .. figure:: images/9.png

#. Note that the VM immediately moves to another host to comply with the affinity policy.

   .. note::

      This behavior should also be validated as part of any node failure testing during the POC.

Filtering and Searching
+++++++++++++++++++++++

*As Prism Central is designed to scale to environments with thousands of VMs, it's important to easily be able to filter large amounts of data or efficiently search for entities based on a wide number of parameters.*

#. Select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Select **Filters** and expand/select some of the available options.

   .. figure:: images/10.png

   *Imagine you have 10 different clusters being managed by Prism Central, and you want to identify just the VMs in two specific clusters with low memory utilization.*

#. Click in the **Search Bar** and click :fa:`star`.

   .. figure:: images/11.png

   *You can save sets of filters to be able rapidly access commonly used queries.*

#. Using your filtered list, click **Focus > Performance**.

   .. figure:: images/12.png

   *Each entity collects dozens of different metrics, so depending on your goal, you may want to see specific metrics for your filtered list. We include a few default views we think are helpful, but you can also easily create your own custom focus views.*

#. Click **Focus > + Add Custom**.

#. Provide a focus name (e.g. **POC**) and select a few metrics. Click **Save** to display your custom focus.

   .. figure:: images/13.png

   .. note::

      You can also show how filtering and focus can be used for other entity types in Prism Central, like Images, Hosts, etc. This makes for a very consistent and familiar workflow for managing the entire stack.

#. Click on the linked **Name** of one of your **WinServer** VMs.

   .. figure:: images/15.png

   *You can drill down into each entity (VMs, Hosts, Clusters, etc.) to view all of the metrics, alerts, etc. associated with that entity. For a VM you get a summary view of the most important information, but we can drill deeper into each of these areas, for instance, if we wanted to see a chart of the working set size of our VM over the past day.*

   .. figure:: images/16.png

#. Begin typing **Search Cheatsheet** in the **Search Bar** to access the **Search Guidelines**, which provides a few examples of how search can be used anywhere within Prism Central to provide fast navigation and filtering.

   .. figure:: images/14.png

Categories & RBAC
+++++++++++++++++

*Categories are a key component to how role based administration and policies (like VM protection/replication) are applied to entities in Prism Central. Each category is a key:value pair, where each named category could have several different values. For example, a VM could have Environment category assigned with a value of Production, Dev, Staging, or Testing. There are multiple built-in system categories that can be used, including adding custom values, or entirely custom categories can be created to suit the needs of your environment. To get you thinking about how they could be applied, we'll walk through a simple example using our WinServer VMs.*

#. Select :fa:`bars` **> Virtual Infrastructure > Categories**. Click **Show more** to show the values associated with the default categories.

#. Select the **Environment** category and click **Actions > Update**.

   .. figure:: images/17.png

#. Click **+** and add a **Value** named **POC**. Click **Save**.

   .. figure:: images/18.png

#. Return to :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Select your **WinServer** and **AutoAD** (if present) VMs and click **Actions > Manage Categories**.

#. Search for **Environment:Production** and click **+** to apply the category. Click **Save**.

   .. figure:: images/19.png

#. Select your **WinServer-\*** clones and click **Actions > Manage Categories**.

#. Apply the **Environment:POC** value and save.

   *Now we need to associate those categories with specific roles.*

#. Select :fa:`bars` **> Administration > Roles**.

   *Similar to categories, Prism Central provides some built-in roles. While these system roles can't be modified, you can easily duplicate a system role to use as a starting point for your own custom role, allowing you to select individual permissions related to VMs, Calm blueprints, networks, images, and more.*

   .. note::

      You can click **Create Role** to quickly show the available permissions. See the full list of permissions by expanding an entity and clicking **Change** next to **Set custom permissions**.

#. Select the **Operator** role and click **Actions > Manage Assignment**.

#. If using **AutoAD**, specify the **SSP Operators** security group. If using customer-provided AD, use the name of their pre-requisite Operator security group or individual user.

   .. figure:: images/20.png

   .. note::

      If AD is properly configured, these values should begin auto-completing as you type.

#. Under **Entities**, click the dropdown and note you can create role mappings to a number of different entities, including **Categories**. Select **Categories**.

   .. figure:: images/21.png

#. Specify the **Environment:Production** category and click **Save**.

#. Repeat **Steps 10-13** to create a role assignment for the **Developer** role to the **SSP Developers** security group for the **Environment:POC** category.

   .. figure:: images/22.png

#. Sign-out of Prism Central and login as a **Developer** user.

   .. figure:: images/23.png

#. Verify you no longer have access to cluster administration options, and see only entities with the **Environment:POC** category.

   .. figure:: images/24.png

#. Repeat as an **Operator** user and confirm you have access to manage the appropriate resources.

   *This simple, but powerful, policy engine can let you roll out self-service VM administration to your users, making sure the right people have access to the right resources and abilities. This can be further extended using Projects to help enforce quotas.*

Protecting VMs
++++++++++++++

*The new Protection Policies in Prism Central allow for VM-based assignment of storage protection for your VMs, and can leverage the same categories we used in the previous exercise. We'll create a simple policy to ensure hourly backup of all Production VMs.*

#. Sign-out of Prism Central and login as an **Admin** user.

#. Select :fa:`bars` **> Policies > Protection Policies**. Click **Create Protection Policy**.

#. Fill out the following fields:

   - **Name** - ProdVM-Protection
   - **Primary Cluster(s)** - *Our POC cluster*
   - **Recovery Location** - *We'll leave blank as we do not have a second cluster configured, this would be used for selecting remote replication or DR target.*
   - **Policy Type** - Asynchronous

      *AHV can support an async RPO as low as 1-minute, or even perform synchronous replication with other AHV clusters provided adequate bandwidth and a round trip latency < 5ms.*

   - **Retention Policy** - Roll-up
   - **Location Retention** - 7 days
   - Click **+ Add Categories**

      - Select **Environment:Production**
      - Click **Save**

   *This will ensure any existing VMs with this category assigned will automatically have this policy applied, as well as any newly created VMs assigned to the category.*

   .. figure:: images/25.png

#. Click **Save**.

   .. note::

      After a few moments you should see **Tasks** appear to protect Production VM entities.

#. Return to :fa:`bars` **> Virtual Infrastructure > VMs** and select your **WinServer** VM assigned to the **Environment:Production** category.

#. From the left-hand menu, select **Recovery Points** and note that you now see an available, local snapshot.

   .. figure:: images/26.png

#. Select the Recovery Point and click **Actions > Restore**. *This will allow us to create an instant clone of the VM using the crash consistent snapshot*.

   .. figure:: images/27.png

#. If desired, update the clone name. Click **Restore**.

#. Click **Back to VMs** and note the clone is already available to be powered on.

#. Select the **Clone** VM and note that it does not inherit the categories of its parent VM. Assign the **Environment:Production** category to the **Clone** VM and verify that after a few moments it is added to the policy and its inital snapshot is created.

   *We can also manually assign Protection Policies to VMs within Prism Central, without using categories.*

#. Select both the **WinServer-1** and **WinServer-2** VMs and click **Actions > Protect**.

#. Select the **ProdVM-Protection** policy and click **Protect**.

   .. figure:: images/28.png

   *No additional software to configure, just define your RPO, how long you want to keep your snapshots, and optionally what additional clusters should they replicate to - and you have primary storage protection for your VMs throughout their entire lifecycle.*
