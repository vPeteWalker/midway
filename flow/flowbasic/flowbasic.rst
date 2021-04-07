.. _flowbasic:

-------------------
Starting with Flow
-------------------

In this module you will enable Flow within Prism Central and create a simple isolation policy to provide microsegmentation between two VM categories.

**Prerequisite:**

This exercise assumes you have already applied categories to your VMs as described in :ref:`categories` in the :ref:`vmmanage` exercise. If you used the automatic staging for your cluster, you will need to manually apply the VM categories as described in the :ref:`categories` section.

**(Optional) Prerequisite:** Completion of :ref:`deploygraylog`

**Expected Module Duration:** 30 minutes

**Covered Test IDs:** N/A

Enabling Flow
+++++++++++++

*Flow offers policy-based network security and is tightly integrated into both AHV and Prism Central. Using the same categories construct we looked at for VM management, you can easily implement a distributed firewall providing you app-centric policy management for securing VM traffic.*

#. Select :fa:`bars` **> Policies > Security**.

#. Click **Enable**.

   .. note::

      Without licenses installed, you can enable Flow with a 60 day trial, which is more than adequate for a POC.

#. Select **Enable Microsegmentation** and click **Enable**.

   .. note::

      Enabling Flow will add an additional 1GiB of memory to your **PrismCentral** VM. This occurs automatically with Prism Central deployed on an AHV cluster, and does not require restarting Prism Central.

   *Because Flow microsegmentation exists as a policy engine within Prism Central, it can be enabled instantly without the need to deploy additional service VMs, installing software, or using a separate console - unlike competitive solutions like NSX which can be slow and costly to deploy and maintain.*

Isolation Policy
++++++++++++++++

*To show you how easy it is to get started with Flow, we'll begin with a very simple policy that isolates sets of VMs from one another, using the previously created WinServer VMs.*

#. Select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Verify you have your original **WinServer** VM assigned to **Environment:Production** and multiple VMs assigned to **Environment:POC** (e.g. **WinServer-1**-**WinServer-5**).

#. Select **WinServer** and **WinServer-1** and additionally assign the **TemplateType:Application** category and save.

   .. figure:: images/0.png

   *Note we do not see the existing categories assigned to these VMs as the bulk operation will only list categories that the VMs have in common.*

   .. note::

      The choice of using the TemplateType category is somewhat arbitrary. The goal is to have an additional category we can use to group your two WinServer VMs. You could optionally create your own customer category:value pair for this purpose and use these for the exercise.

#. From your original, **Environment:Production** **WinServer** VM, open the VM console and start recurring pings (`ping -t <IP ADDRESS>`) to both an **Environment:POC** **WinServer** VM and an **Environment:POC** + **TemplateType:Application** **WinServer** VM.

   .. figure:: images/1.png

   .. note::

      - Use the VM list to note the IP Addresses of the VMs.

      - If your pings fail BEFORE creating and enabling your Flow policy, confirm the Windows Firewall has been disabled within each VM, as it will block the ICMP traffic by default if left enabled.

      **FEEDBACK** Should we illustrate how to disable the Windows firewall?

   *We'll use the ping as a simple indication of traffic being allowed between the VMs.*

#. Select :fa:`bars` **> Policies > Security**.

   *Similar to previous exercises, you again have the ability to search and filter through all of the security policies within Prism Central.*

#. Click **Create Security Policy**.

#. Select **Isolate Environment (Isolation Policy)** and click **Create**.

#. Fill out the following fields:

   - **Name** - POC-Isolation
   - **Purpose** - Restricting POC SQL VMs from communication with Production VMs
   - **Isolate This Category** - Environment:POC
   - **From This Category** - Environment:Production
   - Click **Apply the isolation only within a subset of the data center**

      - Search for **TemplateType:Application**

   - (Optional - if **Syslog** configured within Prism Central via :ref:`deploygraylog` section) Enable **Policy Hit Logs**

   .. figure:: images/2.png

   *As you can see, parameters for this type of policy are self-explanatory, and the policy can be further narrowed using an additional, third category - in this case, applying the isolation only to Microsoft SQL VMs rather than all Production/POC VMs. You could introduce other categories such as which project a VM belongs to, group ownership, physical location, etc. to create these simple isolation policies.*

#. Click **Save and Monitor**.

   *We could apply the policy immediately to begin blocking traffic, but one of the benefits of Flow is to be able to visualize real-time network traffic between the groups of VMs to understand what communication is currently taking place. This can be very helpful when creating other policy types, by identifying necessary connections that may otherwise get inadvertently blocked.*

#. Click the **Name** of your newly created policy to view discovered traffic.

   .. figure:: images/3.png

#. Click on the stream of traffic traveling from the **Production** category TO the **POC** category.

   .. figure:: images/4.png

   *Here we see the ICMP traffic to the POC VMs has been discovered.*

#. Close the **Connection Details**.

#. Select **Apply** from the top of the screen. Type **APPLY** into the text field and click **OK** to begin enforcement of the policy.

#. Return to your **WinServer** VM console.

   .. figure:: images/5.png

   *Within seconds we see the POC and Production VMs tagged as SQL as no longer able to communicate, whereas the Production VM is still able to communicate with other non-SQL categorized POC VMs.*

Quarantining VMs
++++++++++++++++

*Occasionally, you may have cause to lock down all communications to/from a specific VM regardless of additional policies - such as in the case of malware. With Flow you can quarantine these VMs quickly.*

#. Select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Select the **WinServer-** clone VM which your **Environment:Production** VM is still currently pinging.

#. Click **Actions > Quarantine VMs**.

   *Here you'll see you have two different options for quarantining a VM, a complete lockdown, or allowing restricted access to the VM from specific forensic tools which can be defined in the built-in quarantine policy.*

#. Select **Strict**, and then click **Quarantine**.

   *Now we see all sources unable to reach the quarantined VM, including devices outside of the Nutanix cluster.*

#. (Optional) Return to **Security** and open/update the quarantine policy to show that approved forensic tools could be allowed access to these VMs based on either IP address/range, or by using categories (e.g. adding a **SecurityForsensics** value to the **AppType** category or defining a new category entirely).

#. (Optional) Launch the VM console for the quarantined VM and validate it can no longer reach any outside networks, or ping other WinServer clone VMs.

#. Finally, stop all running ping commands.

Before you go
+++++++++++++

It's important that you revert each of the above modifications you made before continuing on with the workshop. Unquarantine your VM and set your **POC-Isolation** policy back to **Monitor** mode.
